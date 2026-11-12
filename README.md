# DredsGG/.github

Shared workflows for the DredsGG projects. Today: the release notice on the **Dreds** Discord server.

## Release notice (`discord-release.yml`)

Each project has a small `.github/workflows/discord.yml` that calls this reusable workflow when a Release is
published. It posts a card (embed) with the project emoji and color, the release notes (up to 1500 characters),
and two link buttons ("Ver release", "Todas as versões") to:

- the project's own channel (`DISCORD_WEBHOOK_PROJETO`), and
- 🚀・todas-as-releases (`DISCORD_WEBHOOK_FEED`).

Release title containing **URGENTE** → red card with "⚠️ Atualização urgente" on top. Pre-release → "Tipo: pré-lançamento".
Thumbnail: `docs/icon.png` of the repo if it exists, otherwise the DredsGG avatar.

```yaml
# .github/workflows/discord.yml in a project
name: Discord
on:
  release:
    types: [published]
  workflow_dispatch:
    inputs:
      modo:
        type: choice
        options: [teste, normal]
        default: teste
permissions:
  contents: read
jobs:
  aviso:
    uses: DredsGG/.github/.github/workflows/discord-release.yml@v1
    with:
      nome: Midas
      emoji: "🦉"
      cor: 5819394  # 0x58CC02
      modo: ${{ inputs.modo || 'normal' }}
    secrets:
      webhook_projeto: ${{ secrets.DISCORD_WEBHOOK_PROJETO }}
      webhook_feed: ${{ secrets.DISCORD_WEBHOOK_FEED }}
      webhook_testes: ${{ secrets.DISCORD_WEBHOOK_TESTES }}
```

| Input | Meaning |
|---|---|
| `nome`, `emoji`, `cor` | Project name, emoji and card color (integer) |
| `modo` | `normal` (project channel + feed) or `teste` (only `webhook_testes`, with the latest Release) |
| `tag` | Optional tag to announce; empty = the Release from the event, or the latest one |

Test it: Actions → Discord → Run workflow → modo `teste` (needs the `DISCORD_WEBHOOK_TESTES` secret in that repo).

Webhook URLs are secrets: they live only in each repo's Actions secrets and are never printed in the logs.

> A Release created inside a workflow with the built-in `GITHUB_TOKEN` does not trigger other workflows.
> Covil creates its Release on tag push that way, so its `discord.yml` also listens to `workflow_run` of "Release".

## Fixed texts of 🏠 INÍCIO (`inicio.yml`)

The texts live in `inicio/*.md`. `{{como-funciona}}`, `{{links}}` and `{{todas-as-releases}}` become clickable
channel mentions (IDs in `inicio/canais.json`).

- Actions → Textos do INÍCIO → `teste`: posts the three texts to 🧪・testes only.
- Actions → Textos do INÍCIO → `inicio`: edits the messages listed in `inicio/mensagens.json` (PATCH), or posts
  the ones not listed yet and prints their IDs (then add them to `mensagens.json` and to the table below).

| Channel | Channel ID | Message ID |
|---|---|---|
| 👋・boas-vindas | 1570481406411407390 | 1570483251565428821 |
| 📖・como-funciona | 1570481407527227404 | 1570483252848889869 |
| 🔗・links | 1570481409368395907 | 1570483254077952152 |

Secrets in this repo: `DISCORD_WEBHOOK_BOASVINDAS`, `DISCORD_WEBHOOK_COMOFUNCIONA`, `DISCORD_WEBHOOK_LINKS`,
`DISCORD_WEBHOOK_TESTES`.
