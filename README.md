# ollama-nothink-proxy

A Docker sidecar that disables thinking mode for Qwen3 models in Ollama.

## Why this exists

The obvious solutions **do not work**:

- **Modelfile `PARAMETER think false`** — Ollama rejects it. `think` is not a valid Modelfile parameter.
- **Modelfile `SYSTEM` prompt** — The model ignores it and thinks anyway.
- **Zed `enable_thinking: false`** — Zed does not send `think: false` to Ollama. It injects `/no_think` as a user message, which the chat template also ignores.

The only confirmed working method is passing `"think": false` in the API request body — which Zed cannot do.

## What this does

Sits between Zed (or any client) and Ollama. Intercepts every `/api/chat` and `/api/generate` request and injects `"think": false` before forwarding.

## Usage

1. Attach the container to your network with a static IP
2. Point your client's Ollama `api_url` at the proxy instead of Ollama directly

```yaml
# In your Zed settings.json
"language_models": {
  "ollama": {
    "api_url": "http://<proxy-ip>:11435"
  }
}
```

## Configuration

| Variable | Default | Description |
|---|---|---|
| `OLLAMA_HOST` | `http://10.236.224.24:11434` | Upstream Ollama address |
| `PROXY_PORT` | `11435` | Port the proxy listens on |

## Requirements

- Docker Compose v2.23.0+ (required for `configs.content`)
- An existing Docker network with the IP range you want to use
- Being frustrated enough to want to hack a fix
