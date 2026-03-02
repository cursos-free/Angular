[⬅️ Voltar ao Índice](../README.md) | [Anterior: Conceitos Fundamentais](03-conceitos-fundamentais.md) | [Próximo: Tópicos Intermediários ➡️](05-topicos-intermediarios.md)

---

# 💻 Sintaxe Básica

Neste guia, vamos ver a **sintaxe que você vai usar todos os dias** ao trabalhar com Angular. É o seu kit de sobrevivência!

---

## 📝 TypeScript — O Básico que Precisa Saber

Angular usa **TypeScript** (TS) — que é basicamente JavaScript com **tipos**. Pense como JavaScript com cinto de segurança: te protege de erros.

### Variáveis com Tipos

```typescript
// JavasScript (sem tipos — pode dar erro silencioso)
let nome = 'João';
nome = 42; // Não dá erro, mas tá errado!

// TypeScript (com tipos — pega o erro antes de rodar)
let nome: string = 'João';
nome = 42; // ❌ ERRO! O TypeScript avisa na hora
```

### Tipos Mais Comuns

```typescript
// Tipos básicos
let nome: string = 'Maria';
let idade: number = 25;
let ativo: boolean = true;

// Arrays
let frutas: string[] = ['Maçã', 'Banana', 'Uva'];
let numeros: number[] = [1, 2, 3];

// Objetos com Interface
interface Usuario {
  nome: string;
  idade: number;
  email?: string;  // ? = opcional
}

let usuario: Usuario = {
  nome: 'Carlos',
  idade: 30
};

// Funções com tipos
function somar(a: number, b: number): number {
  return a + b;
}

// Arrow function com tipos
const dobrar = (valor: number): number => valor * 2;
```

### 📊 Tabela de Tipos TypeScript

| Tipo | Exemplo | Descrição |
|------|---------|-----------|
| `string` | `'texto'` | Texto |
| `number` | `42`, `3.14` | Número |
| `boolean` | `true`, `false` | Verdadeiro/falso |
| `string[]` | `['a', 'b']` | Lista de textos |
| `number[]` | `[1, 2, 3]` | Lista de números |
| `any` | qualquer coisa | Sem tipo (evite usar!) |
| `void` | — | Função que não retorna nada |
| `null` | `null` | Valor nulo |
| `undefined` | `undefined` | Valor não definido |
| `object` | `{ }` | Objeto genérico |

---

## 🧩 Sintaxe de Componentes

### Componente Básico

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-saudacao',    // Tag HTML: <app-saudacao>
  standalone: true,             // Independente (padrão no Angular 17+)
  template: `                   // HTML inline (para templates pequenos)
    <h1>{{ mensagem }}</h1>
  `,
  styles: [`                    // CSS inline (para estilos pequenos)
    h1 { color: blue; }
  `]
})
export class SaudacaoComponent {
  mensagem: string = 'Olá, Angular!';
}
```

### Componente com Arquivos Externos

```typescript
@Component({
  selector: 'app-perfil',
  standalone: true,
  templateUrl: './perfil.component.html',    // HTML em arquivo separado
  styleUrl: './perfil.component.css'         // CSS em arquivo separado
})
export class PerfilComponent {
  nome: string = 'Ana';
  foto: string = 'assets/avatar.png';
}
```

### ❌ Errado vs ✅ Certo — Nomenclatura

| ❌ Errado | ✅ Certo | Regra |
|-----------|---------|-------|
| `selector: 'saudacao'` | `selector: 'app-saudacao'` | Sempre use prefixo (`app-`) |
| `MeuComponente` | `MeuComponent` | Sufixo em inglês `Component` |
| `meu.component.ts` | `meu.component.ts` | Use kebab-case nos nomes de arquivo |
| `class meu_componente` | `class MeuComponent` | Use PascalCase na classe |

---

## 📋 Sintaxe de Templates

### Interpolação `{{ }}`

Exibe valores no HTML:

```typescript
export class AppComponent {
  nome: string = 'João';
  preco: number = 49.99;
  itens: number = 3;
}
```

```html
<!-- Variáveis simples -->
<p>Nome: {{ nome }}</p>

<!-- Expressões -->
<p>Total: R$ {{ preco * itens }}</p>

<!-- Métodos -->
<p>Nome em maiúsculo: {{ nome.toUpperCase() }}</p>

<!-- Operador ternário -->
<p>{{ itens > 0 ? 'Tem itens' : 'Carrinho vazio' }}</p>
```

### Property Binding `[ ]`

```html
<!-- Vincular atributos HTML -->
<img [src]="urlFoto" [alt]="descricao">
<a [href]="link">Clique aqui</a>
<button [disabled]="carregando">Enviar</button>

<!-- Classes condicionais -->
<div [class.ativo]="estaAtivo">Conteúdo</div>
<div [class.erro]="temErro">Mensagem</div>

<!-- Estilos dinâmicos -->
<div [style.background-color]="corFundo">Colorido</div>
<div [style.font-size.px]="tamanhoFonte">Texto</div>
```

### Event Binding `( )`

```html
<!-- Eventos do mouse -->
<button (click)="salvar()">Salvar</button>
<div (dblclick)="abrirEditor()">Duplo clique</div>
<div (mouseenter)="mostrar()" (mouseleave)="esconder()">Hover</div>

<!-- Eventos do teclado -->
<input (keyup)="buscar()" placeholder="Digite...">
<input (keyup.enter)="enviar()" placeholder="Enter para enviar">
<input (keyup.escape)="cancelar()" placeholder="Esc para cancelar">

<!-- Eventos de formulário -->
<form (submit)="onSubmit()">
<input (focus)="onFoco()" (blur)="onDesfocar()">
<input (input)="onDigitar($event)">

<!-- $event = dados do evento -->
<input (input)="onInput($event)">
```

```typescript
export class AppComponent {
  onInput(evento: Event): void {
    const input = evento.target as HTMLInputElement;
    console.log('Digitou:', input.value);
  }
}
```

---

## 🔄 Diretivas de Controle de Fluxo

### Condicional `@if` (Angular 17+)

```html
<!-- Simples -->
@if (logado) {
  <p>Bem-vindo!</p>
}

<!-- Com else -->
@if (logado) {
  <p>Bem-vindo, {{ nomeUsuario }}!</p>
} @else {
  <p>Faça login para continuar.</p>
}

<!-- Múltiplas condições -->
@if (perfil === 'admin') {
  <app-painel-admin />
} @else if (perfil === 'editor') {
  <app-painel-editor />
} @else {
  <app-painel-visitante />
}
```

### Repetição `@for` (Angular 17+)

```typescript
export class AppComponent {
  tarefas = [
    { id: 1, texto: 'Estudar Angular', feita: true },
    { id: 2, texto: 'Fazer exercícios', feita: false },
    { id: 3, texto: 'Ler documentação', feita: false },
  ];
}
```

```html
<!-- Lista simples -->
@for (tarefa of tarefas; track tarefa.id) {
  <div>
    <input type="checkbox" [checked]="tarefa.feita">
    {{ tarefa.texto }}
  </div>
} @empty {
  <p>Nenhuma tarefa encontrada!</p>
}

<!-- Com index -->
@for (tarefa of tarefas; track tarefa.id; let i = $index) {
  <p>{{ i + 1 }}. {{ tarefa.texto }}</p>
}

<!-- Variáveis disponíveis no @for -->
<!-- $index  → índice atual (0, 1, 2...) -->
<!-- $first  → true se é o primeiro -->
<!-- $last   → true se é o último -->
<!-- $even   → true se índice é par -->
<!-- $odd    → true se índice é ímpar -->
<!-- $count  → total de itens -->
```

### Switch `@switch` (Angular 17+)

```html
@switch (diaSemana) {
  @case ('segunda') { <p>Início da semana 😴</p> }
  @case ('sexta') { <p>Sextou! 🎉</p> }
  @case ('sabado') { <p>Fim de semana! 🏖️</p> }
  @case ('domingo') { <p>Descanso merecido 😴</p> }
  @default { <p>Dia normal de trabalho 💼</p> }
}
```

---

## 🔗 Pipes — Transformadores de Dados

Pipes formatam dados **diretamente no template**, sem alterar o valor original:

```html
<!-- Texto -->
<p>{{ nome | uppercase }}</p>          <!-- MARIA -->
<p>{{ nome | lowercase }}</p>          <!-- maria -->
<p>{{ nome | titlecase }}</p>          <!-- Maria -->

<!-- Números -->
<p>{{ preco | number:'1.2-2' }}</p>    <!-- 49,99 -->
<p>{{ porcentagem | percent }}</p>      <!-- 75% -->
<p>{{ valor | currency:'BRL' }}</p>     <!-- R$ 49,99 -->

<!-- Datas -->
<p>{{ hoje | date:'dd/MM/yyyy' }}</p>          <!-- 02/03/2026 -->
<p>{{ hoje | date:'fullDate' }}</p>             <!-- segunda-feira, 2 de março de 2026 -->
<p>{{ hoje | date:'shortTime' }}</p>            <!-- 14:30 -->

<!-- JSON (ótimo para debug!) -->
<pre>{{ objeto | json }}</pre>

<!-- Encadear pipes -->
<p>{{ nome | uppercase | slice:0:3 }}</p>  <!-- MAR -->
```

### 📊 Tabela de Pipes Mais Usados

| Pipe | Exemplo | Resultado |
|------|---------|-----------|
| `uppercase` | `'angular' \| uppercase` | `ANGULAR` |
| `lowercase` | `'ANGULAR' \| lowercase` | `angular` |
| `titlecase` | `'olá mundo' \| titlecase` | `Olá Mundo` |
| `date` | `data \| date:'dd/MM/yyyy'` | `02/03/2026` |
| `currency` | `49.9 \| currency:'BRL'` | `R$ 49,90` |
| `number` | `3.14159 \| number:'1.2-2'` | `3,14` |
| `percent` | `0.75 \| percent` | `75%` |
| `json` | `{a: 1} \| json` | `{ "a": 1 }` |
| `slice` | `'Angular' \| slice:0:3` | `Ang` |
| `async` | `observable$ \| async` | Valor atual |

### Criando um Pipe Personalizado

```bash
ng generate pipe pipes/cpf
```

```typescript
// pipes/cpf.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'cpf',
  standalone: true
})
export class CpfPipe implements PipeTransform {
  transform(value: string): string {
    if (!value || value.length !== 11) return value;

    return `${value.slice(0, 3)}.${value.slice(3, 6)}.${value.slice(6, 9)}-${value.slice(9)}`;
  }
}
```

```html
<!-- Uso -->
<p>CPF: {{ '12345678901' | cpf }}</p>
<!-- Resultado: 123.456.789-01 -->
```

---

## 📐 Template Reference Variables `#`

Crie referências a elementos HTML:

```html
<!-- Referência a um input -->
<input #caixaTexto type="text" placeholder="Digite algo">
<button (click)="mostrar(caixaTexto.value)">Mostrar</button>

<!-- Referência a um componente -->
<app-timer #meuTimer></app-timer>
<button (click)="meuTimer.pausar()">Pausar Timer</button>
```

---

## 🎛️ Signals — Estado Reativo (Angular 16+)

Signals são a forma moderna de gerenciar estado no Angular:

```typescript
import { Component, signal, computed } from '@angular/core';

@Component({
  selector: 'app-contador',
  standalone: true,
  template: `
    <p>Contagem: {{ contagem() }}</p>
    <p>Dobro: {{ dobro() }}</p>
    <button (click)="incrementar()">+1</button>
    <button (click)="decrementar()">-1</button>
    <button (click)="resetar()">Resetar</button>
  `
})
export class ContadorComponent {
  // Signal — estado reativo
  contagem = signal(0);

  // Computed — valor derivado (atualiza automaticamente)
  dobro = computed(() => this.contagem() * 2);

  incrementar(): void {
    this.contagem.update(valor => valor + 1);
  }

  decrementar(): void {
    this.contagem.update(valor => valor - 1);
  }

  resetar(): void {
    this.contagem.set(0);
  }
}
```

### 📊 Métodos de Signals

| Método | O que faz | Exemplo |
|--------|-----------|---------|
| `signal(valor)` | Cria um signal | `signal(0)` |
| `.set(valor)` | Define novo valor | `contagem.set(10)` |
| `.update(fn)` | Atualiza com base no valor atual | `contagem.update(v => v + 1)` |
| `computed(fn)` | Cria valor derivado | `computed(() => x() * 2)` |
| `effect(fn)` | Reage a mudanças | `effect(() => console.log(x()))` |

---

## 📂 Importando Componentes

No Angular standalone (padrão), você precisa importar componentes para usá-los:

```typescript
import { Component } from '@angular/core';
import { HeaderComponent } from './header/header.component';
import { FooterComponent } from './footer/footer.component';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    CommonModule,        // Pipes e diretivas comuns
    HeaderComponent,     // Componente de cabeçalho
    FooterComponent,     // Componente de rodapé
  ],
  templateUrl: './app.component.html'
})
export class AppComponent { }
```

---

## ❌ Errado vs ✅ Certo

| ❌ Errado | ✅ Certo | Por quê |
|-----------|---------|---------|
| `{{ metodoComplexo() }}` no template | Usar computed signal ou pipe | Métodos executam toda hora |
| `[attr.src]="url"` para `<img>` | `[src]="url"` | `src` é property, não attribute |
| `*ngIf` + `*ngFor` no mesmo elemento | Usar `@if` dentro de `@for` | Não pode ter 2 diretivas estruturais |
| `any` em todo lugar | Tipar corretamente | Perde as vantagens do TypeScript |
| Template com 200 linhas | Dividir em componentes menores | Difícil de manter |
| `(click)="variavel = true"` | `(click)="metodo()"` | Mantenha lógica no .ts |

---

## 📊 Cola Rápida — Sintaxes Essenciais

```
┌──────────────────────────────────────────────────┐
│              SINTAXE ANGULAR                      │
│                                                  │
│  {{ valor }}          → Mostrar dados            │
│  [prop]="valor"       → Vincular propriedade     │
│  (evento)="fn()"      → Escutar evento           │
│  [(ngModel)]="var"    → Two-way binding          │
│                                                  │
│  @if (cond) { }       → Condicional              │
│  @for (x of list) { } → Repetição               │
│  @switch (val) { }    → Multi-condição           │
│                                                  │
│  {{ val | pipe }}     → Transformar dado          │
│  #ref                 → Referência no template    │
│  signal(val)          → Estado reativo            │
│                                                  │
│  @Input()             → Receber dados do pai     │
│  @Output()            → Emitir eventos pro pai   │
└──────────────────────────────────────────────────┘
```

---

[⬅️ Voltar ao Índice](../README.md) | [Anterior: Conceitos Fundamentais](03-conceitos-fundamentais.md) | [Próximo: Tópicos Intermediários ➡️](05-topicos-intermediarios.md)
