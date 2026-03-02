[⬅️ Voltar ao Índice](../README.md) | [Anterior: Tópicos Intermediários](05-topicos-intermediarios.md) | [Próximo: Boas Práticas ➡️](07-boas-praticas.md)

---

# 🚀 Tópicos Avançados

Estes são os recursos mais usados no mercado de trabalho. Dominar eles vai te diferenciar de outros desenvolvedores Angular.

---

## ⏳ Lazy Loading — Carregamento Sob Demanda

### O que é?

Imagine um **restaurante com cardápio digital**. Você não baixa fotos de **todos os pratos** de uma vez — só carrega as fotos quando abre cada categoria. **Lazy loading** é isso: carrega o código de cada página **só quando o usuário acessa ela**.

### Por que usar?

```
SEM Lazy Loading:                COM Lazy Loading:

Usuário acessa o site            Usuário acessa o site
        │                                │
        ▼                                ▼
Baixa TODO o código              Baixa só a HOME (rápido!)
(Home + Sobre + Admin +                  │
 Dashboard + Config...)          Clica em "Sobre"
        │                                │
        ▼                                ▼
Demora pra carregar 😴           Baixa só o "Sobre" (rápido!)
```

### Como implementar

```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: '',
    component: HomeComponent  // Carrega imediatamente
  },
  {
    path: 'dashboard',
    loadComponent: () =>      // ← Lazy loading de componente!
      import('./pages/dashboard/dashboard.component')
        .then(m => m.DashboardComponent)
  },
  {
    path: 'admin',
    loadChildren: () =>       // ← Lazy loading de módulo inteiro!
      import('./pages/admin/admin.routes')
        .then(m => m.ADMIN_ROUTES)
  }
];
```

```typescript
// pages/admin/admin.routes.ts
import { Routes } from '@angular/router';

export const ADMIN_ROUTES: Routes = [
  {
    path: '',
    loadComponent: () =>
      import('./admin-dashboard/admin-dashboard.component')
        .then(m => m.AdminDashboardComponent)
  },
  {
    path: 'usuarios',
    loadComponent: () =>
      import('./admin-usuarios/admin-usuarios.component')
        .then(m => m.AdminUsuariosComponent)
  }
];
```

---

## 🛡️ Guards — Proteção de Rotas

### O que são?

Guards são como **seguranças na porta de uma boate** — verificam se a pessoa pode entrar antes de deixar passar.

### Tipos de Guards

| Guard | Quando roda | Uso comum |
|-------|-------------|-----------|
| `canActivate` | Antes de entrar na rota | Verificar se está logado |
| `canActivateChild` | Antes de entrar em rotas filhas | Proteger área admin |
| `canDeactivate` | Antes de sair da rota | Perguntar se quer salvar |
| `canMatch` | Antes de tentar casar a rota | Controle avançado |

### Guard de Autenticação (Functional Guard — Angular 15+)

```typescript
// guards/auth.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.estaLogado()) {
    return true;   // ✅ Pode entrar
  }

  // ❌ Redireciona para login
  router.navigate(['/login'], {
    queryParams: { returnUrl: state.url }
  });
  return false;
};
```

### Guard de Permissão (Role Guard)

```typescript
// guards/role.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const roleGuard: CanActivateFn = (route) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  const rolesPermitidas = route.data['roles'] as string[];
  const roleUsuario = authService.getRole();

  if (rolesPermitidas.includes(roleUsuario)) {
    return true;
  }

  router.navigate(['/acesso-negado']);
  return false;
};
```

### Usando Guards nas Rotas

```typescript
// app.routes.ts
export const routes: Routes = [
  { path: 'login', component: LoginComponent },
  {
    path: 'dashboard',
    component: DashboardComponent,
    canActivate: [authGuard]           // ← Precisa estar logado
  },
  {
    path: 'admin',
    component: AdminComponent,
    canActivate: [authGuard, roleGuard], // ← Logado + role admin
    data: { roles: ['admin'] }
  },
  {
    path: 'editor',
    component: EditorComponent,
    canDeactivate: [unsavedChangesGuard] // ← Avisa antes de sair
  }
];
```

### Guard "Tem Certeza?" (canDeactivate)

```typescript
// guards/unsaved-changes.guard.ts
import { CanDeactivateFn } from '@angular/router';

export interface HasUnsavedChanges {
  temAlteracoes(): boolean;
}

export const unsavedChangesGuard: CanDeactivateFn<HasUnsavedChanges> = (component) => {
  if (component.temAlteracoes()) {
    return confirm('Você tem alterações não salvas. Deseja sair?');
  }
  return true;
};
```

---

## 🔌 Interceptors — Middleware HTTP

### O que são?

Interceptors são como um **pedágio na estrada** — toda requisição HTTP passa por eles. Você pode adicionar headers, tratar erros, mostrar loading, etc.

```
Componente → Interceptor 1 → Interceptor 2 → API
           ←               ←               ←
```

### Interceptor de Autenticação (Functional — Angular 15+)

```typescript
// interceptors/auth.interceptor.ts
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';
import { AuthService } from '../services/auth.service';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const token = authService.getToken();

  if (token) {
    // Clona a requisição e adiciona o header de autorização
    const reqComToken = req.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`
      }
    });
    return next(reqComToken);
  }

  return next(req);
};
```

### Interceptor de Loading

```typescript
// interceptors/loading.interceptor.ts
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';
import { finalize } from 'rxjs';
import { LoadingService } from '../services/loading.service';

export const loadingInterceptor: HttpInterceptorFn = (req, next) => {
  const loadingService = inject(LoadingService);
  loadingService.mostrar();

  return next(req).pipe(
    finalize(() => loadingService.esconder())
  );
};
```

### Interceptor de Erro Global

```typescript
// interceptors/error.interceptor.ts
import { HttpInterceptorFn, HttpErrorResponse } from '@angular/common/http';
import { inject } from '@angular/core';
import { catchError, throwError } from 'rxjs';
import { Router } from '@angular/router';

export const errorInterceptor: HttpInterceptorFn = (req, next) => {
  const router = inject(Router);

  return next(req).pipe(
    catchError((error: HttpErrorResponse) => {
      switch (error.status) {
        case 401:
          console.error('Não autorizado! Redirecionando para login...');
          router.navigate(['/login']);
          break;
        case 403:
          console.error('Acesso proibido!');
          router.navigate(['/acesso-negado']);
          break;
        case 404:
          console.error('Recurso não encontrado!');
          break;
        case 500:
          console.error('Erro interno do servidor!');
          break;
        default:
          console.error('Erro desconhecido:', error.message);
      }
      return throwError(() => error);
    })
  );
};
```

### Registrando Interceptors

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { authInterceptor } from './interceptors/auth.interceptor';
import { loadingInterceptor } from './interceptors/loading.interceptor';
import { errorInterceptor } from './interceptors/error.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([
        authInterceptor,     // 1° - Adiciona token
        loadingInterceptor,  // 2° - Mostra/esconde loading
        errorInterceptor,    // 3° - Trata erros
      ])
    ),
  ]
};
```

---

## 📦 Gerenciamento de Estado com Signals

### Estado global com Serviço + Signals

```typescript
// services/carrinho.service.ts
import { Injectable, signal, computed } from '@angular/core';

interface ItemCarrinho {
  id: number;
  nome: string;
  preco: number;
  quantidade: number;
}

@Injectable({ providedIn: 'root' })
export class CarrinhoService {
  // Estado
  private itens = signal<ItemCarrinho[]>([]);

  // Computed (derivados)
  totalItens = computed(() =>
    this.itens().reduce((acc, item) => acc + item.quantidade, 0)
  );

  totalPreco = computed(() =>
    this.itens().reduce((acc, item) => acc + (item.preco * item.quantidade), 0)
  );

  getItens() {
    return this.itens;
  }

  adicionar(produto: { id: number; nome: string; preco: number }): void {
    this.itens.update(itens => {
      const existe = itens.find(i => i.id === produto.id);
      if (existe) {
        return itens.map(i =>
          i.id === produto.id
            ? { ...i, quantidade: i.quantidade + 1 }
            : i
        );
      }
      return [...itens, { ...produto, quantidade: 1 }];
    });
  }

  remover(id: number): void {
    this.itens.update(itens => itens.filter(i => i.id !== id));
  }

  limpar(): void {
    this.itens.set([]);
  }
}
```

```typescript
// componente usando o carrinho
@Component({
  selector: 'app-carrinho',
  standalone: true,
  template: `
    <h2>🛒 Carrinho ({{ carrinhoService.totalItens() }} itens)</h2>
    <ul>
      @for (item of carrinhoService.getItens()(); track item.id) {
        <li>
          {{ item.nome }} — {{ item.quantidade }}x — R$ {{ item.preco * item.quantidade | number:'1.2-2' }}
          <button (click)="carrinhoService.remover(item.id)">🗑️</button>
        </li>
      }
    </ul>
    <p><strong>Total: R$ {{ carrinhoService.totalPreco() | number:'1.2-2' }}</strong></p>
    <button (click)="carrinhoService.limpar()">Limpar carrinho</button>
  `
})
export class CarrinhoComponent {
  constructor(public carrinhoService: CarrinhoService) {}
}
```

---

## 📐 Resolvers — Pré-carregamento de Dados

Carregam dados **antes** de exibir a página:

```typescript
// resolvers/produto.resolver.ts
import { inject } from '@angular/core';
import { ResolveFn } from '@angular/router';
import { ProdutoService } from '../services/produto.service';

export const produtoResolver: ResolveFn<any> = (route) => {
  const produtoService = inject(ProdutoService);
  const id = Number(route.paramMap.get('id'));
  return produtoService.buscarPorId(id);
};
```

```typescript
// Usando na rota
{
  path: 'produto/:id',
  component: ProdutoDetalheComponent,
  resolve: { produto: produtoResolver }
}
```

```typescript
// No componente — dados já carregados!
export class ProdutoDetalheComponent implements OnInit {
  produto: any;

  constructor(private route: ActivatedRoute) {}

  ngOnInit(): void {
    this.produto = this.route.snapshot.data['produto'];
  }
}
```

---

## 🎭 Diretivas Personalizadas

### Diretiva de Atributo

```typescript
// directives/destaque.directive.ts
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appDestaque]',
  standalone: true
})
export class DestaqueDirective {
  @Input() appDestaque: string = 'yellow';

  constructor(private el: ElementRef) {}

  @HostListener('mouseenter')
  aoEntrar(): void {
    this.el.nativeElement.style.backgroundColor = this.appDestaque;
  }

  @HostListener('mouseleave')
  aoSair(): void {
    this.el.nativeElement.style.backgroundColor = '';
  }
}
```

```html
<!-- Uso -->
<p appDestaque>Fica amarelo ao passar o mouse</p>
<p [appDestaque]="'lightblue'">Fica azul claro ao passar o mouse</p>
```

---

## 🧪 Testes Básicos

### Teste de Componente

```typescript
// meu.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { MeuComponent } from './meu.component';

describe('MeuComponent', () => {
  let component: MeuComponent;
  let fixture: ComponentFixture<MeuComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [MeuComponent]  // Standalone component
    }).compileComponents();

    fixture = TestBed.createComponent(MeuComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('deve criar o componente', () => {
    expect(component).toBeTruthy();
  });

  it('deve exibir o título', () => {
    component.titulo = 'Olá!';
    fixture.detectChanges();
    const html = fixture.nativeElement as HTMLElement;
    expect(html.querySelector('h1')?.textContent).toContain('Olá!');
  });
});
```

### Comandos de Teste

```bash
# Rodar todos os testes
ng test

# Rodar testes com coverage
ng test --code-coverage

# Rodar testes uma vez (CI/CD)
ng test --no-watch --browsers=ChromeHeadless
```

---

## ❌ Errado vs ✅ Certo

| ❌ Errado | ✅ Certo | Por quê |
|-----------|---------|---------|
| Carregar tudo de uma vez | Usar lazy loading | Performance |
| Guard como classe (antigo) | Guard como função (funcional) | Padrão moderno |
| Interceptor como classe | Interceptor funcional | Mais simples |
| Guardar token no `localStorage` sem cuidado | Usar `HttpOnly cookies` quando possível | Segurança (XSS) |
| Estado espalhado em vários componentes | Centralizar em um serviço com signals | Manutenibilidade |
| Ignorar testes | Testar pelo menos a lógica crítica | Previne bugs em produção |

---

## 📊 Cola Rápida

```
┌──────────────────────────────────────────────────┐
│            TÓPICOS AVANÇADOS                      │
│                                                  │
│  LAZY LOADING                                    │
│  loadComponent: () => import('./comp')           │
│  loadChildren: () => import('./routes')          │
│                                                  │
│  GUARDS                                          │
│  canActivate: [authGuard]                        │
│  canDeactivate: [unsavedGuard]                   │
│                                                  │
│  INTERCEPTORS                                    │
│  provideHttpClient(withInterceptors([...]))      │
│                                                  │
│  RESOLVERS                                       │
│  resolve: { dados: meuResolver }                 │
│                                                  │
│  SIGNALS                                         │
│  signal() | computed() | effect()                │
└──────────────────────────────────────────────────┘
```

---

[⬅️ Voltar ao Índice](../README.md) | [Anterior: Tópicos Intermediários](05-topicos-intermediarios.md) | [Próximo: Boas Práticas ➡️](07-boas-praticas.md)
