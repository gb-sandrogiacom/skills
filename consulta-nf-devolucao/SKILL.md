---
name: consulta-nf-devolucao
description: Consulta no S4 o status de notas fiscais de devolução a partir de uma ou mais referências de cliente e devolve uma tabela. Use quando o usuário pedir status de NF de devolução, BR-Dev Lojas, BR-NC, DadosNF-e, chave de acesso, ou informar uma lista de referências de cliente para buscar no S4.
---

# Consulta de status de NF de devolução no S4

O usuário informa referências de cliente separadas por vírgula. Consultar **uma por vez**. Montar a tabela só quando a lista acabar.

A consulta termina na aba **DadosNF-e**. O status da nota é o campo **Status doc.** dessa aba. Não há tela seguinte.

Snippets de browser que já funcionaram: [reference.md](reference.md).

## Antes de começar

- Usar o browser integrado do Cursor. A sessão SAP precisa estar autenticada.
- Sem rota VPN para `10.180.0.0`, `s4prd.sap.grupoboticario.digital` responde `ERR_CONNECTION_TIMED_OUT`.
- Travar o browser (`browser_lock`) durante cada referência e soltar ao terminar a lista.
- Não clicar em **Modificar documento de faturamento**.
- Não forçar `ClientAction=submit`. Isso tira a sessão da transação.

## Entrada

Separar a mensagem do usuário em referências numéricas. Ignorar espaços. Exemplo: `164058928, 164574700, 164950530`.

Se não houver referência, pedir a lista e parar.

## Para cada referência

Recomeçar pela URL do Fiori. A VF03 substitui a página inteira.

`https://s4prd.sap.grupoboticario.digital/sap/bc/ui2/flp/FioriLaunchpad.html?sap-client=500#SalesOrder-display?sap-ui-tech-hint=GUI`

Os passos 1 a 3 acontecem dentro do iframe `application-SalesOrder-display-iframe`. O snapshot do browser não vê o WebGUI. Disparar cliques no `contentDocument` do iframe (`pointerdown`, `mousedown`, `pointerup`, `mouseup`, `click`). `browser_press_key` e `browser_mouse_click_xy` não entram no iframe.

### 1. Pesquisar

Esperar o título **Exibir documentos de vendas**.

- Campo: input com título `Referência de cliente como campo matchcode` (id estável `M0:46:::5:22`). Preencher com `oLS.oGetControlById(id).setValue(referencia)` e `setChanged(true)`.
- Botão: texto `Executar pesquisa` (id estável `M0:46:::12:1`).

Esperar o diálogo **Restringir interv.valores**.

### 2. BR-Dev Lojas

Clicar em OK (`NSH2_copy`). Esperar um título que contenha `BR-Dev`, não basta conter `Exibir`.

Guardar o input com título `Documento de vendas` (id estável `M0:46:1::0:17`). Conferir que o input `Referência do cliente` é a referência pedida.

### 3. Fluxo e VF03

Clicar em **Exibir fluxo de documentos** (`M0:48::btn[5]`). Esperar o título **Fluxo de documentos**.

Ler o número no texto da árvore, com os zeros à esquerda:

`BR-NC Dev Outra Entrada\s+(\d+)`

Não selecionar a linha da árvore e não clicar em **Exibir documento**. O servidor responde "Selecionar um documento de vendas" para seleção sintética. O id da árvore muda a cada renderização.

Abrir a VF03 no documento principal, fora do iframe:

`https://s4prd.sap.grupoboticario.digital/sap/bc/gui/sap/its/webgui?sap-client=500&sap-language=PT&~transaction=*VF03%20VBRK-VBELN=<numero>;DYNP_OKCODE=/00`

Esperar o título **BR-NC Dev Outra Entrada**.

### 4. Contabilidade e documento da NFe

Clicar em **Contabilidade** (`M0:48::btn[16]`). Esperar o diálogo **Lista de documentos na contabilidade**.

Achar a célula cujo texto é exatamente `Nota Fiscal Brasil` e disparar `dblclick` nela (dois cliques e depois `dblclick`). O id da grade muda (`grid#C…`).

Guardar o número do título com `NFe\s+(\d+)`. Exemplo de título: `Exibir Devolução NF de saída (entrada) - NFe 70868032`. Esse número é o documento interno da NFe. Não usar o Nº da NF-e do formulário no lugar dele.

### 5. DadosNF-e

Clicar na aba com role `tab` e texto `DadosNF-e` (`M0:46:3::0:7-title`).

Ler pelo título do campo, não só pelo id:

- Status doc.: input com título `Status do documento`
- Nº da NF-e: input com título `Número de nota fiscal eletrônica` cujo id contém `4B277`
- Séries: input com título `Séries` cujo id contém `4B277`
- Chave de acesso: texto de 44 dígitos ao lado do rótulo `Chave de acesso` (não é input)

Não gravar nome, CPF ou endereço de cliente.

## Falha de uma referência

Seguir para a próxima. Na linha, preencher a referência e a coluna que falhou com o motivo. Motivos previstos: diálogo sem linha, título sem `BR-Dev`, fluxo sem `BR-NC Dev Outra Entrada`, lista sem `Nota Fiscal Brasil`, aba sem os quatro campos, timeout ou mensagem do SAP.

## Tabela

Colunas, nesta ordem:

- Referência cliente
- BR-Dev Lojas
- BR-NC
- Documento NFe
- Status doc.
- Nº da NF-e
- Séries
- Chave de acesso

Mostrar a tabela no chat. Atualizar o canvas [consulta-nf-devolucao.canvas.tsx](/Users/sandro.giacomozzi/.cursor/projects/Users-sandro-giacomozzi-cursor-local-worker/canvases/consulta-nf-devolucao.canvas.tsx) com as linhas desta execução: título, os dois totais (consultadas e autorizadas) e a data. `rowTone` `success` só quando Status doc. contiver `Autorizado`.
