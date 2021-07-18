# Crear un componente de función

Por el momento, `HeroesComponent` muestra tanto la lista de héroes como los detalles del héroe seleccionado.

Mantener todas las funciones en un componente a medida que la aplicación crece no será posible. Querrá dividir los componentes grandes en subcomponentes más pequeños, cada uno enfocado en una tarea o flujo de trabajo específico.

En esta página, darás el primer paso en esa dirección al mover los detalles del héroe a un sitio separado y reutilizable `HeroDetailComponent`.

El `HeroesComponent` solo presentará la lista de héroes. El `HeroDetailComponent` presentará detalles de un héroe seleccionado.

> Para ver la aplicación de muestra que se describe en esta página, consulte la [ejemplo en vivo](https://angular.io/generated/live-examples/toh-pt2/stackblitz.html)/[ejemplo de descarga](https://angular.io/generated/zips/toh-pt2/toh-pt2.zip).

---

## Hacer el `HeroDetailComponent`

Use la CLI de Angular para generar un nuevo componente llamado `hero-detail`.

```bash
ng generate component hero-detail
```

El comando andamia lo siguiente:

- Crea un directorio `src/app/hero-detail`.

Dentro de ese directorio se generan cuatro archivos:

- Un archivo CSS para los estilos de los componentes.
- Un archivo HTML para la plantilla del componente.
- Un archivo TypeScript con una clase de componente denominada `HeroDetailComponent`.
- Un archivo de prueba para la clase `HeroDetailComponent`.

El comando también agrega `HeroDetailComponent` como una declaración en el decorador [`@NgModule`](https://angular.io/api/core/NgModule) del archivo `src/app/app.module.ts`.

### Escribe la plantilla

Corte el HTML para el detalle del héroe desde la parte inferior de la plantilla `HeroesComponent` y péguelo sobre el texto estándar generado en la plantilla `HeroDetailComponent`.

El HTML pegado se refiere a un `selectedHero`. El nuevo `HeroDetailComponent` puede presentar a _cualquier_ héroe, no solo a un héroe seleccionado. Así que reemplace "selectedHero" con "hero" en todas las partes de la plantilla.

Cuando haya terminado, la plantilla `HeroDetailComponent` debería verse así:

```html
<!-- src/app/hero-detail/hero-detail.component.html -->

<div *ngIf="hero">
  <h2>{{hero.name | uppercase}} Details</h2>
  <div><span>id: </span>{{hero.id}}</div>
  <div>
    <label for="hero-name">Hero name: </label>
    <input id="hero-name" [(ngModel)]="hero.name" placeholder="name" />
  </div>
</div>
```

### Agregar la propiedad [`@Input()`](https://angular.io/api/core/Input) del héroe

La plantilla `HeroDetailComponent` se une a la propiedad `hero` del componente que es de tipo `Hero`.

Abra el archivo de clase `HeroDetailComponent` e importe el símbolo `Hero`.

```ts
// src/app/hero-detail/hero-detail.component.ts (import Hero)

import { Hero } from "../hero";
```

La propiedad `hero` [debe ser una propiedad de _entrada_](https://angular.io/guide/inputs-outputs), anotada con el decorador [`@Input()`](https://angular.io/api/core/Input), porque el `HeroesComponent` externo [se unirá a ella]() de esta manera.

```html
<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```

Modifique la declaración de importación `@angular/core` para incluir el símbolo [`Input`](https://angular.io/api/core/Input).

```ts
// src/app/hero-detail/hero-detail.component.ts (import Input)

import { Component, OnInit, Input } from "@angular/core";
```

Agregue una propiedad `hero`, precedida por el decorador [`Input`](https://angular.io/api/core/Input).

```ts
// src/app/hero-detail/hero-detail.component.ts

@Input() hero?: Hero;
```

Ese es el único cambio que debes hacer en la clase `HeroDetailComponent`. No hay más propiedades. No hay lógica de presentación. Este componente solo recibe un objeto héroe a través de su propiedad `hero` y lo muestra.

---

## Mostrar el `HeroDetailComponent`

`HeroesComponent` se utiliza para mostrar los detalles del héroe por sí solo, antes de eliminar esa parte de la plantilla. Esta sección lo guía a través de la delegación de lógica a `HeroDetailComponent`.

Los dos componentes tendrán una relación padre/hijo. El padre `HeroesComponent` controlará al hijo `HeroDetailComponent` enviándole un nuevo héroe para que se muestre cada vez que el usuario seleccione un héroe de la lista.

No cambiará la _clase_ `HeroesComponent` pero cambiará su _plantilla_.

### Actualizar la plantilla `HeroesComponent`

El selector de `HeroDetailComponent` es `'app-hero-detail'`. Agregue un elemento `<app-hero-detail>` cerca de la parte inferior de la plantilla `HeroesComponent`, donde solía estar la vista de detalles del héroe.

Vincula el `HeroesComponent.selectedHero` a la propiedad `hero` del elemento de esta manera.

```html
<!-- heroes.component.html (HeroDetail binding) -->

<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```

`[hero]="selectedHero"` es un [enlace de propiedad](https://angular.io/guide/property-binding) de Angular .

Es un enlace de datos _unidireccional_ desde la propiedad `selectedHero` de `HeroesComponent` a la propiedad `hero` del elemento de destino, que se asigna a la propiedad `hero` de `HeroDetailComponent`.

Ahora, cuando el usuario hace clic en un héroe en la lista, el `selectedHero` cambia. Cuando el `selectedHero` cambia, la _propiedad vinculante_ actualiza `hero` y `HeroDetailComponent` muestra el nuevo héroe.

La plantilla `HeroesComponent` revisada debería verse así:

```html
<!-- heroes.component.html -->

<h2>My Heroes</h2>

<ul class="heroes">
  <li
    *ngFor="let hero of heroes"
    [class.selected]="hero === selectedHero"
    (click)="onSelect(hero)"
  >
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>

<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```

El navegador se actualiza y la aplicación vuelve a funcionar como antes.

---

## ¿Qué cambió?

Como [antes](./2-Mostrar-una-lista), cada vez que un usuario hace clic en el nombre de un héroe, el detalle del héroe aparece debajo de la lista de héroes. Ahora `HeroDetailComponent` presenta esos detalles en lugar de `HeroesComponent`.

Refactorizar el original `HeroesComponent` en dos componentes produce beneficios, tanto ahora como en el futuro:

1. Redujiste las responsabilidades de `HeroesComponent`.

2. Puede convertir el `HeroDetailComponent` en un editor de héroe rico sin tocar al padre `HeroesComponent`.

3. Puedes evolucionar `HeroesComponent` sin tocar la vista de detalles del héroe.

4. Puede reutilizar `HeroDetailComponent` en la plantilla de algún componente futuro.

---

## Revisión final del código

Aquí están los archivos de código discutidos en esta página.

```ts
// src/app/hero-detail/hero-detail.component.ts

import { Component, OnInit, Input } from "@angular/core";
import { Hero } from "../hero";

@Component({
  selector: "app-hero-detail",
  templateUrl: "./hero-detail.component.html",
  styleUrls: ["./hero-detail.component.css"],
})
export class HeroDetailComponent implements OnInit {
  @Input() hero?: Hero;

  constructor() {}

  ngOnInit() {}
}
```

```html
<!-- src/app/hero-detail/hero-detail.component.html -->

<div *ngIf="hero">
  <h2>{{hero.name | uppercase}} Details</h2>
  <div><span>id: </span>{{hero.id}}</div>
  <div>
    <label for="hero-name">Hero name: </label>
    <input id="hero-name" [(ngModel)]="hero.name" placeholder="name" />
  </div>
</div>
```

```html
<!-- src/app/heroes/heroes.component.html -->

<h2>My Heroes</h2>

<ul class="heroes">
  <li
    *ngFor="let hero of heroes"
    [class.selected]="hero === selectedHero"
    (click)="onSelect(hero)"
  >
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>

<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```

```ts
// src/app/app.module.ts

import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
import { FormsModule } from "@angular/forms";

import { AppComponent } from "./app.component";
import { HeroesComponent } from "./heroes/heroes.component";
import { HeroDetailComponent } from "./hero-detail/hero-detail.component";

@NgModule({
  declarations: [AppComponent, HeroesComponent, HeroDetailComponent],
  imports: [BrowserModule, FormsModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

---

## Resumen

- Creaste un archivo `HeroDetailComponent`.
- Usó un [enlace de propiedad](https://angular.io/guide/property-binding) para dar al padre `HeroesComponent` control sobre el hijo `HeroDetailComponent`.
- Usaste el decorador [`@Input`](https://angular.io/guide/inputs-outputs) para hacer que la propiedad `hero` estuviera disponible para que la vinculara el `HeroesComponent` externo.
