# Security Policy

## Supported Versions

Use this section to tell people about which versions of your project are
currently being supported with security updates.

| Version | Supported          |
| ------- | ------------------ |
| 5.1.x   | :white_check_mark: |
| 5.0.x   | :x:                |
| 4.0.x   | :white_check_mark: |
| < 4.0   | :x:                |

## Reporting a Vulnerability

Use this section to tell people how to report a vulnerability.

docker-compose.yml
VULNERABILIDADES NESSE CÓDIGOProblemaRiscoPor quêPOSTGRES_PASSWORD: postgresCríticoSenha padrão. Qualquer bot na internet testa postgres/postgres e entra.POSTGRES_USER: postgresAltoUsuário superuser padrão. Se invadir, tem controle total do banco.ports: "5432:5432"AltoExpõe o banco pra internet inteira. Qualquer um pode tentar conectar.Sem restart: unless-stoppedMédioSe o container cair, seu banco não volta sozinho.Sem network isoladaMédioOutros containers podem acessar o banco direto sem restrição.Dados sem criptografiaMédioSe alguém pegar o postgres-data, lê tudo. Não tem criptografia em repouso.Sem limite de recursoBaixoUm ataque pode derrubar o container consumindo CPU/RAM.VERSÃO SEGURA PRA PRODUÇÃOyamlservices:
  db:
    image: postgres:17-alpine
    container_name: taskdb
    environment:
      POSTGRES_USER: ${DB_USER}           # Vem do .env
      POSTGRES_PASSWORD: ${DB_PASSWORD}   # Vem do .env - senha forte
      POSTGRES_DB: taskdb
    # ports: REMOVIDO - não expõe pra fora
    # Se precisar acessar local: "127.0.0.1:5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - backend
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER} -d taskdb"]
      interval: 10s
      timeout: 5s
      retries: 5
    security_opt:
      - no-new-privileges:true
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M

volumes:
  postgres-data:

networks:
  backend:
    driver: bridgeCria um arquivo .env na mesma pasta:javascriptDB_USER=taskuser
DB_PASSWORD=GereUmaSenhaForteAqui123

Nunca commita o .env no Git. Coloca no .gitignore.
