[⬅️ Voltar ao Índice](../README.md) | [Anterior: Sintaxe Básica](04-sintaxe-basica.md) | [Próximo: Tópicos Avançados ➡️](06-topicos-avancados.md)

---

# 🔀 Tópicos Intermediários

Agora que você domina os fundamentos, é hora de aprender as ferramentas que você vai usar em **projetos reais**: rotas, formulários e requisições HTTP.

---

## 🗺️ Roteamento — Navegação entre Páginas

### O que é?

O roteamento permite criar **múltiplas "páginas"** na sua aplicação sem recarregar o navegador. Pense como um **controle remoto de TV** — cada botão mostra um canal diferente, mas a TV continua ligada.

### Configurando Rotas

```typescript
// app.routes.ts
import { Routes } from '@angular/router';
import { HomeComponent } from './pages/home/home.component';
import { SobreComponent } from './pages/sobre/sobre.component';
import { ContatoComponent } from './pages/contato/contato.component';

export const routes: Routes = [
  { path: '',          component: HomeComponent },        // Página inicial
  { path: 'sobre',     component: SobreComponent },       // /sobre
  { path: 'contato',   component: ContatoComponent },     // /contato
  { path: '**',        redirectTo: '' },                   // Qualquer outra → home
];
```

### Adicionando o `<router-outlet>`

O `<router-outlet>` é o **espaço onde as páginas aparecem**:

```html
<!-- app.component.html -->
<nav>
  <a routerLink="/" routerLinkActive="ativo" [routerLinkActiveOptions]="{exact: true}">Home</a>
  <a routerLink="/sobre" routerLinkActive="ativo">Sobre</a>
  <a routerLink="/contato" routerLinkActive="ativo">Contato</a>
</nav>

<!-- Aqui as páginas aparecem dinamicamente -->
<router-outlet></router-outlet>
```

```typescript
// app.component.ts
import { Component } from '@angular/core';
import { RouterOutlet, RouterLink, RouterLinkActive } from '@angular/router';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RouterLink, RouterLinkActive],
  templateUrl: './app.component.html'
})
export class AppComponent { }
```

```
┌──────────────────────────────────────────┐
│  [Home]  [Sobre]  [Contato]   ← nav     │
├──────────────────────────────────────────┤
│                                          │
│    ← Aqui aparece o conteúdo da página   │
│       (router-outlet)                    │
│                                          │
└──────────────────────────────────────────┘
```

### Rotas com Parâmetros

```typescript
// app.routes.ts
export const routes: Routes = [
  { path: 'produto/:id', component: ProdutoDetalheComponent },
  { path: 'usuario/:id/pedidos', component: PedidosComponent },
];
```

```typescript
// produto-detalhe.component.ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-produto-detalhe',
  standalone: true,
  template: `
    <h1>Produto #{{ produtoId }}</h1>
  `
})
export class ProdutoDetalheComponent implements OnInit {
  produtoId: string = '';

  constructor(private route: ActivatedRoute) {}

  ngOnInit(): void {
    this.produtoId = this.route.snapshot.params['id'];

    // Ou reativo (melhor para quando o parâmetro muda sem sair da página):
    this.route.params.subscribe(params => {
      this.produtoId = params['id'];
    });
  }
}
```

### Navegação por Código

```typescript
import { Router } from '@angular/router';

export class MeuComponent {
  constructor(private router: Router) {}

  irParaHome(): void {
    this.router.navigate(['/']);
  }

  irParaProduto(id: number): void {
    this.router.navigate(['/produto', id]);
  }

  irComQueryParams(): void {
    this.router.navigate(['/busca'], {
      queryParams: { q: 'angular', pagina: 1 }
    });
    // Resultado: /busca?q=angular&pagina=1
  }
}
```

### 📊 Cola de Roteamento

| Recurso | Sintaxe | Exemplo |
|---------|---------|---------|
| Link simples | `routerLink="/rota"` | `routerLink="/sobre"` |
| Link com parâmetro | `[routerLink]="['/rota', id]"` | `[routerLink]="['/produto', 42]"` |
| Classe ativa | `routerLinkActive="classe"` | `routerLinkActive="ativo"` |
| Pegar parâmetro | `route.snapshot.params['nome']` | `params['id']` |
| Navegar por código | `router.navigate(['/rota'])` | `router.navigate(['/home'])` |
| Rota coringa | `path: '**'` | Captura todas as rotas não encontradas |

---

## 📝 Formulários

Angular tem **dois tipos** de formulários. Vamos ver os dois!

### Template-Driven Forms (Mais Simples)

Ideal para formulários pequenos. A lógica fica principalmente no template:

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-cadastro',
  standalone: true,
  imports: [FormsModule],
  template: `
    <form #form="ngForm" (ngSubmit)="onSubmit(form)">
      <div>
        <label>Nome:</label>
        <input
          type="text"
          name="nome"
          [(ngModel)]="usuario.nome"
          required
          minlength="3"
          #nome="ngModel"
        >
        @if (nome.invalid && nome.touched) {
          <span class="erro">Nome deve ter ao menos 3 caracteres</span>
        }
      </div>

      <div>
        <label>Email:</label>
        <input
          type="email"
          name="email"
          [(ngModel)]="usuario.email"
          required
          email
          #email="ngModel"
        >
        @if (email.invalid && email.touched) {
          <span class="erro">Email inválido</span>
        }
      </div>

      <button type="submit" [disabled]="form.invalid">Cadastrar</button>
    </form>
  `
})
export class CadastroComponent {
  usuario = {
    nome: '',
    email: ''
  };

  onSubmit(form: any): void {
    if (form.valid) {
      console.log('Dados:', this.usuario);
      // Enviar para API...
    }
  }
}
```

### Reactive Forms (Mais Poderoso)

Ideal para formulários complexos. A lógica fica no componente:

```typescript
import { Component } from '@angular/core';
import { ReactiveFormsModule, FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-registro',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="formulario" (ngSubmit)="onSubmit()">
      <div>
        <label>Nome:</label>
        <input formControlName="nome">
        @if (formulario.get('nome')?.invalid && formulario.get('nome')?.touched) {
          <span class="erro">Nome obrigatório (mín. 3 caracteres)</span>
        }
      </div>

      <div>
        <label>Email:</label>
        <input formControlName="email" type="email">
        @if (formulario.get('email')?.errors?.['email']) {
          <span class="erro">Email inválido</span>
        }
      </div>

      <div>
        <label>Senha:</label>
        <input formControlName="senha" type="password">
        @if (formulario.get('senha')?.errors?.['minlength']) {
          <span class="erro">Mínimo 6 caracteres</span>
        }
      </div>

      <button type="submit" [disabled]="formulario.invalid">Registrar</button>

      <!-- Debug: ver valores do formulário -->
      <pre>{{ formulario.value | json }}</pre>
    </form>
  `
})
export class RegistroComponent {
  formulario: FormGroup;

  constructor(private fb: FormBuilder) {
    this.formulario = this.fb.group({
      nome:  ['', [Validators.required, Validators.minLength(3)]],
      email: ['', [Validators.required, Validators.email]],
      senha: ['', [Validators.required, Validators.minLength(6)]],
    });
  }

  onSubmit(): void {
    if (this.formulario.valid) {
      console.log('Dados:', this.formulario.value);
    }
  }
}
```

### 📊 Comparação: Template-Driven vs Reactive

| Aspecto | Template-Driven | Reactive |
|---------|----------------|----------|
| Complexidade | Simples | Mais controle |
| Onde fica a lógica | Template (HTML) | Componente (TS) |
| Import necessário | `FormsModule` | `ReactiveFormsModule` |
| Binding | `[(ngModel)]` | `formControlName` |
| Validação | Atributos HTML | `Validators` no TS |
| Testes | Mais difícil | Mais fácil |
| Quando usar | Formulários simples | Formulários complexos |

### Validadores Mais Comuns

| Validador | Template-Driven | Reactive Forms |
|-----------|----------------|----------------|
| Obrigatório | `required` | `Validators.required` |
| Mínimo de caracteres | `minlength="3"` | `Validators.minLength(3)` |
| Máximo de caracteres | `maxlength="50"` | `Validators.maxLength(50)` |
| Email | `email` | `Validators.email` |
| Padrão (regex) | `pattern="[0-9]+"` | `Validators.pattern('[0-9]+')` |
| Valor mínimo | `min="0"` | `Validators.min(0)` |
| Valor máximo | `max="100"` | `Validators.max(100)` |

---

## 🌐 HTTP — Requisições para APIs

### Configuração

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient } from '@angular/common/http';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient(),   // ← Habilita o HttpClient
  ]
};
```

### Criando um Serviço HTTP

```typescript
// services/produto.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

interface Produto {
  id: number;
  nome: string;
  preco: number;
  descricao: string;
}

@Injectable({
  providedIn: 'root'
})
export class ProdutoService {
  private apiUrl = 'https://api.exemplo.com/produtos';

  constructor(private http: HttpClient) {}

  // GET — Buscar todos
  listar(): Observable<Produto[]> {
    return this.http.get<Produto[]>(this.apiUrl);
  }

  // GET — Buscar por ID
  buscarPorId(id: number): Observable<Produto> {
    return this.http.get<Produto>(`${this.apiUrl}/${id}`);
  }

  // POST — Criar
  criar(produto: Produto): Observable<Produto> {
    return this.http.post<Produto>(this.apiUrl, produto);
  }

  // PUT — Atualizar
  atualizar(id: number, produto: Produto): Observable<Produto> {
    return this.http.put<Produto>(`${this.apiUrl}/${id}`, produto);
  }

  // DELETE — Remover
  remover(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

### Usando o Serviço no Componente

```typescript
import { Component, OnInit } from '@angular/core';
import { ProdutoService } from '../services/produto.service';

@Component({
  selector: 'app-lista-produtos',
  standalone: true,
  template: `
    @if (carregando) {
      <p>Carregando...</p>
    } @else if (erro) {
      <p class="erro">{{ erro }}</p>
    } @else {
      <ul>
        @for (produto of produtos; track produto.id) {
          <li>
            {{ produto.nome }} — R$ {{ produto.preco | number:'1.2-2' }}
            <button (click)="remover(produto.id)">🗑️</button>
          </li>
        } @empty {
          <p>Nenhum produto encontrado.</p>
        }
      </ul>
    }
  `
})
export class ListaProdutosComponent implements OnInit {
  produtos: any[] = [];
  carregando: boolean = true;
  erro: string = '';

  constructor(private produtoService: ProdutoService) {}

  ngOnInit(): void {
    this.produtoService.listar().subscribe({
      next: (dados) => {
        this.produtos = dados;
        this.carregando = false;
      },
      error: (err) => {
        this.erro = 'Erro ao carregar produtos';
        this.carregando = false;
        console.error(err);
      }
    });
  }

  remover(id: number): void {
    this.produtoService.remover(id).subscribe({
      next: () => {
        this.produtos = this.produtos.filter(p => p.id !== id);
      },
      error: (err) => console.error('Erro ao remover:', err)
    });
  }
}
```

### 📊 Métodos HTTP

| Método | Uso | Exemplo |
|--------|-----|---------|
| `GET` | Buscar dados | `http.get<T>(url)` |
| `POST` | Criar dados | `http.post<T>(url, body)` |
| `PUT` | Atualizar tudo | `http.put<T>(url, body)` |
| `PATCH` | Atualizar parcial | `http.patch<T>(url, body)` |
| `DELETE` | Remover | `http.delete<T>(url)` |

---

## 🔔 RxJS — O Básico de Observables

Angular usa **RxJS** (Reactive Extensions) para lidar com dados assíncronos. Pense como uma **assinatura de revista** — você se inscreve e recebe cada edição quando sai.

### Conceitos Base

```typescript
import { Observable, of, interval } from 'rxjs';
import { map, filter, take } from 'rxjs/operators';

// Criar um Observable simples
const numeros$ = of(1, 2, 3, 4, 5);

// Se inscrever para receber os valores
numeros$.subscribe(valor => console.log(valor));
// Saída: 1, 2, 3, 4, 5

// Transformar valores com operadores
numeros$.pipe(
  filter(n => n % 2 === 0),   // Só pares
  map(n => n * 10)             // Multiplicar por 10
).subscribe(valor => console.log(valor));
// Saída: 20, 40
```

### Operadores Mais Usados

| Operador | O que faz | Exemplo |
|----------|-----------|---------|
| `map` | Transforma cada valor | `map(x => x * 2)` |
| `filter` | Filtra valores | `filter(x => x > 5)` |
| `tap` | Efeito colateral (debug) | `tap(x => console.log(x))` |
| `switchMap` | Troca para outro Observable | Busca ao digitar |
| `catchError` | Trata erros | `catchError(err => of([]))` |
| `debounceTime` | Espera antes de emitir | `debounceTime(300)` para buscas |
| `distinctUntilChanged` | Ignora valores repetidos | Evita buscas duplicadas |
| `take` | Pega N valores e para | `take(5)` |
| `takeUntil` | Para quando outro Observable emite | Cancelar subscriptions |

### Exemplo Prático: Campo de Busca

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { FormControl, ReactiveFormsModule } from '@angular/forms';
import { debounceTime, distinctUntilChanged, switchMap, Subject, takeUntil } from 'rxjs';
import { ProdutoService } from '../services/produto.service';

@Component({
  selector: 'app-busca',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <input [formControl]="campoBusca" placeholder="Buscar produtos...">
    <ul>
      @for (produto of resultados; track produto.id) {
        <li>{{ produto.nome }}</li>
      }
    </ul>
  `
})
export class BuscaComponent implements OnInit, OnDestroy {
  campoBusca = new FormControl('');
  resultados: any[] = [];
  private destroy$ = new Subject<void>();

  constructor(private produtoService: ProdutoService) {}

  ngOnInit(): void {
    this.campoBusca.valueChanges.pipe(
      debounceTime(300),              // Espera 300ms após parar de digitar
      distinctUntilChanged(),          // Ignora se o texto não mudou
      switchMap(termo =>               // Troca para a busca na API
        this.produtoService.buscar(termo!)
      ),
      takeUntil(this.destroy$)         // Cancela quando o componente é destruído
    ).subscribe(resultados => {
      this.resultados = resultados;
    });
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

---

## ❌ Errado vs ✅ Certo

| ❌ Errado | ✅ Certo | Por quê |
|-----------|---------|---------|
| Usar `<a href="/rota">` | Usar `<a routerLink="/rota">` | `href` recarrega toda a página |
| Esquecer de dar `subscribe()` | Sempre dar subscribe ou usar `async` pipe | Sem subscribe, a request nem acontece |
| Não cancelar subscriptions | Sempre cancelar no `ngOnDestroy` | Causa vazamento de memória |
| Chamar HTTP no componente diretamente | Criar um serviço para chamadas HTTP | Separação de responsabilidades |
| Fazer validação só no front-end | Validar no front E no back-end | Front-end pode ser burlado |
| Usar `any` nos responses HTTP | Criar interfaces tipadas | TypeScript perde utilidade |

---

## 📊 Cola Rápida

```
┌──────────────────────────────────────────────────┐
│           TÓPICOS INTERMEDIÁRIOS                  │
│                                                  │
│  ROTAS                                           │
│  { path: 'rota', component: Comp }               │
│  <router-outlet></router-outlet>                 │
│  routerLink="/rota"                              │
│                                                  │
│  FORMULÁRIOS                                     │
│  Template: [(ngModel)] + FormsModule             │
│  Reactive: formControlName + ReactiveFormsModule │
│                                                  │
│  HTTP                                            │
│  provideHttpClient() no app.config.ts            │
│  http.get/post/put/delete → subscribe()          │
│                                                  │
│  OBSERVABLE                                      │
│  .pipe(operadores).subscribe(callback)           │
│  Sempre cancelar no ngOnDestroy!                 │
└──────────────────────────────────────────────────┘
```

---

[⬅️ Voltar ao Índice](../README.md) | [Anterior: Sintaxe Básica](04-sintaxe-basica.md) | [Próximo: Tópicos Avançados ➡️](06-topicos-avancados.md)
