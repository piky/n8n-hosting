# Self-hosted n8n
This repository contains configuration files for running n8n (a workflow automation platform) as a self-hosted instance on your local machine or GitHub Codespace.

## Architecture Overview

```mermaid
flowchart TB
    subgraph User["User Environment"]
        direction TB
        CLI["docker compose up -d"]
        ENV[".env File\n DOMAIN_NAME\n SUBDOMAIN\n TUNNEL_TOKEN"]
    end

    subgraph Docker["Docker Network"]
        subgraph Traefik["Traefik Reverse Proxy"]
            LB["Load Balancer\n :80, :443"]
            ACME["Let's Encrypt\nTLS Certificate"]
            HTTPS["HTTPS Redirect"]
        end

        subgraph N8N["n8n Container"]
            API["n8n API\n:5678"]
            WF["Workflow Engine"]
            Runner["Workers\nN8N_RUNNERS_ENABLED=true"]
        end

        subgraph CloudflareTunnel["Cloudflare Tunnel"]
            Tunnel["cloudflared\nTUNNEL_TOKEN"]
        end
    end

    subgraph External["External Services"]
        DNS["DNS\n8n.domain.com"]
        Internet["Internet"]
    end

    CLI --> ENV
    ENV --> Docker
    LB --> HTTPS
    HTTPS --> ACME
    ACME --> LB
    LB --> API
    API --> WF
    WF --> Runner
    Tunnel --> DNS
    DNS --> Internet
    Internet --> Tunnel
```

## Key Features
* Docker Compose setups for various deployment scenarios (basic, with PostgreSQL, with worker mode)
* Kubernetes manifests for K8s deployments
* Traefik for reverse proxy
* CloudFlare Tunnel integration to expose your local n8n instance to the internet

## Pre-requisites
The `.env` file that contains all these required credentials:  
```console
SUBDOMAIN=n8n
DOMAIN_NAME=yourdomain.com
TUNNEL_TOKEN=<CloudFlare tunnel token>
```

### Estimated Monthly Cost
| Service Provider | Monthly Cost | *Notes |
|------------------|---------|------|
| n8n Cloud ([free 14-day trial](https://n8n.io)) | $29 | *self-hosted VPS ~$10* |
| OpenAI LLM | $10 | *Credit top-up can be varied* |
| LINE Official Account for Messaging API| Free for 1:1 chat messaging | *$40 for broadcast* |
| CloudFlare Domain Name| $1 |  |
| Ollama Cloud for LLM | free |  |
| **Sum** | **$80** |

## How-to Run 
`docker compose up -d`

## Configure an Example Workflow
1. To be able to import the example workflow you need to create new workflow from `Start from scratch` first.
2. Create a credential for your provider of choice. Here the Ollama Cloud endpoint will be used.
3. Create Line API credentials.
4. Install Line Messaging node by community.
5. Copy the generated `webhook` from `Line Messaging API`.
6. Do not for get to `Publish` the workflow.

## Challenges
* Wisely choose the most *COST-EFFICIENCY * model that suits your node/task.
* Searching nodes/templates from community first before draw your own workflow.

## Contribution
[Issues](https://github.com/piky/n8n-hosting/issues) or [suggestions](https://github.com/piky/n8n-hosting/discussions) for further improvement are welcome.
