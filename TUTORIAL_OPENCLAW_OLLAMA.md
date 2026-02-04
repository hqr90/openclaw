# 🦞 Tutorial Completo: OpenClaw com Ollama e DeepSeek Local

Este tutorial documenta o processo completo de instalação do **OpenClaw** com **Ollama** para rodar modelos de IA localmente, sem depender de APIs pagas.

> **Plataformas suportadas**: macOS, Windows e Linux

---

## 📋 Índice

1. [Visão Geral](#visão-geral)
2. [Requisitos do Sistema](#requisitos-do-sistema)
3. [Instalação do Node.js](#instalação-do-nodejs)
4. [Instalação do OpenClaw CLI](#instalação-do-openclaw-cli)
5. [Instalação do Ollama](#instalação-do-ollama)
6. [Download do Modelo DeepSeek](#download-do-modelo-deepseek)
7. [Configuração do OpenClaw para Ollama](#configuração-do-openclaw-para-ollama)
8. [Iniciando o Gateway](#iniciando-o-gateway)
9. [Verificação e Troubleshooting](#verificação-e-troubleshooting)
10. [Segurança](#segurança)
11. [Comandos Úteis](#comandos-úteis)
12. [Próximos Passos](#próximos-passos)

---

## Visão Geral

**OpenClaw** é um framework de agentes de IA que permite conectar assistentes de IA a diferentes canais de comunicação (WhatsApp, Telegram, Discord, etc.). 

**Ollama** é uma ferramenta para rodar LLMs (Large Language Models) localmente na sua máquina, sem precisar de APIs pagas.

### Por que usar modelos locais?

- ✅ **Gratuito** - Sem custos de API
- ✅ **Privacidade** - Seus dados não saem da sua máquina
- ✅ **Offline** - Funciona sem internet (após download do modelo)
- ✅ **Velocidade** - Sem latência de rede

---

## Requisitos do Sistema

### Hardware Mínimo

| Componente | Requisito |
|------------|-----------|
| **RAM** | 16GB (para modelos de 7-14B parâmetros) |
| **CPU** | Processador moderno (x86_64 ou ARM64) |
| **Storage** | 20GB+ livre (modelos ocupam 4-15GB+ cada) |
| **GPU** | Opcional, mas acelera inferência |

### Sistemas Operacionais Suportados

| OS | Versão Mínima |
|----|---------------|
| **macOS** | 12.0 (Monterey) ou superior |
| **Windows** | 10 (build 1903+) ou 11 |
| **Linux** | Ubuntu 20.04+, Debian 11+, Fedora 36+ |

### Software Necessário

- **Node.js** >= 22
- **Git** (geralmente já instalado)

---

## Instalação do Node.js

### 🍎 macOS

**Opção 1: Homebrew (recomendado)**
```bash
# Instalar Homebrew (se não tiver)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Instalar Node.js 22
brew install node@22
```

**Opção 2: Instalador oficial**
1. Baixe em: https://nodejs.org/
2. Execute o instalador `.pkg`

---

### 🪟 Windows

**Opção 1: Instalador oficial (recomendado)**
1. Baixe o instalador LTS em: https://nodejs.org/
2. Execute o `.msi` e siga o assistente
3. Marque a opção "Automatically install tools..."

**Opção 2: winget**
```powershell
winget install OpenJS.NodeJS.LTS
```

**Opção 3: Chocolatey**
```powershell
choco install nodejs-lts
```

---

### 🐧 Linux

**Ubuntu/Debian**
```bash
# Adicionar repositório NodeSource
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -

# Instalar Node.js
sudo apt-get install -y nodejs
```

**Fedora/RHEL**
```bash
# Adicionar repositório NodeSource
curl -fsSL https://rpm.nodesource.com/setup_22.x | sudo bash -

# Instalar Node.js
sudo dnf install nodejs -y
```

**Arch Linux**
```bash
sudo pacman -S nodejs npm
```

---

### Verificar instalação

```bash
node --version
# Deve retornar v22.x.x ou superior
```

---

## Instalação do OpenClaw CLI

### 🍎 macOS / 🐧 Linux

**Método 1: Script de instalação (recomendado)**
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**Método 2: Via npm**
```bash
npm install -g openclaw@latest
```

---

### 🪟 Windows

**Via npm (PowerShell como Administrador)**
```powershell
npm install -g openclaw@latest
```

**Via npx (sem instalar globalmente)**
```powershell
npx openclaw@latest --version
```

---

### Verificar instalação (todos os sistemas)

```bash
openclaw --version
# Exemplo: 🦞 OpenClaw 2026.2.2-3 (9c5941b)
```

---

## Instalação do Ollama

### 🍎 macOS

**Opção 1: Homebrew**
```bash
# Instalar
brew install ollama

# Iniciar como serviço (roda em background)
brew services start ollama
```

**Opção 2: Download direto**
1. Baixe em: https://ollama.ai/download/mac
2. Arraste para a pasta Aplicativos
3. Execute o Ollama.app

---

### 🪟 Windows

**Instalador oficial**
1. Baixe em: https://ollama.ai/download/windows
2. Execute o instalador `.exe`
3. O Ollama será iniciado automaticamente na bandeja do sistema

**Via winget**
```powershell
winget install Ollama.Ollama
```

> **Nota**: No Windows, o Ollama roda como um serviço do sistema automaticamente.

---

### 🐧 Linux

**Script de instalação (todas as distros)**
```bash
curl -fsSL https://ollama.ai/install.sh | sh
```

**Iniciar o serviço**
```bash
# Iniciar manualmente
ollama serve &

# Ou como serviço systemd
sudo systemctl enable ollama
sudo systemctl start ollama
```

---

### Verificar instalação (todos os sistemas)

```bash
ollama --version
# Exemplo: ollama version 0.15.4
```

**Verificar se o serviço está rodando:**

```bash
# macOS/Linux
curl http://127.0.0.1:11434/v1/models

# Windows (PowerShell)
Invoke-WebRequest -Uri http://127.0.0.1:11434/v1/models | Select-Object -Expand Content
```

---

## Download do Modelo DeepSeek

O **DeepSeek R1** é um modelo de IA de código aberto com excelente capacidade de raciocínio.

### Modelos Disponíveis

| Modelo | Tamanho | RAM Necessária | Uso |
|--------|---------|----------------|-----|
| `deepseek-r1:7b` | ~4.7GB | 8GB | Máquinas mais simples |
| `deepseek-r1:14b` | ~9GB | 16GB | **Recomendado** - Bom equilíbrio |
| `deepseek-r1:32b` | ~20GB | 32GB | Melhor qualidade |

### Baixar o modelo (todos os sistemas)

```bash
# Modelo recomendado para 16GB de RAM
ollama pull deepseek-r1:14b
```

> ⏱️ O download pode levar alguns minutos (~9GB).

### Verificar modelos instalados

```bash
ollama list
# NAME               ID              SIZE      MODIFIED
# deepseek-r1:14b    c333b7232bdb    9.0 GB    10 minutes ago
```

### Testar o modelo

```bash
ollama run deepseek-r1:14b "Olá, como você está?"
```

---

## Configuração do OpenClaw para Ollama

### Localização do arquivo de configuração

| Sistema | Caminho |
|---------|---------|
| **macOS** | `~/.openclaw/openclaw.json` |
| **Linux** | `~/.openclaw/openclaw.json` |
| **Windows** | `%USERPROFILE%\.openclaw\openclaw.json` |

### Criar diretório de workspace

**macOS/Linux:**
```bash
mkdir -p ~/.openclaw/workspace
```

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.openclaw\workspace"
```

### Criar arquivo de configuração

Crie/edite o arquivo `openclaw.json` no diretório acima:

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/deepseek-r1:14b"
      },
      "models": {
        "ollama/deepseek-r1:14b": {
          "alias": "DeepSeek"
        }
      },
      "workspace": "~/.openclaw/workspace"
    }
  },
  "models": {
    "mode": "merge",
    "providers": {
      "ollama": {
        "baseUrl": "http://127.0.0.1:11434/v1",
        "apiKey": "ollama",
        "api": "openai-completions",
        "models": [
          {
            "id": "deepseek-r1:14b",
            "name": "DeepSeek R1 14B",
            "reasoning": true,
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 128000,
            "maxTokens": 32000
          }
        ]
      }
    }
  },
  "gateway": {
    "port": 18789,
    "mode": "local",
    "auth": {
      "token": "SEU_TOKEN_AQUI"
    }
  }
}
```

### Gerar um token seguro

**macOS/Linux:**
```bash
openssl rand -hex 32
```

**Windows (PowerShell):**
```powershell
-join ((1..32) | ForEach-Object { "{0:x2}" -f (Get-Random -Maximum 256) })
```

Substitua `"SEU_TOKEN_AQUI"` pelo token gerado.

### Nota para Windows

No Windows, substitua `~/.openclaw/workspace` por:
```json
"workspace": "%USERPROFILE%\\.openclaw\\workspace"
```

Ou use o caminho completo:
```json
"workspace": "C:\\Users\\SeuUsuario\\.openclaw\\workspace"
```

---

## Iniciando o Gateway

### Verificar configuração (todos os sistemas)

```bash
openclaw models list
# Model                           Input   Ctx    Local Auth  Tags
# ollama/deepseek-r1:14b          text    125k   yes   yes   default,configured,alias:DeepSeek
```

### Iniciar o gateway

**macOS/Linux:**
```bash
# Em foreground (ver logs)
openclaw gateway --port 18789 --verbose

# Ou em background
openclaw gateway --port 18789 &
```

**Windows (PowerShell):**
```powershell
# Em foreground
openclaw gateway --port 18789 --verbose

# Em background (nova janela)
Start-Process -NoNewWindow openclaw -ArgumentList "gateway", "--port", "18789"
```

### Saída esperada

```
🦞 OpenClaw 2026.2.2-3 (9c5941b)

21:35:04 [canvas] host mounted at http://127.0.0.1:18789/__openclaw__/canvas/
21:35:04 [heartbeat] started
21:35:04 [gateway] agent model: ollama/deepseek-r1:14b
21:35:04 [gateway] listening on ws://127.0.0.1:18789 (PID 22290)
```

### Acessar Dashboard

Abra no navegador: **http://127.0.0.1:18789/**

---

## Verificação e Troubleshooting

### Verificar status geral

```bash
openclaw status
```

### Verificar saúde

```bash
openclaw health
```

### Problemas Comuns

#### 1. "gateway closed (1006 abnormal closure)"

**Causa**: Gateway não está rodando.

**Solução**: Inicie o gateway com `openclaw gateway --port 18789 --verbose`

#### 2. "Gateway start blocked: set gateway.mode=local"

**Causa**: Falta configurar o modo do gateway.

**Solução**: Adicione `"mode": "local"` dentro de `"gateway"` no `openclaw.json`.

#### 3. "no token is configured"

**Causa**: Falta token de autenticação.

**Solução**: Gere um token e adicione em `gateway.auth.token`.

#### 4. Ollama não responde

**macOS:**
```bash
brew services restart ollama
```

**Linux:**
```bash
sudo systemctl restart ollama
# ou
pkill ollama && ollama serve &
```

**Windows:**
- Reinicie o Ollama pela bandeja do sistema
- Ou reinicie o serviço: `Restart-Service Ollama`

---

## Segurança

### Permissões de arquivo

**macOS/Linux:**
```bash
chmod 600 ~/.openclaw/openclaw.json
chmod 700 ~/.openclaw
```

**Windows (PowerShell como Administrador):**
```powershell
$path = "$env:USERPROFILE\.openclaw\openclaw.json"
$acl = Get-Acl $path
$acl.SetAccessRuleProtection($true, $false)
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule($env:USERNAME, "FullControl", "Allow")
$acl.AddAccessRule($rule)
Set-Acl $path $acl
```

### Auditoria de segurança (todos os sistemas)

```bash
openclaw security audit
openclaw security audit --deep
openclaw security audit --fix
```

---

## Comandos Úteis

### Gerenciamento de Modelos

```bash
openclaw models list
openclaw models status
openclaw models set ollama/deepseek-r1:14b
```

### Gerenciamento do Gateway

```bash
openclaw gateway status
openclaw gateway --port 18789 --verbose
openclaw logs --follow
```

### Manutenção

```bash
openclaw health
openclaw doctor
openclaw doctor --fix
```

### Ollama

```bash
ollama list           # Listar modelos
ollama pull <modelo>  # Baixar modelo
ollama rm <modelo>    # Remover modelo
ollama run <modelo>   # Testar modelo
```

---

## Próximos Passos

### 1. Conectar canais de comunicação

```bash
openclaw channels login
openclaw status --deep
```

### 2. Instalar como serviço do sistema

**macOS:**
```bash
openclaw onboard --install-daemon
```

**Linux (systemd):**
```bash
openclaw onboard --install-daemon
# Ou manualmente com systemctl
```

**Windows:**
```powershell
# Usar Task Scheduler para iniciar automaticamente
```

### 3. Explorar outros modelos

| Modelo | Comando | Uso |
|--------|---------|-----|
| Llama 3.2 | `ollama pull llama3.2` | Chat geral |
| Mistral | `ollama pull mistral` | Eficiente e rápido |
| Qwen 2.5 | `ollama pull qwen2.5` | Multilingue |
| Phi-3 | `ollama pull phi3` | Leve e rápido |

---

## Resumo dos Arquivos Importantes

| Arquivo | macOS/Linux | Windows |
|---------|-------------|---------|
| Config | `~/.openclaw/openclaw.json` | `%USERPROFILE%\.openclaw\openclaw.json` |
| Workspace | `~/.openclaw/workspace/` | `%USERPROFILE%\.openclaw\workspace\` |
| Modelos Ollama | `~/.ollama/models/` | `%USERPROFILE%\.ollama\models\` |
| Logs | `/tmp/openclaw/` | `%TEMP%\openclaw\` |

---

## Recursos Adicionais

- 📚 **Documentação OpenClaw**: https://docs.openclaw.ai
- 🦙 **Modelos Ollama**: https://ollama.ai/library
- 🔧 **Configuração avançada**: https://docs.openclaw.ai/gateway/configuration
- 🔒 **Segurança**: https://docs.openclaw.ai/gateway/security

---

**Tutorial criado em**: 4 de Fevereiro de 2026  
**Versão OpenClaw**: 2026.2.2-3  
**Plataformas**: macOS, Windows, Linux
