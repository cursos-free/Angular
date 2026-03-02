[⬅️ Voltar ao Índice](../README.md) | [Próximo: Configuração Inicial ➡️](02-configuracao-inicial.md)

---

# 🔧 Instalação do Angular

Antes de começar a programar com Angular, precisamos instalar algumas ferramentas no computador. Pense nisso como **preparar a bancada antes de começar a cozinhar** — sem os utensílios certos, não dá pra fazer o prato!

---

## 📋 O que vamos instalar?

| Ferramenta | O que é | Por que precisa |
|-----------|---------|-----------------|
| **Node.js** | Ambiente que roda JavaScript fora do navegador | Angular precisa dele para funcionar |
| **npm** | Gerenciador de pacotes (vem junto com Node.js) | Para instalar bibliotecas e o Angular CLI |
| **Angular CLI** | Ferramenta de linha de comando do Angular | Para criar projetos, gerar código, rodar o app |
| **Editor de código** | Onde você vai escrever o código | VS Code é o mais recomendado |

---

## 🖥️ Instalando o Node.js

### 🪟 Windows

1. Acesse [nodejs.org](https://nodejs.org/)
2. Clique no botão **LTS** (versão estável recomendada)
3. Execute o instalador baixado (.msi)
4. Clique **Next** em tudo (as opções padrão estão OK)
5. Verifique a instalação abrindo o **Prompt de Comando** e digitando:

```bash
node --version
# Deve aparecer algo como: v20.x.x

npm --version
# Deve aparecer algo como: 10.x.x
```

> 💡 **Dica:** Se o comando não funcionar, feche e abra o terminal novamente.

### 🍎 macOS

**Opção 1 — Instalador (mais fácil):**

1. Acesse [nodejs.org](https://nodejs.org/)
2. Baixe a versão **LTS**
3. Execute o instalador (.pkg)
4. Siga as instruções na tela

**Opção 2 — Usando Homebrew (recomendado para devs):**

```bash
# Instale o Homebrew (se ainda não tiver)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Instale o Node.js
brew install node
```

Verifique:

```bash
node --version
npm --version
```

### 🐧 Linux (Ubuntu/Debian)

```bash
# Atualize os pacotes
sudo apt update

# Instale o Node.js via NodeSource (versão LTS)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Verifique
node --version
npm --version
```

**Para Fedora / RHEL:**

```bash
curl -fsSL https://rpm.nodesource.com/setup_20.x | sudo bash -
sudo dnf install -y nodejs
```

---

## 🅰️ Instalando o Angular CLI

Com o Node.js instalado, agora podemos instalar o **Angular CLI** (Command Line Interface). É a ferramenta que usamos para criar e gerenciar projetos Angular.

```bash
npm install -g @angular/cli
```

> 📝 O `-g` significa **global** — instala para todo o sistema, não só para um projeto.

Verifique a instalação:

```bash
ng version
```

Você deve ver algo assim:

```
     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/

Angular CLI: 17.x.x
Node: 20.x.x
Package Manager: npm 10.x.x
```

---

## 📝 Instalando o VS Code

O **Visual Studio Code** é o editor mais usado pela comunidade Angular.

### Download

1. Acesse [code.visualstudio.com](https://code.visualstudio.com/)
2. Baixe a versão para seu sistema operacional
3. Instale normalmente

### Extensões Recomendadas

Abra o VS Code e instale estas extensões (Ctrl+Shift+X):

| Extensão | Pra que serve |
|----------|---------------|
| **Angular Language Service** | Autocomplete e validação de templates Angular |
| **Angular Snippets** | Atalhos para gerar código Angular rapidamente |
| **Prettier** | Formata seu código automaticamente |
| **ESLint** | Detecta erros e problemas no código |
| **Material Icon Theme** | Ícones bonitos para os arquivos |
| **Thunder Client** | Testar APIs sem sair do VS Code |

---

## ✅ Checklist de Verificação

Rode todos estes comandos no terminal. Se todos retornarem versões, você está pronto!

```bash
# Node.js instalado?
node --version

# npm instalado?
npm --version

# Angular CLI instalado?
ng version

# VS Code instalado? (abre o VS Code)
code --version
```

---

## 🐛 Problemas Comuns na Instalação

### ❌ "ng não é reconhecido como comando"

**Causa:** O Angular CLI não foi instalado globalmente ou o PATH não está configurado.

**Solução:**
```bash
# Reinstale globalmente
npm install -g @angular/cli

# Se persistir no Windows, feche e abra o terminal
# Se persistir no Linux/Mac, use:
sudo npm install -g @angular/cli
```

### ❌ "Permission denied" (Linux/Mac)

**Causa:** Sem permissão para instalar pacotes globais.

**Solução:**
```bash
# Opção 1: Use sudo
sudo npm install -g @angular/cli

# Opção 2 (recomendada): Corrija as permissões do npm
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
npm install -g @angular/cli
```

### ❌ "Node.js version not supported"

**Causa:** Versão do Node.js muito antiga.

**Solução:** Atualize o Node.js para a versão LTS mais recente (18+).

### ❌ Instalação muito lenta

**Causa:** npm baixando pacotes devagar.

**Solução:**
```bash
# Troque o registro para um mais rápido
npm config set registry https://registry.npmmirror.com

# Ou use o yarn como alternativa
npm install -g yarn
```

---

## 📊 Cola Rápida

```
┌─────────────────────────────────────────────┐
│          INSTALAÇÃO EM 3 PASSOS             │
│                                             │
│  1. Instale Node.js: nodejs.org (LTS)       │
│  2. npm install -g @angular/cli             │
│  3. Instale VS Code: code.visualstudio.com  │
│                                             │
│  Verificar: node -v && npm -v && ng version │
└─────────────────────────────────────────────┘
```

---

[⬅️ Voltar ao Índice](../README.md) | [Próximo: Configuração Inicial ➡️](02-configuracao-inicial.md)
