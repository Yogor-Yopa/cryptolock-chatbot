# 🚀 Como Testar o CryptoLock Chatbot

## Status Atual

✅ **Servidor está rodando em:** `http://localhost:8000`
✅ **Todas as credenciais configuradas**
✅ **Conectado com WhatsApp e Gemini**

---

## 3 Formas de Testar

### 1️⃣ **Via WhatsApp (Recomendado - Teste Real)**

**Pré-requisitos:**
- Seu número está registrado na Meta (como testador)
- O webhook está corretamente configurado na Meta App Console

**Para Configurar o Webhook na Meta:**

1. Acesse: https://developers.facebook.com/apps
2. Selecione seu app
3. Vá para **WhatsApp > Configuration**
4. Em **Webhook URL**, configure:
   - **URL:** `https://seu-dominio.com/webhook` (ou tunnel ngrok)
   - **Verify Token:** `token_secreto_louco` (do `.env`)
5. Clique em **Verify and Save**
6. Inscreva-se nos eventos: `messages`, `message_status`

**Para expor seu localhost local (desenvolvimento):**
```bash
# Use ngrok para criar um tunnel
ngrok http 8000

# Você receberá algo como: https://abc123def456.ngrok.io
# Use isso como URL no webhook da Meta
```

**Depois:** Envie uma mensagem para o número testador via WhatsApp e aguarde a resposta!

---

### 2️⃣ **Via API (Teste Local Rápido)**

**Com curl (Windows PowerShell):**

```powershell
# Teste 1: Verificar saúde da aplicação
curl http://localhost:8000/health

# Teste 2: Simular recebimento de mensagem
$body = @{
    "entry" = @(
        @{
            "changes" = @(
                @{
                    "value" = @{
                        "messages" = @(
                            @{
                                "from" = "1234567890"
                                "id" = "msg123"
                                "timestamp" = "1699000000"
                                "type" = "text"
                                "text" = @{
                                    "body" = "Olá, como você está?"
                                }
                            }
                        )
                    }
                }
            )
        }
    )
} | ConvertTo-Json -Depth 10

curl -X POST http://localhost:8000/webhook `
  -H "Content-Type: application/json" `
  -d $body
```

**Com Postman:**

1. Abra Postman
2. Crie uma nova requisição **POST**
3. URL: `http://localhost:8000/webhook`
4. Body (raw JSON):
```json
{
  "entry": [
    {
      "changes": [
        {
          "value": {
            "messages": [
              {
                "from": "1234567890",
                "id": "msg123",
                "timestamp": "1699000000",
                "type": "text",
                "text": {
                  "body": "Teste de mensagem - O que é criptomoeda?"
                }
              }
            ]
          }
        }
      ]
    }
  ]
}
```
5. Clique em **Send**
6. Verifique a resposta e os logs do servidor

---

### 3️⃣ **Via Swagger UI (Documentação Interativa)**

1. Acesse: `http://localhost:8000/docs`
2. Você verá a documentação interativa do Swagger
3. Clique em **POST /webhook**
4. Clique em **Try it out**
5. Cole o JSON de teste no body
6. Clique em **Execute**

---

## 📊 Fluxo de uma Mensagem

```
WhatsApp (Usuário)
    ↓
    ├─→ Servidor recebe em POST /webhook
    │
    ├─→ Valida e processa os dados
    │
    ├─→ Obtém ou cria sessão de chat para o usuário
    │
    ├─→ Envia mensagem para Gemini AI
    │
    ├─→ Recebe resposta da IA
    │
    ├─→ Envia resposta de volta via WhatsApp Cloud API
    │
    └─→ WhatsApp (Resposta do Bot)
```

---

## 🔍 Como Verificar os Logs

**O servidor mostra em tempo real:**

```
INFO:     Started server process [11432]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)

# Quando receber uma mensagem:
[INFO] Mensagem recebida de 1234567890: "Olá, como você está?"
[INFO] Resposta enviada para 1234567890
```

---

## 🛠️ Endpoints Disponíveis

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| **GET** | `/webhook` | Verifica token de segurança do webhook |
| **POST** | `/webhook` | Recebe mensagens do WhatsApp |
| **GET** | `/health` | Verifica saúde da aplicação |
| **GET** | `/status` | Informações detalhadas do sistema |
| **GET** | `/docs` | Swagger UI (documentação interativa) |
| **GET** | `/redoc` | ReDoc (documentação alternativa) |

---

## ⚙️ Variáveis de Ambiente

Configuradas em `.env`:

```
VERIFY_TOKEN=token_secreto_louco
GEMINI_API_KEY=AIzaSyD5kjBhK0QQeU5TCD0e0ujpQ6VBdsYPbKo
WHATSAPP_API_TOKEN=EAAcQVs9UhIcBPy3zGxjLVymOW1pLOo5FTZBxu8m7sAOJ2nesBpi...
PHONE_NUMBER_ID=871512352711922
```

---

## ❌ Troubleshooting

**Problema:** "Token inválido"
- **Solução:** Certifique-se que `VERIFY_TOKEN` no `.env` = Token configurado na Meta

**Problema:** "Mensagem não enviada"
- **Solução:** Verifique se `WHATSAPP_API_TOKEN` é válido
- Verifique logs do servidor

**Problema:** "Erro do Gemini"
- **Solução:** Verifique se `GEMINI_API_KEY` é válida
- Certifique-se de que sua cota não foi excedida

**Problema:** Servidor não inicia
- **Solução:** Execute `python app.py` na pasta correta
- Verifique se dependências estão instaladas: `pip install -r requirements.txt`

---

## 📝 Próximos Passos

1. ✅ Servidor rodando localmente
2. ➡️ Configure webhook na Meta Console (ngrok para teste)
3. ➡️ Envie primeira mensagem via WhatsApp
4. ➡️ Veja a resposta da IA em tempo real
5. ➡️ Deploy em produção (servidor com domínio)

---

**Tem dúvidas?** Verifique os logs do servidor em tempo real!
