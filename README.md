# OG Burgers - API de Checkout (Velana)

Pequena API que cria checkouts na Velana para o combo fixo (2 hambúrgueres + 1 bebida) com preço unitário de R$ 39,90.

Como funciona
- A API expõe `POST /checkout`.
- Body esperado (JSON):
  - `quantity` (number, obrigatório) — número de combos desejados.
  - `burger1`, `burger2`, `drink` (strings, opcionais) — usado como metadata.
  - `splits` (array, opcional) — se quiser repassar `splits` para Velana.
  - `settings` (object, opcional) — sobrescreve as configurações padrão de pagamento.

O servidor calcula `amount = quantity * 3990` (em centavos) e monta o payload enviado para `https://api.velana.com.br/v1/checkouts`.

Variáveis de ambiente
- `VELANA_SECRET` — sua chave secreta Velana. Se não definida, a API vai apenas retornar o payload montado sem chamar Velana.
- `VELANA_URL` — (opcional) URL do endpoint Velana (default `https://api.velana.com.br/v1/checkouts`).

Instalação
```powershell
cd C:\Users\mateu\ogburgers
npm install
```

Execução
```powershell
# desenvolvimento
npm run dev

# produção
npm start
```

Exemplo de request (curl)
```bash
curl -X POST http://localhost:3000/checkout \
  -H "Content-Type: application/json" \
  -d '{"quantity":3, "burger1":"Tradicional Angus", "burger2":"Cheddar Bacon", "drink":"Coca-Cola Lata"}'
```

Resposta de exemplo (quando `VELANA_SECRET` não está setada):
```json
{
  "note": "VELANA_SECRET not set, returning payload only",
  "payload": { /* payload enviado para Velana */ }
}
```

Deploy
------

Usando Docker (local ou host com container):

```bash
# build
docker build -t ogburgers:latest .

# run (defina VELANA_SECRET ao rodar em produção)
docker run -e VELANA_SECRET="$VELANA_SECRET" -p 3000:3000 ogburgers:latest
```

Heroku
------

1. Faça login no Heroku e crie um app.
2. Configure a variável `VELANA_SECRET` em `Settings > Config Vars`.
3. Faça push do repositório (Heroku detectará o `Procfile`):

```bash
git push heroku main
```

Passo-a-passo rápido (CLI)
-------------------------

```bash
# 1) instale o Heroku CLI: https://devcenter.heroku.com/articles/heroku-cli
heroku login

# 2) crie o app (ou use o nome desejado)
heroku create ogburgers-myapp

# 3) defina a variável secreta (exemplo)
heroku config:set VELANA_SECRET="sua_chave_secreta" --app ogburgers-myapp

# 4) push para o remoto Heroku
git push heroku main

# 5) abra o app
heroku open --app ogburgers-myapp
```

Observações
- O `Procfile` já está presente; Heroku usará `web: node server.js`.
- Se preferir, use a aba "Deploy" do dashboard Heroku e conecte ao GitHub para deploy automático.
- Use `heroku logs --tail --app <app>` para ver logs durante testes.

Render / Railway / outros PaaS
--------------------------------

1. Crie um novo serviço web apontando para este repositório.
2. Defina `PORT` (geralmente gerenciado pela plataforma) e `VELANA_SECRET` nas variáveis de ambiente.
3. Use o `Dockerfile` se preferir deploy por container.

Boas práticas
-------------

- Nunca commite o arquivo `.env` — use variáveis de ambiente na plataforma de deploy.
- Ajuste `CORS_ORIGIN` para o domínio do frontend em produção.
- Reveja `npm audit` e atualize dependências antes do deploy em produção.
