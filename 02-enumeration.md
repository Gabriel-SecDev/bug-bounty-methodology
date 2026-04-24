# 02 — Enumeração / Enumeration

> **PT-BR** | [English below](#english-version)

---

## 🇧🇷 Enumeração

Após o recon, o objetivo é expandir a superfície de ataque identificando subdomínios ativos, endpoints e parâmetros escondidos.

### Enumeração de Subdomínios

#### Ferramentas Principais
```bash
# Subfinder — rápido, passivo, múltiplas fontes
subfinder -d target.com -o subdomains.txt

# Amass — mais profundo, passivo + ativo
amass enum -passive -d target.com

# Combinando resultados
cat subdomains.txt | sort -u > all_subdomains.txt
```

#### Verificar Quais Estão Ativos
```bash
# httpx — checa quais subdomínios respondem HTTP/HTTPS
cat all_subdomains.txt | httpx -silent -o live_subdomains.txt

# Verificar status codes
cat all_subdomains.txt | httpx -status-code -title -o live_with_info.txt
```

#### Subdomain Takeover
```bash
# Subzy — verifica se subdomínios apontam para serviços não reclamados
subzy run --targets all_subdomains.txt
```
- Subdomínios apontando para S3, GitHub Pages, Heroku sem claim ativo = takeover possível

---

### Enumeração de Endpoints

#### Crawling
```bash
# Katana — crawler moderno
katana -u https://target.com -o endpoints.txt

# Waybackurls — endpoints históricos
echo "target.com" | waybackurls | tee wayback_urls.txt
```

#### Força Bruta de Diretórios
```bash
# Feroxbuster
feroxbuster -u https://target.com -w /usr/share/wordlists/dirb/common.txt

# FFUF
ffuf -u https://target.com/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,301,302,403
```

#### Endpoints de API
```bash
# Buscar por padrões comuns de API
ffuf -u https://target.com/api/FUZZ -w wordlist-api.txt -mc 200,201,400,401,403

# Padrões a testar manualmente
/api/v1/
/api/v2/
/graphql
/swagger.json
/openapi.json
/api-docs
```

---

### Enumeração de Parâmetros

#### Parâmetros em URLs
```bash
# Arjun — descobre parâmetros GET/POST escondidos
arjun -u https://target.com/endpoint

# Paramspider — coleta parâmetros de URLs históricas
paramspider -d target.com
```

#### O que observar
- Parâmetros numéricos sequenciais → possível IDOR (`?id=1`, `?user=123`)
- Parâmetros que refletem valor na resposta → possível XSS
- Parâmetros de redirecionamento → possível Open Redirect (`?redirect=`, `?url=`, `?next=`)

---

### ✅ Checklist de Enumeração

- [ ] Subdomínios coletados (subfinder + amass)
- [ ] Subdomínios ativos verificados (httpx)
- [ ] Subdomain takeover verificado (subzy)
- [ ] Endpoints coletados via crawling + wayback
- [ ] Força bruta de diretórios executada
- [ ] Endpoints de API mapeados
- [ ] Parâmetros escondidos testados (arjun)

---

---

## 🇺🇸 Enumeration <a name="english-version"></a>

After recon, the goal is to expand the attack surface by identifying active subdomains, endpoints, and hidden parameters.

### Subdomain Enumeration

#### Main Tools
```bash
# Subfinder — fast, passive, multiple sources
subfinder -d target.com -o subdomains.txt

# Amass — deeper, passive + active
amass enum -passive -d target.com

# Merging results
cat subdomains.txt | sort -u > all_subdomains.txt
```

#### Check Which Are Live
```bash
# httpx — checks which subdomains respond to HTTP/HTTPS
cat all_subdomains.txt | httpx -silent -o live_subdomains.txt

# Check status codes
cat all_subdomains.txt | httpx -status-code -title -o live_with_info.txt
```

#### Subdomain Takeover
```bash
subzy run --targets all_subdomains.txt
```
- Subdomains pointing to unclaimed S3, GitHub Pages, Heroku = potential takeover

---

### Endpoint Enumeration

#### Crawling
```bash
katana -u https://target.com -o endpoints.txt
echo "target.com" | waybackurls | tee wayback_urls.txt
```

#### Directory Bruteforce
```bash
feroxbuster -u https://target.com -w /usr/share/wordlists/dirb/common.txt
ffuf -u https://target.com/FUZZ -w wordlist.txt -mc 200,301,302,403
```

#### API Endpoints
```bash
ffuf -u https://target.com/api/FUZZ -w api-wordlist.txt -mc 200,201,400,401,403

# Common patterns to test manually
/api/v1/
/api/v2/
/graphql
/swagger.json
/openapi.json
/api-docs
```

---

### Parameter Enumeration

#### URL Parameters
```bash
arjun -u https://target.com/endpoint
paramspider -d target.com
```

#### What to look for
- Sequential numeric parameters → possible IDOR (`?id=1`, `?user=123`)
- Parameters reflected in response → possible XSS
- Redirect parameters → possible Open Redirect (`?redirect=`, `?url=`, `?next=`)

---

### ✅ Enumeration Checklist

- [ ] Subdomains collected (subfinder + amass)
- [ ] Live subdomains verified (httpx)
- [ ] Subdomain takeover checked (subzy)
- [ ] Endpoints collected via crawling + wayback
- [ ] Directory bruteforce executed
- [ ] API endpoints mapped
- [ ] Hidden parameters tested (arjun)
