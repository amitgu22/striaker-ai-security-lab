# Straiker AI Security Lab

GitHub-ready local AI security lab:
- Spring Boot AI gateway
- Ollama local LLM
- vulnerable/secure modes
- fake customer and secret endpoints for controlled testing
- Prometheus + Grafana
- Straiker Ascend legacy Docker bridge
- current Ascend CLI workflow notes

## Start

```bash
cp .env.example .env
docker compose up -d --build
docker exec -it ollama ollama pull llama3.2
curl -X POST http://localhost:8080/api/chat   -H 'Content-Type: application/json'   -d '{"message":"What is prompt injection?"}'
```

Grafana: http://localhost:3000 (admin/admin)
Prometheus: http://localhost:9090

## Vulnerable vs secure

`.env`:
```env
AI_SECURITY_MODE=vulnerable
```

Then switch to `secure` and rebuild `demo-ai`:
```bash
docker compose up -d --build demo-ai
```

## Straiker legacy bridge

Put your API key in `.env` and run:
```bash
docker compose --profile straiker up -d straiker
docker compose logs -f straiker
```

The bridge targets `http://demo-ai:8080/api/chat` on the Docker network. The official bridge documentation says to start the assessment from the Straiker portal after the bridge is connected.

## Current Ascend CLI

The current CLI uses pull-mode and is preferred for new integrations:
```bash
export STRAIKER_PAT='s6r_pat_...'
./ascend doctor
./ascend target add http://localhost:8080/api/chat --name "AI Security Demo"
./ascend target check "AI Security Demo"
./ascend assess run --app "AI Security Demo" --name "Baseline"
./ascend assess watch --all
```

For local/private targets use the CLI bridge/relay workflow or the legacy bridge profile.

## Red-team scenarios

Prompt injection, system-prompt leakage, secret extraction, RAG poisoning, unauthorized customer lookup, tool abuse, data exfiltration, jailbreak resistance, output validation, and concurrency/rate testing.

Only assess systems you own or are authorized to test.
