<p align="center">
  <img src="https://angular.io/assets/images/logos/angular/angular.svg" alt="Angular Logo" width="120">
</p>

<h1 align="center">🅰️ Guia Angular para Iniciantes</h1>

<p align="center">
  <strong>Aprenda Angular do zero, em português, com exemplos práticos e linguagem acessível.</strong>
</p>

<p align="center">
  <a href="#-início-rápido">Início Rápido</a> •
  <a href="#-índice-dos-guias">Guias</a> •
  <a href="#-como-contribuir">Contribuir</a>
</p>

---

## 🤔 O que é Angular?

Imagine que você está construindo uma **casa**. Você poderia fazer tudo do zero — fabricar os tijolos, cortar a madeira, fundir o vidro. Ou poderia usar **materiais prontos** e seguir uma **planta arquitetônica** que organiza tudo pra você.

**Angular é essa planta arquitetônica para sites e aplicações web.**

Ele é um **framework** (kit de ferramentas completo) criado e mantido pelo **Google** que te ajuda a construir aplicações web modernas de forma organizada, rápida e profissional.

### 🏠 Analogia da Casa

| Conceito | Na Casa | No Angular |
|----------|---------|------------|
| **Estrutura** | Paredes, teto, fundação | Componentes, módulos |
| **Aparência** | Pintura, decoração | Templates HTML + CSS |
| **Funcionamento** | Elétrica, hidráulica | TypeScript (lógica) |
| **Planta** | Planta baixa | Arquitetura do projeto |
| **Cômodos** | Sala, quarto, cozinha | Páginas/rotas da aplicação |

### 🌟 Por que aprender Angular?

- ✅ Usado por empresas gigantes (Google, Microsoft, Samsung, Forbes)
- ✅ Mercado de trabalho aquecido no Brasil e no mundo
- ✅ Framework completo — não precisa ficar juntando várias bibliotecas
- ✅ Padrões bem definidos — facilita trabalhar em equipe
- ✅ Comunidade enorme e documentação excelente

---

## 🏗️ Como o Angular Funciona (Visão Geral)

```
┌─────────────────────────────────────────────────────────────┐
│                    SUA APLICAÇÃO ANGULAR                     │
│                                                             │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│  │  Compo-  │    │  Compo-  │    │  Compo-  │              │
│  │  nente A │    │  nente B │    │  nente C │  ← Pedaços   │
│  │ (Header) │    │  (Lista) │    │ (Footer) │    da tela   │
│  └────┬─────┘    └────┬─────┘    └────┬─────┘              │
│       │               │               │                     │
│       └───────────┬───┘───────────────┘                     │
│                   │                                         │
│           ┌───────▼────────┐                                │
│           │   Roteador     │  ← Controla qual página        │
│           │   (Router)     │    aparece na tela              │
│           └───────┬────────┘                                │
│                   │                                         │
│           ┌───────▼────────┐                                │
│           │   Serviços     │  ← Buscam dados, fazem         │
│           │  (Services)    │    cálculos, conversam com API  │
│           └───────┬────────┘                                │
│                   │                                         │
│           ┌───────▼────────┐                                │
│           │   API / Back   │  ← Servidor externo            │
│           │    -end        │    (banco de dados, etc.)       │
│           └────────────────┘                                │
└─────────────────────────────────────────────────────────────┘
```

---

## 📚 Índice dos Guias

Siga a ordem abaixo para um aprendizado progressivo:

| # | Guia | Descrição |
|---|------|-----------|
| 1 | [🔧 Instalação](docs/01-instalacao.md) | Como instalar tudo que precisa (Windows, macOS, Linux) |
| 2 | [⚙️ Configuração Inicial](docs/02-configuracao-inicial.md) | Criando seu primeiro projeto Angular |
| 3 | [🧱 Conceitos Fundamentais](docs/03-conceitos-fundamentais.md) | Componentes, templates, módulos — a base de tudo |
| 4 | [💻 Sintaxe Básica](docs/04-sintaxe-basica.md) | Comandos e sintaxe do dia a dia com exemplos |
| 5 | [🔀 Tópicos Intermediários](docs/05-topicos-intermediarios.md) | Rotas, formulários, requisições HTTP |
| 6 | [🚀 Tópicos Avançados](docs/06-topicos-avancados.md) | Lazy loading, guards, interceptors e mais |
| 7 | [✨ Boas Práticas](docs/07-boas-praticas.md) | Convenções da indústria e código limpo |
| 8 | [🐛 Erros Comuns](docs/08-erros-comuns.md) | Problemas frequentes e como resolver |

---

## ⚡ Início Rápido

Quer ver o Angular rodando em **menos de 5 minutos**? Siga estes passos:

### Pré-requisitos

- [Node.js](https://nodejs.org/) (versão 18 ou superior)
- Um terminal (Prompt de Comando, PowerShell ou Terminal)

### Passo a passo

```bash
# 1. Instale o Angular CLI (ferramenta de linha de comando)
npm install -g @angular/cli

# 2. Crie um novo projeto
ng new meu-primeiro-app

# 3. Entre na pasta do projeto
cd meu-primeiro-app

# 4. Rode o servidor de desenvolvimento
ng serve
```

### 🎉 Pronto!

Abra o navegador em **http://localhost:4200** e veja sua aplicação rodando!

```
  Terminal                          Navegador
  ┌──────────────────┐             ┌──────────────────────┐
  │ $ ng serve       │             │                      │
  │                  │    ──►      │   Olá, Angular! 🅰️   │
  │ ✔ Compiled OK   │             │                      │
  └──────────────────┘             └──────────────────────┘
```

---

## 🗺️ Roteiro de Aprendizado

```
  🔧 Instalação
       │
       ▼
  ⚙️ Configuração Inicial
       │
       ▼
  🧱 Conceitos Fundamentais ◄── Você está aqui se nunca usou Angular
       │
       ▼
  💻 Sintaxe Básica
       │
       ▼
  🔀 Tópicos Intermediários ◄── Aqui você já consegue fazer projetos
       │
       ▼
  🚀 Tópicos Avançados
       │
       ▼
  ✨ Boas Práticas ◄── Aqui você escreve código profissional
       │
       ▼
  🐛 Erros Comuns ◄── Consulte sempre que travar em algo
```

---

## 🛠️ Tecnologias Utilizadas pelo Angular

| Tecnologia | Pra que serve |
|-----------|---------------|
| **TypeScript** | Linguagem principal (JavaScript com "superpoderes") |
| **HTML** | Estrutura das páginas |
| **CSS/SCSS** | Estilização (cores, layout, fontes) |
| **Node.js** | Ambiente de execução para ferramentas |
| **npm** | Gerenciador de pacotes (instalar bibliotecas) |
| **Angular CLI** | Ferramenta de linha de comando do Angular |

---

## 🤝 Como Contribuir

Encontrou um erro? Tem uma sugestão? Quer adicionar conteúdo?

1. Faça um **Fork** deste repositório
2. Crie uma **branch** para sua alteração: `git checkout -b minha-melhoria`
3. Faça suas alterações e **commit**: `git commit -m "Melhoria: descrição"`
4. Envie para o GitHub: `git push origin minha-melhoria`
5. Abra um **Pull Request**

### 📋 Diretrizes

- Mantenha a linguagem **simples e acessível**
- Use **exemplos práticos** sempre que possível
- Siga o padrão de formatação dos guias existentes
- Escreva em **Português BR**

---

## 📎 Recursos Externos

### Documentação Oficial
- 🌐 [Angular.dev (Novo site oficial)](https://angular.dev/)
- 📖 [Documentação Angular](https://angular.dev/overview)
- 🎓 [Tutorial Oficial](https://angular.dev/tutorials)

### Cursos Gratuitos
- 🎥 [Loiane Groner — Curso Angular (YouTube)](https://loiane.training/curso/angular)
- 🎥 [Cod3r — Angular 9 Essencial (YouTube)](https://www.youtube.com/playlist?list=PLdPPE0hUkt0rPyAkdhHIIquKbwrGUkvw3)

### Comunidades
- 💬 [Angular Brasil (Telegram)](https://t.me/AngularBrasil)
- 💬 [Stack Overflow em Português](https://pt.stackoverflow.com/questions/tagged/angular)
- 💬 [Reddit r/angular](https://www.reddit.com/r/Angular2/)

### Ferramentas Úteis
- 🔧 [VS Code](https://code.visualstudio.com/) — Editor de código recomendado
- 🔧 [Angular Language Service](https://marketplace.visualstudio.com/items?itemName=Angular.ng-template) — Extensão VS Code para Angular
- 🔧 [Angular DevTools](https://angular.dev/tools/devtools) — Extensão do navegador para debug

---

## 📝 Licença

Este projeto está sob a licença **MIT**. Sinta-se livre para usar, modificar e compartilhar!

---

<p align="center">
  Feito com ❤️ para a comunidade brasileira de desenvolvedores
</p>