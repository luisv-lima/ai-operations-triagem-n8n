# 🤖 AI Operations: Automação Empresarial Inteligente de Triagem e Atendimento

![n8n](https://img.shields.io/badge/n8n-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)
![Google Gemini](https://img.shields.io/badge/Google%20Gemini-8E75B2?style=for-the-badge&logo=googlegemini&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)
![Telegram](https://img.shields.io/badge/Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white)

Solução end-to-end de **AI Operations** para automação e orquestração do fluxo de atendimento e suporte financeiro empresarial, integrando plataformas Low-Code, Inteligência Artificial Generativa (LLM) e arquitetura **Human-in-the-Loop (HITL)**.

---

## 📌 Problema de Negócio

Empresas em crescimento sofrem com gargalos na triagem de solicitações de clientes recebidas por formulários ou e-mails. O processo manual gera:
* **Alto SLA de Resposta:** Demora no atendimento de dúvidas simples do dia a dia.
* **Erros de Triagem:** Falha humana na categorização e priorização dos chamados.
* **Riscos Financeiros:** Falta de alçada e validação prévia em solicitações de reembolso.

---

## 🎯 Solução Proposta

Desenvolvimento de um fluxo orquestrado no **n8n** que recebe chamados em tempo real, utiliza o **Google Gemini API** para analisar o contexto e tomar decisões lógicas, e divide a operação em dois caminhos:
1. **Ação Direta (Autônoma):** Dúvidas gerais (R$ 0) recebem uma resposta gerada pela IA diretamente no e-mail do cliente via **Gmail**.
2. **Supervisão Humana (HITL):** Pedidos de reembolso/crédito (> R$ 0) acionam um alerta formatado via **Telegram Bot** para validação do gestor antes de qualquer execução financeira.

---

## 🏗️ Arquitetura do Sistema

```text
[ Google Forms ]
       │
       ▼
[ Google Sheets ] ──(Apps Script: Webhook POST)──► [ n8n Webhook Node ]
                                                           │
                                                           ▼
                                                [ Google Gemini API ]
                                            (gemini-3.1-flash-lite)
                                                           │
                                                           ▼
                                                   [ Nó Condicional IF ]
                                                      /          \
                                                     /            \
                                           (True: > R$ 0)    (False: R$ 0)
                                                 /                \
                                                ▼                  ▼
                                     [ Telegram Bot ]       [ Gmail Node ]
                                     (Human-in-Loop)      (Resposta Direta)

```
## 🛠️ Tecnologias Utilizadas

* **Orquestrador Low-Code:** n8n (Self-hosted / Cloud)
* **Modelo de IA:** Google Gemini API (`models/gemini-3.1-flash-lite`)
* **Gatilho e Integração:** Google Forms, Google Sheets, Google Apps Script (Webhook)
* **Canais de Comunicação:** Telegram Bot API (Notificação HITL) e Gmail API (Resposta ao Cliente)
* **Formatação de Dados:** JSON estruturado, JavaScript Expressions, Markdown

---

## 🧠 Papel da Inteligência Artificial (Agentic Triaging)

O nó do Gemini atua como um agente classificador e tomador de decisão. O prompt foi estruturado para forçar um retorno estritamente em **JSON** contendo a seguinte estrutura:

```json
{
  "categoria_identificada": "REEMBOLSO / DUVIDA",
  "prioridade": "ALTA / BAIXA",
  "requer_aprovacao_humana": true,
  "motivo_decisao": "Justificativa lógica da decisão",
  "resposta_sugerida": "Mensagem cortês formulada para o cliente"
}
```

📋 Regras de Negócio Inseridas no Prompt:
requer_aprovacao_humana: true ➔ Quando a categoria envolve reembolso ou transação financeira acima de R$ 0.

requer_aprovacao_humana: false ➔ Quando se trata de uma dúvida informacional sem valor financeiro envolvido.


## 📸 Evidências de Execução

### 1. Workflow Completo no n8n
*Figura 1: Visão geral do fluxo orquestrado com execução bem-sucedida.*  
<img width="733" height="279" alt="Captura de tela 2026-09-30 214542" src="https://github.com/user-attachments/assets/7f0ce6c5-9512-4432-80e7-1d060cd91053" />

### 2. Análise e Saída JSON do Gemini AI
*Figura 2: Configuração do prompt no Gemini e saída estruturada em JSON.*  
<img width="997" height="819" alt="Captura de tela 2026-09-30 234302" src="https://github.com/user-attachments/assets/263b905b-785a-454b-af61-7265347b098f" />

### 3. Human-in-the-Loop no Telegram (Rota True)
*Figura 3: Alerta de aprovação enviado ao gestor contendo os dados do cliente e motivo da IA.*  
<img width="714" height="1600" alt="image" src="https://github.com/user-attachments/assets/4df9a335-99a7-4d02-b440-26945b58939d" />

### 4. Resposta Automática no Gmail (Rota False)
*Figura 4: E-mail enviado automaticamente ao cliente com a resposta gerada pela IA.*  
<img width="1080" height="1071" alt="image" src="https://github.com/user-attachments/assets/30478907-cab5-44fd-b89b-cd4ca4b7ffc9" />

---

## 🚀 Como Replicar este Projeto

### Google Sheets / Forms:
1. Crie um formulário com os campos: `Nome`, `E-mail`, `Tipo de Solicitação`, `Valor Solicitado` e `Descrição`.
2. No menu **Extensões ➔ Apps Script** da planilha, cole o script de Webhook apontando para o seu n8n.
3. Configure o acionador (*Trigger*) para disparar `Ao enviar formulário`.

### n8n Workflow:
1. Importe o fluxo do n8n utilizando o arquivo `workflow.json` disponível neste repositório.
2. Configure as credenciais do **Google Gemini API Key**, **Telegram Bot Token** e **Gmail OAuth2**.
3. Ative o workflow (**Publish**).

---

## 📄 Licença

Este projeto está licenciado sob a licença [MIT](LICENSE).
