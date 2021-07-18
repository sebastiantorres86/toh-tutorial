# Crea un nuevo proyecto

Empiece por crear una aplicación inicial utilizando la CLI de Angular. A lo largo de este tutorial, modificará y ampliará esa aplicación de inicio para crear la aplicación Tour of Heroes.

En esta parte del tutorial, hará lo siguiente:

1. Configurar tu entorno.
2. Crear un nuevo espacio de trabajo y un proyecto de aplicación inicial.
3. Sirva la aplicación.
4. Realizar cambios en la aplicación.

> Para ver la aplicación de muestra que se describe en esta página, consulte el [ejemplo en vivo](https://angular.io/generated/live-examples/toh-pt0/stackblitz.html)/[ejemplo de descarga](https://angular.io/generated/zips/toh-pt0/toh-pt0.zip).

---

## Configura tu entorno

Para configurar su entorno de desarrollo, siga las instrucciones en [Configuración del entorno local](https://angular.io/guide/setup-local).

---

## Crea un nuevo espacio de trabajo y una aplicación inicial.

Desarrolla aplicaciones en el contexto de un [espacio de trabajo](https://angular.io/guide/glossary#workspace) de Angular. Un espacio de trabajo contiene los archivos de uno o más [proyectos](https://angular.io/guide/glossary#project). Un proyecto es el conjunto de archivos que componen una aplicación o una biblioteca. Para este tutorial, creará un nuevo espacio de trabajo.

Para crear un nuevo espacio de trabajo y un proyecto de aplicación inicial:

1. Asegúrese de no estar ya en una carpeta de espacio de trabajo angular. Por ejemplo, si ha creado previamente el espacio de trabajo de Introducción, cambie al padre de esa carpeta.

2. Ejecute el comando CLI `ng new` y proporcione el nombre `angular-tour-of-heroes`, como se muestra aquí:

   ```bash
   ng new angular-tour-of-heroes
   ```

3. El comando `ng new` le solicita información sobre las funciones que debe incluir en el proyecto de aplicación inicial. Acepte los valores predeterminados presionando la tecla Intro o Retorno.

La CLI de Angular instala los paquetes `npm` de Angular necesarios y otras dependencias. Esto puede tardar unos minutos.

También crea el siguiente espacio de trabajo y archivos de proyecto de inicio:

- Un nuevo espacio de trabajo, con una carpeta raíz llamada `angular-tour-of-heroes`.
- Un esqueleto de proyecto de aplicación inicial en la subcarpeta `src/app`.
- Archivos de configuración relacionados.

El proyecto de la aplicación inicial contiene una aplicación de bienvenida simple, lista para ejecutarse.

---

## Sirva la solicitud

Vaya al directorio del espacio de trabajo e inicie la aplicación.

```bash
cd angular-tour-of-heroes
ng serve --open
```

> El comando `ng serve` crea la aplicación, inicia el servidor de desarrollo, observa los archivos de origen y reconstruye la aplicación a medida que realiza cambios en esos archivos.
>
> La bandera `--open` abre un navegador a` http://localhost:4200/`.

Debería ver la aplicación ejecutándose en su navegador.

---

## Componentes de Angular

La página que ve es el _shell de la aplicación_. El shell está controlado por un **componente** de Angular llamado `AppComponent`.

Los _componentes_ son los bloques de construcción fundamentales de las aplicaciones de Angular. Muestran datos en la pantalla, escuchan la entrada del usuario y toman medidas en función de esa entrada.

---

## Realizar cambios en la aplicación

Abra el proyecto en su editor o IDE favorito y navegue hasta la carpeta `src/app` para realizar algunos cambios en la aplicación de inicio.

Encontrará la implementación del shell de `AppComponent` distribuida en tres archivos:

- `app.component.ts`- el código de la clase del componente, escrito en TypeScript.
- `app.component.html`- la plantilla del componente, escrita en HTML.
- `app.component.css`- los estilos CSS privados del componente.

### Cambiar el título de la aplicación

Abra el archivo de clase del componente (`app.component.ts`) y cambie el valor de la propiedad `title` a 'Tour of Heroes'.

```ts
// app.component.ts (class title property)

title = "Tour of Heroes";
```

Abra el archivo de plantilla de componente (`app.component.html`) y elimine la plantilla predeterminada generada por Angular CLI. Reemplácelo con la siguiente línea de HTML.

```html
<!-- app.component.html (template) -->

<h1>{{title}}</h1>
```

Las llaves dobles son la sintaxis de _enlace de interpolación_ de Angular . Este enlace de interpolación presenta el valor de propiedad `title` del componente dentro de la etiqueta de encabezado HTML.

El navegador se actualiza y muestra el nuevo título de la aplicación.

### Agregar estilos de aplicacion

La mayoría de las aplicaciones se esfuerzan por lograr una apariencia coherente en toda la aplicación. La CLI generó un styles.css vacío para este propósito. Ponga sus estilos para toda la aplicación allí.

Abra `src/styles.css` y agregue el siguiente código al archivo.

```css
/* src/styles.css (excerpt) */

/* Application-wide Styles */
h1 {
  color: #369;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 250%;
}
h2,
h3 {
  color: #444;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: lighter;
}
body {
  margin: 2em;
}
body,
input[type="text"],
button {
  color: #333;
  font-family: Cambria, Georgia, serif;
}
/* everywhere else */
* {
  font-family: Arial, Helvetica, sans-serif;
}
```

---

## Revisión final del código

Aquí están los archivos de código discutidos en esta página.

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
```

```css
/* src/styles.css (excerpt) */

/* Application-wide Styles */
h1 {
  color: #369;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 250%;
}
h2,
h3 {
  color: #444;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: lighter;
}
body {
  margin: 2em;
}
body,
input[type="text"],
button {
  color: #333;
  font-family: Cambria, Georgia, serif;
}
/* everywhere else */
* {
  font-family: Arial, Helvetica, sans-serif;
}
```

---

## Resumen

- Creaste la estructura de la aplicación inicial usando Angular CLI.
- Aprendió que los componentes de Angular muestran datos.
- Usó las llaves dobles de interpolación para mostrar el título de la aplicación.
