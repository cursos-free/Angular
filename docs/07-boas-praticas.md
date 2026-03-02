[⬅️ Voltar ao Índice](../README.md) | [Anterior: Tópicos Avançados](06-topicos-avancados.md) | [Próximo: Erros Comuns ➡️](08-erros-comuns.md)

---

# ✨ Boas Práticas e Convenções

Seguir boas práticas é como seguir leis de trânsito — todo mundo anda na mesma direção e ninguém bate. Aqui estão as convenções usadas pelas melhores equipes do mercado.

---

## 📁 Estrutura de Pastas

### ❌ Errado — Tudo jogado

```
src/app/
├── header.component.ts
├── footer.component.ts
├── login.component.ts
├── dashboard.component.ts
├── user.service.ts
├── auth.service.ts
├── cpf.pipe.ts
├── auth.guard.ts
└── ... (50 arquivos na mesma pasta 😱)
```

### ✅ Certo — Organizado por funcionalidade

```
src/app/
├── 📁 core/                    ← Serviços e lógica global (singleton)
│   ├── 📁 guards/
│   │   └── auth.guard.ts
│   ├── 📁 interceptors/
│   │   ├── auth.interceptor.ts
│   │   └── error.interceptor.ts
│   ├── 📁 services/
│   │   ├── auth.service.ts
│   │   └── notification.service.ts
│   └── 📁 models/
│       ├── usuario.model.ts
│       └── produto.model.ts
│
├── 📁 shared/                  ← Componentes e pipes reutilizáveis
│   ├── 📁 components/
│   │   ├── 📁 botao/
│   │   ├── 📁 card/
│   │   └── 📁 modal/
│   ├── 📁 pipes/
│   │   ├── cpf.pipe.ts
│   │   └── telefone.pipe.ts
│   └── 📁 directives/
│       └── destaque.directive.ts
│
├── 📁 features/                ← Páginas e funcionalidades
│   ├── 📁 home/
│   │   ├── home.component.ts
│   │   ├── home.component.html
│   │   └── home.component.css
│   ├── 📁 dashboard/
│   │   ├── dashboard.component.ts
│   │   ├── 📁 components/     ← Componentes específicos dessa feature
│   │   │   ├── grafico/
│   │   │   └── resumo/
│   │   └── 📁 services/
│   │       └── dashboard.service.ts
│   └── 📁 auth/
│       ├── login/
│       └── registro/
│
├── 📁 layout/                  ← Componentes de layout
│   ├── header/
│   ├── footer/
│   └── sidebar/
│
├── app.component.ts
├── app.config.ts
└── app.routes.ts
```

### 📊 Regra dos 3 Baldes

| Balde | O que vai dentro | Exemplo |
|-------|-------------------|---------|
| **core/** | Coisas que existem **uma vez** no app | Guards, interceptors, serviços globais |
| **shared/** | Coisas **reutilizadas** em várias features | Componentes genéricos, pipes, diretivas |
| **features/** | **Páginas e funcionalidades** específicas | Home, Dashboard, Login |

---

## 📝 Convenções de Nomenclatura

### Nomes de Arquivos

```
✅ Correto:
hero-list.component.ts        (kebab-case + sufixo)
hero.service.ts
auth.guard.ts
cpf.pipe.ts
user.model.ts

❌ Errado:
HeroList.component.ts         (PascalCase no arquivo)
herolistComponent.ts          (sem separação)
heroes.ts                     (sem sufixo — é o que?)
```

### Nomes de Classes

```typescript
// ✅ Correto: PascalCase + sufixo descritivo
export class HeroListComponent { }
export class HeroService { }
export class AuthGuard { }
export class CpfPipe { }

// ❌ Errado
export class heroList { }         // camelCase
export class Heroes { }           // Sem sufixo
export class HERO_SERVICE { }     // SCREAMING_CASE
```

### Nomes de Variáveis e Métodos

```typescript
// ✅ Correto: camelCase
let nomeUsuario: string = '';
let estaLogado: boolean = false;
const MAX_TENTATIVAS: number = 3;  // Constantes em UPPER_CASE

function buscarUsuarios(): void { }
function onClicarBotao(): void { }

// ❌ Errado
let NomeUsuario = '';      // PascalCase (nome de classe)
let nome_usuario = '';     // snake_case (não é Python!)
function BuscarUsuarios() { }  // PascalCase (parece classe)
```

### 📊 Tabela de Convenções

| Tipo | Convenção | Exemplo |
|------|-----------|---------|
| Arquivo | `kebab-case.tipo.ts` | `user-list.component.ts` |
| Classe | `PascalCase + Sufixo` | `UserListComponent` |
| Variável | `camelCase` | `userName` |
| Constante | `UPPER_SNAKE_CASE` | `MAX_RETRY` |
| Interface | `PascalCase` | `User` (sem prefixo `I`) |
| Enum | `PascalCase` | `UserRole` |
| Observable | `camelCase$` | `users$` (com `$` no final) |
| Signal | `camelCase` | `count` (sem `$`) |
| Selector | `app-kebab-case` | `app-user-list` |

---

## 🧩 Boas Práticas em Componentes

### 1. Componentes Pequenos e Focados

```typescript
// ❌ Errado: componente com 500 linhas fazendo tudo
@Component({ ... })
export class PaginaComponent {
  usuarios = [];
  produtos = [];
  pedidos = [];
  // ... 50 métodos diferentes
}

// ✅ Certo: cada componente faz UMA coisa
@Component({
  template: `
    <app-header />
    <app-lista-usuarios [usuarios]="usuarios" />
    <app-lista-produtos [produtos]="produtos" />
    <app-footer />
  `
})
export class PaginaComponent {
  usuarios = this.usuarioService.getUsuarios();
  produtos = this.produtoService.getProdutos();

  constructor(
    private usuarioService: UsuarioService,
    private produtoService: ProdutoService
  ) {}
}
```

### 2. Use `inject()` ao invés de constructor injection

```typescript
// ✅ Moderno (Angular 14+)
export class MeuComponent {
  private service = inject(MeuService);
  private router = inject(Router);
}

// 🟡 Também funciona (forma clássica)
export class MeuComponent {
  constructor(
    private service: MeuService,
    private router: Router
  ) {}
}
```

### 3. Use `OnPush` Change Detection

```typescript
// ✅ Melhor performance
@Component({
  selector: 'app-card',
  changeDetection: ChangeDetectionStrategy.OnPush,  // ← Adicione isso
  template: `<div>{{ dados().nome }}</div>`
})
export class CardComponent {
  dados = input.required<Usuario>();  // Signal input
}
```

### 4. Desinscrevendo Observables

```typescript
// ✅ Forma 1: takeUntilDestroyed (Angular 16+, a melhor!)
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

export class MeuComponent {
  private destroyRef = inject(DestroyRef);

  ngOnInit(): void {
    this.meuService.getData().pipe(
      takeUntilDestroyed(this.destroyRef)
    ).subscribe(dados => { ... });
  }
}

// ✅ Forma 2: async pipe no template (não precisa desinscrever!)
@Component({
  template: `
    @for (item of items$ | async; track item.id) {
      <p>{{ item.nome }}</p>
    }
  `
})
export class MeuComponent {
  items$ = this.service.getItems();
}

// ❌ Errado: esquecer de desinscrever
export class MeuComponent {
  ngOnInit(): void {
    this.service.getData().subscribe(dados => { ... }); // 🔴 Vazamento de memória!
  }
}
```

---

## ⚙️ Boas Práticas em Serviços

### 1. Serviços como fonte única de verdade

```typescript
// ✅ Certo: serviço gerencia o estado
@Injectable({ providedIn: 'root' })
export class TodoService {
  private todos = signal<Todo[]>([]);

  getTodos = this.todos.asReadonly(); // Só leitura externamente

  adicionar(texto: string): void {
    this.todos.update(lista => [...lista, { id: Date.now(), texto, feito: false }]);
  }
}

// ❌ Errado: componente gerencia estado que deveria ser compartilhado
@Component({ ... })
export class TodoComponent {
  todos: Todo[] = []; // Estado preso no componente
}
```

### 2. Tipagem forte nos serviços HTTP

```typescript
// ✅ Certo: interface definida
interface ApiResponse<T> {
  dados: T;
  total: number;
  pagina: number;
}

interface Usuario {
  id: number;
  nome: string;
  email: string;
}

@Injectable({ providedIn: 'root' })
export class UsuarioService {
  private http = inject(HttpClient);
  private apiUrl = '/api/usuarios';

  listar(): Observable<ApiResponse<Usuario[]>> {
    return this.http.get<ApiResponse<Usuario[]>>(this.apiUrl);
  }
}

// ❌ Errado: sem tipagem
listar(): Observable<any> {
  return this.http.get(this.apiUrl);
}
```

---

## 🎨 Boas Práticas em Templates

### 1. Use `trackBy` no `@for`

```html
<!-- ✅ Certo: Angular sabe identificar cada item -->
@for (usuario of usuarios; track usuario.id) {
  <app-card [usuario]="usuario" />
}

<!-- ❌ Errado: usa índice (performance ruim ao reordenar) -->
@for (usuario of usuarios; track $index) {
  <app-card [usuario]="usuario" />
}
```

### 2. Evite lógica no template

```html
<!-- ❌ Errado: lógica complexa no template -->
<p>{{ usuario.nome.split(' ')[0].toUpperCase() + ' - ' + (usuario.ativo ? 'Ativo' : 'Inativo') }}</p>

<!-- ✅ Certo: use um computed signal ou pipe -->
<p>{{ nomeFormatado() }}</p>
```

```typescript
nomeFormatado = computed(() => {
  const primeiro = this.usuario().nome.split(' ')[0].toUpperCase();
  const status = this.usuario().ativo ? 'Ativo' : 'Inativo';
  return `${primeiro} - ${status}`;
});
```

### 3. Prefira `@if` com variáveis intermediárias

```html
<!-- ❌ Errado: múltiplas chamadas -->
@if (getUsuario() && getUsuario().ativo && getUsuario().permissoes.includes('admin')) {
  <p>{{ getUsuario().nome }}</p>
}

<!-- ✅ Certo (Angular 17+): -->
@if (usuario(); as u) {
  @if (u.ativo && u.permissoes.includes('admin')) {
    <p>{{ u.nome }}</p>
  }
}
```

---

## 🌍 Environment e Configuração

### Variáveis de ambiente

```typescript
// environments/environment.ts (desenvolvimento)
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000/api',
  appName: 'Meu App (DEV)',
};

// environments/environment.prod.ts (produção)
export const environment = {
  production: true,
  apiUrl: 'https://api.meuapp.com/api',
  appName: 'Meu App',
};
```

```typescript
// Usando no serviço
import { environment } from '../../environments/environment';

@Injectable({ providedIn: 'root' })
export class ApiService {
  private apiUrl = environment.apiUrl;
}
```

---

## 📏 ESLint — Padronizando o Código

### Instalação

```bash
ng add @angular-eslint/schematics
```

### Rodando o linter

```bash
# Verificar erros
ng lint

# Corrigir automaticamente
ng lint --fix
```

---

## ❌ Errado vs ✅ Certo — Resumo Geral

| ❌ Errado | ✅ Certo |
|-----------|---------|
| Arquivos sem sufixo (`.component.ts`) | Sempre use o sufixo correto |
| Componentes fazendo tudo | Componentes pequenos e focados |
| Lógica no template | Usar computed, pipes ou métodos |
| `any` em todo lugar | Interfaces e tipos |
| Subscriptions sem unsubscribe | `takeUntilDestroyed` ou `async` pipe |
| Serviços criados com `new` | Injeção de dependência |
| CSS global para tudo | CSS encapsulado no componente |
| Hard-coded URLs de API | Variáveis de ambiente |
| `console.log` em produção | Sistema de logging adequado |
| Ignorar linter | Configurar ESLint e seguir as regras |

---

## 📊 Cola Rápida

```
┌──────────────────────────────────────────────────┐
│            BOAS PRÁTICAS                          │
│                                                  │
│  ESTRUTURA: core/ + shared/ + features/          │
│  NOMES: kebab-case.tipo.ts / PascalCaseClasse    │
│  COMPONENTES: pequenos, OnPush, inject()         │
│  SERVIÇOS: tipados, centralizados                │
│  TEMPLATES: sem lógica, use pipes/computed        │
│  OBSERVABLES: sempre desinscrever!               │
│  AMBIENTE: environment.ts para configs           │
│  LINT: ng lint (ESLint configurado)              │
└──────────────────────────────────────────────────┘
```

---

[⬅️ Voltar ao Índice](../README.md) | [Anterior: Tópicos Avançados](06-topicos-avancados.md) | [Próximo: Erros Comuns ➡️](08-erros-comuns.md)
