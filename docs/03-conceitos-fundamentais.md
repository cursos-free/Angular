[⬅️ Voltar ao Índice](../README.md) | [Anterior: Configuração Inicial](02-configuracao-inicial.md) | [Próximo: Sintaxe Básica ➡️](04-sintaxe-basica.md)

---

# 🧱 Conceitos Fundamentais

Este é o guia mais importante! Aqui você vai entender os **pilares do Angular**. Pense nisso como aprender as regras de um jogo antes de jogar — sem isso, nada faz sentido.

---

## 🎯 Os 4 Pilares do Angular

```
┌───────────────────────────────────────────────────┐
│              PILARES DO ANGULAR                    │
│                                                   │
│  🧩 Componentes    → Pedaços da interface          │
│  📋 Templates      → O HTML de cada componente     │
│  ⚙️ Serviços       → Lógica compartilhada          │
│  🗺️ Rotas          → Navegação entre páginas       │
│                                                   │
└───────────────────────────────────────────────────┘
```

---

## 🧩 Componentes — Os Blocos de Lego

### O que são?

Um **componente** é um pedaço reutilizável da interface. Imagine que sua página é feita de **blocos de Lego** — cada bloco é um componente.

```
┌─────────────────────────────────────────────┐
│  ┌─────────────────────────────────────┐    │
│  │         🧩 HeaderComponent          │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ┌──────────────┐  ┌──────────────────┐    │
│  │ 🧩 Sidebar   │  │  🧩 Conteúdo     │    │
│  │  Component   │  │   Component      │    │
│  │              │  │                  │    │
│  │  • Menu 1    │  │  Olá, mundo!     │    │
│  │  • Menu 2    │  │                  │    │
│  │  • Menu 3    │  │  [🧩 Card]       │    │
│  │              │  │  [🧩 Card]       │    │
│  └──────────────┘  └──────────────────┘    │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │         🧩 FooterComponent          │    │
│  └─────────────────────────────────────┘    │
└─────────────────────────────────────────────┘
```

### Anatomia de um Componente

Todo componente Angular tem **3 partes**:

```
componente/
├── componente.component.ts     ← 🧠 Cérebro (lógica)
├── componente.component.html   ← 👁️ Aparência (template)
└── componente.component.css    ← 🎨 Estilo (visual)
```

É como uma **pessoa**: tem o **cérebro** (lógica), a **aparência** (template) e a **roupa** (estilo).

### Criando um Componente

```typescript
// header.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-header',          // Nome da "tag HTML" do componente
  standalone: true,                 // Componente independente
  templateUrl: './header.component.html',  // Onde está o HTML
  styleUrl: './header.component.css'       // Onde está o CSS
})
export class HeaderComponent {
  titulo: string = 'Meu Site Angular';    // Dados do componente
  logoUrl: string = 'assets/logo.png';
}
```

```html
<!-- header.component.html -->
<header>
  <img [src]="logoUrl" alt="Logo">
  <h1>{{ titulo }}</h1>
</header>
```

```css
/* header.component.css */
header {
  background-color: #1976d2;
  color: white;
  padding: 16px;
  display: flex;
  align-items: center;
  gap: 12px;
}
```

### Usando o Componente

Depois de criado, use a **tag personalizada** em qualquer template:

```html
<!-- app.component.html -->
<app-header></app-header>
<main>
  <p>Conteúdo da página</p>
</main>
```

> 💡 O `selector: 'app-header'` define o nome da tag `<app-header>`.

---

## 📋 Templates — O HTML Turbinado

Os templates Angular são **HTML com superpoderes**. Você pode usar expressões, condições e repetições diretamente no HTML.

### Interpolação `{{ }}`

Mostra valores de variáveis no HTML:

```typescript
// app.component.ts
export class AppComponent {
  nome: string = 'Maria';
  idade: number = 25;
}
```

```html
<!-- app.component.html -->
<p>Olá, {{ nome }}! Você tem {{ idade }} anos.</p>
<p>Ano que vem terá {{ idade + 1 }} anos.</p>
```

**Resultado na tela:**
```
Olá, Maria! Você tem 25 anos.
Ano que vem terá 26 anos.
```

### Property Binding `[propriedade]`

Liga uma propriedade do HTML a uma variável do componente:

```typescript
export class AppComponent {
  imagemUrl: string = 'foto.jpg';
  desabilitado: boolean = true;
}
```

```html
<!-- Liga a propriedade src à variável imagemUrl -->
<img [src]="imagemUrl" alt="Foto">

<!-- Liga a propriedade disabled à variável desabilitado -->
<button [disabled]="desabilitado">Clique</button>
```

### Event Binding `(evento)`

Conecta eventos do HTML a métodos do componente:

```typescript
export class AppComponent {
  contador: number = 0;

  incrementar(): void {
    this.contador++;
  }

  mostrarMensagem(texto: string): void {
    alert(texto);
  }
}
```

```html
<p>Contador: {{ contador }}</p>
<button (click)="incrementar()">+1</button>
<button (click)="mostrarMensagem('Olá!')">Dizer olá</button>
```

### Two-Way Binding `[(ngModel)]`

Sincronização em **duas vias** — o que o usuário digita atualiza a variável e vice-versa:

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [FormsModule],  // Precisa importar FormsModule!
  template: `
    <input [(ngModel)]="nome" placeholder="Digite seu nome">
    <p>Olá, {{ nome }}!</p>
  `
})
export class AppComponent {
  nome: string = '';
}
```

> ⚠️ Para usar `[(ngModel)]`, você precisa importar o `FormsModule`!

### 📊 Tabela Resumo de Bindings

| Tipo | Sintaxe | Direção | Exemplo |
|------|---------|---------|---------|
| Interpolação | `{{ valor }}` | Componente → Template | `{{ nome }}` |
| Property Binding | `[prop]="valor"` | Componente → Template | `[src]="url"` |
| Event Binding | `(evento)="metodo()"` | Template → Componente | `(click)="salvar()"` |
| Two-Way Binding | `[(ngModel)]="valor"` | Componente ↔ Template | `[(ngModel)]="nome"` |

---

## 🔄 Diretivas — Controlando o HTML

Diretivas são **instruções especiais** que modificam o comportamento do HTML.

### `@if` — Mostrar/Esconder (Angular 17+)

```typescript
export class AppComponent {
  logado: boolean = true;
  admin: boolean = false;
}
```

```html
@if (logado) {
  <p>Bem-vindo de volta!</p>
} @else {
  <p>Faça login para continuar.</p>
}

@if (admin) {
  <button>Painel Admin</button>
}
```

> 📝 Em versões anteriores do Angular (< 17), usava-se `*ngIf`:
> ```html
> <p *ngIf="logado">Bem-vindo de volta!</p>
> ```

### `@for` — Repetir Elementos (Angular 17+)

```typescript
export class AppComponent {
  frutas: string[] = ['🍎 Maçã', '🍌 Banana', '🍇 Uva', '🍊 Laranja'];
}
```

```html
<ul>
  @for (fruta of frutas; track $index) {
    <li>{{ fruta }}</li>
  } @empty {
    <li>Nenhuma fruta na lista.</li>
  }
</ul>
```

> 📝 Em versões anteriores (< 17), usava-se `*ngFor`:
> ```html
> <li *ngFor="let fruta of frutas">{{ fruta }}</li>
> ```

### `@switch` — Múltiplas Condições (Angular 17+)

```typescript
export class AppComponent {
  status: string = 'ativo';
}
```

```html
@switch (status) {
  @case ('ativo') {
    <span style="color: green">● Ativo</span>
  }
  @case ('inativo') {
    <span style="color: red">● Inativo</span>
  }
  @case ('pendente') {
    <span style="color: orange">● Pendente</span>
  }
  @default {
    <span>● Desconhecido</span>
  }
}
```

---

## ⚙️ Serviços — O Cérebro Compartilhado

### O que são?

Um **serviço** é uma classe que contém lógica que pode ser **compartilhada entre vários componentes**. Pense como um **funcionário da empresa** que atende vários departamentos.

### Por que usar serviços?

```
❌ SEM SERVIÇO (código repetido em cada componente):

┌──────────┐  ┌──────────┐  ┌──────────┐
│ Comp A   │  │ Comp B   │  │ Comp C   │
│ buscar() │  │ buscar() │  │ buscar() │  ← Código repetido!
│ salvar() │  │ salvar() │  │ salvar() │
└──────────┘  └──────────┘  └──────────┘

✅ COM SERVIÇO (lógica centralizada):

┌──────────┐  ┌──────────┐  ┌──────────┐
│ Comp A   │  │ Comp B   │  │ Comp C   │
└────┬─────┘  └────┬─────┘  └────┬─────┘
     │             │             │
     └──────────┬──┘─────────────┘
                │
        ┌───────▼────────┐
        │   Serviço      │  ← Um lugar só!
        │   buscar()     │
        │   salvar()     │
        └────────────────┘
```

### Criando um Serviço

```bash
ng generate service services/usuario
# ou
ng g s services/usuario
```

```typescript
// services/usuario.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'  // Disponível em toda a aplicação
})
export class UsuarioService {
  private usuarios: string[] = ['Ana', 'Bruno', 'Carlos'];

  getUsuarios(): string[] {
    return this.usuarios;
  }

  adicionar(nome: string): void {
    this.usuarios.push(nome);
  }

  remover(index: number): void {
    this.usuarios.splice(index, 1);
  }
}
```

### Usando o Serviço em um Componente

```typescript
// lista-usuarios.component.ts
import { Component } from '@angular/core';
import { UsuarioService } from '../services/usuario.service';

@Component({
  selector: 'app-lista-usuarios',
  standalone: true,
  template: `
    <h2>Usuários</h2>
    <ul>
      @for (usuario of usuarios; track $index) {
        <li>{{ usuario }}</li>
      }
    </ul>
  `
})
export class ListaUsuariosComponent {
  usuarios: string[];

  // O Angular injeta o serviço automaticamente (Injeção de Dependência)
  constructor(private usuarioService: UsuarioService) {
    this.usuarios = this.usuarioService.getUsuarios();
  }
}
```

> 💡 **Injeção de Dependência**: Você não cria o serviço com `new` — o Angular faz isso pra você! Basta colocar no `constructor`.

---

## 🔌 Input e Output — Comunicação entre Componentes

### Input — Pai envia dados para o Filho

```typescript
// filho.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-card',
  standalone: true,
  template: `
    <div class="card">
      <h3>{{ titulo }}</h3>
      <p>{{ descricao }}</p>
    </div>
  `
})
export class CardComponent {
  @Input() titulo: string = '';
  @Input() descricao: string = '';
}
```

```html
<!-- pai.component.html -->
<app-card titulo="Angular" descricao="Framework incrível!"></app-card>
<app-card [titulo]="meuTitulo" [descricao]="minhaDescricao"></app-card>
```

### Output — Filho envia eventos para o Pai

```typescript
// filho.component.ts
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-botao',
  standalone: true,
  template: `<button (click)="aoClicar()">{{ texto }}</button>`
})
export class BotaoComponent {
  @Input() texto: string = 'Clique';
  @Output() clicado = new EventEmitter<string>();

  aoClicar(): void {
    this.clicado.emit('Botão foi clicado!');
  }
}
```

```html
<!-- pai.component.html -->
<app-botao texto="Salvar" (clicado)="onSalvar($event)"></app-botao>
```

```typescript
// pai.component.ts
export class PaiComponent {
  onSalvar(mensagem: string): void {
    console.log(mensagem); // "Botão foi clicado!"
  }
}
```

### 📊 Resumo da Comunicação

```
┌────────────────────┐
│  COMPONENTE PAI    │
│                    │        [Input]
│  dados ──────────────────►  ┌──────────────────┐
│                    │        │ COMPONENTE FILHO  │
│  metodo() ◄──────────────── │                  │
│                    │        └──────────────────┘
│                    │        (Output)
└────────────────────┘
```

---

## 🔄 Ciclo de Vida dos Componentes

Componentes nascem, vivem e morrem. O Angular avisa quando cada fase acontece:

```
Criação do Componente
        │
        ▼
  ngOnInit()        ← "Nasceu!" — Inicialização (mais usado!)
        │
        ▼
  ngOnChanges()     ← "Algo mudou!" — Quando @Input muda
        │
        ▼
  ngDoCheck()       ← "Verificando..." — A cada ciclo de detecção
        │
        ▼
  ngAfterViewInit() ← "Tela pronta!" — Depois que o HTML renderizou
        │
        ▼
  ngOnDestroy()     ← "Morrendo..." — Limpeza antes de sumir
```

### Exemplo prático

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-relogio',
  standalone: true,
  template: `<p>{{ horaAtual }}</p>`
})
export class RelogioComponent implements OnInit, OnDestroy {
  horaAtual: string = '';
  private intervalo: any;

  ngOnInit(): void {
    // Roda quando o componente é criado
    console.log('Relógio criado!');
    this.intervalo = setInterval(() => {
      this.horaAtual = new Date().toLocaleTimeString();
    }, 1000);
  }

  ngOnDestroy(): void {
    // Roda quando o componente é destruído (limpeza!)
    console.log('Relógio destruído!');
    clearInterval(this.intervalo);
  }
}
```

---

## ❌ Errado vs ✅ Certo

| ❌ Errado | ✅ Certo | Por quê |
|-----------|---------|---------|
| Colocar lógica pesada no template | Usar métodos ou pipes | Template deve ser simples |
| Chamar APIs diretamente no componente | Criar um serviço para chamadas HTTP | Separação de responsabilidades |
| Criar serviços com `new MinhaClasse()` | Usar injeção de dependência (`constructor`) | O Angular gerencia as instâncias |
| Esquecer de limpar no `ngOnDestroy` | Cancelar subscriptions e intervals | Evita vazamento de memória |
| Um componente com 500 linhas | Dividir em componentes menores | Mais fácil de manter e testar |

---

## 📊 Cola Rápida

| Conceito | O que é | Analogia |
|----------|---------|----------|
| **Componente** | Pedaço reutilizável da tela | Bloco de Lego |
| **Template** | HTML do componente | A cara do bloco |
| **Serviço** | Lógica compartilhada | Funcionário serve vários setores |
| **@Input** | Dado do pai → filho | Pai dá presente ao filho |
| **@Output** | Evento do filho → pai | Filho avisa o pai |
| **Diretiva** | Instrução no HTML | Controlador de tráfego |
| **Ciclo de vida** | Fases do componente | Nascer, viver, morrer |

---

[⬅️ Voltar ao Índice](../README.md) | [Anterior: Configuração Inicial](02-configuracao-inicial.md) | [Próximo: Sintaxe Básica ➡️](04-sintaxe-basica.md)
