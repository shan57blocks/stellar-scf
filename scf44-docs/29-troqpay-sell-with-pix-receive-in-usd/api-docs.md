Source: https://docs.troqpay.com/llms.txt

# TroqPay

> A TroqPay é uma API para cobrar com Pix no Brasil, acompanhar pagamentos por webhook, ler saldo e solicitar saques em produção. Teste usa `https://api-test.troqpay.com`; produção usa `https://api.troqpay.com`. Toda chamada autenticada usa `Authorization: Bearer <chave>`. A moeda das cobranças é sempre `BRL`. O checkout hospedado usa `https://pay-test.troqpay.com/pay/{checkoutId}` em teste e `https://pay.troqpay.com/pay/{checkoutId}` em produção. Saques (Pix ou carteira USDT) exigem conta aprovada, destino aprovado, saldo disponível, chave com permissão de saque e `Idempotency-Key`.

Notas importantes:

- Chaves: `trq_test_<segredo>` usa `https://api-test.troqpay.com`; `trq_live_<segredo>` usa `https://api.troqpay.com`. O host e a chave precisam pertencer ao mesmo ambiente.
- SDK JavaScript: use `@troqpay/sdk` no backend. Cliente: `new Troqpay({ apiKey: process.env.TROQPAY_API_KEY })`. Recursos: `checkouts.create`, `checkouts.retrieve`, `balance.retrieve`, `withdrawals.create`, `withdrawals.retrieve`.
- Agentes: o plugin MCP para Claude/Codex expõe `troqpay_create_checkout`, `troqpay_get_checkout` e `troqpay_get_balance`. Ferramentas de saque só devem ser disponibilizadas a contas e fluxos autorizados. A chave deve vir do ambiente seguro, nunca do prompt.
- Acesso a produção: `trq_live_` só funciona quando a conta está marcada como `APPROVED`. O gate é aplicado em `POST /v1/checkouts`, `GET /v1/balance` e saques em produção. Em contas não-aprovadas, essas rotas retornam `403 forbidden`. `GET /v1/checkouts/{checkoutId}` continua funcionando mesmo sem `APPROVED`, desde que a chave tenha `CHECKOUT:READ`.
- Valores: `amount` no checkout é **inteiro em centavos de BRL**. Os campos de saldo são **strings decimais** em BRL.
- Permissões: `CHECKOUT:CREATE` para `POST /v1/checkouts`, `CHECKOUT:READ` para `GET /v1/checkouts/{checkoutId}`, `BALANCE:READ` para `GET /v1/balance`, `WITHDRAWAL:CREATE` para `POST /v1/withdrawals`, `WITHDRAWAL:READ` para `GET /v1/withdrawals/{withdrawalId}`, `PAYMENT_LINK:CREATE` para `POST /v1/payment-links`, `PAYMENT_LINK:READ` para `GET /v1/payment-links` e `GET /v1/payment-links/{paymentLinkId}`. Sem permissão, a API retorna `403 api_key_scope_forbidden`.
- Saques: `POST /v1/withdrawals` cria uma solicitação de saque somente em produção. Body: `{ "rail": "BRL_PIX" | "USDT_WALLET", "amount": "<decimal BRL>" }`. O destino não vai no body; a API usa o destino aprovado na conta. A solicitação reserva saldo imediatamente e a conclusão segue os controles e validações aplicáveis à conta. `GET /v1/withdrawals/{withdrawalId}` consulta um saque.
- `Idempotency-Key`: opcional/recomendado em criações de checkout e obrigatório em criações de saque. A API hash o corpo com serialização JSON estável e key-sorted. Reordenar chaves equivale ao mesmo corpo. Não há expiração. Primeira criação devolve `201`; reentrega idempotente devolve `200`.
- `Request-Id` (header de resposta, sempre presente): formato `req_<24 hex>`. O cliente pode mandar `Request-Id` ou `X-Request-Id` na requisição e a API ecoa o mesmo valor.
- Envelope de erro: `{ "error": { "type", "code", "message", "requestId" } }`. Mensagens em inglês literais. 401 emite sempre `code: "unauthorized"`.
- URL hospedada do checkout: use `https://pay-test.troqpay.com/pay/{checkoutId}` em teste e `https://pay.troqpay.com/pay/{checkoutId}` em produção. O prefixo legacy `/c/{checkoutId}` faz redirect 3xx para `/pay/{checkoutId}` no host correspondente.
- Sandbox: o Pix TEST é sintético e não pagável. A simulação de pagamento fica somente na página hospedada `pay-test` e não faz parte do SDK nem da API pública.
- Customer no checkout: `phone` é aceito no envio mas **não é retornado** na resposta nem no payload do webhook. `document` aceita 3-32 caracteres e não é validado como CPF/CNPJ.
- Webhooks: assinatura HMAC-SHA256 do corpo bruto em hex, no header `x-troqpay-signature`. Segredo único por endpoint. Até 5 tentativas, backoff `60s → 5m → 15m → 60m`, timeout de 5 segundos. O entregador **não segue redirects** — qualquer 3xx no seu endpoint vira falha.
- Links de pagamento: há API REST pública. `POST /v1/payment-links` cria um link (scope `PAYMENT_LINK:CREATE`, header `Idempotency-Key` obrigatório, body: `productId` obrigatório + opcionais `expiresInSeconds`/`successUrl`/`returnUrl`). `GET /v1/payment-links` lista links com paginação por cursor (`limit` + `cursor`, scope `PAYMENT_LINK:READ`). `GET /v1/payment-links/{paymentLinkId}` lê um link (scope `PAYMENT_LINK:READ`). A resposta **não** inclui campo `url` — devolve `paymentLinkId` e os campos do link; o link resolve no host `pay-test` ou `pay` correspondente ao ambiente. **Não** há update, delete ou toggle-active via API. Os scopes `PAYMENT_LINK:CREATE`/`PAYMENT_LINK:READ` são emitidos por padrão em toda chave nova (test e live); chaves antigas não os têm e recebem `403 api_key_scope_forbidden` até serem reemitidas.
- Status público: `GET /health` retorna `{ ok, service, requestId }`, não exige autenticação e não expõe dados de merchants.

## Checkouts (cobrar com Pix)

- [Checkouts Pix](https://docs.troqpay.com/flows/checkouts-pix): conceito, ciclo de vida, campos da requisição e da resposta.
- [SDK JavaScript](https://docs.troqpay.com/sdks/javascript): integração com `@troqpay/sdk` para Node.js/TypeScript.
- [POST /v1/checkouts](https://docs.troqpay.com/api-reference): cria uma cobrança Pix. Body mínimo: `{ "amount": <int em centavos>, "description": <string>, "externalId": <string> }`. Aceita `customer` (com `name` mais um de `email`/`document`/`phone`), `metadata` (string-only), `expiresIn` (900-86400). `currency` é sempre `BRL`. Devolve `201` na criação ou `200` em reentrega idempotente.
- [GET /v1/checkouts/{checkoutId}](https://docs.troqpay.com/api-reference): lê o checkout no formato `SerializedCheckout`. Não há endpoint de listagem.

## Saldo

- [Saldo](https://docs.troqpay.com/flows/saldo): como ler `GET /v1/balance` e cada campo.
- [GET /v1/balance](https://docs.troqpay.com/api-reference): retorna `{ livemode, currency, grossAmount, feeAmount, pendingAmount, availableAmount, reservedAmount, blockedAmount }`. Todos os valores monetários são strings decimais em BRL.

## Saques

- [Saques](https://docs.troqpay.com/flows/saques): como criar e consultar saques em produção.
- [POST /v1/withdrawals](https://docs.troqpay.com/api-reference): cria saque. Requer `trq_live_`, conta aprovada, permissão de saque, destino aprovado, saldo disponível e `Idempotency-Key`.
- [GET /v1/withdrawals/{withdrawalId}](https://docs.troqpay.com/api-reference): consulta saque. Requer permissão de leitura de saque.

## Webhooks

- [Webhooks](https://docs.troqpay.com/flows/webhooks): cadastro, assinatura, retentativas, restrições de endpoint.
- Eventos disponíveis: `checkout.created`, `checkout.paid`, `checkout.expired`.
- Envelope: `{ id, type, createdAt, livemode, data: { checkout: SerializedCheckout } }`. `data.checkout` é o checkout completo, igual ao retornado por `GET /v1/checkouts/{id}`.
- Assinatura: `x-troqpay-signature = HMAC_SHA256(rawBody, signingSecret).hex()`. Segredo único por endpoint. Até 5 tentativas com backoff `60s → 5m → 15m → 60m`. Sem follow-redirect.

## Links de Pagamento

- [Links de pagamento](https://docs.troqpay.com/flows/links-de-pagamento): conceito e fluxo.
- [POST /v1/payment-links](https://docs.troqpay.com/api-reference): cria um link de pagamento. Requer scope `PAYMENT_LINK:CREATE` e `Idempotency-Key`. Body: `productId` obrigatório + opcionais `expiresInSeconds`, `successUrl`, `returnUrl`. Devolve `201` na criação ou `200` em reentrega idempotente. A resposta traz `paymentLinkId` e os campos do link (sem campo `url`).
- [GET /v1/payment-links](https://docs.troqpay.com/api-reference): lista links com paginação por cursor (query `limit` + `cursor`). Requer scope `PAYMENT_LINK:READ`. Resposta: `{ data, nextCursor, hasMore }`.
- [GET /v1/payment-links/{paymentLinkId}](https://docs.troqpay.com/api-reference): lê um link de pagamento. Requer scope `PAYMENT_LINK:READ`.
- Não há update, delete ou toggle-active via API. URLs públicas usam `pay-test.troqpay.com` em teste e `pay.troqpay.com` em produção. Cada nova tentativa do comprador cria um checkout — recuperável pelos endpoints de checkout acima. Reenvios da mesma tentativa são deduplicados.

## Erros

- [Erros](https://docs.troqpay.com/flows/errors): formato do erro, status HTTP por categoria, lista completa de `code`.
- Códigos emitidos no escopo público: `invalid_json` (400), `invalid_request_body` (400), `invalid_query` (400, payment-link), `invalid_cursor` (400, payment-link), `idempotency_key_required` (400, saque e payment-link), `unauthorized` (401), `forbidden` (403), `api_key_scope_forbidden` (403), `test_withdrawals_not_supported` (403, saque), `withdrawal_readiness_required` (403, saque), `account_suspended` (403), `live_operations_blocked` (403, checkout e payment-link), `withdrawals_blocked` (403, saque), `idempotency_conflict` (409), `invalid_expires_in` (422, checkout e payment-link), `invalid_amount` (422, saque), `insufficient_balance` (422, saque), `quote_unavailable` (422, saque), `provider_rejected` (422, checkout), `limit_exceeded` (422, saque), `invalid_destination` (422, saque), `checkout_not_found` (404), `withdrawal_not_found` (404), `product_not_found` (404, payment-link), `payment_link_not_found` (404, payment-link), `provider_unavailable` (503), `rate_limit_exceeded` (429).

## Status

- [GET /health](https://docs.troqpay.com/api-reference): endpoint público para monitorar disponibilidade básica da API.

## SDK e agentes

- [SDK JavaScript](https://docs.troqpay.com/sdks/javascript): pacote `@troqpay/sdk` para backends Node.js.
- [Claude e Codex](https://docs.troqpay.com/agents/claude-codex): plugin MCP para agentes.

## Glossário

- [Glossário](https://docs.troqpay.com/glossary): definições rápidas + subseção de prefixos de ID.
- Prefixos: `chk_<20 hex>` (checkout), `wdr_<20 hex>` (saque), `evt_<24 hex>` (evento), `req_<24 hex>` (requisição), `plink_<16 hex>` (link de pagamento).

---

OpenAPI canônica: https://docs.troqpay.com/api-reference/openapi.json
Documentação humana: https://docs.troqpay.com
App da TroqPay: https://app.troqpay.com
