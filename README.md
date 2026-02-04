# OpenClaw + Ollama Local AI Setup

🦞 **Tutoriais completos para rodar IA local com OpenClaw e Ollama**

---

## 📚 Documentação

| Arquivo | Descrição |
|---------|-----------|
| [TUTORIAL_OPENCLAW_OLLAMA.md](./TUTORIAL_OPENCLAW_OLLAMA.md) | Tutorial prático passo-a-passo |
| [TUTORIAL_PROFESSOR_OPENCLAW.md](./TUTORIAL_PROFESSOR_OPENCLAW.md) | Tutorial educacional com explicações detalhadas |

## 🖥️ Plataformas Suportadas

- 🍎 **macOS** (Intel e Apple Silicon)
- 🪟 **Windows** 10/11
- 🐧 **Linux** (Ubuntu, Debian, Fedora, Arch)

## 🚀 Quick Start

### 1. Instalar Ollama
```bash
# macOS
brew install ollama && brew services start ollama

# Linux
curl -fsSL https://ollama.ai/install.sh | sh

# Windows - baixe em https://ollama.ai/download
```

### 2. Baixar modelo DeepSeek
```bash
ollama pull deepseek-r1:14b
```

### 3. Instalar OpenClaw
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
# ou
npm install -g openclaw@latest
```

### 4. Configurar
```bash
# Copiar exemplo de configuração
mkdir -p ~/.openclaw
cp config/openclaw.example.json ~/.openclaw/openclaw.json

# Gerar token (macOS/Linux)
TOKEN=$(openssl rand -hex 32)
sed -i '' "s/YOUR_TOKEN_HERE/$TOKEN/" ~/.openclaw/openclaw.json
```

### 5. Iniciar
```bash
openclaw gateway --port 18789 --verbose
```

## 📁 Estrutura do Projeto

```
openclaw/
├── README.md                         # Este arquivo
├── TUTORIAL_OPENCLAW_OLLAMA.md       # Tutorial prático
├── TUTORIAL_PROFESSOR_OPENCLAW.md    # Tutorial educacional
└── config/                           # Configurações de exemplo
    ├── openclaw.example.json         # Config principal (template)
    ├── agents/                       # Configuração de agentes
    ├── canvas/                       # Interface web
    └── cron/                         # Jobs agendados
```

## 🔧 Configuração

Copie `config/openclaw.example.json` para `~/.openclaw/openclaw.json` e substitua `YOUR_TOKEN_HERE` por um token gerado:

```bash
# macOS/Linux
openssl rand -hex 32

# Windows PowerShell
-join ((1..32) | ForEach-Object { "{0:x2}" -f (Get-Random -Maximum 256) })
```

## ✅ Verificação

```bash
# Verificar modelos
openclaw models list

# Verificar status
openclaw status

# Verificar saúde
openclaw health
```

## 🔒 Segurança

```bash
# Proteger arquivos de configuração (macOS/Linux)
chmod 600 ~/.openclaw/openclaw.json
chmod 700 ~/.openclaw
```

## 📖 Recursos

- [OpenClaw Docs](https://docs.openclaw.ai)
- [Ollama Models](https://ollama.ai/library)
- [DeepSeek](https://www.deepseek.com)

---

**Criado em**: Fevereiro 2026  
**Versão OpenClaw**: 2026.2.2-3  
**Modelo**: DeepSeek R1 14B
