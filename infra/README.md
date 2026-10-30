# Ollama na VM Oracle

Nginx (porta 80) na frente do Ollama. Os modelos ficam no volume `ollama_data`.

```bash
docker compose up -d
docker compose exec ollama ollama pull llama3.2:1b
```

O backend usa `OLLAMA_BASE_URL=http://<host-da-vm>` e `OLLAMA_MODEL` no `.env`.

Não use `docker compose down -v`: apaga os modelos.
