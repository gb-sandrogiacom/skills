# Snippets do WebGUI

Avaliar com `browser_cdp` / `Runtime.evaluate` e `awaitPromise: true`. Substituir `REF` e `NUMERO`.

Clique dentro de uma janela `w`:

```javascript
function click(el, w) {
  const r = el.getBoundingClientRect();
  const init = { bubbles: true, cancelable: true, view: w, button: 0, buttons: 1, clientX: r.left + 8, clientY: r.top + 6 };
  ["pointerdown", "mousedown", "pointerup", "mouseup", "click"].forEach((t) =>
    el.dispatchEvent(new (t.startsWith("pointer") ? w.PointerEvent : w.MouseEvent)(t, init))
  );
}
```

## Pesquisa, OK e BR-Dev

O iframe é `application-SalesOrder-display-iframe`. Esperar o título `BR-Dev` depois do OK. `Exibir documentos de vendas` também casa com `/Exibir/` e encerra o poll cedo demais.

```javascript
const f = document.getElementById("application-SalesOrder-display-iframe");
const doc = f.contentDocument, w = f.contentWindow;
const input = [...doc.querySelectorAll("input")].find((el) =>
  /Referência de cliente como campo matchcode/.test(el.title || "")
);
const btn = [...doc.querySelectorAll("[role=button]")].find((el) =>
  /Executar pesquisa/.test(el.innerText || "")
);
const ctrl = w.oLS.oGetControlById(input.id);
ctrl.focus();
ctrl.setValue(REF);
ctrl.setChanged && ctrl.setChanged(true);
click(btn, w);
// esperar NSH2_copy, click(ok, w), esperar /BR-Dev/ no título
// Documento de vendas: input title === "Documento de vendas"
```

## Fluxo

```javascript
const btn = [...doc.querySelectorAll("[role=button]")].find((b) =>
  /Exibir fluxo de documentos/.test(b.innerText || "")
);
click(btn, w);
// texto do body: /BR-NC Dev Outra Entrada\s+(\d+)/
```

## VF03

```
https://s4prd.sap.grupoboticario.digital/sap/bc/gui/sap/its/webgui?sap-client=500&sap-language=PT&~transaction=*VF03%20VBRK-VBELN=NUMERO;DYNP_OKCODE=/00
```

A partir daqui o WebGUI é `document`, sem iframe.

## Contabilidade, duplo clique e DadosNF-e

```javascript
function dbl(el) {
  const r = el.getBoundingClientRect();
  const init = { bubbles: true, cancelable: true, view: window, button: 0, buttons: 1, clientX: r.left + 8, clientY: r.top + 6, detail: 2 };
  ["pointerdown", "mousedown", "pointerup", "mouseup", "click", "pointerdown", "mousedown", "pointerup", "mouseup", "click", "dblclick"].forEach((t) =>
    el.dispatchEvent(new (t.startsWith("pointer") ? PointerEvent : MouseEvent)(t, init))
  );
}
const contab = [...document.querySelectorAll("[role=button]")].find((b) => (b.innerText || "").trim() === "Contabilidade");
click(contab, window);
const cell = [...document.querySelectorAll("td")].find((e) =>
  (e.innerText || "").replace(/\s+/g, " ").trim() === "Nota Fiscal Brasil" && (e.id || "").startsWith("grid#")
);
dbl(cell.querySelector("span") || cell);
// título: /NFe\s+(\d+)/
const tab = [...document.querySelectorAll("[role=tab]")].find((t) => /DadosNF-e/.test(t.innerText || ""));
click(tab, window);
// Status do documento: input title === "Status do documento"
// Número de nota fiscal eletrônica e Séries: id contém 4B277
// Chave: span cujo texto casa /^\d{44}$/
```

Ids estáveis observados em 29/09/2026: pesquisa `M0:46:::5:22` e `M0:46:::12:1`, OK `NSH2_copy`, documento `M0:46:1::0:17`, fluxo `M0:48::btn[5]`, Contabilidade `M0:48::btn[16]`, aba `M0:46:3::0:7-title`. O id da árvore e o id da grade mudam.
