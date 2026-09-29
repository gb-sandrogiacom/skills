# consulta-nf-devolucao

Skill do Cursor Agent para consultar no **SAP S4** o status de **notas fiscais de devolução**, a partir de uma ou mais **referências de cliente**. O fluxo percorre BR-Dev Lojas, o documento BR-NC, a NFe interna e a aba **DadosNF-e**, onde o status oficial é o campo **Status doc.**

## Pré-requisitos

- Skill instalada em `.cursor/skills/` (projeto) ou `~/.cursor/skills/` (máquina). Veja [instalação](../README.md#como-usar) no README do repositório.
- **VPN** com rota para a rede interna (sem isso, `s4prd.sap.grupoboticario.digital` pode dar timeout).
- **Sessão SAP autenticada** no browser integrado do Cursor.
- Agent com acesso ao browser (a skill orienta o agente a automatizar o WebGUI/Fiori).

## Como usar

1. Confira em **Customize → Skills** se `consulta-nf-devolucao` aparece.
2. No chat do Agent, invoque com `/consulta-nf-devolucao` ou descreva o pedido em linguagem natural (o agente pode aplicar a skill pela descrição).
3. Informe as **referências de cliente** separadas por vírgula, por exemplo:

   ```text
   Consulta status de NF de devolução: 164058928, 164574700, 164950530
   ```

4. Aguarde o agente processar **uma referência por vez** e, ao final, exibir a tabela consolidada.

### Quando pedir

Use esta skill quando precisar de status de NF de devolução, **BR-Dev Lojas**, **BR-NC**, dados da aba **DadosNF-e**, **Nº da NF-e**, **séries** ou **chave de acesso (44 dígitos)** a partir de referências de cliente.

## Resultado esperado

Tabela com as colunas (nesta ordem):

| Coluna | Conteúdo |
| --- | --- |
| Referência cliente | Referência informada |
| BR-Dev Lojas | Documento de vendas na tela BR-Dev |
| BR-NC | Número lido no fluxo (BR-NC Dev Outra Entrada) |
| Documento NFe | Número interno da NFe (título da tela de devolução) |
| Status doc. | Status do documento na aba DadosNF-e |
| Nº da NF-e | Número da NF-e eletrônica |
| Séries | Série da NF-e |
| Chave de acesso | Chave de 44 dígitos |

Se uma referência falhar (documento inexistente, timeout SAP, etc.), a linha traz o motivo na coluna correspondente e as demais referências seguem sendo consultadas.

Em execuções com canvas configurado, o agente também pode atualizar o painel visual com totais (consultadas / autorizadas) e data.

## Arquivos da skill

| Arquivo | Papel |
| --- | --- |
| [SKILL.md](SKILL.md) | Instruções completas para o agente (fluxo S4, regras e formato da saída). |
| [reference.md](reference.md) | Snippets JavaScript/CDP para cliques no WebGUI dentro do iframe. |

O agente lê o `SKILL.md` automaticamente; você não precisa anexá-lo manualmente.
