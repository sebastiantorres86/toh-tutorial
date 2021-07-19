# Agregar servicios

El componente `HeroesComponent` de Tour of Heroes está obteniendo y mostrando datos falsos.

Después de la refactorización en este tutorial, `HeroesComponent` se simplificará y se centrará en apoyar la vista. También será más fácil realizar pruebas unitarias con un servicio simulado.

> Para ver la aplicación de muestra que se describe en esta página, consulte el [ejemplo en vivo](https://angular.io/generated/live-examples/toh-pt2/stackblitz.html)/[ejemplo de descarga](https://angular.io/generated/zips/toh-pt2/toh-pt2.zip).

---

## Por que servicios

Los componentes no deben buscar o guardar datos directamente y ciertamente no deben presentar datos falsos a sabiendas. Deben centrarse en presentar datos y delegar el acceso a los datos a un servicio.

En este tutorial, creará un archivo `HeroService` que todas las clases de la aplicación pueden usar para obtener héroes. En lugar de crear ese servicio con la [palabra clave `new`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new), dependerá de la [_inyección de dependencia_](https://angular.io/guide/dependency-injection) de Angular para inyectarla en el constructor de `HeroesComponent`.

Los servicios son una excelente manera de compartir información entre clases _que no se conocen entre sí_. Creará un `MessageService` y lo inyectará en dos lugares.

1. Inyectará en HeroService, que usa el servicio para enviar un mensaje.
2. Inyectará en MessagesComponent, que muestra ese mensaje y también muestra la ID cuando el usuario hace clic en un héroe.

---

## Crea el HeroService

Usando la CLI de Angular, cree un servicio llamado `hero`.

```bash
ng generate service hero
```

El comando genera un esqueleto de clase `HeroService` en `src/app/hero.service.ts` de la siguiente manera:

```ts
// src/app/hero.service.ts (new service)

import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class HeroService {
  constructor() {}
}
```

### servicios [`@Injectable()`](https://angular.io/api/core/Injectable)

Observe que el nuevo servicio importa el símbolo [`Injectable`](https://angular.io/api/core/Injectable) de Angular y anota la clase con el decorador [`@Injectable()`](https://angular.io/api/core/Injectable). Esto marca a la clase como una que participa en el _sistema de inyección de dependencia_. La clase `HeroService` proporcionará un servicio inyectable y también puede tener sus propias dependencias inyectadas. Aún no tiene dependencias, pero [pronto las tendrá]().

El decorador [`@Injectable()`](https://angular.io/api/core/Injectable) acepta un objeto de metadatos para el servicio, de la misma manera que lo hizo el decorador [`@Component()`](https://angular.io/api/core/Component) para sus clases de componentes.

### Obtener datos de héroe

El `HeroService` podría obtener los datos de héroe desde cualquier lugar -un servicio web, almacenamiento local, o una fuente de datos simulada.

Eliminar el acceso a los datos de los componentes significa que puede cambiar de opinión sobre la implementación en cualquier momento, sin tocar ningún componente. No saben cómo funciona el servicio.

La implementación en este tutorial continuará ofreciendo _héroes simulados_.

Importe el `Hero` y `HEROES`.

```ts
// src/app/hero.service.ts

import { Hero } from "./hero";
import { HEROES } from "./mock-heroes";
```

Agrega un método `getHeroes` para devolver los _héroes simulados_.

```ts
// src/app/hero.service.ts

getHeroes(): Hero[] {
  return HEROES;
}
```

---

## Proporcionar el HeroService

Debe poner a `HeroService` a disposición del sistema de inyección de dependencias antes de que Angular pueda _inyectarlo_ en el `HeroesComponent` registrando un _proveedor_. Un proveedor es algo que puede crear o entregar un servicio; en este caso, crea una instancia de la clase `HeroService` para proporcionar el servicio.

Para asegurarse de que el `HeroService` puede brindar este servicio, regístrelo con el _inyector_, que es el objeto que se encarga de elegir e inyectar el proveedor donde la aplicación lo requiera.

De forma predeterminada, el comando Angular CLI `ng generate service` registra un proveedor con el _inyector raíz_ para su servicio al incluir metadatos del proveedor, que es `providedIn: 'root'` en el decorador [`@Injectable()`](https://angular.io/api/core/Injectable).

```ts
@Injectable({
  providedIn: 'root',
})
```

Cuando proporciona el servicio en el nivel raíz, Angular crea una instancia única compartida `HeroService` e inyecta en cualquier clase que lo solicite. El registro del proveedor en los metadatos de [`@Injectable`](https://angular.io/api/core/Injectable) también permite a Angular optimizar una aplicación eliminando el servicio si resulta que no se usa después de todo.

> Para obtener más información sobre los proveedores, consulte la [sección Proveedores](https://angular.io/guide/providers). Para obtener más información sobre los inyectores, consulte la [guía de Inyección por dependencia](https://angular.io/guide/dependency-injection).

El `HeroService` está ahora listo para conectar a `HeroesComponent`.

> Esta es una muestra de código provisional que le permitirá proporcionar y utilizar el `HeroService`. En este punto, el código será diferente al `HeroService` de la ["revisión final del código"](#revision-final-de-codigo).

---

## Actualizar HeroesComponent

Abra el archivo de la clase `HeroesComponent`.

Elimina la importación `HEROES`, porque ya no la necesitarás. Importe el `HeroService`.

```ts
// rc/app/heroes/heroes.component.ts (import HeroService)

import { HeroService } from "../hero.service";
```

Reemplace la definición de la propiedad `heroes` con una declaración.

```ts
// src/app/heroes/heroes.component.ts

heroes: Hero[] = [];
```

### Inyectar el `HeroService`

Agregue un parámetro privado `heroService` de tipo `HeroService` al constructor.

```ts
// src/app/heroes/heroes.component.ts

constructor(private heroService: HeroService) {}
```

El parámetro define simultáneamente una propiedad privada `heroService` y la identifica como un sitio de inyección `HeroService`.

Cuando Angular crea un `HeroesComponent`, el sistema de [inyección de dependencia](https://angular.io/guide/dependency-injection) establece el parámetro `heroService` en la instancia única de `HeroService`.

### Agregar `getHeroes()`

Crea un método para recuperar a los héroes del servicio.

```ts
// src/app/heroes/heroes.component.ts

getHeroes(): void {
  this.heroes = this.heroService.getHeroes();
}
```

### Llama a `ngOnInit()`

Si bien puede llamar al constructor `getHeroes()`, esa no es la mejor práctica.

Reserve el constructor para una inicialización mínima, como conectar los parámetros del constructor a las propiedades. El constructor no debería _hacer nada_. Ciertamente no debería llamar a una función que realiza solicitudes HTTP a un servidor remoto como lo haría un servicio de datos _real_.

En su lugar, llame `getHeroes()` dentro del [_enlace del ciclo de vida ngOnInit_](https://angular.io/guide/lifecycle-hooks) y deje que Angular llame a `ngOnInit()` en el momento apropiado después de construir una instancia `HeroesComponent`.

```ts
// src/app/heroes/heroes.component.ts

ngOnInit() {
  this.getHeroes();
}
```

### Verlo correr

Después de que se actualice el navegador, la aplicación debería ejecutarse como antes, mostrando una lista de héroes y una vista de detalles de héroe cuando haces clic en el nombre de un héroe.

---

## Datos observables

El método `HeroService.getHeroes()` tiene una _firma sincrónica_, lo que implica que `HeroService` puede buscar héroes sincrónicamente. El `HeroesComponent` consume el resultado `getHeroes()` como si los héroes se encontraron de forma sincrónica.

```ts
// src/app/heroes/heroes.component.ts

this.heroes = this.heroService.getHeroes();
```

Esto no funcionará en una aplicación real. Se está saliendo con la suya ahora porque el servicio actualmente devuelve _héroes simulados_. Pero pronto la aplicación buscará héroes de un servidor remoto, que es una operación intrínsecamente _asincrónica_.

El `HeroService` debe esperar a que el servidor responda, `getHeroes()` no puede devolver inmediatamente con los datos de héroe, y el navegador no bloqueará mientras los espera el servicio.

`HeroService.getHeroes()` debe tener una _firma asincrónica_ de algún tipo.

En este tutorial, `HeroService.getHeroes()` devolverá un `Observable` porque eventualmente usará el método de Angular `HttpClient.get` para buscar a los héroes y [`HttpClient.get()` devolverá un `Observable`](https://angular.io/guide/http).

### Observable HeroService

`Observable` es una de las clases clave en la [Biblioteca RxJS](https://rxjs.dev/).

En un [tutorial posterior sobre HTTP](./toh-pt6), aprenderá que los métodos de Angular [`HttpClient`](https://angular.io/api/common/http/HttpClient) devuelven `Observable`s RxJS. En este tutorial, simulará la obtención de datos del servidor con la función RxJS `of()`.

Abra el archivo `HeroService` e importe los símbolos `Observable` y `of` desde RxJS.

```ts
// src/app/hero.service.ts (Observable imports)

import { Observable, of } from "rxjs";
```

Reemplace el método `getHeroes()` con lo siguiente:

```ts
// src/app/hero.service.ts

getHeroes(): Observable<Hero[]> {
  const heroes = of(HEROES);
  return heroes;
}
```

`of(HEROES)` devuelve un valor `Observable<Hero[]>` que emite _un solo valor_, la matriz de héroes simulados.

> En el [tutorial de HTTP](), llamará a `HttpClient.get<Hero[]>()` que también devuelve un `Observable<Hero[]>` que emite _un valor único_, una matriz de héroes del cuerpo de la respuesta HTTP.

### Suscríbete en `HeroesComponent`

El método `HeroService.getHeroes` utilizado para devolver un `Hero[]`. Ahora devuelve un `Observable<Hero[]>`.

Tendrás que adaptarte a esa diferencia en `HeroesComponent`.

Encuentre el método `getHeroes` y reemplácelo con el siguiente código (que se muestra en paralelo con la versión anterior para comparar)

```ts
// heroes.component.ts (Observable)

getHeroes(): void {
  this.heroService.getHeroes()
      .subscribe(heroes => this.heroes = heroes);
}
```

```ts
// heroes.component.ts (Original)

getHeroes(): void {
  this.heroes = this.heroService.getHeroes();
}
```

`Observable.subscribe()` es la diferencia fundamental.

La versión anterior asigna una matriz de héroes a la propiedad `heroes` del componente. La asignación se produce de forma _sincrónica_, como si el servidor pudiera devolver héroes instantáneamente o el navegador pudiera congelar la interfaz de usuario mientras esperaba la respuesta del servidor.

Eso _no funcionará_ cuando en `HeroService` en realidad esté realizando solicitudes a un servidor remoto.

La nueva versión espera a `Observable` emita la matriz de héroes, lo que podría suceder ahora o dentro de varios minutos. El método `subscribe()` pasa la matriz emitida a la devolución de llamada, que establece la propiedad `heroes` del componente .

Este enfoque asincrónico _funcionará_ cuando `HeroService` solicite héroes del servidor.

---

## Mostrar mensajes

Esta sección lo guía a través de lo siguiente:

- agregando un `MessagesComponent` que muestra los mensajes de la aplicación en la parte inferior de la pantalla
- creando un inyectable, `MessageService` en toda la aplicación para enviar mensajes que se mostrarán
- inyectando `MessageService` en el `HeroService`
- mostrando un mensaje cuando `HeroService` recupera héroes con éxito

### Crear MessagesComponent

Utilice la CLI para crear el `MessagesComponent`.

```bash
ng generate component messages
```

La CLI crea los archivos de componentes en la carpeta `src/app/messages` y declara el archivo `MessagesComponent` en `AppModule`.

Modifique la plantilla `AppComponent` para mostrar la generada `MessagesComponent`.

```html
<!-- src/app/app.component.html -->

<h1>{{title}}</h1>
<app-heroes></app-heroes>
<app-messages></app-messages>
```

Debería ver el párrafo predeterminado `MessagesComponent` en la parte inferior de la página.

### Crea el MessageService

Utilice la CLI para crear el archivo `MessageService` en `src/app`.

```bash
ng generate service message
```

Abra `MessageService` y reemplace su contenido con lo siguiente.

```ts
// src/app/message.service.ts

import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class MessageService {
  messages: string[] = [];

  add(message: string) {
    this.messages.push(message);
  }

  clear() {
    this.messages = [];
  }
}
```

El servicio expone su caché de `messages` y dos métodos: uno para `add()` (agregar) un mensaje en el caché y otro para `clear()` (limpiar) el caché.

### Inyectarlo en el HeroService

En `HeroService`, importe el `MessageService`.

```ts
// src/app/hero.service.ts (import MessageService)

import { MessageService } from "./message.service";
```

Modifique el constructor con un parámetro que declare una propiedad privada `messageService`. Angular inyectará `MessageService` único en esa propiedad cuando cree el `HeroService`.

```ts
// src/app/hero.service.ts

constructor(private messageService: MessageService) { }
```

> Este es un escenario típico de _"servicio en servicio"_: inyecta `MessageService` en el `HeroService` que se inyecta en el `HeroesComponent`.

### Enviar un mensaje desde HeroService

Modifica el método `getHeroes()` para enviar un mensaje cuando se recuperan los héroes.

```ts
// src/app/hero.service.ts

getHeroes(): Observable<Hero[]> {
  const heroes = of(HEROES);
  this.messageService.add('HeroService: fetched heroes');
  return heroes;
}
```

### Mostrar el mensaje de HeroService

El `MessagesComponent` debería mostrar todos los mensajes, incluido el mensaje enviado por el `HeroService` cuando busca héroes.

Abra `MessagesComponent` e importe el `MessageService`.

```ts
// src/app/messages/messages.component.ts (import MessageService)

import { MessageService } from "../message.service";
```

Modifique el constructor con un parámetro que declare una propiedad **pública** `messageService`. Angular inyectará el `MessageService` único en esa propiedad cuando cree el `MessagesComponent`.

```ts
// src/app/messages/messages.component.ts

constructor(public messageService: MessageService) {}
```

La propiedad `messageService` **debe ser pública** porque la vinculará en la plantilla.

> Angular solo se une a las propiedades de los componentes _públicos_.

### Enlazar con el MessageService

Reemplace la plantilla `MessagesComponent` generada por CLI con lo siguiente.

```html
<!-- src/app/messages/messages.component.html -->

<div *ngIf="messageService.messages.length">
  <h2>Messages</h2>
  <button class="clear" (click)="messageService.clear()">Clear messages</button>
  <div *ngFor="let message of messageService.messages">{{message}}</div>
</div>
```

Esta plantilla se une directamente al componente `messageService`.

- El [`*ngIf`](https://angular.io/api/common/NgIf) solo muestra el área de mensajes si hay mensajes para mostrar.
- Un [`*ngFor`](https://angular.io/api/common/NgForOf) presenta la lista de mensajes en elementos `<div>` repetidos.
- Un [enlace de evento](https://angular.io/guide/event-binding) de Angular vincula el evento de clic del botón a `MessageService.clear()`.

Los mensajes se verán mejor cuando agregue los estilos CSS privados a `messages.component.css` como se enumeran en una de las pestañas de ["revisión de código final"]() a continuación.

---

## Agregar mensajes adicionales al servicio de héroe

El siguiente ejemplo muestra cómo enviar y mostrar un mensaje cada vez que el usuario hace clic en un héroe, mostrando un historial de las selecciones del usuario. Esto será útil cuando llegue a la siguiente sección sobre [Enrutamiento]().

```ts
// src/app/heroes/heroes.component.ts

import { Component, OnInit } from "@angular/core";

import { Hero } from "../hero";
import { HeroService } from "../hero.service";
import { MessageService } from "../message.service";

@Component({
  selector: "app-heroes",
  templateUrl: "./heroes.component.html",
  styleUrls: ["./heroes.component.css"],
})
export class HeroesComponent implements OnInit {
  selectedHero?: Hero;

  heroes: Hero[] = [];

  constructor(
    private heroService: HeroService,
    private messageService: MessageService
  ) {}

  ngOnInit() {
    this.getHeroes();
  }

  onSelect(hero: Hero): void {
    this.selectedHero = hero;
    this.messageService.add(`HeroesComponent: Selected hero id=${hero.id}`);
  }

  getHeroes(): void {
    this.heroService.getHeroes().subscribe((heroes) => (this.heroes = heroes));
  }
}
```

Actualice el navegador para ver la lista de héroes y desplácese hasta la parte inferior para ver los mensajes del HeroService. Cada vez que haces clic en un héroe, aparece un nuevo mensaje para registrar la selección. Utilice el botón **Clear messages** para borrar el historial de mensajes.

---

## Revisión final del código

Aquí están los archivos de código discutidos en esta página.

```ts
// src/app/hero.service.ts

import { Injectable } from "@angular/core";

import { Observable, of } from "rxjs";

import { Hero } from "./hero";
import { HEROES } from "./mock-heroes";
import { MessageService } from "./message.service";

@Injectable({
  providedIn: "root",
})
export class HeroService {
  constructor(private messageService: MessageService) {}

  getHeroes(): Observable<Hero[]> {
    const heroes = of(HEROES);
    this.messageService.add("HeroService: fetched heroes");
    return heroes;
  }
}
```

```ts
// src/app/message.service.ts

import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class MessageService {
  messages: string[] = [];

  add(message: string) {
    this.messages.push(message);
  }

  clear() {
    this.messages = [];
  }
}
```

```ts
// src/app/heroes/heroes.component.ts

import { Component, OnInit } from "@angular/core";

import { Hero } from "../hero";
import { HeroService } from "../hero.service";
import { MessageService } from "../message.service";

@Component({
  selector: "app-heroes",
  templateUrl: "./heroes.component.html",
  styleUrls: ["./heroes.component.css"],
})
export class HeroesComponent implements OnInit {
  selectedHero?: Hero;

  heroes: Hero[] = [];

  constructor(
    private heroService: HeroService,
    private messageService: MessageService
  ) {}

  ngOnInit() {
    this.getHeroes();
  }

  onSelect(hero: Hero): void {
    this.selectedHero = hero;
    this.messageService.add(`HeroesComponent: Selected hero id=${hero.id}`);
  }

  getHeroes(): void {
    this.heroService.getHeroes().subscribe((heroes) => (this.heroes = heroes));
  }
}
```

```ts
// src/app/messages/messages.component.ts

import { Component, OnInit } from "@angular/core";
import { MessageService } from "../message.service";

@Component({
  selector: "app-messages",
  templateUrl: "./messages.component.html",
  styleUrls: ["./messages.component.css"],
})
export class MessagesComponent implements OnInit {
  constructor(public messageService: MessageService) {}

  ngOnInit() {}
}
```

```html
<!-- src/app/messages/messages.component.html -->

<div *ngIf="messageService.messages.length">
  <h2>Messages</h2>
  <button class="clear" (click)="messageService.clear()">Clear messages</button>
  <div *ngFor="let message of messageService.messages">{{message}}</div>
</div>
```

```css
/* src/app/messages/messages.component.css */

/* MessagesComponent's private CSS styles */
h2 {
  color: #a80000;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: lighter;
}

.clear {
  color: #333;
  background-color: #eee;
  margin-bottom: 12px;
  padding: 1rem;
  border-radius: 4px;
  font-size: 1rem;
}
.clear:hover {
  color: white;
  background-color: #42545c;
}
```

```ts
// src/app/app.module.ts

import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
import { FormsModule } from "@angular/forms";
import { AppComponent } from "./app.component";
import { HeroesComponent } from "./heroes/heroes.component";
import { HeroDetailComponent } from "./hero-detail/hero-detail.component";
import { MessagesComponent } from "./messages/messages.component";

@NgModule({
  declarations: [
    AppComponent,
    HeroesComponent,
    HeroDetailComponent,
    MessagesComponent,
  ],
  imports: [BrowserModule, FormsModule],
  providers: [
    // no need to place any providers due to the `providedIn` flag...
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

```html
<!-- src/app/app.component.html -->

<h1>{{title}}</h1>
<app-heroes></app-heroes>
<app-messages></app-messages>
```

---

## Resumen

- Refactorizó el acceso a los datos de la clase `HeroService`.
- Usted registró `HeroService` como _proveedor_ de su servicio en el nivel raíz para que pueda inyectarse en cualquier lugar de la aplicación.
- Usó la [inyección de dependencia de Angular](https://angular.io/guide/dependency-injection) para inyectarla en un componente.
- Le dio al método de obtención de datos `HeroService` una firma asincrónica.
- Descubrió `Observable` y la biblioteca _observable_ RxJS .
- Usó RxJS `of()` para devolver un observable de simulacros de héroes (`Observable<Hero[]>`).
- El hook del ciclo de vida `ngOnInit` del componente llama al método `HeroService`, no al constructor.
- Creaste un `MessageService` para la comunicación débilmente acoplada entre clases.
- El `HeroService` inyectado en un componente se crea con otro servicio inyectado, `MessageService`.
