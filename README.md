# Skills

Agregador de [Agent Skills](https://cursor.com/docs/skills) do Cursor. Cada pasta na raiz que contém um `SKILL.md` é uma skill versionada: o agente lê a descrição e aplica o fluxo quando o pedido combina com ela.

## Catálogo

| Skill | Quando usar |
| --- | --- |
| [consulta-nf-devolucao](consulta-nf-devolucao/SKILL.md) | Status de notas fiscais de devolução no S4 a partir de referências de cliente (BR-Dev Lojas, BR-NC, DadosNF-e, chave de acesso). |

## Como usar

O Cursor descobre skills nestes diretórios:

| Caminho | Escopo |
| --- | --- |
| `.cursor/skills/` ou `.agents/skills/` | Projeto em que a pasta foi copiada |
| `~/.cursor/skills/` ou `~/.agents/skills/` | Todos os projetos desta máquina |

Copie a pasta da skill inteira (o `SKILL.md` e os arquivos ao lado, como `reference.md`).

No projeto de trabalho:

```bash
mkdir -p .cursor/skills
cp -R consulta-nf-devolucao .cursor/skills/
```

Na sua máquina:

```bash
mkdir -p ~/.cursor/skills
cp -R consulta-nf-devolucao ~/.cursor/skills/
```

A partir de outro repositório, troque a origem pelo clone deste repo:

```bash
git clone git@github.com:gb-sandrogiacom/skills.git
cp -R skills/consulta-nf-devolucao .cursor/skills/
```

Abra **Customize → Skills** para conferir se a skill apareceu. No chat do Agent, digite `/` e o nome da skill para invocá-la. Sem a barra, o agente também pode aplicá-la quando a descrição bater com o pedido.

Skills em `~/.cursor/skills/` ficam na máquina local. Para o Cloud Agent usar as mesmas skills pessoais, ative **Sync Skills for Cloud Agents** em **Settings → Agents**. Skills de projeto entram no Cloud Agent quando estão no repositório que ele abre.

## Estrutura de uma skill

```text
nome-da-skill/
├── SKILL.md          # obrigatório
├── reference.md      # opcional, lido sob demanda
├── scripts/          # opcional, comandos que o agente executa
└── assets/           # opcional, templates e arquivos estáticos
```

O `SKILL.md` começa com frontmatter YAML. `name` é o identificador (letras minúsculas, números e hífens, no máximo 64 caracteres) e precisa ser igual ao nome da pasta. `description` diz o que a skill faz e em que situação usá-la; é esse texto que o agente usa para decidir se ela entra no contexto.

```markdown
---
name: nome-da-skill
description: O que a skill faz e quando usá-la. Inclua os termos que a pessoa costuma escrever no pedido.
---

# Nome da skill

Instruções objetivas para o agente.
```

Mantenha o `SKILL.md` curto. Detalhe de tela, snippets e tabelas longas ficam em arquivos irmãos, com link direto a partir do `SKILL.md`.

## Adicionar uma skill

1. Crie a pasta na raiz deste repositório, com o mesmo nome do campo `name`.
2. Escreva o `SKILL.md` com o que a skill faz, quando usá-la e os passos que o agente não acertaria sozinho.
3. Coloque material longo em `reference.md`, `scripts/` ou `assets/`.
4. Inclua uma linha no catálogo deste README.
5. Abra um pull request.

Para redigir a skill no Cursor, use `/create-skill`.
