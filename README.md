# Snowpipe Streaming V2 -- Cortex Code Skills

> **Disclaimer:** This is a community demo project by a Snowflake employee, **not** an officially supported Snowflake product or service. It is provided "as-is" for demonstration and educational purposes only. No warranty is expressed or implied. For production streaming ingestion, refer to the official [Snowpipe Streaming documentation](https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-overview).

Two [Cortex Code](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code) skills that automate end-to-end Snowpipe Streaming V2 demos. Say the trigger phrase in Cortex Code and the skill handles everything -- from RSA key generation to live dashboards.

## Skills

### 1. SSv2 Quickstart

**Trigger:** `ssv2 quickstart` or `try snowpipe streaming`

A zero-to-streaming pipeline in ~5 minutes:

- Detects your OS, verifies Python 3.9+, gathers Snowflake context
- Generates RSA key-pair for SDK authentication
- Creates database, schema, table, demo user/role, and grants
- Writes SDK config and streaming script, sets up Python venv
- Deploys a real-time **Streamlit in Snowflake** dashboard
- Streams fake user data via the default auto-created pipe (~10 rows/sec)
- Summarizes results and cleans up

### 2. SSv2 AI Webinar

**Trigger:** `ssv2 ai webinar` or `ssv2 webinar demo`

Everything in the quickstart, **plus** an AI layer for live presentations:

- Streams data **in the background** (30 min) so you can keep presenting
- Creates a **Semantic View** with rich synonyms, computed dimensions, and 8 metrics
- Creates a **Cortex Agent** for natural-language queries on the live streaming data
- Runs showcase queries to prove everything works before handing off to the presenter
- Presenter can toggle between the live Streamlit dashboard and conversational AI in Snowsight

## Installation

### Prerequisites

- [Cortex Code CLI](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code) installed and authenticated
- Python 3.9+
- OpenSSL (for RSA key generation)
- A Snowflake account with ACCOUNTADMIN or SYSADMIN + USERADMIN privileges

> **Security Notice:** These skills are designed for **short-lived demos only**. They generate unencrypted RSA private keys and create temporary Snowflake users with elevated privileges. **Do not use this pattern in production.** For production deployments, use encrypted keys with a passphrase or a secrets manager, and follow the principle of least privilege. All demo objects (users, roles, keys) are cleaned up automatically at the end of the demo.

### Install the skills

Copy the skill directories into your Cortex Code skills folder:

```bash
# Create the skills directory if it doesn't exist
mkdir -p ~/.snowflake/cortex/skills

# Copy both skills
cp -r skills/ssv2-quickstart ~/.snowflake/cortex/skills/
cp -r skills/ssv2-AI-webinar ~/.snowflake/cortex/skills/
```

That's it. The skills are automatically discovered by Cortex Code on the next session.

### Verify installation

Start Cortex Code and type:

```
ssv2 quickstart
```

or

```
ssv2 ai webinar
```

The skill will confirm your intent before creating any resources.

## How It Works

### Architecture

```
Local machine                          Snowflake Cloud
+------------------+                   +---------------------------+
| Python script    |  Snowpipe SDK     | Landing table             |
| (ssv2_demo.py)   | ===============> | (SSV2_QUICKSTART_USERS)   |
| Generates fake   |  RSA key-pair     |                           |
| user data        |  auth (JWT)       | Streamlit dashboard       |
+------------------+                   | (auto-refreshes 2s)       |
                                       |                           |
                                       | Semantic View (AI webinar)|
                                       | Cortex Agent (AI webinar) |
                                       +---------------------------+
```

### Key Concepts

**Default pipe** -- SSv2 auto-creates a pipe on first ingest. No `CREATE PIPE` SQL needed. The SDK references it as `<TABLE_NAME>-streaming` (hyphen, not underscore).

**Demo user** -- A dedicated `SSV2_DEMO_USER` with RSA key-pair auth is created to avoid overwriting any existing keys on your account. Cleaned up automatically.

**Table ownership** -- The demo role owns the table (required for default pipe access). `SELECT` is granted back to your primary role so the Streamlit dashboard can read it.

## What Gets Created

### Snowflake objects (cleaned up automatically)

| Object | Name |
|--------|------|
| Database | `SSV2_QUICKSTART_DB` (or user-chosen) |
| Schema | `SSV2_SCHEMA` |
| Table | `SSV2_QUICKSTART_USERS` (or user-chosen) |
| User | `SSV2_DEMO_USER` |
| Role | `SSV2_DEMO_ROLE` |
| Streamlit app | `SSV2_STREAMING_MONITOR` |
| Stage | `SSV2_STREAMLIT_STAGE` |
| Semantic view | `SSV2_STREAMING_ANALYTICS` (AI webinar only) |
| Cortex Agent | `SSV2_STREAMING_AGENT` (AI webinar only) |

### Local files (preserved after cleanup)

| File | Purpose |
|------|---------|
| `ssv2_demo_sql.log` | Every SQL statement executed during the demo |
| `ssv2_demo.py` | Python streaming script |
| `streamlit_app.py` | Streamlit dashboard source |
| `profile.json` | SDK connection config |
| `rsa_key.p8` | RSA private key (unencrypted, demo-only) |
| `rsa_key.pub` | RSA public key |
| `ssv2_venv/` | Python virtual environment |

## Supported Platforms

| Platform | Status |
|----------|--------|
| macOS (ARM64) | Fully supported |
| Linux (x86_64, ARM64) | Fully supported (glibc >= 2.26) |
| Windows (x86_64) | Experimental (WSL2 or Git Bash recommended) |

## Resources

- [Snowpipe Streaming V2 Overview](https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-overview)
- [SSv2 Getting Started Tutorial](https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-getting-started)
- [Python SDK Reference](https://docs.snowflake.com/en/user-guide/snowpipe-streaming-sdk-python/reference/latest/index)
- [Python SDK on PyPI](https://pypi.org/project/snowpipe-streaming/)
- [Cortex Code Documentation](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code)
- [Cortex Code Skills Guide](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code-skills)

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.

## Trademarks

Snowflake, Snowpipe, Snowpipe Streaming, Snowsight, Cortex, and Streamlit are trademarks or registered trademarks of Snowflake Inc. All other trademarks are the property of their respective owners. Use of these trademarks does not imply endorsement.
