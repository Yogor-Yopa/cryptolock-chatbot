# CryptoLock Chatbot

CryptoLock Chatbot é uma API em FastAPI que faz a integração entre o WhatsApp Cloud API (Meta) e o modelo Gemini (Google GenAI), permitindo responder automaticamente mensagens recebidas via webhook. O projeto foi desenvolvido como exemplo de integração entre serviços de mensagens e IA.

---

## Funcionalidades
- Recepção de webhooks do WhatsApp (GET/POST) para verificação e processamento de mensagens.
- Integração com Google Gemini para gerar respostas automatizadas.
- Envio de respostas via Meta WhatsApp Cloud API.
- Endpoints de saúde e status para monitoramento.

---

## Estrutura do projeto
- `app.py` - Arquivo principal que configura a aplicação FastAPI e as rotas.
- `config.py` - Configurações e variáveis de ambiente.
- `handlers/` - Handlers de webhook (verificação e processamento).
- `services/` - Integrações externas (Gemini, Meta/WhatsApp).
- `models.py` - Modelos Pydantic para validação de payloads.
- `requirements.txt` - Dependências do projeto.
- `.env.example` - Exemplo de variáveis de ambiente (copiar para `.env` e preencher).

---

## Pré-requisitos
- Python 3.11 instalado no sistema.
- Conta e credenciais da Meta (WhatsApp Cloud API) e Google (Gemini API Key) para integração.
- `py` (Python launcher) disponível no Windows facilita a criação do venv.

---

## Primeiros passos — Desenvolvimento (Windows / PowerShell)

1. Clone o repositório e entre na pasta do projeto:

```powershell
git clone <repo-url>
cd cryptolock-chatbot
```

2. Crie e ative o ambiente virtual (Python 3.11):

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -V  # deve retornar Python 3.11.x
```

3. Atualize o pip e instale as dependências:

```powershell
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

4. Crie o arquivo `.env` a partir do template e preencha suas credenciais:

```powershell
Copy-Item .env.example .env
# Edite .env com as chaves reais (VERIFY_TOKEN, GEMINI_API_KEY, WHATSAPP_API_TOKEN, PHONE_NUMBER_ID)
```

> Observação: `.env` está listado no `.gitignore` — não comite suas chaves.

---

## Variáveis de ambiente
As seguintes variáveis são obrigatórias para a aplicação iniciar corretamente:

- `VERIFY_TOKEN` — Token para validar o webhook do WhatsApp (usado no GET `/webhook`).
- `GEMINI_API_KEY` — Chave de API do Google Gemini (genai).
- `WHATSAPP_API_TOKEN` — Token de acesso da WhatsApp Cloud API (Meta).
- `PHONE_NUMBER_ID` — ID do número de telefone no WhatsApp Cloud API.
- `DEBUG` — (Opcional) `True`/`False` para habilitar reload/DEBUG.

Exemplo de `.env`:

```
VERIFY_TOKEN=your_verify_token_here
GEMINI_API_KEY=your_gemini_api_key_here
WHATSAPP_API_TOKEN=your_whatsapp_api_token_here
PHONE_NUMBER_ID=your_phone_number_id_here
DEBUG=False
```

---

## Rodando a aplicação (desenvolvimento)

```powershell
# Com a venv ativada e .env configurado
python -m uvicorn app:app --reload --host 127.0.0.1 --port 8000
```

Ao iniciar, a API ficará disponível em `http://127.0.0.1:8000`.

---

## Endpoints principais

- `GET /webhook` — Endpoint de verificação do webhook (usado para validar o webhook junto à Meta). Parâmetros esperados pela Meta no query string:
  - `hub.mode` — geralmente `subscribe`.
  - `hub.verify_token` — token para validar.
  - `hub.challenge` — challenge que deve ser retornado em texto puro.

  Exemplo (curl):
  ```bash
  curl "http://127.0.0.1:8000/webhook?hub.mode=subscribe&hub.verify_token=YOUR_TOKEN&hub.challenge=CHALLENGE"
  ```

- `POST /webhook` — Endpoint que recebe eventos do webhook do WhatsApp. A aplicação processa as mensagens de texto, invoca o Gemini (Google GenAI) para gerar uma resposta e envia a resposta ao remetente via WhatsApp Cloud API.

  Exemplo (curl) para testar localmente (estrutura simplificada):
  ```bash
  curl -X POST http://127.0.0.1:8000/webhook -H "Content-Type: application/json" -d '{"entry": [{"changes": [{"value": {"messages": [{"from": "5511999999999", "type": "text", "text": {"body": "Olá"}}]}}]}]}'
  ```

- `GET /health` — Retorna status de saúde (`ok`) e informa o número de sessões de chat ativas.
- `GET /status` — Retorna informações do status da aplicação (sessions, meta_client availability e config_loaded).

---

## Arquitetura e fluxo
1. O WhatsApp envia eventos para `POST /webhook`.
2. `handlers/webhook_handler.py` valida/filtra o payload e retorna um dicionário com dados processados.
3. `ChatSessionManager` (em `services/gemini_client.py`) gerencia sessões com o Gemini para manter contexto.
4. A resposta gerada pelo Gemini é enviada de volta ao usuário com `MetaAPIClient.send_text_message()`.

---

## Troubleshooting (problemas comuns)
- Erro: `Variáveis de ambiente obrigatórias não configuradas` — crie um `.env` com as variáveis necessárias ou exporte-as no ambiente.
- Problemas de Activation no PowerShell: ajustar a política de execução (apenas se seguro):
  ```powershell
  Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
  ```
- Se o servidor não iniciar, verifique se as dependências foram instaladas e se a versão do Python é 3.11.

---

## Segurança
- Nunca faça commit de `.env` com credenciais. Use serviços de gerenciamento de segredos para produção.
- Garanta que os tokens e chaves sejam rotacionados periodicamente.

---

## Contribuições
- Contribuições são bem-vindas! Abra issues e PRs para melhorias, correções e novos testes.

---

## Licença
- Coloque aqui a sua licença (ex.: MIT) se desejar controlar o uso do projeto.


