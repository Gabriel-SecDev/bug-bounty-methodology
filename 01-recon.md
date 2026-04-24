# 01 — Reconhecimento / Reconnaissance

> **PT-BR** | [English below](#english-version)

---

## 🇧🇷 Reconhecimento

O reconhecimento é a fase mais importante. Quanto mais informação você coleta antes de tocar no alvo, mais cirúrgico e eficiente você é nos testes.

### Fase 1 — Reconhecimento Passivo

Coletar informações sem interagir diretamente com o alvo.

#### 🔍 Informações do Domínio
```
whois target.com
```
- Registrante, datas de criação/expiração, nameservers
- Buscar por domínios relacionados registrados pelo mesmo dono

#### 🗂️ Google Dorks
```
site:target.com
site:target.com filetype:pdf
site:target.com inurl:admin
site:target.com inurl:api
site:target.com "senha" OR "password" OR "token"
```

#### 🗃️ Wayback Machine / URLs Históricas
```
https://web.archive.org/web/*/target.com/*
```
- Buscar endpoints antigos que podem ainda estar ativos
- Parâmetros expostos em URLs históricas

#### 📧 Coleta de E-mails / Funcionários
- LinkedIn: buscar funcionários da empresa alvo
- Hunter.io: padrão de e-mails corporativos
- Isso ajuda a entender a superfície de ataque e tecnologias usadas

#### 🔐 Certificados SSL (Certificate Transparency)
```
https://crt.sh/?q=%.target.com
```
- Revela subdomínios via logs de certificados públicos
- Um dos melhores métodos para descoberta passiva de subdomínios

#### 🛠️ Shodan / Censys
```
hostname:target.com
ssl:target.com
```
- Serviços expostos, portas abertas, banners
- Versões de software que podem ter CVEs conhecidos

---

### Fase 2 — Reconhecimento Ativo

Interagir diretamente com o alvo de forma controlada.

#### 📡 DNS
```bash
# Resolução básica
nslookup target.com
dig target.com ANY

# Tentativa de transferência de zona (raramente funciona, mas vale tentar)
dig axfr @ns1.target.com target.com
```

#### 🌐 Detecção de Tecnologias
- **Wappalyzer** (extensão de browser) — stack do front-end
- **Whatweb** — fingerprinting via terminal
- Headers HTTP — framework, servidor, versões
- Cookies — nomes de sessão revelam tecnologias (PHPSESSID, JSESSIONID, etc.)

#### 🗺️ Mapeamento de Superfície
- Página inicial, login, cadastro, recuperação de senha
- Áreas autenticadas vs públicas
- Documentação pública de API (Swagger, Postman collections)
- Mobile apps relacionados (APK para análise)

---

### ✅ Checklist de Recon

- [ ] WHOIS e histórico do domínio
- [ ] Google Dorks aplicados
- [ ] crt.sh para subdomínios via certificados
- [ ] Wayback Machine para URLs históricas
- [ ] Shodan/Censys para serviços expostos
- [ ] Tecnologias identificadas
- [ ] Superfície de ataque mapeada
- [ ] Documentação de API localizada

---

---

## 🇺🇸 Reconnaissance <a name="english-version"></a>

Reconnaissance is the most critical phase. The more information you gather before touching the target, the more precise and efficient your testing will be.

### Phase 1 — Passive Reconnaissance

Collect information without directly interacting with the target.

#### 🔍 Domain Information
```
whois target.com
```
- Registrant, creation/expiration dates, nameservers
- Look for related domains registered by the same owner

#### 🗂️ Google Dorks
```
site:target.com
site:target.com filetype:pdf
site:target.com inurl:admin
site:target.com inurl:api
site:target.com "password" OR "token" OR "secret"
```

#### 🗃️ Wayback Machine / Historical URLs
```
https://web.archive.org/web/*/target.com/*
```
- Look for old endpoints that may still be active
- Parameters exposed in historical URLs

#### 📧 Email / Employee Harvesting
- LinkedIn: search for target company employees
- Hunter.io: corporate email patterns
- Helps understand attack surface and tech stack

#### 🔐 SSL Certificates (Certificate Transparency)
```
https://crt.sh/?q=%.target.com
```
- Reveals subdomains via public certificate logs
- One of the best methods for passive subdomain discovery

#### 🛠️ Shodan / Censys
```
hostname:target.com
ssl:target.com
```
- Exposed services, open ports, banners
- Software versions with known CVEs

---

### Phase 2 — Active Reconnaissance

Directly interact with the target in a controlled way.

#### 📡 DNS
```bash
nslookup target.com
dig target.com ANY

# Zone transfer attempt
dig axfr @ns1.target.com target.com
```

#### 🌐 Technology Detection
- **Wappalyzer** (browser extension) — frontend stack
- **Whatweb** — terminal fingerprinting
- HTTP Headers — framework, server, versions
- Cookies — session names reveal technologies

#### 🗺️ Surface Mapping
- Homepage, login, register, password recovery
- Authenticated vs public areas
- Public API docs (Swagger, Postman collections)
- Related mobile apps (APK for analysis)

---

### ✅ Recon Checklist

- [ ] WHOIS and domain history
- [ ] Google Dorks applied
- [ ] crt.sh for certificate-based subdomain discovery
- [ ] Wayback Machine for historical URLs
- [ ] Shodan/Censys for exposed services
- [ ] Technologies identified
- [ ] Attack surface mapped
- [ ] API documentation located
