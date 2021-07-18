# Agregar navegación con enrutamiento

Hay nuevos requisitos para la aplicación Tour of Heroes:

- Agregue una vista de _Dashboard_.
- Agregue la capacidad de navegar entre las vistas de _Heroes_ y _Dashboard_.
- Cuando los usuarios hacen clic en el nombre de un héroe en cualquiera de las vistas, navega a una vista detallada del héroe seleccionado.
- Cuando los usuarios hacen clic en un _enlace profundo_ en un correo electrónico, abre la vista de detalles de un héroe en particular.

> Para ver la aplicación de muestra que se describe en esta página, consulte la [ejemplo en vivo](https://angular.io/generated/live-examples/toh-pt1/stackblitz.html)/[ejemplo de descarga](https://angular.io/generated/zips/toh-pt1/toh-pt1.zip).

Cuando haya terminado, los usuarios podrán navegar por la aplicación de esta manera:

![](images/1.png)

---

## Añade el AppRoutingModule

En Angular, la mejor práctica es cargar y configurar el enrutador en un módulo de nivel superior separado que esté dedicado al enrutamiento e importado por la raíz `AppModule`.

Por convención, el nombre de la clase del módulo es `AppRoutingModule` y pertenece `app-routing.module.ts` en la carpeta `src/app`.

Utilice la CLI para generarlo.

```bash
ng generate module app-routing --flat --module=app
```

> `--flat` coloca el archivo en `src/app` en lugar de su propia carpeta.
> `--module=apple` dice a la CLI que lo registre en la matriz `imports` de `AppModule`.

El archivo generado se ve así:

```ts
// src/app/app-routing.module.ts (generated)

import { NgModule } from "@angular/core";
import { CommonModule } from "@angular/common";

@NgModule({
  imports: [CommonModule],
  declarations: [],
})
export class AppRoutingModule {}
```

Reemplácelo con lo siguiente:

```ts
// src/app/app-routing.module.ts (updated)

import { NgModule } from "@angular/core";
import { RouterModule, Routes } from "@angular/router";
import { HeroesComponent } from "./heroes/heroes.component";

const routes: Routes = [{ path: "heroes", component: HeroesComponent }];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

Primero, el archivo `app-routing.module.ts` importa [`RouterModule`](https://angular.io/api/router/RouterModule) y [`Routes`](https://angular.io/api/router/Routes) por lo tanto la aplicación puede tener funcionalidad de enrutamiento. La siguiente importación, `HeroesComponent` le dará al enrutador un lugar adonde ir una vez que configure las rutas.

Tenga en cuenta que las referencias de [`CommonModule`](https://angular.io/api/common/CommonModule) y la matriz `declarations` son innecesarias, por lo que ya no forman parte de `AppRoutingModule`. Las siguientes secciones explican el resto de `AppRoutingModule` con más detalle.

### Rutas

La siguiente parte del archivo es donde configura sus rutas. _Routes_ le indican al enrutador qué vista mostrar cuando un usuario hace clic en un enlace o pega una URL en la barra de direcciones del navegador.

Como `app-routing.module.ts` ya importa `HeroesComponent`, puede usarlo en la matriz `routes`:

```ts
// src/app/app-routing.module.ts

const routes: Routes = [{ path: "heroes", component: HeroesComponent }];
```

Un típico Angular [`Route`](https://angular.io/api/router/Route) tiene dos propiedades:

- `path`: una cadena que coincide con la URL en la barra de direcciones del navegador.
- `component`: el componente que debe crear el enrutador al navegar a esta ruta.

Esto le dice al enrutador que haga coincidir esa URL con `path: 'heroes'` y muestre `HeroesComponent` cuando la URL es algo así como `localhost:4200/heroes`.

### [`RouterModule.forRoot()`](https://angular.io/api/router/RouterModule#forRoot)

Los metadatos de [`@NgModule`](https://angular.io/api/core/NgModule) inicializan el enrutador y lo inician a escuchar los cambios de ubicación del navegador.

La siguiente línea agrega el `RouterModule` a la matriz `imports` de `AppRoutingModule` y lo configura con el `routes` en un paso llamando` RouterModule.forRoot()`:

```ts
// src/app/app-routing.module.ts

imports: [ RouterModule.forRoot(routes) ],
```

> El método se llama `forRoot()` porque configura el enrutador en el nivel raíz de la aplicación. El método `forRoot()` proporciona los proveedores de servicios y las directivas necesarias para el enrutamiento y realiza la navegación inicial en función de la URL del navegador actual.

A continuación, `AppRoutingModule` exporta [`RouterModule`](https://angular.io/api/router/RouterModule) para que esté disponible en toda la aplicación.

```ts
// src/app/app-routing.module.ts (exports array)

exports: [RouterModule];
```

---

## Agregar RouterOutlet

Abra la plantilla `AppComponent` y reemplace el elemento `<app-heroes>` con un elemento [`<router-outlet>`](https://angular.io/api/router/RouterOutlet).

```html
<!-- src/app/app.component.html (router-outlet) -->

<h1>{{title}}</h1>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

La plantilla `AppComponent` ya no necesita `<app-heroes>` porque la aplicación solo mostrará `HeroesComponent` cuando el usuario navegue hacia ella.

El `<router-outlet>` le dice al enrutador dónde mostrar las vistas enrutadas.

> El [`RouterOutlet`](https://angular.io/api/router/RouterOutlet) es una de las directivas de router que se hicieron disponibles al `AppComponent` porque `AppModule` importa `AppRoutingModule` que exportó [`RouterModule`](https://angular.io/api/router/RouterModule). El comando `ng generate` que ejecutó al comienzo de este tutorial agregó esta importación debido al flag `--module=app`. Si ha creado manualmente `app-routing.module.ts` o utilizó una herramienta distinta de la CLI para hacerlo, tendrá que importar `AppRoutingModule` en `app.module.ts` y añadirlo a la matriz `imports` del `NgModule`.

### Intentalo

Debería seguir ejecutando este comando CLI.

```bash
ng serve
```

El navegador debería actualizar y mostrar el título de la aplicación, pero no la lista de héroes.

Mira la barra de direcciones del navegador. La URL termina en `/`. La ruta de acceso a `HeroesComponent` es `/heroes`.

Agregue `/heroes` a la URL en la barra de direcciones del navegador. Debería ver la vista familiar master/detail de héroes.

Elimine `/heroes` de la URL en la barra de direcciones del navegador. El navegador debería actualizar y mostrar el título de la aplicación, pero no la lista de héroes.

---

## Agregar un enlace de navegación ([`routerLink`](https://angular.io/api/router/RouterLink))

Idealmente, los usuarios deberían poder hacer clic en un enlace para navegar en lugar de pegar una URL de ruta en la barra de direcciones.

Agregue un elemento `<nav>` y, dentro de él, un elemento de ancla que, cuando se hace clic, activa la navegación al `HeroesComponent`. La plantilla revisada `AppComponent` se ve así:

```html
<!-- src/app/app.component.html (heroes RouterLink) -->

<h1>{{title}}</h1>
<nav>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

Un atributo `routerLink` se establece en `"/heroes"`, el string con la que el enrutador coincide con la ruta `HeroesComponent`. El [`routerLink`](https://angular.io/api/router/RouterLink) es el selector de la [directiva `RouterLink`](https://angular.io/api/router/RouterLink) que convierte los clics del usuario en navegaciones del enrutador. Es otra de las directivas públicas en [`RouterModule`](https://angular.io/api/router/RouterModule).

El navegador se actualiza y muestra el título de la aplicación y el enlace de héroes, pero no la lista de héroes.

Haga clic en el enlace. La barra de direcciones se actualiza a `/heroes` y aparece la lista de héroes.

> Haga que este y los enlaces de navegación futuros se vean mejor agregando estilos CSS privados a `app.component.css` como se enumeran en la [revisión final del código]() a continuación.

---

## Agregar una vista de dashboard

El enrutamiento tiene más sentido cuando hay varias vistas. Hasta ahora solo existe la vista de los héroes.

Agregue un `DashboardComponent` usando la CLI:

```bash
ng generate component dashboard
```

La CLI genera los archivos `DashboardComponent` y los declara en formato `AppModule`.

Reemplace el contenido del archivo predeterminado en estos tres archivos de la siguiente manera:

```html
<!-- src/app/dashboard/dashboard.component.html -->

<h2>Top Heroes</h2>
<div class="heroes-menu">
  <a *ngFor="let hero of heroes"> {{hero.name}} </a>
</div>
```

```ts
// src/app/dashboard/dashboard.component.ts

import { Component, OnInit } from "@angular/core";
import { Hero } from "../hero";
import { HeroService } from "../hero.service";

@Component({
  selector: "app-dashboard",
  templateUrl: "./dashboard.component.html",
  styleUrls: ["./dashboard.component.css"],
})
export class DashboardComponent implements OnInit {
  heroes: Hero[] = [];

  constructor(private heroService: HeroService) {}

  ngOnInit() {
    this.getHeroes();
  }

  getHeroes(): void {
    this.heroService
      .getHeroes()
      .subscribe((heroes) => (this.heroes = heroes.slice(1, 5)));
  }
}
```

```css
/* src/app/dashboard/dashboard.component.css */

/* DashboardComponent's private CSS styles */

h2 {
  text-align: center;
}

.heroes-menu {
  padding: 0;
  margin: auto;
  max-width: 1000px;

  /* flexbox */
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
  align-content: flex-start;
  align-items: flex-start;
}

a {
  background-color: #3f525c;
  border-radius: 2px;
  padding: 1rem;
  font-size: 1.2rem;
  text-decoration: none;
  display: inline-block;
  color: #fff;
  text-align: center;
  width: 100%;
  min-width: 70px;
  margin: 0.5rem auto;
  box-sizing: border-box;

  /* flexbox */
  order: 0;
  flex: 0 1 auto;
  align-self: auto;
}

@media (min-width: 600px) {
  a {
    width: 18%;
    box-sizing: content-box;
  }
}

a:hover {
  background-color: #000;
}
```

La _plantilla_ presenta una cuadrícula de enlaces de nombres de héroes.

- El repetidor [`*ngFor`](https://angular.io/api/common/NgForOf) crea tantos enlaces como haya en la matriz `heroes` del componente.
- Los enlaces tienen el estilo de bloques de colores de `dashboard.component.css`.
- Los enlaces no van a ninguna parte todavía, pero [lo harán en breve]().

La _clase_ es similar a la clase `HeroesComponent`.

- Define una propiedad de matriz `heroes`.
- El constructor espera que Angular inyecte el `HeroService` en una propiedad privada `heroService`.
- El hook del ciclo de vida `ngOnInit()` llama a `getHeroes()`.

Este `getHeroes()` devuelve la lista dividida de héroes en las posiciones 1 y 5, devolviendo solo cuatro de los mejores héroes (segundo, tercero, cuarto y quinto).

```ts
// src/app/dashboard/dashboard.component.ts

getHeroes(): void {
  this.heroService.getHeroes()
    .subscribe(heroes => this.heroes = heroes.slice(1, 5));
}
```

### Agregar la ruta del dashboard

Para navegar hasta el dashboard, el enrutador necesita una ruta adecuada.

Importe el `DashboardComponent` en el archivo `app-routing-module.ts`.

```ts
// src/app/app-routing.module.ts

import { DashboardComponent } from "./dashboard/dashboard.component";
```

Agregue una ruta a la matriz `routes` que coincida con una ruta de `DashboardComponent`.

```ts
// src/app/app-routing.module.ts

{ path: 'dashboard', component: DashboardComponent },
```

### Agregar una ruta predeterminada

Cuando se inicia la aplicación, la barra de direcciones del navegador apunta a la raíz del sitio web. Eso no coincide con ninguna ruta existente, por lo que el enrutador no navega a ninguna parte. El espacio debajo de `<router-outlet>` está en blanco.

Para que la aplicación navegue hasta el dashboard automáticamente, agregue la siguiente ruta a la matriz `routes`.

```ts
// src/app/app-routing.module.ts

{ path: '', redirectTo: '/dashboard', pathMatch: 'full' },
```

Esta ruta redirige una URL que coincide completamente con la ruta vacía a la ruta cuya ruta es `'/dashboard'`.

Una vez que el navegador se actualiza, el enrutador carga `DashboardComponent` y la barra de direcciones del navegador muestra la URL `/dashboard`.

### Agregar el enlace del dashboard al shell

El usuario debe poder navegar hacia adelante y hacia atrás entre el `DashboardComponent` y el `HeroesComponent` haciendo clic en los enlaces en el área de navegación cerca de la parte superior de la página.

Agregue un enlace de navegación del panel de control a la plantilla `AppComponent` de shell, justo encima del enlace _Heroes_.

```html
<!-- src/app/app.component.html -->

<h1>{{title}}</h1>
<nav>
  <a routerLink="/dashboard">Dashboard</a>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

Después de que el navegador se actualice, puede navegar libremente entre las dos vistas haciendo clic en los enlaces.

---

## Navegando a los detalles del héroe

Los `HeroDetailComponent` muestran detalles de un héroe seleccionado. Por el momento, `HeroDetailComponent` solo es visible en la parte inferior del `HeroesComponent`

El usuario debería poder acceder a estos detalles de tres formas.

- Haciendo clic en un héroe en el tablero.
- Haciendo clic en un héroe de la lista de héroes.
- Pegando una URL de "enlace profundo" en la barra de direcciones del navegador que identifica al héroe a mostrar.

En esta sección, habilitará la navegación en `HeroDetailComponent` y la liberará de `HeroesComponent`.

### Eliminar _detalles de héroe_ de `HeroesComponent`

Cuando el usuario hace clic en un elemento de héroe en `HeroesComponent`, la aplicación debe navegar hasta `HeroDetailComponent`, reemplazando la vista de lista de héroes con la vista de detalles de héroe. La vista de lista de héroes ya no debería mostrar los detalles de los héroes como lo hace ahora.

Abra la plantilla `HeroesComponent` (`heroes/heroes.component.html`) y elimine el elemento `<app-hero-detail>` de la parte inferior.

Hacer clic en un elemento de héroe ahora no hace nada. [Lo solucionará]() poco después de habilitar el enrutamiento al `HeroDetailComponent`.

### Agregar una ruta de _detalle de héroe_

Una URL como `~/detail/11` sería una buena URL para navegar a la vista _Hero Detail_ del héroe cuya `id` es `11`.

Abrir `app-routing.module.ts` e importar `HeroDetailComponent`.

```ts
// src/app/app-routing.module.ts (import HeroDetailComponent)

import { HeroDetailComponent } from "./hero-detail/hero-detail.component";
```

Luego, agregue una ruta _parametrizada_ a la matriz `routes` que coincida con el patrón de ruta de la vista _hero detail_.

```ts
// src/app/app-routing.module.ts

{ path: 'detail/:id', component: HeroDetailComponent },
```

El signo de dos puntos (:) en `path` indica que `:id` es un marcador de posición para un `id` específico héroe.

En este punto, todas las rutas de aplicación están en su lugar.

```ts
// src/app/app-routing.module.ts (all routes)

const routes: Routes = [
  { path: "", redirectTo: "/dashboard", pathMatch: "full" },
  { path: "dashboard", component: DashboardComponent },
  { path: "detail/:id", component: HeroDetailComponent },
  { path: "heroes", component: HeroesComponent },
];
```

### Enlaces de héroe de DashboardComponent

Los enlaces de héroe de `DashboardComponent` no hacen nada por el momento.

Ahora que el enrutador tiene una ruta hacia `HeroDetailComponent`, corrija los enlaces del héroe del tablero para navegar usando la ruta del tablero _parametrizada_.

```ts
// src/app/dashboard/dashboard.component.html (hero links)

<a *ngFor="let hero of heroes"
  routerLink="/detail/{{hero.id}}">
  {{hero.name}}
</a>
```

Está utilizando el [enlace de interpolación](https://angular.io/guide/interpolation) de Angular dentro del repetidor [`*ngFor`](https://angular.io/api/common/NgForOf) para insertar las iteraciones actuales de `hero.id` en cada uno de los `routerLink`.

### Enlaces de héroe de `HeroesComponent`

Los elementos de héroe en `HeroesComponent` son elementos `<li>` cuyos eventos de clic están vinculados al método `onSelect()` del componente.

```html
<!-- src/app/heroes/heroes.component.html (list with onSelect) -->

<ul class="heroes">
  <li
    *ngFor="let hero of heroes"
    [class.selected]="hero === selectedHero"
    (click)="onSelect(hero)"
  >
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
```

Limpie el `<li>` para que quede solo su [`*ngFor`](https://angular.io/api/common/NgForOf), envuelva la insignia y el nombre en un elemento de anclaje (`<a>`) y agregue un atributo [`routerLink`](https://angular.io/api/router/RouterLink) al ancla que sea el mismo que en la plantilla del dashboard.

```html
<!-- src/app/heroes/heroes.component.html (list with links) -->

<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <a routerLink="/detail/{{hero.id}}">
      <span class="badge">{{hero.id}}</span> {{hero.name}}
    </a>
  </li>
</ul>
```

Tendrá que arreglar la hoja de estilo privada (`heroes.component.css`) para que la lista se vea como antes. Los estilos revisados ​​se encuentran en la [revisión final del código]() al final de esta guía.

### Eliminar código muerto (opcional)

Si bien la clase `HeroesComponent` todavía funciona, el método `onSelect()` y la propiedad `selectedHero` ya no se utilizan.

Es bueno poner en orden y te lo agradecerás más tarde. Aquí está la clase después de podar el código muerto.

```ts
// src/app/heroes/heroes.component.ts (cleaned up)

export class HeroesComponent implements OnInit {
  heroes: Hero[] = [];

  constructor(private heroService: HeroService) {}

  ngOnInit() {
    this.getHeroes();
  }

  getHeroes(): void {
    this.heroService.getHeroes().subscribe((heroes) => (this.heroes = heroes));
  }
}
```

---

## `HeroDetailComponent` enrutable

Anteriormente, el padre `HeroesComponent` establecía la propiedad `HeroDetailComponent.hero` y `HeroDetailComponent` mostraba el héroe.

`HeroesComponent` ya no hace eso. Ahora el enrutador crea el `HeroDetailComponent` en respuesta a una URL como `~/detail/11`.

El `HeroDetailComponent` necesita una nueva forma de obtener el héroe-a-pantalla. Esta sección explica lo siguiente:

- Obtén la ruta que lo creó
- Extrae el `id` de la ruta
- Adquirir el héroe con el `id` del servidor usando el `HeroService`

Agregue las siguientes importaciones:

```ts
// src/app/hero-detail/hero-detail.component.ts

import { ActivatedRoute } from "@angular/router";
import { Location } from "@angular/common";

import { HeroService } from "../hero.service";
```

Inyectar los servicios [`ActivatedRoute`](https://angular.io/api/router/ActivatedRoute), `HeroService` y [`Location`](https://angular.io/api/common/Location) en el constructor, el salvando sus valores en los campos privados:

```ts
// src/app/hero-detail/hero-detail.component.ts

constructor(
  private route: ActivatedRoute,
  private heroService: HeroService,
  private location: Location
) {}
```

El `ActivatedRoute` guarda información sobre la ruta a esta instancia del `HeroDetailComponent`. Este componente está interesado en los parámetros de la ruta extraídos de la URL. El parámetro "id" es el `id` del héroe para mostrar.

El `HeroService` obtiene datos del héroe desde el servidor remoto y este componente utilizara para conseguir el héroe-a-pantalla.

`location` es un servicio de Angular para interactuar con el navegador. Lo usará [más tarde]() para volver a la vista que navegó aquí.

### Extraer el parámetro `id` de ruta

En el [hook ciclo de vida](https://angular.io/guide/lifecycle-hooks#oninit) `ngOnInit()`, llame a `getHero()` y defínalo de la siguiente manera.

```ts
// src/app/hero-detail/hero-detail.component.ts

ngOnInit(): void {
  this.getHero();
}

getHero(): void {
  const id = Number(this.route.snapshot.paramMap.get('id'));
  this.heroService.getHero(id)
    .subscribe(hero => this.hero = hero);
}
```

El `route.snapshot` es una imagen estática de la información de ruta poco después de que el componente fue creado.

El `paramMap` es un diccionario de valores de parámetros de ruta extraídos de la URL. La clave `"id"` devuelve la `id` del héroe a buscar.

Los parámetros de ruta son siempre strings. La función `Number` de JavaScript convierte el string en un número, que es lo que `id` de un héroe debería ser.

El navegador se actualiza y la aplicación se bloquea con un error del compilador. `HeroService` no tiene un método `getHero()`. Agréguelo ahora.

### Agregar `HeroService.getHero()`

Abra `HeroService` y agregue el siguiente método `getHero()` con el id después del método `getHeroes()`:

```ts
// src/app/hero.service.ts (getHero)

getHero(id: number): Observable<Hero> {
  // For now, assume that a hero with the specified `id` always exists.
  // Error handling will be added in the next step of the tutorial.
  const hero = HEROES.find(h => h.id === id)!;
  this.messageService.add(`HeroService: fetched hero id=${id}`);
  return of(hero);
}
```

> Tenga en cuenta las comillas invertidas (\`) que definen una [_plantilla literal_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) JavaScript para incrustar el `id`.

Como `getHeroes()`, `getHero()` tiene una firma asincrónica. Devuelve un _héroe simulado_ como un `Observable`, usando la función RxJS `of()`.

Podrá volver a implementar `getHero()` como una solicitud `Http` real sin tener que cambiar el `HeroDetailComponent` que lo llama.

#### **Intentalo**

El navegador se actualiza y la aplicación vuelve a funcionar. Puedes hacer clic en un héroe en el tablero o en la lista de héroes y navegar a la vista de detalles de ese héroe.

Si pega `localhost:4200/detail/11` en la barra de direcciones del navegador, el enrutador navega a la vista detallada del héroe con `id: 11`, "Dr Nice".

### Encuentra el camino de regreso

Al hacer clic en el botón Atrás del navegador, puede volver a la lista de héroes o la vista del dashboard, según el que lo envió a la vista de detalles.

Sería bueno tener un botón en la vista `HeroDetail` que pueda hacer eso.

Agregue un botón _de retroceso_ en la parte inferior de la plantilla del componente y vincúlelo al método `goBack()` del componente .

```html
<!-- src/app/hero-detail/hero-detail.component.html (back button) -->

<button (click)="goBack()">go back</button>
```

Agregue un _método_ `goBack()` a la clase de componente que navegue hacia atrás un paso en la pila del historial del navegador utilizando el servicio [`Location`](https://angular.io/api/common/Location) que [inyectó anteriormente]().

```ts
// src/app/hero-detail/hero-detail.component.ts (goBack)

goBack(): void {
  this.location.back();
}
```

Actualice el navegador y comience a hacer clic. Los usuarios pueden navegar por la aplicación, desde el dashboard hasta los detalles de los héroes y viceversa, desde la lista de héroes hasta el mini detalle y los detalles de los héroes y de regreso a los héroes nuevamente.

Los detalles se verán mejor cuando agregue los estilos CSS privados a los `hero-detail.component.css` que se enumeran en una de las pestañas de ["revisión de código final"]() a continuación.

---

## Revisión final del código

Aquí están los archivos de código discutidos en esta página.

### `AppRoutingModule`, `AppModule` y `HeroService`

```ts
// src/app/app-routing.module.ts

import { NgModule } from "@angular/core";
import { RouterModule, Routes } from "@angular/router";

import { DashboardComponent } from "./dashboard/dashboard.component";
import { HeroesComponent } from "./heroes/heroes.component";
import { HeroDetailComponent } from "./hero-detail/hero-detail.component";

const routes: Routes = [
  { path: "", redirectTo: "/dashboard", pathMatch: "full" },
  { path: "dashboard", component: DashboardComponent },
  { path: "detail/:id", component: HeroDetailComponent },
  { path: "heroes", component: HeroesComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

```ts
// src/app/app.module.ts

import { NgModule } from "@angular/core";
import { BrowserModule } from "@angular/platform-browser";
import { FormsModule } from "@angular/forms";

import { AppComponent } from "./app.component";
import { DashboardComponent } from "./dashboard/dashboard.component";
import { HeroDetailComponent } from "./hero-detail/hero-detail.component";
import { HeroesComponent } from "./heroes/heroes.component";
import { MessagesComponent } from "./messages/messages.component";

import { AppRoutingModule } from "./app-routing.module";

@NgModule({
  imports: [BrowserModule, FormsModule, AppRoutingModule],
  declarations: [
    AppComponent,
    DashboardComponent,
    HeroesComponent,
    HeroDetailComponent,
    MessagesComponent,
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

```ts
// src/app/hero.service.ts

import { Injectable } from "@angular/core";

import { Observable, of } from "rxjs";

import { Hero } from "./hero";
import { HEROES } from "./mock-heroes";
import { MessageService } from "./message.service";

@Injectable({ providedIn: "root" })
export class HeroService {
  constructor(private messageService: MessageService) {}

  getHeroes(): Observable<Hero[]> {
    const heroes = of(HEROES);
    this.messageService.add("HeroService: fetched heroes");
    return heroes;
  }

  getHero(id: number): Observable<Hero> {
    // For now, assume that a hero with the specified `id` always exists.
    // Error handling will be added in the next step of the tutorial.
    const hero = HEROES.find((h) => h.id === id)!;
    this.messageService.add(`HeroService: fetched hero id=${id}`);
    return of(hero);
  }
}
```

### `AppComponent`

```html
<!-- src/app/app.component.html -->

<h1>{{title}}</h1>
<nav>
  <a routerLink="/dashboard">Dashboard</a>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

```css
/* src/app/app.component.css */

/* AppComponent's private CSS styles */
h1 {
  margin-bottom: 0;
}
nav a {
  padding: 1rem;
  text-decoration: none;
  margin-top: 10px;
  display: inline-block;
  background-color: #e8e8e8;
  color: #3d3d3d;
  border-radius: 4px;
}

nav a:hover {
  color: white;
  background-color: #42545c;
}
nav a.active {
  background-color: black;
}
```

### `DashboardComponent`

```html
<!-- src/app/dashboard/dashboard.component.html -->

<h2>Top Heroes</h2>
<div class="heroes-menu">
  <a *ngFor="let hero of heroes" routerLink="/detail/{{hero.id}}">
    {{hero.name}}
  </a>
</div>
```

```ts
// src/app/dashboard/dashboard.component.ts

import { Component, OnInit } from "@angular/core";
import { Hero } from "../hero";
import { HeroService } from "../hero.service";

@Component({
  selector: "app-dashboard",
  templateUrl: "./dashboard.component.html",
  styleUrls: ["./dashboard.component.css"],
})
export class DashboardComponent implements OnInit {
  heroes: Hero[] = [];

  constructor(private heroService: HeroService) {}

  ngOnInit() {
    this.getHeroes();
  }

  getHeroes(): void {
    this.heroService
      .getHeroes()
      .subscribe((heroes) => (this.heroes = heroes.slice(1, 5)));
  }
}
```

```css
/* src/app/dashboard/dashboard.component.css */

/* DashboardComponent's private CSS styles */

h2 {
  text-align: center;
}

.heroes-menu {
  padding: 0;
  margin: auto;
  max-width: 1000px;

  /* flexbox */
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
  align-content: flex-start;
  align-items: flex-start;
}

a {
  background-color: #3f525c;
  border-radius: 2px;
  padding: 1rem;
  font-size: 1.2rem;
  text-decoration: none;
  display: inline-block;
  color: #fff;
  text-align: center;
  width: 100%;
  min-width: 70px;
  margin: 0.5rem auto;
  box-sizing: border-box;

  /* flexbox */
  order: 0;
  flex: 0 1 auto;
  align-self: auto;
}

@media (min-width: 600px) {
  a {
    width: 18%;
    box-sizing: content-box;
  }
}

a:hover {
  background-color: #000;
}
```

### `HeroesComponent`

```html
<!-- src/app/heroes/heroes.component.html -->

<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <a routerLink="/detail/{{hero.id}}">
      <span class="badge">{{hero.id}}</span> {{hero.name}}
    </a>
  </li>
</ul>
```

```ts
// src/app/heroes/heroes.component.ts

import { Component, OnInit } from "@angular/core";

import { Hero } from "../hero";
import { HeroService } from "../hero.service";

@Component({
  selector: "app-heroes",
  templateUrl: "./heroes.component.html",
  styleUrls: ["./heroes.component.css"],
})
export class HeroesComponent implements OnInit {
  heroes: Hero[] = [];

  constructor(private heroService: HeroService) {}

  ngOnInit() {
    this.getHeroes();
  }

  getHeroes(): void {
    this.heroService.getHeroes().subscribe((heroes) => (this.heroes = heroes));
  }
}
```

```css
/* src/app/heroes/heroes.component.css */

/* HeroesComponent's private CSS styles */
.heroes {
  margin: 0 0 2em 0;
  list-style-type: none;
  padding: 0;
  width: 15em;
}
.heroes li {
  position: relative;
  cursor: pointer;
}

.heroes li:hover {
  left: 0.1em;
}

.heroes a {
  color: #333;
  text-decoration: none;
  background-color: #eee;
  margin: 0.5em;
  padding: 0.3em 0;
  height: 1.6em;
  border-radius: 4px;
  display: block;
  width: 100%;
}

.heroes a:hover {
  color: #2c3a41;
  background-color: #e6e6e6;
}

.heroes a:active {
  background-color: #525252;
  color: #fafafa;
}

.heroes .badge {
  display: inline-block;
  font-size: small;
  color: white;
  padding: 0.8em 0.7em 0 0.7em;
  background-color: #405061;
  line-height: 1em;
  position: relative;
  left: -1px;
  top: -4px;
  height: 1.8em;
  min-width: 16px;
  text-align: right;
  margin-right: 0.8em;
  border-radius: 4px 0 0 4px;
}
```

### `HeroDetailComponent`

```html
<!-- src/app/hero-detail/hero-detail.component.html -->

<div *ngIf="hero">
  <h2>{{hero.name | uppercase}} Details</h2>
  <div><span>id: </span>{{hero.id}}</div>
  <div>
    <label for="hero-name">Hero name: </label>
    <input id="hero-name" [(ngModel)]="hero.name" placeholder="Hero name" />
  </div>
  <button (click)="goBack()">go back</button>
</div>
```

```ts
// src/app/hero-detail/hero-detail.component.ts

import { Component, OnInit } from "@angular/core";
import { ActivatedRoute } from "@angular/router";
import { Location } from "@angular/common";

import { Hero } from "../hero";
import { HeroService } from "../hero.service";

@Component({
  selector: "app-hero-detail",
  templateUrl: "./hero-detail.component.html",
  styleUrls: ["./hero-detail.component.css"],
})
export class HeroDetailComponent implements OnInit {
  hero: Hero | undefined;

  constructor(
    private route: ActivatedRoute,
    private heroService: HeroService,
    private location: Location
  ) {}

  ngOnInit(): void {
    this.getHero();
  }

  getHero(): void {
    const id = Number(this.route.snapshot.paramMap.get("id"));
    this.heroService.getHero(id).subscribe((hero) => (this.hero = hero));
  }

  goBack(): void {
    this.location.back();
  }
}
```

```css
/* src/app/hero-detail/hero-detail.component.css */

/* HeroDetailComponent's private CSS styles */
label {
  color: #435960;
  font-weight: bold;
}
input {
  font-size: 1em;
  padding: 0.5rem;
}
button {
  margin-top: 20px;
  background-color: #eee;
  padding: 1rem;
  border-radius: 4px;
  font-size: 1rem;
}
button:hover {
  background-color: #cfd8dc;
}
button:disabled {
  background-color: #eee;
  color: #ccc;
  cursor: auto;
}
```

---

## Resumen

- Agregó el enrutador de Angular para navegar entre diferentes componentes.
- Convirtió el `AppComponent` en un shell de navegación con enlaces `<a>` y un [`<router-outlet>`](https://angular.io/api/router/RouterOutlet).
- Configuró el enrutador en un `AppRoutingModule`
- Definió rutas, una ruta de redireccionamiento y una ruta parametrizada.
- Usó la directiva [`routerLink`](https://angular.io/api/router/RouterLink) en elementos de anclaje.
- Refactorizó una vista master/detail estrechamente acoplada en una vista de detalle enrutada.
- Usó parámetros de enlace del enrutador para navegar a la vista detallada de un héroe seleccionado por el usuario.
- Compartiste `HeroService` entre varios componentes.
