# 📋 Boletim Orçamentário Federal — AEB

Newsletter automática sobre **LOA, LDO e PPA**, gerada por IA com busca em tempo real e enviada por e-mail nos horários configurados.

**Elaborado por:** Divisão de Planejamento Orçamentário e Financeiro  
**Órgão:** Agência Espacial Brasileira — AEB

---

## Como funciona

```
⏰ GitHub Actions (horários configurados)
        ↓
🔍 Claude busca manchetes na web (LOA, LDO, PPA...)
        ↓
✍️  IA organiza, prioriza e analisa as notícias
        ↓
📧 SendGrid envia e-mail personalizado para cada destinatário
```

---

## Configuração — passo a passo

### 1. Crie um repositório privado no GitHub

Acesse https://github.com/new, crie um repositório **privado** chamado `newsletter-orcamento` e faça upload de todos os arquivos desta pasta, mantendo a estrutura:

```
newsletter-orcamento/
├── newsletter.py
├── README.md
└── .github/
    └── workflows/
        └── newsletter.yml
```

---

### 2. Obtenha as chaves de API

#### Claude (Anthropic)
1. Acesse https://console.anthropic.com/settings/keys
2. Clique em **Create Key**
3. Copie a chave — começa com `sk-ant-...`

#### SendGrid (envio de e-mail — gratuito até 100/dia)
1. Crie conta gratuita em https://sendgrid.com
2. Vá em **Settings → API Keys → Create API Key**
3. Permissão: **Restricted Access → Mail Send** (ativar)
4. Copie a chave — começa com `SG.`
5. Verifique seu e-mail remetente em **Settings → Sender Authentication → Single Sender Verification**

---

### 3. Configure os segredos no GitHub

No repositório, vá em **Settings → Secrets and variables → Actions → New repository secret** e adicione:

| Segredo            | Valor                                                    | Exemplo                                      |
|--------------------|----------------------------------------------------------|----------------------------------------------|
| `ANTHROPIC_API_KEY`| Chave da API do Claude                                   | `sk-ant-api03-...`                           |
| `SENDGRID_API_KEY` | Chave do SendGrid                                        | `SG.xxxxxxxxxxxx`                            |
| `FROM_EMAIL`       | E-mail remetente verificado no SendGrid                  | `boletim@aeb.gov.br`                         |
| `RECIPIENTS`       | Lista de destinatários (ver formato abaixo)              | `joao@aeb.gov.br:João,maria@aeb.gov.br:Maria`|

#### Formato da variável `RECIPIENTS`

Separe os destinatários por vírgula, usando `email:Nome`:

```
joao@aeb.gov.br:João Silva,maria@aeb.gov.br:Maria Costa,pedro@aeb.gov.br:Pedro Alves
```

Cada pessoa recebe um e-mail individualizado com seu próprio nome na saudação.

---

### 4. Configure os agendamentos

Edite o arquivo `.github/workflows/newsletter.yml` e ajuste as linhas de `cron`:

```yaml
schedule:
  - cron: '30 10 * * 1-5'   # 07:30 BRT — dias úteis
  - cron: '0 15 * * 5'      # 12:00 BRT — sextas-feiras
  - cron: '0 11 * * 1'      # 08:00 BRT — segundas-feiras
```

**Referência rápida de horários (BRT → UTC):**

| Horário BRT | Cron (UTC) |
|-------------|------------|
| 06:00       | `0 9 * * ...` |
| 07:00       | `0 10 * * ...` |
| 07:30       | `30 10 * * ...` |
| 08:00       | `0 11 * * ...` |
| 09:00       | `0 12 * * ...` |
| 12:00       | `0 15 * * ...` |
| 18:00       | `0 21 * * ...` |

**Referência de dias:**

| Código | Dias               |
|--------|--------------------|
| `1-5`  | Segunda a Sexta    |
| `1`    | Segunda-feira      |
| `5`    | Sexta-feira        |
| `1,3,5`| Seg, Qua e Sex     |
| `*`    | Todos os dias      |

---

### 5. Ative e teste

1. Na aba **Actions** do repositório, clique em **"I understand my workflows, go ahead and enable them"** (se solicitado)
2. Selecione o workflow **Newsletter Orçamentária AEB**
3. Clique em **Run workflow → Run workflow** para disparar um teste imediato
4. Verifique se o e-mail chegou na caixa de entrada de todos os destinatários

---

## Personalização

### Alterar os temas monitorados

No arquivo `newsletter.yml`, ajuste a variável `TOPICS`:

```yaml
TOPICS: "LOA,LDO,PPA"               # padrão
TOPICS: "LOA,LDO,PPA,PLOA,Fiscal"   # mais temas
TOPICS: "LOA,Tesouro,SIAFI"         # temas específicos
```

### Adicionar ou remover destinatários

Atualize o segredo `RECIPIENTS` no GitHub:

- **Adicionar:** inclua `novo@email.com:Nome` separado por vírgula
- **Remover:** apague a entrada correspondente e salve o segredo

---

## Estrutura dos arquivos

```
newsletter-orcamento/
├── newsletter.py                  # Script principal (busca + geração + envio)
├── README.md                      # Esta documentação
└── .github/
    └── workflows/
        └── newsletter.yml         # Agendamento automático (GitHub Actions)
```

---

## Estimativa de custos

| Serviço        | Plano gratuito              | Custo mensal (22 dias úteis) |
|----------------|-----------------------------|------------------------------|
| GitHub Actions | 2.000 min/mês gratuitos     | ~15 min/mês — **gratuito**   |
| SendGrid       | 100 e-mails/dia gratuitos   | até 22 envios — **gratuito** |
| Anthropic API  | Pago por uso                | ~R$ 1,50 a R$ 4,00           |

**Custo total estimado: menos de R$ 4,00 por mês.**

---

## Suporte

Em caso de dúvidas ou problemas, entre em contato com a  
**Divisão de Planejamento Orçamentário e Financeiro — AEB**.
