# 03 — Testes Manuais / Manual Testing

> **PT-BR** | [English below](#english-version)

---

## 🇧🇷 Testes Manuais

Ferramentas automatizadas encontram o óbvio. Vulnerabilidades de alto impacto são encontradas com raciocínio manual. Esta é a fase onde a diferença é feita.

---

### IDOR (Insecure Direct Object Reference)

Uma das vulnerabilidades mais comuns e impactantes em aplicações web.

**O que procurar:**
- IDs numéricos sequenciais em URLs, parâmetros ou corpo da requisição
- GUIDs/UUIDs que identificam recursos de outros usuários
- Endpoints que retornam dados sem validar se o usuário autenticado é o dono

**Como testar:**
1. Crie duas contas (Conta A e Conta B)
2. Com a Conta A, acesse um recurso e capture o ID
3. Com a Conta B, tente acessar o mesmo recurso usando o ID da Conta A
4. Se retornar dados → IDOR confirmado

```
# Exemplos de endpoints a testar
GET /api/users/1234/profile
GET /api/invoices/5678
GET /api/orders/9012/receipt
POST /api/documents/delete {"id": "1234"}
```

**Impacto esperado:**
- Acesso a dados privados de outros usuários
- Modificação ou exclusão de recursos alheios
- Em contexto financeiro: acesso a boletos, extratos, dados bancários

---

### XSS (Cross-Site Scripting)

**Tipos:**
- **Reflected** — payload refletido imediatamente na resposta
- **Stored** — payload armazenado e executado para outros usuários
- **DOM-based** — manipulação do DOM via JavaScript do lado cliente

**Pontos de entrada a testar:**
- Campos de busca
- Parâmetros de URL
- Campos de perfil/cadastro
- Comentários e posts
- Headers (User-Agent, Referer)

**Payloads iniciais:**
```javascript
// Teste básico de reflexo
<script>alert(1)</script>
"><script>alert(1)</script>
'"><img src=x onerror=alert(1)>

// Bypass de filtros simples
<svg onload=alert(1)>
<img src=x onerror=confirm(1)>
javascript:alert(1)
```

**Observar:**
- A resposta sanitiza o input?
- Qual é a política CSP? (`Content-Security-Policy` header)
- O valor é refletido dentro de atributo, tag, ou JavaScript?

---

### JWT Attacks

**Inspecionar o token:**
```bash
# Decodificar (base64)
echo "eyJ..." | base64 -d

# Ou usar jwt.io
```

**Testes a realizar:**

1. **Algoritmo `none`** — remover assinatura
```
Header: {"alg": "none", "typ": "JWT"}
Remover a parte da assinatura
```

2. **Confusão RS256 → HS256** — usar chave pública como segredo HMAC

3. **Secret fraco** — tentar brute force da chave
```bash
hashcat -a 0 -m 16500 token.jwt wordlist.txt
```

4. **Manipulação de claims** — alterar `role`, `userId`, `isAdmin` e reenviar

---

### Broken Access Control

- Acessar endpoints autenticados sem token
- Acessar endpoints de admin com conta comum
- Testar métodos HTTP alternativos (`PUT`, `DELETE`, `PATCH`) em endpoints que só aceitam `GET`
- Manipular parâmetros de role: `"role":"user"` → `"role":"admin"`

---

### API Security

```bash
# Testar versões antigas da API
/api/v1/ vs /api/v2/   ← v1 pode ter menos proteção

# Testar verbos HTTP não esperados
OPTIONS /api/users
HEAD /api/users/1234

# Mass Assignment — enviar campos extras no body
{"username": "gabriel", "role": "admin", "verified": true}
```

---

### ✅ Checklist de Testes

- [ ] IDOR testado em todos os endpoints com IDs
- [ ] XSS testado em todos os campos de input
- [ ] JWT analisado e manipulado
- [ ] Access control verificado entre roles
- [ ] Endpoints de API testados com métodos alternativos
- [ ] Versões antigas de API verificadas
- [ ] Mass assignment testado em POSTs e PUTs

---

---

## 🇺🇸 Manual Testing <a name="english-version"></a>

Automated tools find the obvious. High-impact vulnerabilities are found through manual reasoning. This is the phase where the real difference is made.

---

### IDOR (Insecure Direct Object Reference)

**What to look for:**
- Sequential numeric IDs in URLs, parameters, or request body
- GUIDs/UUIDs identifying other users' resources
- Endpoints returning data without validating ownership

**How to test:**
1. Create two accounts (Account A and Account B)
2. With Account A, access a resource and capture the ID
3. With Account B, try accessing the same resource using Account A's ID
4. If it returns data → IDOR confirmed

**Expected impact:**
- Access to other users' private data
- Modification or deletion of others' resources
- In financial context: access to invoices, statements, banking data

---

### XSS (Cross-Site Scripting)

**Entry points to test:**
- Search fields
- URL parameters
- Profile/registration fields
- Comments and posts
- Headers (User-Agent, Referer)

**Initial payloads:**
```javascript
<script>alert(1)</script>
"><script>alert(1)</script>
'"><img src=x onerror=alert(1)>
<svg onload=alert(1)>
```

**Observe:**
- Does the response sanitize input?
- What's the CSP policy?
- Is the value reflected inside an attribute, tag, or JavaScript?

---

### JWT Attacks

**Tests to perform:**
1. **`none` algorithm** — remove signature
2. **RS256 → HS256 confusion** — use public key as HMAC secret
3. **Weak secret** — brute force the key
4. **Claims manipulation** — change `role`, `userId`, `isAdmin`

---

### Broken Access Control

- Access authenticated endpoints without token
- Access admin endpoints with regular account
- Test alternative HTTP methods on restricted endpoints
- Manipulate role parameters: `"role":"user"` → `"role":"admin"`

---

### ✅ Testing Checklist

- [ ] IDOR tested on all endpoints with IDs
- [ ] XSS tested on all input fields
- [ ] JWT analyzed and manipulated
- [ ] Access control verified between roles
- [ ] API endpoints tested with alternative methods
- [ ] Old API versions checked
- [ ] Mass assignment tested on POSTs and PUTs
