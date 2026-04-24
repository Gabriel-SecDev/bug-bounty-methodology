# 04 — Relatórios / Reporting

> **PT-BR** | [English below](#english-version)

---

## 🇧🇷 Como Estruturo Meus Relatórios

Um relatório bem escrito é tão importante quanto a vulnerabilidade em si. Um triage que não entende o impacto vai fechar como "Informative". Um relatório claro e com evidências sólidas vai resultar em "Triaged".

---

### Estrutura do Relatório

#### 1. Título
Seja específico. Descreva a vulnerabilidade e onde está.

```
✅ IDOR no endpoint /api/invoices/{id} permite acesso a dados financeiros de outros usuários
❌ IDOR encontrado
```

#### 2. Severidade (CVSS)
Use a escala padrão:
- **Critical** — RCE, SQLi com exfiltração, acesso a dados massivos
- **High** — IDOR com dados sensíveis, Auth Bypass, Stored XSS
- **Medium** — IDOR com dados menos sensíveis, Reflected XSS, CSRF
- **Low** — informações não críticas expostas, missing headers
- **Informative** — comportamento público por design, sem impacto real

#### 3. Descrição
Explique o que é a vulnerabilidade em linguagem clara. Assuma que o triage pode não ser especialista naquele vetor específico.

```
O endpoint /api/invoices/{id} não valida se o usuário autenticado 
é o proprietário do recurso solicitado. Um atacante autenticado pode 
iterar sobre IDs numéricos e acessar faturas de outros usuários.
```

#### 4. Impacto
**Esta é a parte mais importante.** O que um atacante consegue fazer com isso? Seja concreto.

```
Um atacante autenticado pode:
- Acessar dados financeiros (valor, vencimento, CPF) de qualquer usuário da plataforma
- Enumerar todos os boletos ativos via iteração de IDs
- Em contexto brasileiro: violação direta da LGPD (Art. 46)
```

#### 5. Passos para Reproduzir
Numerados, precisos, reproduzíveis. O triage deve conseguir reproduzir sem fazer perguntas.

```
1. Criar conta A (victima@email.com) e gerar um boleto
2. Anotar o ID retornado: /api/invoices/1042
3. Fazer login com conta B (atacante@email.com)
4. Enviar requisição: GET /api/invoices/1041
5. Observar que os dados financeiros da conta A são retornados
```

#### 6. Evidências
- Screenshots numerados e anotados
- Requisições HTTP completas (request + response)
- Vídeo de PoC quando necessário

#### 7. Recomendação de Remediação
Mostre que você entende o problema técnico.

```
Implementar verificação de propriedade no lado servidor:
- Validar que invoice.user_id === req.user.id antes de retornar dados
- Nunca confiar em IDs enviados pelo cliente sem validação de ownership
```

---

### Erros Comuns que Geram "Informative"

| Erro | Correção |
|---|---|
| Reportar comportamento público por design | Verificar se o dado acessado é realmente privado |
| Sem evidência de dados únicos/privados | Provar acesso a dado que só a vítima deveria ver |
| PoC vago ("parece vulnerável") | Passos exatos e screenshot do dado exposto |
| Impacto genérico | Impacto específico e concreto para aquele contexto |
| Título genérico | Título com endpoint, tipo e impacto |

---

### Template de Relatório

```markdown
**Título:** [Tipo de Vuln] em [Endpoint] permite [Impacto]

**Severidade:** High / Medium / Low

**Descrição:**
[Explicação clara do problema]

**Impacto:**
[O que um atacante consegue fazer concretamente]

**Passos para Reproduzir:**
1. 
2. 
3. 

**Evidências:**
[Screenshots / Request + Response]

**Recomendação:**
[Como corrigir tecnicamente]
```

---

---

## 🇺🇸 How I Structure My Reports <a name="english-version"></a>

A well-written report is as important as the vulnerability itself. A triage that doesn't understand the impact will close it as "Informative". A clear report with solid evidence will result in "Triaged".

---

### Report Structure

#### 1. Title
Be specific. Describe the vulnerability and where it is.

```
✅ IDOR on /api/invoices/{id} endpoint allows access to other users' financial data
❌ IDOR found
```

#### 2. Severity (CVSS)
- **Critical** — RCE, SQLi with exfiltration, massive data access
- **High** — IDOR with sensitive data, Auth Bypass, Stored XSS
- **Medium** — IDOR with less sensitive data, Reflected XSS, CSRF
- **Low** — non-critical info exposed, missing security headers
- **Informative** — public by design behavior, no real impact

#### 3. Description
Explain the vulnerability in clear language. Assume the triage may not be an expert in that specific vector.

#### 4. Impact
**This is the most important part.** What can an attacker actually do? Be concrete.

#### 5. Steps to Reproduce
Numbered, precise, reproducible. Triage should be able to reproduce without asking questions.

#### 6. Evidence
- Numbered and annotated screenshots
- Full HTTP requests (request + response)
- PoC video when necessary

#### 7. Remediation Recommendation
Show that you understand the technical problem.

---

### Common Mistakes That Generate "Informative"

| Mistake | Fix |
|---|---|
| Reporting public by design behavior | Verify the accessed data is actually private |
| No evidence of unique/private data | Prove access to data only the victim should see |
| Vague PoC ("seems vulnerable") | Exact steps and screenshot of exposed data |
| Generic impact | Specific and concrete impact for that context |
| Generic title | Title with endpoint, type and impact |

---

### Report Template

```markdown
**Title:** [Vuln Type] on [Endpoint] allows [Impact]

**Severity:** High / Medium / Low

**Description:**
[Clear explanation of the problem]

**Impact:**
[What an attacker can concretely do]

**Steps to Reproduce:**
1. 
2. 
3. 

**Evidence:**
[Screenshots / Request + Response]

**Recommendation:**
[How to fix it technically]
```
