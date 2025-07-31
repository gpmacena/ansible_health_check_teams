# 🔍 Health Check Dynatrace → Microsoft Teams

Este projeto é uma **automação com Ansible** para **consultar monitores no Dynatrace** filtrados por **TAGs específicas** e enviar o resultado do health check para canais do **Microsoft Teams** (ou outros destinos compatíveis via webhook).  
Ele processa os dados obtidos via API do Dynatrace, identifica possíveis falhas e envia mensagens formatadas em **Adaptive Cards** para fácil visualização.

---

## 📌 Funcionalidades

- **Consulta automática** de monitores Dynatrace via API.
- **Filtro por TAGs** para organizar monitores em grupos lógicos.
- Identificação de **status HTTP válidos** (`200, 202, 301, 302, 401`).
- Detecção de falhas e **separação por grupo/TAG**.
- **Envio para Microsoft Teams** com Adaptive Cards:
  - Mensagem "✅ OK" quando todos os monitores estão saudáveis.
  - Lista de falhas com detalhes (nome do monitor, URL e status HTTP).
- **Fuso horário de São Paulo (-3 GMT)** para todos os registros.
- Suporte para **múltiplos webhooks**.

---

## 📂 Estrutura do Projeto

```
.
health_check_teams/
├── roles/
│   └── health_check_teams/
│       ├── tasks/
│       │   ├── main.yml               # Sequência principal das tasks
│       │   ├── processa_monitor.yml   # Task para processar monitores
│       │   └── envia_template.yml     # Task para envio do template
│       └── vars/
│           └── main.yml               # Variáveis usadas na role
│
└── health_check_teams.yml             # Playbook que chama a role

```

---

## ⚙️ Pré-requisitos

- **Ansible** (>= 2.9 recomendado)
- **Acesso à API do Dynatrace** com permissões para ler monitores e execuções.
- **Token de API do Dynatrace**.
- **Webhook do Microsoft Teams** (ou outro destino suportado).
- Python com módulos necessários para Ansible.

---

## 🔧 Configuração

1. **Clone este repositório**:
   ```bash
   git clone https://github.com/SEU-USUARIO/SEU-REPO.git
   cd SEU-REPO
   ```

2. **Defina as variáveis no arquivo `vars/main.yml`**:
   ```yaml
   webhook_urls:
     - "https://SEU-WEBHOOK-TEAMS"
     - "https://OUTRO-WEBHOOK-OPCIONAL"

   dynatrace_api_url:
     - name: "GRUPO-1"
       url: "https://SEU-DYNATRACE/e/SEU-ENV-ID/api/v1/synthetic/monitors?tag=TAG1&type=HTTP&enabled=true"

     - name: "GRUPO-2"
       url: "https://SEU-DYNATRACE/e/SEU-ENV-ID/api/v1/synthetic/monitors?tag=TAG2&type=HTTP&enabled=true"

   dynatrace_url: "https://SEU-DYNATRACE/e/SEU-ENV-ID"
   api_token: "SEU-TOKEN-DYNATRACE"
   ```

   > 💡 **Recomendado:** Proteja o `api_token` usando **Ansible Vault**:
   ```bash
   ansible-vault encrypt vars/main.yml
   ```

3. **Instale as dependências** (se necessário):
   ```bash
   pip install ansible
   ```

---

## ▶️ Execução

Para executar a verificação manualmente:
```bash
ansible-playbook -i localhost, health_check_teams.yml
```

- O playbook:
  1. Consulta todos os monitores definidos em `vars/main.yml`.
  2. Processa cada monitor individualmente.
  3. Identifica falhas.
  4. Envia mensagens formatadas para os webhooks configurados.

---

## 📜 Fluxo de Execução

1. **Inicializar variáveis** → Cria dicionários globais para armazenar resultados.
2. **Consultar monitores Dynatrace** → Para cada TAG definida.
3. **Processar monitores** → Extrai informações, consulta execução e analisa status HTTP.
4. **Agrupar resultados por TAG** → Separa monitores OK e com falha.
5. **Enviar para Teams**:
   - Mensagem de sucesso se não houver falhas.
   - Lista de falhas detalhadas caso existam.

---

## 📊 Exemplo de Mensagem no Teams

**✅ Todos os monitores OK**
```
Health Check - SISTEMA
⏰ Atualizado em 2025-07-31 10:35:00
🟢 Todos os endpoints monitorados retornaram status OK.
```

**❌ Falhas detectadas**
```
Health Check - GRUPO-1 com FALHAS
⏰ Atualizado em 2025-07-31 10:35:00
🔴 Monitor X
🌐 https://exemplo.com
⚠️ HTTP 500 Internal Server Error
---
```

---

## 🚀 Possíveis melhorias

- Adicionar suporte para **outros tipos de monitor** (Browser, ICMP).
- Implementar **alertas por e-mail** como opção adicional.
- Adicionar **logs persistentes** para histórico de falhas.
- Configuração para **execução agendada** via cron.

---

## 📄 Licença

Este projeto é distribuído sob a licença **MIT**.  
Sinta-se livre para usar, modificar e distribuir conforme necessário.

---
**👨‍💻 Autor:** Gabriel Pereira Macena
