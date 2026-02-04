# 🎓 Tutorial do Professor: OpenClaw + Ollama — Entendendo Cada Detalhe

> *"Para ensinar é preciso primeiro entender. Este documento não é apenas um guia de instalação — é uma aula sobre como funcionam os componentes de um sistema de IA local moderno."*

> **Plataformas cobertas**: 🍎 macOS, 🪟 Windows, 🐧 Linux

---

## 📚 Filosofia deste Tutorial

Este tutorial foi criado com a mentalidade de um professor que quer que você **entenda**, não apenas **copie e cole**. Para cada passo, vamos explorar:

- **O que estamos fazendo?** — A ação em si
- **Por que fazemos isso?** — A motivação técnica
- **O que acontece por baixo dos panos?** — Os mecanismos internos
- **E se der errado?** — Como diagnosticar problemas
- **Diferenças entre sistemas** — O que muda entre macOS, Windows e Linux

---

## 🧠 Parte 1: Entendendo a Arquitetura

Antes de instalar qualquer coisa, vamos entender **o que estamos construindo**.

### O Que é um LLM (Large Language Model)?

Um LLM é uma rede neural treinada em bilhões de textos para prever a próxima palavra em uma sequência. Pense nele como um autocompletar extremamente sofisticado.

```
Entrada: "O céu é"
Modelo pensa: Qual palavra tem maior probabilidade de vir depois?
Saída: "azul" (ou "lindo", "infinito", etc.)
```

**Conceito importante**: O modelo não "sabe" nada — ele calcula probabilidades estatísticas baseadas nos padrões que viu durante o treinamento.

### A Arquitetura do Nosso Sistema

```
┌─────────────────────────────────────────────────────────────────┐
│                        VOCÊ (Usuário)                           │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    OpenClaw Gateway                              │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ • Recebe mensagens de canais (WhatsApp, Telegram, etc.)     ││
│  │ • Gerencia contexto e sessões                               ││
│  │ • Roteia para o modelo de IA                                ││
│  │ • Formata respostas de volta                                ││
│  └─────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         Ollama                                   │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ • Carrega o modelo na memória (RAM/GPU)                     ││
│  │ • Expõe API compatível com OpenAI                           ││
│  │ • Processa tokens e gera respostas                          ││
│  └─────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    DeepSeek R1 14B                               │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ • 14 bilhões de parâmetros (pesos da rede neural)           ││
│  │ • Arquitetura Transformer otimizada                         ││
│  │ • Treinado para raciocínio (chain-of-thought)               ││
│  └─────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
```

### Por que essa arquitetura em camadas?

1. **Separação de responsabilidades**: Cada componente faz uma coisa bem feita
2. **Flexibilidade**: Você pode trocar o modelo (Ollama) sem mudar o gateway (OpenClaw)
3. **Escalabilidade**: Múltiplos gateways podem usar o mesmo Ollama
4. **Manutenibilidade**: Atualizar um componente não quebra os outros

### A arquitetura é a mesma em todos os sistemas?

**Sim!** O diagrama acima funciona identicamente em macOS, Windows e Linux. 

A única diferença está na **implementação interna** de cada componente:
- **macOS/Linux**: Processos Unix, sockets BSD
- **Windows**: Serviços Windows, Winsock

Para você, desenvolvedor, isso é transparente.

---

## 🔧 Parte 2: Por que Node.js >= 22?

### O Comando

```bash
node --version
```

### O que está acontecendo?

Este comando pergunta ao Node.js instalado sua versão. Node.js é um runtime JavaScript — ele permite executar código JavaScript fora do navegador.

### Por que precisa ser versão 22 ou superior?

O OpenClaw usa recursos modernos do JavaScript/ECMAScript que só existem em versões recentes:

| Recurso | Versão Mínima | Uso no OpenClaw |
|---------|---------------|-----------------|
| `import` nativo (ES Modules) | Node 12+ | Organização do código |
| `fetch()` nativo | Node 18+ | Requisições HTTP |
| `WebSocket` nativo | Node 21+ | Comunicação em tempo real |
| Performance melhorada | Node 22+ | Processamento de mensagens |

### Diferenças na instalação do Node.js

| 🍎 macOS | 🪟 Windows | 🐧 Linux |
|----------|------------|----------|
| Homebrew, nvm, ou instalador | Instalador .msi, winget, choco | apt, dnf, pacman, ou nvm |
| Binários pré-compilados ARM/x64 | Binários x64 (ARM experimental) | Binários x64/ARM |
| PATH configurado automaticamente | Geralmente automático | Pode precisar configurar manualmente |

### O que é o PATH?

O `PATH` é uma variável de ambiente que diz ao sistema onde procurar executáveis:

```bash
# macOS/Linux
echo $PATH
# /usr/local/bin:/usr/bin:/bin:/opt/homebrew/bin

# Windows (PowerShell)
$env:PATH
# C:\Windows\system32;C:\Program Files\nodejs;...
```

Quando você digita `node`, o sistema procura em cada diretório do PATH até encontrar o executável.

---

## 📦 Parte 3: A Instalação do OpenClaw

### 🍎🐧 macOS/Linux: O Script de Instalação

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

#### Anatomia do comando

```bash
curl                          # Ferramenta para fazer requisições HTTP
  -f                          # "fail silently" - não mostra erro HTML se falhar
  -s                          # "silent" - não mostra barra de progresso
  -S                          # "show error" - mas mostra erros reais
  -L                          # "location" - segue redirecionamentos HTTP
  https://...                 # URL do script
|                             # "pipe" - envia a saída para...
bash                          # ... o interpretador de shell
```

### 🪟 Windows: Via npm/npx

```powershell
npm install -g openclaw@latest
```

#### Por que diferente no Windows?

1. **Não tem `curl | bash` nativo** — PowerShell funciona diferente
2. **npm funciona igual** — Node.js abstrai as diferenças
3. **Segurança** — Windows é mais restritivo com scripts externos

#### O que `-g` significa?

```
npm install openclaw         # Instala localmente (só neste projeto)
npm install -g openclaw      # Instala globalmente (disponível em todo sistema)
```

Local: `./node_modules/.bin/openclaw`  
Global: `/usr/local/bin/openclaw` (macOS/Linux) ou `%AppData%\npm\openclaw.cmd` (Windows)

### ⚠️ Segurança: Entendendo o risco de `curl | bash`

Quando você executa `curl ... | bash`, está dizendo:

> "Baixe um script da internet e execute imediatamente no meu computador"

**Alternativa mais segura (macOS/Linux)**:
```bash
curl -fsSL https://openclaw.ai/install.sh -o install.sh
cat install.sh  # Ler o conteúdo
bash install.sh  # Executar após verificar
```

**No Windows, npm é mais seguro** porque:
- Pacotes são verificados pelo registro npm
- Há assinaturas de integridade
- Código é auditável em node_modules

---

## 🦙 Parte 4: Ollama — O Motor de Inferência

### O que é "inferência"?

**Treinamento** é quando o modelo aprende (feito uma vez, pela empresa que criou o modelo).
**Inferência** é quando o modelo usa o que aprendeu para gerar respostas (feito toda vez que você pergunta algo).

### Por que Ollama?

Antes do Ollama, rodar um LLM localmente exigia:
- Instalar Python e dezenas de dependências
- Configurar CUDA/ROCm para GPU
- Baixar modelos manualmente
- Escrever código para carregar e rodar

Ollama simplifica tudo isso em um único binário.

### Instalação por Sistema Operacional

#### 🍎 macOS: Homebrew ou App

**Via Homebrew:**
```bash
brew install ollama
brew services start ollama
```

**O que `brew services` faz?**

Ele usa o sistema `launchd` do macOS para gerenciar serviços:

```
~/Library/LaunchAgents/homebrew.mxcl.ollama.plist
```

Este arquivo `.plist` (Property List) diz ao macOS:
- Quando iniciar o Ollama (no login)
- Como reiniciar se falhar
- Variáveis de ambiente

#### 🪟 Windows: Instalador nativo

O instalador Windows:
1. Copia binários para `C:\Program Files\Ollama`
2. Registra como serviço do Windows
3. Adiciona ícone na bandeja do sistema

**Verificar o serviço:**
```powershell
Get-Service Ollama
# Status   Name    DisplayName
# ------   ----    -----------
# Running  Ollama  Ollama
```

**Por que Windows usa serviços?**

Serviços Windows (`services.msc`) são processos que:
- Rodam em background sem janela
- Iniciam automaticamente com o sistema
- Têm gerenciamento de permissões integrado

#### 🐧 Linux: Script universal + systemd

```bash
curl -fsSL https://ollama.ai/install.sh | sh
```

**O que este script faz:**
1. Detecta sua distro (Ubuntu, Fedora, Arch...)
2. Baixa o binário correto (x64 ou ARM)
3. Configura permissões
4. Cria unit file do systemd

**O que é systemd?**

É o sistema de init padrão do Linux moderno. Gerencia serviços via "unit files":

```
/etc/systemd/system/ollama.service
```

```ini
[Unit]
Description=Ollama Service
After=network.target

[Service]
ExecStart=/usr/local/bin/ollama serve
Restart=always

[Install]
WantedBy=multi-user.target
```

**Comandos systemd:**
```bash
sudo systemctl start ollama     # Iniciar
sudo systemctl stop ollama      # Parar
sudo systemctl restart ollama   # Reiniciar
sudo systemctl status ollama    # Ver status
sudo systemctl enable ollama    # Iniciar no boot
```

### Verificando se Ollama está rodando

O teste é o mesmo em todos os sistemas — fazer uma requisição HTTP:

```bash
# macOS/Linux
curl http://127.0.0.1:11434/v1/models

# Windows (PowerShell)
Invoke-WebRequest http://127.0.0.1:11434/v1/models | Select-Object -Expand Content
```

**O que cada parte significa?**

- `127.0.0.1` — "localhost", endereço de loopback (sua máquina falando consigo mesma)
- `11434` — Porta padrão do Ollama
- `/v1/models` — Endpoint da API que lista modelos

---

## 🧮 Parte 5: Entendendo os Modelos

### O que significa "14b"?

"14b" = 14 bilhões de **parâmetros** (pesos da rede neural).

| Parâmetros | Qualidade | RAM Necessária | Velocidade |
|------------|-----------|----------------|------------|
| 7B | Básica | ~8GB | Rápido |
| 14B | Boa | ~16GB | Médio |
| 32B | Excelente | ~32GB | Lento |
| 70B+ | Estado da arte | ~64GB+ | Muito lento |

### Por que o arquivo tem 9GB se são 14 bilhões de parâmetros?

**Quantização** — O modelo original usa números de 32 bits:

```
14.000.000.000 × 4 bytes = 56 GB
```

Com quantização de 4 bits:
```
14.000.000.000 × 0.5 bytes = 7 GB (+ overhead)
```

### Onde os modelos ficam armazenados?

| 🍎 macOS | 🪟 Windows | 🐧 Linux |
|----------|------------|----------|
| `~/.ollama/models/` | `%USERPROFILE%\.ollama\models\` | `~/.ollama/models/` |
| Geralmente: `/Users/seu_usuario/.ollama/models/` | `C:\Users\seu_usuario\.ollama\models\` | `/home/seu_usuario/.ollama/models/` |

**Mudar localização (todos os sistemas):**

Defina a variável de ambiente `OLLAMA_MODELS`:

```bash
# macOS/Linux (adicione ao ~/.bashrc ou ~/.zshrc)
export OLLAMA_MODELS=/mnt/hd_externo/ollama_models

# Windows (PowerShell, permanente)
[Environment]::SetEnvironmentVariable("OLLAMA_MODELS", "D:\ollama_models", "User")
```

---

## ⚙️ Parte 6: O Arquivo de Configuração — Linha por Linha

### Localização do arquivo

| Sistema | Caminho | Forma alternativa |
|---------|---------|-------------------|
| 🍎 macOS | `~/.openclaw/openclaw.json` | `/Users/usuario/.openclaw/openclaw.json` |
| 🐧 Linux | `~/.openclaw/openclaw.json` | `/home/usuario/.openclaw/openclaw.json` |
| 🪟 Windows | `%USERPROFILE%\.openclaw\openclaw.json` | `C:\Users\usuario\.openclaw\openclaw.json` |

### O que significa `~` (til)?

- **macOS/Linux**: Atalho para o diretório home (`$HOME`)
- **Windows**: Não é reconhecido nativamente! Use `%USERPROFILE%` ou caminho completo

### Anatomia do arquivo de configuração

```json
{
  "agents": {
```

**O que são "agents"?** Uma "personalidade" do OpenClaw.

```json
      "workspace": "~/.openclaw/workspace"
```

**⚠️ Cuidado no Windows!** 

O OpenClaw interpreta `~` internamente, mas se der problemas, use:
```json
"workspace": "C:\\Users\\SeuUsuario\\.openclaw\\workspace"
```

Note as **barras duplas** — em JSON, `\` é caractere de escape.

```json
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://127.0.0.1:11434/v1",
```

**Por que `127.0.0.1` e não `localhost`?**

Ambos funcionam, mas `127.0.0.1`:
- É mais rápido (não precisa resolver DNS)
- Evita problemas de arquivo hosts
- Funciona identicamente em todos os sistemas

---

## 🔐 Parte 7: Gerando Tokens Seguros

### Por que precisamos de um token?

Mesmo rodando localmente, qualquer processo no seu computador poderia acessar o gateway. O token adiciona uma camada de autenticação.

### Como gerar tokens por sistema

#### 🍎 macOS / 🐧 Linux
```bash
openssl rand -hex 32
# Saída: 1f6b770b63ae08bb435a02835ae73774e5f56d986c9f1649a447a5adc59191eb
```

`openssl` vem pré-instalado em ambos os sistemas.

#### 🪟 Windows (PowerShell)
```powershell
-join ((1..32) | ForEach-Object { "{0:x2}" -f (Get-Random -Maximum 256) })
```

**O que esse comando faz?**
1. `1..32` — Gera números de 1 a 32
2. `ForEach-Object` — Para cada número...
3. `Get-Random -Maximum 256` — Gera número aleatório 0-255
4. `"{0:x2}"` — Converte para hexadecimal (2 dígitos)
5. `-join` — Junta tudo em uma string

**Alternativa com OpenSSL no Windows:**
```powershell
# Se tiver Git Bash instalado
& "C:\Program Files\Git\usr\bin\openssl.exe" rand -hex 32
```

---

## 🔒 Parte 8: Permissões de Arquivo

### Por que permissões importam?

Seu `openclaw.json` contém o token de autenticação. Se outros usuários do sistema pudessem ler, poderiam acessar seu gateway.

### 🍎 macOS / 🐧 Linux: Permissões Unix

```bash
chmod 600 ~/.openclaw/openclaw.json
chmod 700 ~/.openclaw
```

**O que esses números significam?**

Cada dígito representa permissões para: **dono | grupo | outros**

| Dígito | Leitura (r) | Escrita (w) | Execução (x) |
|--------|-------------|-------------|--------------|
| 0 | ❌ | ❌ | ❌ |
| 4 | ✅ | ❌ | ❌ |
| 6 | ✅ | ✅ | ❌ |
| 7 | ✅ | ✅ | ✅ |

```
chmod 600 = rw------- = Só o dono pode ler e escrever
chmod 700 = rwx------ = Só o dono pode ler, escrever e acessar (diretório)
```

**Verificar permissões:**
```bash
ls -la ~/.openclaw/
# drwx------  5 usuario  staff   160 Feb  4 18:00 .
# -rw-------  1 usuario  staff  1234 Feb  4 18:00 openclaw.json
```

### 🪟 Windows: ACLs (Access Control Lists)

Windows usa um sistema diferente — ACLs são mais granulares mas mais complexas:

```powershell
# Mostrar permissões atuais
Get-Acl "$env:USERPROFILE\.openclaw\openclaw.json" | Format-List

# Restringir para apenas seu usuário
$path = "$env:USERPROFILE\.openclaw\openclaw.json"
$acl = Get-Acl $path
$acl.SetAccessRuleProtection($true, $false)  # Desabilita herança
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    $env:USERNAME, 
    "FullControl", 
    "Allow"
)
$acl.AddAccessRule($rule)
Set-Acl $path $acl
```

**O que esse script faz:**
1. Obtém a ACL atual
2. Desabilita herança de permissões do diretório pai
3. Adiciona uma regra: apenas seu usuário tem controle total
4. Aplica a nova ACL

---

## 🚀 Parte 9: O Gateway — Coração do Sistema

### O que o Gateway faz?

1. **Abre um WebSocket** na porta especificada
2. **Aguarda conexões** de clientes (CLI, apps, canais)
3. **Processa mensagens** entrantes
4. **Roteia para o modelo** configurado
5. **Retorna respostas** aos clientes

### Iniciando o Gateway

#### 🍎 macOS / 🐧 Linux

```bash
# Foreground (ver logs)
openclaw gateway --port 18789 --verbose

# Background (terminal livre)
openclaw gateway --port 18789 &

# Com nohup (sobrevive ao fechar terminal)
nohup openclaw gateway --port 18789 > gateway.log 2>&1 &
```

#### 🪟 Windows

```powershell
# Foreground
openclaw gateway --port 18789 --verbose

# Background (nova instância oculta)
Start-Process -WindowStyle Hidden -FilePath "openclaw" -ArgumentList "gateway", "--port", "18789"

# Como job do PowerShell
Start-Job { openclaw gateway --port 18789 }
```

### O que significa cada saída do Gateway

```
21:35:04 [canvas] host mounted at http://127.0.0.1:18789/__openclaw__/canvas/
```
↳ Interface web para visualizar artefatos. Funciona igual em todos os sistemas.

```
21:35:04 [gateway] listening on ws://127.0.0.1:18789 (PID 22290)
```
↳ WebSocket ativo! 

**PID (Process ID)** é útil para:
```bash
# macOS/Linux
kill 22290

# Windows
Stop-Process -Id 22290
```

---

## 🧪 Parte 10: Diagnósticos e Troubleshooting

### Comandos universais (todos os sistemas)

```bash
openclaw health         # Verificações básicas
openclaw status         # Status detalhado
openclaw doctor         # Diagnóstico profundo
openclaw logs --follow  # Logs em tempo real
```

### Problemas específicos por sistema

#### 🍎 macOS: Ollama não inicia

```bash
# Verificar logs do launchd
cat ~/Library/Logs/Homebrew/ollama*.log

# Reiniciar
brew services restart ollama
```

#### 🐧 Linux: Permissão negada

```bash
# Verificar se seu usuário está no grupo correto
groups

# Verificar logs do systemd
journalctl -u ollama -f

# Problemas com GPU NVIDIA
nvidia-smi  # GPU detectada?
```

#### 🪟 Windows: Firewall bloqueando

```powershell
# Verificar se porta está aberta
Test-NetConnection -ComputerName localhost -Port 18789

# Adicionar regra de firewall (PowerShell Admin)
New-NetFirewallRule -DisplayName "OpenClaw Gateway" -Direction Inbound -Port 18789 -Protocol TCP -Action Allow
```

---

## 🧠 Parte 11: Conceitos Avançados

### Tokens vs Palavras

Modelos não processam palavras, processam **tokens**.

```
"Olá, como você está?" 
→ ["Ol", "á", ",", " como", " você", " está", "?"]
→ 7 tokens
```

**Regra aproximada**: 1 token ≈ 0.75 palavras em inglês, menos em português.

### Aceleração por Hardware

| Hardware | macOS | Windows | Linux |
|----------|-------|---------|-------|
| **Apple Silicon (M1-M4)** | ✅ Metal | ❌ N/A | ❌ N/A |
| **NVIDIA GPU** | ❌ Sem suporte | ✅ CUDA | ✅ CUDA |
| **AMD GPU** | ✅ Metal | ⚠️ ROCm limitado | ✅ ROCm |
| **Intel GPU** | ❌ | ⚠️ Experimental | ⚠️ Experimental |

**Como verificar se GPU está sendo usada:**

```bash
# Durante inferência, observe o uso de GPU

# macOS (Activity Monitor ou)
sudo powermetrics --samplers gpu_power

# Windows
nvidia-smi  # ou Task Manager > Performance > GPU

# Linux
nvidia-smi -l 1  # atualiza a cada segundo
# ou
watch -n 1 rocm-smi  # para AMD
```

---

## 📊 Resumo: Diferenças Entre Sistemas

| Aspecto | 🍎 macOS | 🪟 Windows | 🐧 Linux |
|---------|----------|------------|----------|
| **Shell padrão** | zsh | PowerShell | bash |
| **Gerenciador de pacotes** | Homebrew | winget/choco | apt/dnf/pacman |
| **Caminho home** | `/Users/nome` | `C:\Users\nome` | `/home/nome` |
| **Variável home** | `$HOME` ou `~` | `%USERPROFILE%` | `$HOME` ou `~` |
| **Separador de path** | `:` | `;` | `:` |
| **Separador de diretório** | `/` | `\` | `/` |
| **Sistema de serviços** | launchd | Windows Services | systemd |
| **Permissões de arquivo** | Unix (chmod) | ACLs | Unix (chmod) |
| **GPU padrão** | Metal | CUDA/DirectML | CUDA/ROCm |

---

## 🎯 Exercícios de Fixação

### Exercício 1: Entendendo seu sistema

```bash
# Descubra onde o Ollama guarda modelos no seu sistema
# macOS/Linux
ls -la ~/.ollama/models/

# Windows
dir $env:USERPROFILE\.ollama\models\
```

### Exercício 2: Testando a API diretamente

```bash
# macOS/Linux
curl http://127.0.0.1:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "deepseek-r1:14b", "messages": [{"role": "user", "content": "Olá!"}]}'

# Windows (PowerShell)
$body = @{
    model = "deepseek-r1:14b"
    messages = @(@{role = "user"; content = "Olá!"})
} | ConvertTo-Json
Invoke-RestMethod -Uri "http://127.0.0.1:11434/v1/chat/completions" -Method Post -Body $body -ContentType "application/json"
```

### Exercício 3: Monitorando recursos

```bash
# Observe RAM e CPU durante inferência

# macOS
top -pid $(pgrep ollama)

# Linux
htop -p $(pgrep ollama)

# Windows (PowerShell)
while ($true) { Get-Process ollama | Select-Object CPU, WorkingSet; Start-Sleep 1 }
```

---

## 🏆 O Que Você Aprendeu

✅ Arquitetura de sistemas de IA local (igual em todos os SO)  
✅ Diferenças entre gerenciadores de pacotes  
✅ Como shells diferentes funcionam (bash vs PowerShell)  
✅ Sistemas de serviços (launchd, systemd, Windows Services)  
✅ Permissões de arquivo (Unix vs ACLs)  
✅ Caminhos e variáveis de ambiente por sistema  
✅ Aceleração de hardware por plataforma  
✅ Troubleshooting específico por SO  

---

**Tutorial criado em**: 4 de Fevereiro de 2026  
**Plataformas**: macOS, Windows, Linux  
**Filosofia**: Ensinar o "porquê" em qualquer sistema
