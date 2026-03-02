[⬅️ Voltar ao Índice](../README.md) | [Anterior: Instalação](01-instalacao.md) | [Próximo: Conceitos Fundamentais ➡️](03-conceitos-fundamentais.md)

---

# ⚙️ Configuração Inicial

Agora que temos tudo instalado, vamos **criar seu primeiro projeto Angular** e entender a estrutura de pastas. É como montar os alicerces da sua casa — a parte mais importante!

---

## 🆕 Criando um Novo Projeto

Abra o terminal e digite:

```bash
ng new meu-primeiro-app
```

O Angular CLI vai fazer algumas perguntas:

```
? Which stylesheet format would you like to use? (Use arrow keys)
❯ CSS
  SCSS
  Sass
  Less

? Do you want to enable Server-Side Rendering (SSR) and Static Site Generation (SSG/Prerendering)?
  (y/N)
```

### 🎯 Respostas recomendadas para iniciantes

| Pergunta | Resposta Recomendada | Por quê |
|----------|---------------------|---------|
| Stylesheet format | **CSS** | Mais simples para começar |
| SSR/SSG | **N** (não) | Não precisamos disso agora |

Após responder, o CLI vai criar o projeto e instalar as dependências. Aguarde uns minutos.

```
CREATE meu-primeiro-app/angular.json
CREATE meu-primeiro-app/package.json
CREATE meu-primeiro-app/tsconfig.json
CREATE meu-primeiro-app/src/app/app.component.ts
...
✔ Packages installed successfully.
```

---

## 📁 Estrutura do Projeto

Entre na pasta do projeto e veja o que foi criado:

```bash
cd meu-primeiro-app
```

```
meu-primeiro-app/
├── 📁 node_modules/       ← Bibliotecas instaladas (NÃO MEXA aqui)
├── 📁 src/                ← SEU CÓDIGO FICA AQUI
│   ├── 📁 app/            ← Componentes e lógica da aplicação
│   │   ├── app.component.css       ← Estilos do componente principal
│   │   ├── app.component.html      ← Template (HTML) do componente principal
│   │   ├── app.component.spec.ts   ← Testes do componente
│   │   ├── app.component.ts        ← Lógica do componente principal
│   │   ├── app.config.ts           ← Configuração da aplicação
│   │   └── app.routes.ts           ← Definição das rotas (páginas)
│   ├── 📄 index.html      ← Página HTML principal
│   ├── 📄 main.ts         ← Ponto de entrada da aplicação
│   └── 📄 styles.css      ← Estilos globais
├── 📄 angular.json        ← Configurações do projeto Angular
├── 📄 package.json        ← Lista de dependências do projeto
├── 📄 tsconfig.json       ← Configurações do TypeScript
└── 📄 README.md           ← Documentação do projeto
```

### 🧭 Onde mexer? Guia rápido

| Pasta/Arquivo | Quando mexer | Frequência |
|--------------|-------------|------------|
| `src/app/` | Sempre — é aqui que você programa | 🔥 Todo dia |
| `src/styles.css` | Para estilos globais (fontes, cores gerais) | 🟡 De vez em quando |
| `src/index.html` | Raramente — só para meta tags, fontes externas | 🟢 Raramente |
| `angular.json` | Configurações avançadas do build | 🟢 Raramente |
| `package.json` | Ao adicionar novas bibliotecas | 🟡 De vez em quando |
| `node_modules/` | **NUNCA** — é gerenciado automaticamente | 🔴 Nunca |

---

## 🚀 Rodando o Projeto

```bash
ng serve
```

Saída esperada:

```
✔ Compiled successfully.
** Angular Live Development Server is listening on localhost:4200 **
```

Abra o navegador em: **http://localhost:4200**

> 💡 **Dica:** O servidor tem **hot reload** — quando você salva um arquivo, a página atualiza automaticamente!

### Opções úteis do `ng serve`

```bash
# Abrir o navegador automaticamente
ng serve --open
# ou
ng serve -o

# Usar outra porta (se 4200 estiver ocupada)
ng serve --port 3000

# Permitir acesso de outros dispositivos na rede
ng serve --host 0.0.0.0
```

---

## 🎨 Modificando Seu Primeiro Componente

Vamos fazer uma mudança para ver o hot reload em ação!

### 1. Abra o arquivo `src/app/app.component.ts`

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'meu-primeiro-app';
}
```

### 2. Mude o título

```typescript
export class AppComponent {
  title = 'Minha Primeira App Angular! 🎉';
}
```

### 3. Abra o arquivo `src/app/app.component.html`

Apague **todo o conteúdo** e substitua por:

```html
<div style="text-align: center; padding: 50px;">
  <h1>🅰️ {{ title }}</h1>
  <p>Se você está vendo isso, o Angular está funcionando! 🚀</p>

  <div style="margin-top: 30px;">
    <h2>Minha lista de tarefas:</h2>
    <ul style="list-style: none; padding: 0;">
      <li>✅ Instalar Node.js</li>
      <li>✅ Instalar Angular CLI</li>
      <li>✅ Criar meu primeiro projeto</li>
      <li>⬜ Aprender componentes</li>
      <li>⬜ Dominar o Angular!</li>
    </ul>
  </div>
</div>
```

### 4. Salve e veja no navegador

O navegador vai atualizar automaticamente! 🎉

---

## 📂 Gerando Novos Componentes

O Angular CLI pode gerar código pra você. É como ter um assistente que cria a estrutura dos arquivos automaticamente:

```bash
# Gerar um novo componente
ng generate component nome-do-componente

# Forma abreviada
ng g c nome-do-componente
```

**Exemplo prático:**

```bash
ng g c header
```

Isso cria:

```
src/app/header/
├── header.component.css        ← Estilos
├── header.component.html       ← Template
├── header.component.spec.ts    ← Testes
└── header.component.ts         ← Lógica
```

### 🎯 Cola Rápida — Comandos `ng generate`

| Comando | Abreviação | O que gera |
|---------|-----------|------------|
| `ng generate component nome` | `ng g c nome` | Componente completo |
| `ng generate service nome` | `ng g s nome` | Serviço |
| `ng generate pipe nome` | `ng g p nome` | Pipe (transformador de dados) |
| `ng generate directive nome` | `ng g d nome` | Diretiva |
| `ng generate guard nome` | `ng g guard nome` | Guard (proteção de rotas) |
| `ng generate interface nome` | `ng g i nome` | Interface TypeScript |
| `ng generate enum nome` | `ng g e nome` | Enum TypeScript |

---

## 🔧 Configurações Importantes

### `angular.json` — O que saber

Este arquivo controla como o projeto é construído. As configurações mais relevantes:

```json
{
  "projects": {
    "meu-primeiro-app": {
      "architect": {
        "build": {
          "options": {
            "outputPath": "dist/meu-primeiro-app",  // Onde o build vai parar
            "styles": [
              "src/styles.css"  // Estilos globais
            ],
            "scripts": []  // Scripts globais
          }
        }
      }
    }
  }
}
```

### `tsconfig.json` — Configuração do TypeScript

Para iniciantes, a configuração padrão é perfeita. Não precisa mudar nada!

### `package.json` — Dependências

Lista todas as bibliotecas do projeto. Comandos úteis:

```bash
# Instalar uma nova biblioteca
npm install nome-da-biblioteca

# Instalar dependência de desenvolvimento
npm install --save-dev nome-da-biblioteca

# Reinstalar todas as dependências (se apagar node_modules)
npm install
```

---

## 🏗️ Fazendo o Build (Versão de Produção)

Quando estiver pronto para publicar sua aplicação:

```bash
ng build
```

Isso cria a pasta `dist/` com os arquivos otimizados para colocar em um servidor web.

```
dist/meu-primeiro-app/
├── browser/
│   ├── index.html
│   ├── main.js        ← Seu código compilado e minificado
│   ├── polyfills.js
│   └── styles.css
```

---

## ❌ Errado vs ✅ Certo

| ❌ Errado | ✅ Certo | Por quê |
|-----------|---------|---------|
| Editar arquivos em `node_modules/` | Instalar pacotes via `npm install` | `node_modules` é gerado automaticamente |
| Ignorar erros no terminal | Ler e entender as mensagens de erro | O Angular dá dicas ótimas nos erros |
| Criar componentes manualmente | Usar `ng generate component` | O CLI garante a estrutura correta |
| Commitar a pasta `node_modules/` | Adicionar ao `.gitignore` | Essa pasta é enorme e desnecessária no git |

---

## 📊 Cola Rápida

```
┌─────────────────────────────────────────────────┐
│        COMANDOS ESSENCIAIS DO DIA A DIA         │
│                                                 │
│  ng new nome        → Criar projeto             │
│  ng serve -o        → Rodar e abrir navegador   │
│  ng g c nome        → Gerar componente          │
│  ng g s nome        → Gerar serviço             │
│  ng build           → Build de produção         │
│  ng test            → Rodar testes              │
│  npm install pacote → Instalar biblioteca       │
└─────────────────────────────────────────────────┘
```

---

[⬅️ Voltar ao Índice](../README.md) | [Anterior: Instalação](01-instalacao.md) | [Próximo: Conceitos Fundamentais ➡️](03-conceitos-fundamentais.md)
