 # Documentação Funcional do Chatbot de Atendimento com IA

Este documento descreve a funcionalidade e arquitetura de um Chatbot de Atendimento e Vendas projetado para integração com plataformas de mensagens e Inteligência Artificial, isento de nomes de projetos ou proprietários.

## Visão Geral da Funcionalidade

A aplicação atua como um serviço de chatbot inteligente integrado ao WhatsApp (via API de mensagens do Twilio) para comunicação e utilizando um motor de Inteligência Artificial Generativa (Google Gemini 2.5 Flash) para processamento de linguagem e geração de respostas.

O objetivo principal é funcionar como um agente de atendimento contextualizado para suporte comercial e vendas de um produto específico (PSPM).

### Características Essenciais:
- **Comunicação Bidirecional:** Recebe mensagens do WhatsApp e envia respostas em tempo real.
- **Processamento Inteligente:** Utiliza a IA para análise de intenção, gestão de contexto e geração de respostas comerciais.
- **Gerenciamento de Sessão:** Mantém o histórico da conversa (sessão) por usuário para garantir respostas contextualizadas.
- **Suporte a Idiomas:** Implementa detecção e suporte automático aos idiomas Português e Inglês.
- **Escalabilidade:** Arquitetura stateless (sem estado em disco) para suportar expansão horizontal.

## Arquitetura e Fluxo de Mensagens

A arquitetura é construída com FastAPI, servindo como API REST para orquestração de rotas e manipulação de webhooks.

### Fluxo Padrão da Mensagem:
1. **Mensagem Recebida:** O usuário envia uma mensagem via WhatsApp.
2. **Webhook:** O serviço de mensagens (Twilio) envia um POST para o endpoint `/webhook` da aplicação.
3. **Processamento:** Um Handler interno extrai e valida os dados da mensagem.
4. **Sessão:** Um gerenciador identifica ou cria a sessão de chat do usuário.
5. **Geração IA:** A mensagem e o histórico da sessão são enviados ao Cliente Gemini AI, que retorna a resposta conforme o prompt de atendimento.
6. **Resposta:** Um serviço de envio (Twilio) utiliza a API de mensagens para retransmitir a resposta ao WhatsApp do usuário.

**Diagrama de fluxo de mensagens:**

Usuário WhatsApp ⟶ Twilio Webhook ⟶ FastAPI `/webhook` ⟶ Webhook Handler ⟶ Chat Session Manager ⟶ Google Gemini AI ⟶ Twilio Service ⟶ Resposta WhatsApp

## Componentes de Serviço

| Componente           | Função Primária                                         | Tecnologia         |
|----------------------|--------------------------------------------------------|--------------------|
| Aplicação Central    | Orquestração de rotas e health checks.                 | FastAPI            |
| Handler de Webhook   | Parsing, validação de segurança e direcionamento de    |
|                      | requisições do Twilio.                                 | Python             |
| Cliente de Mensagens | Abstração da integração com a API Twilio para envio    |
|                      | de respostas.                                          | Twilio SDK         |
| Cliente de IA        | Comunicação com o modelo de IA e gestão do prompt de   |
|                      | atendimento.                                           | Google GenAI SDK   |

## Endpoints API (Funcionalidade)

| Método | Endpoint   | Descrição Funcional                                                                              |
|--------|------------|-------------------------------------------------------------------------------------------------|
| POST   | /webhook   | Recebe mensagens e dados do webhook do Twilio para processamento e resposta. Conteúdo esperado: application/x-www-form-urlencoded (FormData). |
| GET    | /health    | Checagem rápida do status de saúde da aplicação.                                                  |
| GET    | /status    | Retorna o status detalhado da conexão com os serviços externos (Twilio, Gemini) e o número de sessões ativas. |

## Estrutura de Diretórios (Foco na Funcionalidade)

```bash
.
├── app.py                  # Aplicação principal (rotas e inicialização)
├── config.py               # Gerenciamento de variáveis de ambiente
├── handlers/               # Lógica para manipulação de eventos de entrada (e.g., Twilio)
│   └── webhook_handler.py
├── services/               # Clientes para serviços externos
│   ├── twilio_service.py
│   └── gemini_client.py
└── prompts/                # Configurações do comportamento do agente IA
    └── agente_atendente.yaml
```

Posso fornecer mais detalhes sobre a configuração de variáveis de ambiente ou o processo de instalação do projeto.
