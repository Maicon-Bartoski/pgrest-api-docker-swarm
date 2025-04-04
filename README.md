# PgREST API Stack

**Descrição breve**: Uma stack Docker Swarm com PostgREST que transforma qualquer banco de dados PostgreSQL em uma API RESTful segura, escalável e fácil de implantar. Ideal para criar APIs externas com autenticação JWT e integração com Traefik.

## Pré-requisitos

Antes de começar, certifique-se de ter:
- Docker instalado e configurado em modo Swarm (`docker swarm init`).
- Acesso a um banco de dados PostgreSQL (local ou remoto).
- Traefik configurado como proxy reverso (opcional, mas necessário para as configurações de TLS e roteamento fornecidas).
- Uma rede Docker externa chamada `cloud` (crie com `docker network create --driver overlay cloud` se ainda não existir).

## Instruções de Uso

Siga os passos abaixo para implantar e usar a stack `pgrest-api`:

### 1. Clone o Repositório
Clone este repositório para sua máquina local:
```bash
git clone https://github.com/[seu-usuario]/pgrest-api.git
cd pgrest-api
```

### 2. Configure as Variáveis de Ambiente
Edite o arquivo `docker-compose.yml` e substitua os placeholders pelos seus dados:
- `[USUARIO]`: Nome de usuário do banco PostgreSQL.
- `[SENHA]`: Senha do banco PostgreSQL.
- `[HOST]`: Endereço do servidor PostgreSQL (ex.: `postgres` ou `localhost`).
- `[PORTA]`: Porta do PostgreSQL (geralmente `5432`).
- `[BANCO]`: Nome do banco de dados a ser exposto como API.
- `[URL_DO_SERVIDOR]`: URL base para a API OpenAPI (ex.: `https://api.pgrest.store`).
- `[CHAVE_SECRETA]`: Uma chave JWT segura (gere uma com, por exemplo, `openssl rand -base64 32`).

Exemplo de configuração preenchida:
```yaml
PGRST_DB_URI: postgresql://meu_usuario:minha_senha@postgres:5432/meu_banco
PGRST_OPENAPI_SERVER_PROXY_URI: https://api.pgrest.store
PGRST_JWT_SECRET: minha_chave_secreta_aqui
```

### 3. (Opcional) Ajuste o Domínio
No arquivo `docker-compose.yml`, substitua `api.pgrest.store` pelo domínio que você deseja usar:
```yaml
traefik.http.routers.postgrest.rule=Host(`seu.dominio.com`)
```
Certifique-se de que o domínio esteja configurado no seu DNS para apontar para o nó Swarm.

### 4. Implante a Stack
Execute o comando abaixo para implantar a stack no Docker Swarm:
```bash
docker stack deploy -c docker-compose.yml pgrest-api
```

### 5. Verifique a Implantação
Confirme que o serviço está rodando:
```bash
docker service ls
```
Você verá algo como `pgrest-api_postgrest` com 1 réplica ativa.

### 6. Acesse a API
- Abra seu navegador ou use uma ferramenta como `curl` para testar a API:
  ```bash
  curl https://api.pgrest.store
  ```
- Se configurado corretamente, você verá a documentação OpenAPI gerada automaticamente pelo PostgREST.

### 7. Utilize a API
- Consulte a [documentação do PostgREST](https://postgrest.org/en/stable/) para aprender como fazer requisições (GET, POST, PUT, DELETE) ao seu banco de dados.
- Exemplo de requisição GET para uma tabela chamada `users`:
  ```bash
  curl https://api.pgrest.store/users
  ```

## Notas Adicionais
- **Segurança**: A chave JWT (`PGRST_JWT_SECRET`) é essencial para proteger sua API. Nunca a exponha publicamente.
- **Escalabilidade**: Ajuste o número de réplicas em `deploy.replicas` para escalar horizontalmente.
- **Rede**: A rede `cloud` é assumida como externa. Crie-a antes da implantação se necessário.

## Licença
Este projeto está licenciado sob a [MIT License](LICENSE.md).
