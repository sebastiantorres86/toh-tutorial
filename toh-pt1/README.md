# El editor de héroe

La aplicación ahora tiene un título básico. A continuación, creará un nuevo componente para mostrar la información del héroe y colocará ese componente en el shell de la aplicación.

> Para ver la aplicación de muestra que se describe en esta página, consulte la [ejemplo en vivo](https://angular.io/generated/live-examples/toh-pt1/stackblitz.html)/[ejemplo de descarga](https://angular.io/generated/zips/toh-pt1/toh-pt1.zip).

---

## Crea el componente de héroes

Usando la CLI de Angular, genere un nuevo componente llamado `heroes`.

```bash
ng generate component heroes
```

La CLI crea una nueva carpeta, `src/app/heroes/` y genera los tres archivos `HeroesComponent` junto con un archivo de prueba.

El archivo de clase de HeroesComponent es el siguiente:

```ts
// app/heroes/heroes.component.ts (initial version)

import { Component, OnInit } from "@angular/core";

@Component({
  selector: "app-heroes",
  templateUrl: "./heroes.component.html",
  styleUrls: ["./heroes.component.css"],
})
export class HeroesComponent implements OnInit {
  constructor() {}

  ngOnInit() {}
}
```

Siempre importa el símbolo [`Component`](https://angular.io/api/core/Component) de la biblioteca principal de Angular y anota la clase del componente con [@`Component`](https://angular.io/api/core/Component).

[@`Component`](https://angular.io/api/core/Component) es una función decoradora que especifica los metadatos angulares del componente.

La CLI generó tres propiedades de metadatos:

1. `selector`- el selector de elementos CSS del componente
2. `templateUrl`- la ubicación del archivo de plantilla del componente.
3. `styleUrls`- la ubicación de los estilos CSS privados del componente.

El [Selector de elementos CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/Type_selectors), `'app-heroes'`, Coincide con el nombre del elemento HTML que identifica este componente dentro de la plantilla de un componente padre.

El `ngOnInit()` es un [hook de ciclo de vida](https://angular.io/guide/lifecycle-hooks#oninit). Angular llama a `ngOnInit()` poco después de crear un componente. Es un buen lugar para poner la lógica de inicialización.

Siempre haga `export` de la clase de componente para que pueda hacer hacer el `import` en otro lugar... como en el `AppModule`.

### Agregar una propiedad `hero`

Agrega una propiedad `hero` al `HeroesComponent` de un héroe llamado "Windstorm".

```ts
// heroes.component.ts (propiedad del héroe)

hero = "Windstorm";
```

### Muestra al héroe

Abra el archivo de plantilla `heroes.component.html`. Elimine el texto predeterminado generado por Angular CLI y reemplácelo con un enlace de datos a la nueva propiedad `hero`.

```html
<!-- heroes.component.html -->

<h2>{{hero}}</h2>
```

---

## Mostrar la vista `HeroesComponent`

Para mostrar el `HeroesComponent`, debe agregarlo a la plantilla del shell `AppComponent`.

Recuerda que `app-heroes` es el [selector de elementos](https://angular.io/tutorial/toh-pt1#selector) para `HeroesComponent`. Por lo tanto, agregue un elemento `<app-heroes>` al archivo de plantilla `AppComponent`, justo debajo del título.

```html
<!-- src/app/app.component.html -->

<h1>{{title}}</h1>
<app-heroes></app-heroes>
```

Suponiendo que el comando CLI `ng serve` todavía se está ejecutando, el navegador debería actualizar y mostrar tanto el título de la aplicación como el nombre del héroe.

---

## Crea una interfaz de héroe

Un verdadero héroe es más que un nombre.

Cree una interfaz `Hero` en su propio archivo en la carpeta `src/app`. Dale propiedades `id` y `name`.

```ts
// src/app/hero.ts

export interface Hero {
  id: number;
  name: string;
}
```

Regrese a la clase `HeroesComponent` e importe la interfaz `Hero`.

Refactorice la propiedad `hero` del componente para que sea de tipo `Hero`. Inicialícelo con una `id` de `1` y el nombre `Windstorm`.

El archivo de clase `HeroesComponent` revisado debería verse así:

```ts
// src/app/heroes/heroes.component.ts

import { Component, OnInit } from "@angular/core";
import { Hero } from "../hero";

@Component({
  selector: "app-heroes",
  templateUrl: "./heroes.component.html",
  styleUrls: ["./heroes.component.css"],
})
export class HeroesComponent implements OnInit {
  hero: Hero = {
    id: 1,
    name: "Windstorm",
  };

  constructor() {}

  ngOnInit() {}
}
```

La página ya no se muestra correctamente porque cambiaste el héroe de un string a un objeto.

---

## Muestra el objeto hero

Actualice el enlace en la plantilla para anunciar el nombre del héroe y mostrar ambos `id` y `name` en un diseño de detalles como este:

```html
<!-- heroes.component.html (plantilla de HeroesComponent) -->

<h2>{{hero.name}} Details</h2>
<div><span>id: </span>{{hero.id}}</div>
<div><span>name: </span>{{hero.name}}</div>
```

El navegador se actualiza y muestra la información del héroe.

---

## Formatear con _UppercasePipe_

Modifique el enlace `hero.name` de esta manera.

```html
<!-- src/app/heroes/heroes.component.html -->

<h2>{{hero.name | uppercase}} Details</h2>
```

El navegador se actualiza y ahora el nombre del héroe se muestra en letras mayúsculas.

La palabra [`uppercase`](https://angular.io/api/common/UpperCasePipe) en el enlace de interpolación, justo después del operador pipe (|), activa el archivo integrado `UppercasePipe`.

Las [pipes](https://angular.io/guide/pipes) son una buena forma de formatear strings, importes de moneda, fechas y otros datos de visualización. Angular se envía con varias pipes integradas y puede crear las suyas propias.

---

## Edita el héroe

Los usuarios deberían poder editar el nombre del héroe en un cuadro de texto `<input>`.

El cuadro de texto debe _mostrar_ la propiedad `name` del héroe y _actualizar_ esa propiedad a medida que el usuario escribe. Eso significa que los datos fluyen de la clase del componente _a la pantalla_ y de la pantalla _a la clase_.

Para automatizar ese flujo de datos, configure un enlace de datos bidireccional entre el elemento `<input>` del formulario y la propiedad `hero.name`.

### Enlace bidireccional

Refactorice el área de detalles en la plantilla `HeroesComponent` para que se vea así:

```html
<!-- src/app/heroes/heroes.component.html (HeroesComponent's template) -->

<div>
  <label for="name">Hero name: </label>
  <input id="name" [(ngModel)]="hero.name" placeholder="name" />
</div>
```

**[(ngModel)]** es la sintaxis de enlace de datos bidireccional de Angular.

Aquí vincula la propiedad `hero.name` al cuadro de texto HTML para que los datos puedan fluir en ambas direcciones: desde la propiedad `hero.name` al cuadro de texto, y desde el cuadro de texto de regreso al `hero.name`.

## El _FormsModule_ que falta

Observe que la aplicación dejó de funcionar cuando agregó [([ngModel](https://angular.io/api/forms/NgModel))].

```console
Template parse errors:
Can't bind to 'ngModel' since it isn't a known property of 'input'.
```

Aunque [`ngModel`](https://angular.io/api/forms/NgModel) es una directiva Angular válida, no está disponible de forma predeterminada.

Pertenece al opcional [`FormsModule`](https://angular.io/api/forms/FormsModule) y debe _optar por_ utilizarlo.

---

## _AppModule_

Angular necesita saber cómo encajan las piezas de su aplicación y qué otros archivos y bibliotecas requiere la aplicación. Esta información se llama _metadatos_.

Algunos de los metadatos están en los decoradores [`@Component`](https://angular.io/api/core/Component) que agregó a sus clases de componentes. Otros metadatos críticos están en decoradores `@NgModule`.

El decorador [`@NgModule`](https://angular.io/api/core/NgModule) más importante anota la clase **AppModule** de nivel superior .

La CLI de Angular generó una clase `AppModule` en `src/app/app.module.ts` cuando creó el proyecto. Aquí es donde _se inscribe_ en el [`FormsModule`](https://angular.io/api/forms/FormsModule).

### Importar _FormsModule_

Abra `AppModule`(`app.module.ts`) e importe el símbolo [`FormsModule`](https://angular.io/api/forms/FormsModule) de la biblioteca `@angular/forms`.

```ts
// app.module.ts (FormsModule symbol import)

import { FormsModule } from "@angular/forms"; // <-- NgModel lives here
```

Luego, agregue [`FormsModule`](https://angular.io/api/forms/FormsModule) a la matriz de `imports` de metadatos [`@NgModule`](https://angular.io/api/core/NgModule), que contiene una lista de módulos externos que necesita la aplicación.

```ts
// app.module.ts (@NgModule imports)

imports: [
  BrowserModule,
  FormsModule
],
```

Cuando el navegador se actualice, la aplicación debería funcionar nuevamente. Puedes editar el nombre del héroe y ver los cambios reflejados inmediatamente en el `<h2>` arriba del cuadro de texto.

### Declarar HeroesComponent

Cada componente debe declararse en _exactamente un_ [NgModule](https://angular.io/guide/ngmodules).

_No declaró_ el `HeroesComponent`. Entonces, ¿por qué funcionó la aplicación?

Funcionó porque la CLI de Angular declaró `HeroesComponent` en el `AppModule` momento en que generó ese componente.

Abra` src/app/app.module.ts` y busque `HeroesComponent` importado cerca de la parte superior.

```ts
// src/app/app.module.ts

import { HeroesComponent } from "./heroes/heroes.component";
```

`HeroesComponent` se declara en la matriz [`@NgModule.declarations`](https://angular.io/api/core/NgModule#declarations).

```ts
// src/app/app.module.ts

declarations: [
  AppComponent,
  HeroesComponent
],
```

Tenga en cuenta que `AppModule` declara ambos componentes de la aplicación `AppComponent` y `HeroesComponent`.

---

## Revisión final del código

Aquí están los archivos de código discutidos en esta página.

```ts
// src/app/heroes/heroes.component.ts

import { Component, OnInit } from "@angular/core";
import { Hero } from "../hero";

@Component({
  selector: "app-heroes",
  templateUrl: "./heroes.component.html",
  styleUrls: ["./heroes.component.css"],
})
export class HeroesComponent implements OnInit {
  hero: Hero = {
    id: 1,
    name: "Windstorm",
  };

  constructor() {}

  ngOnInit() {}
}
```

```html
<!-- src/app/heroes/heroes.component.html -->

<h2>{{hero.name | uppercase}} Details</h2>
<div><span>id: </span>{{hero.id}}</div>
<div>
  <label for="name">Hero name: </label>
  <input id="name" [(ngModel)]="hero.name" placeholder="name" />
</div>
```

```ts
// src/app/app.module.ts

import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
import { FormsModule } from "@angular/forms"; // <-- NgModel lives here

import { AppComponent } from "./app.component";
import { HeroesComponent } from "./heroes/heroes.component";

@NgModule({
  declarations: [AppComponent, HeroesComponent],
  imports: [BrowserModule, FormsModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

```ts
// src/app/app.component.ts

import { Component } from "@angular/core";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"],
})
export class AppComponent {
  title = "Tour of Heroes";
}
```

```html
<!-- src/app/app.component.html -->

<h1>{{title}}</h1>
<app-heroes></app-heroes>
```

```ts
// src/app/hero.ts

export interface Hero {
  id: number;
  name: string;
}
```

---

## Resumen

- Usó la CLI para crear un segundo `HeroesComponent`.
- Mostraste el `HeroesComponent` agregándolo al `AppComponent` shell.
- Aplicó el `UppercasePipe` para formatear el nombre.
- Usó enlace de datos bidireccional con la directiva [`ngModel`](https://angular.io/api/forms/NgModel).
- Aprendiste sobre el `AppModule`.
- Importó el [`FormsModule`](https://angular.io/api/forms/FormsModule) en el `AppModule` para que Angular reconociera y aplicara la directiva [`ngModel`](https://angular.io/api/forms/NgModel).
- Aprendió la importancia de declarar componentes en el `AppModule` y agradeció que la CLI lo declarara por usted.

