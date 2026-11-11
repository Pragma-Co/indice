# Ollama na VM Oracle

Nginx (porta 80) na frente do Ollama. Os modelos ficam no volume `ollama_data`.

```bash
docker compose up -d
docker compose exec ollama ollama pull llama3.2:1b
```

O backend usa `OLLAMA_BASE_URL=http://<host-da-vm>` e `OLLAMA_MODEL` no `.env`.

Não use `docker compose down -v`: apaga os modelos.

## Bancos de dados (Postgres e MongoDB)

A VM serve o Postgres (5432) e o MongoDB (27017). Volumes: `postgres_data` e `mongo_data` (`down -v` apaga os dois).

As portas 5432 e 27017 ficam publicadas na VM para o backend dos devs conectar direto. A proteção é a rede, não o compose:

- OCI (Security List ou NSG): ingress TCP 5432 e 27017 só para o IP de cada dev (`/32`). Nunca `0.0.0.0/0`.
- Firewall da VM (iptables): liberar as mesmas portas para os mesmos IPs.
- Novo dev ou IP mudou: adicionar/atualizar a regra na OCI.
- Se o IP dos devs variar muito, usar túnel SSH: `ssh -N -L 5432:127.0.0.1:5432 -L 27017:127.0.0.1:27017 pragma` (nesse caso voltar as portas do compose para `127.0.0.1`).

### Backup e LGPD

- Backup diário (`pg_dump` e `mongodump`) para o Object Storage da OCI, com retenção definida.
- Um restore traz de volta contas já excluídas: manter um registro das exclusões (id e data, sem dado pessoal) e reaplicá-lo após cada restore.
