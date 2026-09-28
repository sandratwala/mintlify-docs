# Probe Technical Documentation (Mintlify)

Technical documentation for **Probe**, a B2B battery-recycling platform, rebuilt on [Mintlify](https://mintlify.com) from the original GitHub Pages site.

## Links

| Resource | URL |
|---|---|
| Published docs (Mintlify) | https://sandratwala.mintlify.site/docs/index |
| Original docs (GitHub Pages) | https://akirachix.github.io/HERckers_Technical_Documentation/ |
| System architecture diagram (Lucid) | https://lucid.app/lucidchart/7456fda0-595f-4455-a829-b33f54da52d1/edit |
| MCP server | https://sandratwala.mintlify.site/mcp |

## What's covered

- **Architecture**: layers, components, and data flow across the web dashboard, mobile app, FastAPI backend, PostgreSQL, and the IoT pipeline
- **Hardware & State of Health**: battery sensors, ESP32, HiveMQ MQTT, and the SoH calculation
- **Backend**: API reference, authentication (JWT + RBAC), database schema, setup, deployment, and testing
- **Applications**: Next.js web dashboard and Flutter mobile app
- **Development**: code standards, deployment, and testing/QA
- **Reference**: glossary

## Repository structure

```
mintlify-docs/
├── docs.json            # Mintlify config and navigation
├── favicon.svg
├── docs/
│   ├── index.md
│   ├── overview.md
│   ├── architecture.md
│   ├── hardware.md
│   ├── integration.md
│   ├── security.md
│   ├── frontend-web.md
│   ├── mobile.md
│   ├── code-standards.md
│   ├── deployment.md
│   ├── qa.md
│   ├── glossary.md
│   └── backend/         # api-reference, authentication, database,
│                        # deployment, overview, setup, testing
├── images/
├── logo/
└── .gemini/
    └── settings.json    # Gemini CLI MCP connection
```

## Run locally

```bash
npm i -g mint        # install the Mintlify CLI (once)
mint dev             # preview at http://localhost:3000
mint broken-links    # check for broken internal links
```

Pushing to `main` triggers an automatic rebuild and deploy on Mintlify.

## MCP integration

Mintlify generates an MCP server automatically for published public docs. Connect Gemini CLI to it:

```bash
gemini mcp add --transport http probe-docs https://sandratwala.mintlify.site/mcp
```

Authentication uses a Gemini API key set as an environment variable (never commit the key):

```bash
export GEMINI_API_KEY="your-key-here"
```

Then run `gemini` and ask a question about the docs. The `search_probe_docs` tool call confirms the MCP server is being used. To make sure it queries the server rather than reading local files, name it in the prompt:

```
Using the probe-docs MCP server, search for how the database is structured.
```

## Contributing

1. Edit or add pages under `docs/`
2. Register new pages in the `navigation` section of `docs.json`
3. Run `mint dev` and `mint broken-links` before pushing
