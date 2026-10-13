# EU vacancies tracker: MCP server

A remote [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI assistants the open vacancies and the competitions of the EU institutions, agencies and bodies. The lists are read from the official job pages once a day by [EU vacancies tracker](https://eujobs.montoyer.com), an independent site run by Montoyer in Brussels.

```
https://eujobs.montoyer.com/mcp
```

Free, read-only, no account and no key. Nothing to install: the server is hosted, and this repository holds its description only.

## What an assistant can answer with it

- Which EU competitions are open for applications, and when do they close?
- Are there contract agent posts in Luxembourg closing this month?
- Where does the competition EPSO/AD/427/26 stand?
- Which agencies are recruiting temporary agents at grade AD 7 in Belgium?

Every offer comes with the address of its page on the site and of the official notice on the institution's own site, where applications are made.

## Tools

| Tool | Gives |
|---|---|
| `list_vacancies` | The vacancies and open calls that are open or announced, of every contract type, soonest deadline first |
| `list_competitions` | The competitions for officials that are announced, open for applications, or whose selection is under way, one line per competition |
| `get_vacancy` | One vacancy in full, by its reference: department, domains, attached files and the text of the notice |
| `get_competition` | One competition in full, by its reference: its fields, the stage of each, its timeline and the text of the notice |

All four are read-only.

### Filters

Both lists take the same filters. `list_competitions` also takes `stage`.

| Parameter | Values |
|---|---|
| `country` | ISO codes of two letters: `BE`, `LU` |
| `grade` | A category (`AD`, `AST`, `AST/SC`, `FG I` to `FG IV`) or an exact grade (`AD 7`) |
| `contract_type` | `official`, `temporary-agent`, `contract-agent`, `sne`, `trainee`, `local-agent`, `other` |
| `deadline_before`, `deadline_after` | A day, `2026-10-31`, Brussels time |
| `query` | Words to look for in the title, the reference, the institution and the text of the notice |
| `institution` | Acronyms: `EC`, `EP`, `EEAS`, `EMA`, `ECB` |
| `stage` | `announced`, `open`, `under_way` |
| `cursor` | The `next_cursor` of the answer before, for the 50 lines that follow |

Several values of one filter widen the result. Different filters narrow it.

### An answer

```json
{
  "official_pages_last_read": "2026-10-13T03:10:00Z",
  "licence": { "name": "Creative Commons Attribution 4.0 International (CC BY 4.0)" },
  "warning": "Check the deadline on the official notice before applying.",
  "site": "https://eujobs.montoyer.com",
  "total": 34,
  "items": [
    {
      "reference": "ENISA-TA-AD8-2026-14",
      "title": "Cybersecurity expert",
      "type": "vacancy",
      "institution": "European Union Agency for Cybersecurity",
      "institution_code": "ENISA",
      "contract_type": "temporary-agent",
      "grades": ["AD 8"],
      "locations": [{ "city": "Athens", "country": "GR" }],
      "status": "open",
      "deadline_at": "2026-10-12T22:59:00+02:00",
      "url": "https://eujobs.montoyer.com/offers/enisa-enisa-ta-ad8-2026-14",
      "official_url": "https://eu-careers.europa.eu/jobs/cybersecurity-expert_en"
    }
  ]
}
```

A date the site does not have is absent. A planned date is marked as planned.

## Adding it to an assistant

**Claude.** In the settings, under "Connectors", choose "Add custom connector", give it a name and paste the address.

**ChatGPT.** In the settings, under "Apps and connectors", create a connector with the address and no authentication.

**Claude Code.**

```bash
claude mcp add --transport http eu-vacancies https://eujobs.montoyer.com/mcp
```

**A client with a configuration file.**

```json
{
  "mcpServers": {
    "eu-vacancies": {
      "type": "http",
      "url": "https://eujobs.montoyer.com/mcp"
    }
  }
}
```

The site keeps a page for this, with the questions to try: [eujobs.montoyer.com/assistants](https://eujobs.montoyer.com/assistants).

## Good to know

- **Check the official notice.** The official pages are read once a day, and a page can be misread. The deadline on the official notice is the one that counts.
- **Independent.** The site is not affiliated with EPSO, the European Commission or any other EU institution.
- **Fair use.** The address is there for everyone. A caller that asks too much, too fast, is held back.
- **Transport.** Streamable HTTP: one JSON-RPC message per `POST`, one JSON answer, no stream.

## Licence

The lists, as the site compiles them, and this repository are offered under [Creative Commons Attribution 4.0 International](LICENSE): reuse them for any purpose by naming the source, "EU vacancies tracker, Montoyer", with a link to <https://eujobs.montoyer.com>.

The text of a notice and its attached files belong to the institution that published them and stay under its terms.

## Contact

<www@montoyer.com>
