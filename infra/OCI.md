# Configuração da OCI para os bancos

Objetivo: o backend dos devs conecta direto no Postgres (5432) e no MongoDB (27017) da VM, sem abrir as portas para a internet inteira.

## 1. Regras de ingress (Security List ou NSG)

Console OCI: Networking > Virtual Cloud Networks > VCN da VM > Security Lists (ou Network Security Groups).

Uma regra por dev e por porta:

| Origem (CIDR)      | Protocolo | Porta destino |
|--------------------|-----------|---------------|
| `<ip-do-dev>/32`   | TCP       | 5432          |
| `<ip-do-dev>/32`   | TCP       | 27017         |

- Stateful: sim. Porta de origem: vazia (all).
- Nunca usar `0.0.0.0/0` nessas portas.
- IP do dev: `curl ifconfig.me` na máquina dele.
- Novo dev ou IP mudou: adicionar ou atualizar a regra.

## 2. Firewall da VM

A OCI bloqueia também dentro da VM (iptables). Para cada IP de dev:

```bash
sudo iptables -I INPUT -p tcp -s <ip-do-dev> --dport 5432 -j ACCEPT
sudo iptables -I INPUT -p tcp -s <ip-do-dev> --dport 27017 -j ACCEPT
sudo netfilter-persistent save   # Ubuntu
# Oracle Linux: sudo service iptables save
```

Docker publica as portas por conta própria e pode ignorar o firewall do host. Por isso a regra da OCI (passo 1) é a proteção que vale.

## 3. Subir os bancos

```bash
cd infra
docker compose up -d postgres mongodb
```

Trocar `<random-password>` do `docker-compose.yml` antes. As senhas só valem na primeira criação do volume.

## 4. Testar de uma máquina dev

```bash
psql "postgresql://api6_admin:<senha>@<ip-publico-da-vm>:5432/api6"
mongosh "mongodb://api6_admin:<senha>@<ip-publico-da-vm>:27017/api6?authSource=admin"
```

De um IP que não está liberado, a conexão deve dar timeout.

## 5. Backup (Object Storage)

1. Criar um bucket privado no Object Storage, com política de retenção (ex.: 30 dias) para que dados excluídos saiam dos backups no prazo da LGPD.
2. Dar à VM acesso ao bucket: Dynamic Group da instância + policy `Allow dynamic-group <grupo> to manage objects in compartment <compartimento>`.
3. Cron diário na VM com `pg_dump` e `mongodump`, enviando para o bucket (`oci os object put`).
4. Após qualquer restore, reaplicar o registro de contas excluídas antes de reabrir o acesso.

## Pendências

- Senhas reais no compose.
- TLS nos dois bancos (hoje a conexão é sem criptografia).
