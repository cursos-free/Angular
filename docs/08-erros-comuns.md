[⬅️ Voltar ao Índice](../README.md) | [Anterior: Boas Práticas](07-boas-praticas.md)

---

# 🐛 Erros Comuns e Como Resolver

Todo mundo erra — faz parte do aprendizado! Aqui estão os erros mais comuns que você vai encontrar no Angular, com explicações simples e soluções diretas.

---

## 🔴 Erros de Compilação

### 1. `NG8001: 'app-meu-componente' is not a known element`

**O que significa:** O Angular não encontra o componente que você está tentando usar.

**Causa mais comum:** Esqueceu de importar o componente.

```typescript
// ❌ Errado: usando sem importar
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [],  // ← Faltou adicionar aqui!
  template: `<app-header></app-header>`
})
export class AppComponent { }

// ✅ Certo: componente importado
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [HeaderComponent],  // ← Importado!
  template: `<app-header></app-header>`
})
export class AppComponent { }
```

**Checklist de solução:**
- [ ] O componente está no array `imports` do componente pai?
- [ ] O nome do `selector` está correto?
- [ ] O componente tem `standalone: true`?
- [ ] O import no topo do arquivo está correto?

---

### 2. `NG8002: Can't bind to 'ngModel' since it isn't a known property`

**O que significa:** Você está usando `[(ngModel)]` sem importar o `FormsModule`.

```typescript
// ❌ Errado
@Component({
  standalone: true,
  imports: [],  // Faltou FormsModule!
  template: `<input [(ngModel)]="nome">`
})

// ✅ Certo
import { FormsModule } from '@angular/forms';

@Component({
  standalone: true,
  imports: [FormsModule],
  template: `<input [(ngModel)]="nome">`
})
```

---

### 3. `NG0303: Can't bind to 'formGroup' since it isn't a known property`

**O que significa:** Está usando Reactive Forms sem importar `ReactiveFormsModule`.

```typescript
// ❌ Errado
@Component({
  standalone: true,
  imports: [],
  template: `<form [formGroup]="meuForm">...</form>`
})

// ✅ Certo
import { ReactiveFormsModule } from '@angular/forms';

@Component({
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `<form [formGroup]="meuForm">...</form>`
})
```

---

### 4. `NG0100: ExpressionChangedAfterItHasBeenCheckedError`

**O que significa:** Um valor mudou depois que o Angular já verificou ele. É como mudar a resposta da prova depois que o professor já corrigiu.

**Causa comum:** Mudar valores no `ngAfterViewInit` ou em getters do template.

```typescript
// ❌ Errado: muda valor depois da checagem
export class MeuComponent implements AfterViewInit {
  titulo = 'Olá';

  ngAfterViewInit(): void {
    this.titulo = 'Outro valor';  // 🔴 Erro!
  }
}

// ✅ Certo: use setTimeout ou ChangeDetectorRef
import { ChangeDetectorRef } from '@angular/core';

export class MeuComponent implements AfterViewInit {
  titulo = 'Olá';

  constructor(private cdr: ChangeDetectorRef) {}

  ngAfterViewInit(): void {
    this.titulo = 'Outro valor';
    this.cdr.detectChanges();  // Avisa o Angular da mudança
  }
}

// ✅ Melhor ainda: use signals!
export class MeuComponent {
  titulo = signal('Olá');
  // Signals não causam esse problema
}
```

---

### 5. `TypeError: Cannot read properties of undefined (reading 'nome')`

**O que significa:** Está tentando acessar uma propriedade de algo que é `undefined`.

```html
<!-- ❌ Errado: usuario pode ser undefined antes de carregar -->
<p>{{ usuario.nome }}</p>

<!-- ✅ Certo: verificar antes de acessar -->
@if (usuario) {
  <p>{{ usuario.nome }}</p>
}

<!-- ✅ Também funciona: operador ?. (optional chaining) -->
<p>{{ usuario?.nome }}</p>

<!-- ✅ Com fallback -->
<p>{{ usuario?.nome ?? 'Carregando...' }}</p>
```

---

## 🟡 Erros de Execução (Runtime)

### 6. `NullInjectorError: No provider for HttpClient`

**O que significa:** O `HttpClient` não foi configurado.

```typescript
// ❌ Errado: sem provideHttpClient
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    // Faltou HttpClient!
  ]
};

// ✅ Certo
import { provideHttpClient } from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient(),  // ← Adicionado!
  ]
};
```

---

### 7. `CORS Error: Access-Control-Allow-Origin`

**O que significa:** O navegador bloqueou a requisição porque a API não aceita requisições do seu domínio.

```
Access to XMLHttpRequest at 'http://api.exemplo.com/dados'
from origin 'http://localhost:4200' has been blocked by CORS policy.
```

**Soluções:**

```typescript
// ✅ Solução 1: Proxy no Angular (para desenvolvimento)
// Crie o arquivo proxy.conf.json na raiz do projeto:
```

```json
// proxy.conf.json
{
  "/api": {
    "target": "http://localhost:3000",
    "secure": false,
    "changeOrigin": true
  }
}
```

```bash
# Rode com o proxy
ng serve --proxy-config proxy.conf.json
```

```typescript
// No serviço, use caminho relativo:
// ❌ http.get('http://localhost:3000/api/dados')
// ✅ http.get('/api/dados')
```

```
// ✅ Solução 2: Configurar CORS no back-end (a solução real)
// Peça ao time de back-end para liberar o CORS.
```

---

### 8. `ERROR: Uncaught (in promise): Error: NG04002: Cannot match any routes`

**O que significa:** O usuário acessou uma URL que não existe nas suas rotas.

```typescript
// ❌ Errado: sem rota coringa
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'sobre', component: SobreComponent },
  // Se acessar /xyz → ERRO!
];

// ✅ Certo: sempre adicione uma rota coringa no final
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'sobre', component: SobreComponent },
  { path: '**', redirectTo: '' },          // ← Redireciona para home
  // OU
  { path: '**', component: NotFoundComponent }, // ← Página 404
];
```

---

### 9. Subscription não é cancelada (Vazamento de Memória)

**Sintomas:** App ficando lenta com o tempo, requisições HTTP duplicadas.

```typescript
// ❌ Errado: nunca cancela
export class MeuComponent implements OnInit {
  ngOnInit(): void {
    interval(1000).subscribe(n => {
      console.log(n);  // Continua rodando PARA SEMPRE, mesmo saindo da página
    });
  }
}

// ✅ Certo: cancela com takeUntilDestroyed
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

export class MeuComponent {
  private destroyRef = inject(DestroyRef);

  ngOnInit(): void {
    interval(1000).pipe(
      takeUntilDestroyed(this.destroyRef)
    ).subscribe(n => {
      console.log(n);  // Para quando o componente é destruído
    });
  }
}

// ✅ Também certo: usando async pipe (cancela automaticamente!)
@Component({
  template: `<p>{{ contador$ | async }}</p>`
})
export class MeuComponent {
  contador$ = interval(1000);
}
```

---

## 🟠 Erros do Angular CLI

### 10. `ng: command not found` / `ng não é reconhecido`

```bash
# ✅ Reinstalar globalmente
npm install -g @angular/cli

# Se no Linux/Mac e der Permission denied:
sudo npm install -g @angular/cli

# Verificar
ng version
```

---

### 11. `npm ERR! ERESOLVE could not resolve` (Conflito de dependências)

```bash
# ✅ Tente com --legacy-peer-deps
npm install --legacy-peer-deps

# Ou força a instalação
npm install --force

# Último recurso: limpe o cache e reinstale
rm -rf node_modules package-lock.json
npm cache clean --force
npm install
```

---

### 12. `Error: ENOSPC: System limit for number of file watchers reached` (Linux)

```bash
# ✅ Aumente o limite de watchers
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

---

## 🔵 Erros de Lógica (Os Mais Traiçoeiros)

### 13. Modificar array/objeto sem criar nova referência

```typescript
// ❌ Errado: muta o array (OnPush não detecta)
this.itens.push(novoItem);

// ✅ Certo: cria novo array
this.itens = [...this.itens, novoItem];

// ❌ Errado: muta o objeto
this.usuario.nome = 'Novo';

// ✅ Certo: cria novo objeto
this.usuario = { ...this.usuario, nome: 'Novo' };

// ✅ Com signals:
this.itens.update(lista => [...lista, novoItem]);
```

---

### 14. Async/Await vs Observables

```typescript
// ❌ Confuso: misturando Promises com Observables sem cuidado
async ngOnInit() {
  const dados = await this.http.get('/api/dados');  // 🔴 http.get retorna Observable, não Promise!
}

// ✅ Certo com Observable
ngOnInit(): void {
  this.http.get('/api/dados').subscribe(dados => {
    this.dados = dados;
  });
}

// ✅ Certo com await (se precisar)
async ngOnInit() {
  const dados = await firstValueFrom(this.http.get('/api/dados'));
  this.dados = dados;
}
```

---

### 15. Chamar API dentro de um `@for`

```typescript
// ❌ Errado: N+1 requests!
@Component({
  template: `
    @for (userId of userIds; track userId) {
      <!-- Isso faz uma request para CADA item! -->
      <app-user [user]="getUser(userId)"></app-user>
    }
  `
})
export class ListaComponent {
  getUser(id: number) {
    return this.http.get(`/api/users/${id}`);  // 🔴 Chamado a cada detecção!
  }
}

// ✅ Certo: carregue todos os dados antes
export class ListaComponent implements OnInit {
  usuarios: Usuario[] = [];

  ngOnInit(): void {
    this.usuarioService.listarTodos().subscribe(users => {
      this.usuarios = users;
    });
  }
}
```

---

## 📊 Guia Rápido de Diagnóstico

| Sintoma | Possível Causa | Solução |
|---------|---------------|---------|
| "is not a known element" | Componente não importado | Adicionar no `imports[]` |
| "No provider for X" | Serviço não configurado | `providedIn: 'root'` ou `providers[]` |
| "Cannot bind to 'ngModel'" | FormsModule faltando | Importar `FormsModule` |
| Dados não aparecem | Esqueceu `subscribe()` | Adicionar `.subscribe()` ou `\| async` |
| App lenta com o tempo | Subscription não cancelada | `takeUntilDestroyed()` ou `async` pipe |
| CORS Error | Back-end não libera CORS | `proxy.conf.json` ou config no back-end |
| "Cannot match any routes" | Rota não definida | Adicionar `{ path: '**' }` |
| Mudança não reflete na tela | OnPush + mutação | Criar nova referência |
| Erro só em produção | Compilação AOT mais rígida | Rodar `ng build` para encontrar erros |
| Tela branca sem erro | Erro no bootstrap | Ver console do navegador (F12) |

---

## 🔍 Ferramentas de Debug

### Console do Navegador (F12)

```typescript
// Adicione temporariamente para debug
console.log('Valor:', this.meuValor);
console.table(this.listaObjetos);         // Exibe como tabela
console.group('Grupo de Logs');
console.log('Log 1');
console.log('Log 2');
console.groupEnd();
```

### Angular DevTools

Instale a extensão **Angular DevTools** no Chrome/Edge para:
- Ver a árvore de componentes
- Inspecionar propriedades dos componentes
- Verificar performance
- Depurar change detection

### JSON Pipe para Debug

```html
<!-- Mostra o objeto inteiro no HTML (ótimo para debug!) -->
<pre>{{ meuObjeto | json }}</pre>

<!-- Mostra o formulário completo -->
<pre>{{ meuForm.value | json }}</pre>
<pre>Válido: {{ meuForm.valid }}</pre>
<pre>Erros: {{ meuForm.errors | json }}</pre>
```

### Breakpoints no VS Code

1. Abra o arquivo `.ts`
2. Clique na margem esquerda (bolinha vermelha) para adicionar breakpoint
3. Pressione **F5** e selecione **Chrome** com Angular DevServer
4. A execução para no breakpoint — inspecione variáveis!

---

## 🆘 Onde Pedir Ajuda

1. **Stack Overflow em Português**: [pt.stackoverflow.com/questions/tagged/angular](https://pt.stackoverflow.com/questions/tagged/angular)
2. **GitHub Issues do Angular**: [github.com/angular/angular/issues](https://github.com/angular/angular/issues)
3. **Discord Angular**: [discord.gg/angular](https://discord.gg/angular)
4. **Telegram Angular Brasil**: [t.me/AngularBrasil](https://t.me/AngularBrasil)

### 📝 Como perguntar bem:

```
✅ Boa pergunta:
"Estou usando Angular 17 standalone components. Ao usar [(ngModel)]
em um input, recebo o erro NG8002. Já importei FormsModule no
componente. Aqui está meu código: [código]"

❌ Pergunta ruim:
"Não funciona, ajuda!"
```

---

## 📊 Cola Rápida

```
┌──────────────────────────────────────────────────┐
│          ERROS MAIS COMUNS                        │
│                                                  │
│  "not a known element"   → imports[] do comp.    │
│  "No provider"           → providedIn: 'root'    │
│  "Can't bind ngModel"    → FormsModule           │
│  "Can't bind formGroup"  → ReactiveFormsModule   │
│  ExpressionChanged...    → signals ou cdr        │
│  CORS Error              → proxy.conf.json       │
│  Cannot match routes     → path: '**'            │
│  Dados não aparecem      → subscribe() ou async  │
│  App lenta               → cancelar subscriptions│
│  ng not found            → npm i -g @angular/cli │
│                                                  │
│  DEBUG: console.log | json pipe | DevTools       │
└──────────────────────────────────────────────────┘
```

---

[⬅️ Voltar ao Índice](../README.md) | [Anterior: Boas Práticas](07-boas-praticas.md)
