---
title: Características de Material Design Xamarin.Android Designer
description: Este tema describe las características del diseñador que facilitan a los desarrolladores crear diseños de diseño conforme Material. Esta sección presenta y explica cómo usar la cuadrícula de Material, la paleta de colores de Material, la escala tipográficas y el Editor de temas.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 5e6d7b4bdfdf7ea48d26537cb41c763656b050e0
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669666"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Características de diseño del diseñador Material de Xamarin.Android

_Este tema describe las características del diseñador que facilitan a los desarrolladores crear diseños de diseño conforme Material. Esta sección presenta y explica cómo usar la cuadrícula de Material, la paleta de colores de Material, la escala tipográficas y el Editor de temas._

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016: Todos los usuarios pueden crear aplicaciones atractivas con Material Design**

## <a name="overview"></a>Información general

El Diseñador de Xamarin.Android incluye características que facilitan para crear diseños conforme Material diseño. Si no está familiarizado con Material Design, consulte el [introducción de Material Design](https://material.io/design/introduction).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En esta guía, tendremos un vistazo a las características de diseñador siguientes:

-   *Cuadrícula material* &ndash; una superposición en la superficie de diseño que muestra una cuadrícula, espaciado y líneas delimitadoras que le ayudarán a colocar los widgets de diseño según las directrices de Material Design.

-   *Editor de temas* &ndash; un editor de recursos de color pequeñas que le permite establece información de color para un subconjunto de un tema. Por ejemplo, puede obtener una vista previa y modificar los colores del Material como `colorPrimary`, `colorPrimaryDark`, y `colorAccent`.

Se podrá vistazo a cada una de estas características y se proporcionan ejemplos de cómo usarlas.

## <a name="material-design-grid"></a>Cuadrícula de diseño de materiales

El menú de la cuadrícula de diseño de Material está disponible en la barra de herramientas en la parte superior del diseñador:

[![Cuadrícula de diseño de materiales](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

Al hacer clic en el icono de la cuadrícula de diseño de Material, el diseñador muestra una superposición en la superficie de diseño que incluye los siguientes elementos:

-   Líneas delimitadoras (líneas de color naranja)

-   Espaciado (áreas en verde)

-   Una cuadrícula (líneas azules)

Estos elementos se pueden ver en la captura de pantalla anterior. Cada uno de estos elementos de superposición es configurable. Al hacer clic en el botón de puntos suspensivos situado junto al menú de la cuadrícula de diseño de Material, abre un elemento flotante de cuadro de diálogo que le permite habilitar o deshabilitar la cuadrícula, configure la colocación de líneas delimitadoras y establecer vienen. Tenga en cuenta que todos los valores se expresan en `dp` (independientes de la densidad de píxeles):

[![Cuadrícula, línea delimitadora y la configuración de espaciado](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

Para agregar una línea nueva delimitadora, escriba un nuevo valor de desplazamiento en el **desplazamiento** , seleccione una ubicación (**izquierdo**, **superior**, **derecho**, o  **inferior**) y haga clic en el icono para agregar la línea nueva delimitadora +. De forma similar, para agregar un nuevo espacio, especifique el tamaño y posición de desplazamiento (en dp) en el **tamaño** y **desplazamiento** cuadros, respectivamente. Seleccione una ubicación (**izquierdo**, **superior**, **derecho**, o **inferior**) y haga clic en el icono para agregar el nuevo espaciado +.

Al cambiar estos valores de configuración, son guardados en el archivo XML de diseño y volver a usar cuando vuelve a abrir el diseño.


## <a name="theme-editor"></a>Editor de temas

El **Theme Editor** le permite personalizar la información de color para un subconjunto de atributos de tema. Para abrir el **Theme Editor**, haga clic en el icono de pincel en la barra de herramientas:

[![Icono de Editor de temas](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

Aunque el **Theme Editor** es accesible desde la barra de herramientas para todos los destino las versiones de Android y niveles de API, solo un subconjunto de las funcionalidades descritas a continuación están disponibles si el nivel de API de destino es anterior a la API 21 (Android 5.0 Círculo).

El panel izquierdo de la **Theme Editor** muestra la lista de colores que constituyen el tema seleccionado actualmente (en este ejemplo, usamos el `Default Theme`):

[![Editor de temas](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

Cuando se selecciona un color de la izquierda, el panel derecho proporciona las siguientes pestañas para ayudarle a editar ese color:

-   **Heredar** &ndash; muestra un diagrama de herencia de estilo para el color seleccionado y muestra el color de resolver y el código de color asignado a ese color de tema.

-   **Selector de color** &ndash; le permite cambiar el color seleccionado a cualquier valor arbitrario.

-   **Paleta de materiales** &ndash; permite cambiar el color seleccionado en un valor que se ajusta al diseño de materiales.

-   **Recursos** &ndash; le permite cambiar el color seleccionado a uno de los otros recursos de color existente en el tema.

Echemos un vistazo a cada una de estas pestañas en detalle.

### <a name="inherit-tab"></a>Heredar de pestaña

Tal como se muestra en el ejemplo siguiente, la **heredar** pestaña muestra la herencia de estilo para el **en segundo plano** color de la **tema predeterminado**:

[![Heredar de pestaña](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

En este ejemplo, el **tema predeterminado** hereda de un estilo que usa `@color/background_material_light` pero la reemplaza con `color/material_grey_50`, que tiene un valor de código de color de `#fffafafa`.
Para obtener más información sobre la herencia de estilo, consulte [estilos y temas](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selector de colores

Captura de pantalla siguiente se ilustra la **selector de colores**:

[![Selector de colores](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

En este ejemplo, el **en segundo plano** color puede cambiarse a cualquier valor de varias formas:

-   Al hacer clic directamente en un color.
-   Especificar valores de matiz, saturación y luminosidad.
-   Escriba sus valores RGB (rojos, verdes y azules) en formato decimal.
-   Configuración de la versión alfa (opacidad) correspondiente al color seleccionado.
-   Escribir directamente el código de color hexadecimal.

Es el color que elija en el selector de colores *no* restringen las directrices de Material Design o al conjunto de recursos de color disponibles.

### <a name="resources"></a>Recursos

El **recursos** ficha ofrece una lista de recursos de color que ya están presentes en el tema:

[![Recursos](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

Mediante el **recursos** ficha restringe las opciones de esta lista de colores. Tenga en cuenta que si elige un recurso de color que ya está asignado a otra parte del tema, dos elementos adyacentes de la interfaz de usuario "funcionen juntos" (porque tienen el mismo color) y resultar difícil distinguir el usuario.

### <a name="material-palette"></a>Paleta de materiales

El **Material paleta** se abrirá la pestaña el **paleta de colores de diseño de Material**. Elegir un valor de color en esta paleta restringe la selección de color para que sea coherente con las directrices de Material Design:

[![Paleta de materiales](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

La parte superior de la paleta de colores muestra los colores de Material Design principales mientras que la parte inferior de la paleta muestra un intervalo de tonos de color principal seleccionado. Por ejemplo, cuando selecciona **Indigo**, una colección de **Indigo** matices se muestra en la parte inferior del cuadro de diálogo.
Cuando se selecciona un tono, se cambia el color de la propiedad para el matiz seleccionado. En el ejemplo siguiente, la `Background Tint` del botón cambia a *500 Indigo*:

![Seleccione Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint` se establece en el código de color para *500 Indigo* (`#ff3f51b5`), y el diseñador actualiza el color de fondo para reflejar este cambio:

[![Puede cambiar un tono en segundo plano](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Para obtener más información acerca de la paleta de colores de Material Design, consulte el Material Design [Guía de la paleta de colores](https://material.io/design/color/).

### <a name="creating-a-new-theme"></a>Crear un nuevo tema

En el ejemplo siguiente, vamos a usar la paleta de Material para crear un nuevo tema personalizado. En primer lugar, cambiaremos la **en segundo plano** al color *900 azul*:

![Cambiar el fondo a 900 azul](material-design-features-images/vs/12-change-background-to-blue-w158.png)

Cuando se cambia un recurso de color, aparece un mensaje con el mensaje, *del tema actual tiene cambios sin guardar*:

[![Advertencia de cambios no guardados](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

El **en segundo plano** color en el diseñador ha cambiado a la nueva selección de color, pero aún no se ha guardado este cambio. En este momento, puede realizar una de las siguientes acciones:

-   Haga clic en **descartar cambios** para descartar la nueva opción de color (o las opciones) y revertir el tema a su estado original. 

-   Presione <kbd>CTRL+S</kbd> para guardar los cambios realizados en el tema actualmente. 

En el ejemplo siguiente, <kbd>CTRL+S</kbd> se presionó para que los cambios se guardaron en **AppTheme**:

[![Cambios guardados en AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>Resumen

En este tema se describe las características de Material Design disponibles en el Diseñador de Xamarin.Android. Se ha explicado cómo habilitar y configurar la cuadrícula de diseño de Material, y se ha explicado cómo usar el Editor de temas para crear nuevos temas personalizados que cumplan las directrices de Material Design.
Para obtener más información sobre la compatibilidad con Xamarin.Android Material Design, consulte [Material tema](~/android/user-interface/material-theme.md).




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En esta guía, vamos a un echar un vistazo a las características de diseñador siguientes:

-   *Cuadrícula de diseño de materiales* &ndash; una superposición en la superficie de diseño que muestra una cuadrícula, espaciado y líneas delimitadoras que le ayudarán a colocar los widgets de diseño según las directrices de Material Design.

-   *Paleta de colores de diseño material* &ndash; cuadro de diálogo de un panel de propiedades que le ayuda a elegir un color de la paleta de Material Design oficial.

-   *Escala tipográfica* &ndash; cuadro de diálogo de un panel de propiedades que le proporciona una opción de configuración de diseño conforme Material para el `textAppearance` propiedad de los campos de texto.

-   *Editor de temas* &ndash; un editor de recursos de color pequeñas que le permite establece información de color para un subconjunto de un tema. Por ejemplo, puede obtener una vista previa y modificar los colores del Material como `colorPrimary`, `colorPrimaryDark`, y `colorAccent`.

Se podrá vistazo a cada una de estas características y se proporcionan ejemplos de cómo usarlas.

## <a name="material-design-grid"></a>Cuadrícula de diseño de materiales

El menú de la cuadrícula de diseño de Material está disponible en la barra de herramientas en la parte superior del diseñador:

[![Cuadrícula de diseño de materiales](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

Al hacer clic en el icono de la cuadrícula de diseño de Material, el diseñador muestra una superposición en la superficie de diseño que incluye los siguientes elementos:

-   Líneas delimitadoras (líneas de color naranja)

-   Espaciado (áreas en verde)

-   Una cuadrícula (líneas azules)

Estos elementos pueden verse en la captura de pantalla siguiente:

[![Línea delimitadora, el espaciado y la cuadrícula](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

Cada uno de estos elementos de superposición es configurable. Al hacer clic en el botón de puntos suspensivos (&hellip;) junto al menú de la cuadrícula de diseño de Material, abre un elemento flotante de cuadro de diálogo que permite habilitar o deshabilitar la cuadrícula, configure la colocación de líneas delimitadoras y establecer los espaciados. Tenga en cuenta que todos los valores se expresan en `dp` (independientes de la densidad de píxeles):

[![Cuadrícula, línea delimitadora y la configuración de espaciado](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Para agregar una línea nueva delimitadora, escriba un nuevo valor de desplazamiento en el **desplazamiento** , seleccione una ubicación (**izquierdo**, **superior**, **derecho**, o  **inferior**) y haga clic en el icono (que aparece a la derecha cuando se escribe un valor) + para agregar la línea nueva delimitadora. De forma similar, para agregar un nuevo espacio, especifique el tamaño y posición de desplazamiento (en dp) en el **tamaño** y **desplazamiento** cuadros, respectivamente. Seleccione una ubicación (**izquierdo**, **superior**, **derecho**, o **inferior**) y haga clic en el icono para agregar el nuevo espaciado +.

Al cambiar estos valores de configuración, son guardados en el archivo XML de diseño y volver a usar cuando vuelve a abrir el diseño.

## <a name="material-design-color-palette"></a>Paleta de colores de material Design

Cada elemento del panel de propiedad que acepta un color ahora tiene un icono de paleta adicional que puede usar para abrir la paleta de colores de Material de diseño, como se muestra en esta captura de pantalla:

[![Icono de color](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Al hacer clic en este icono, se abre un elemento flotante de cuadro de diálogo que permite configurar el color de esa propiedad de la paleta de colores de Material Design:

[![Paleta de colores de diseño material](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

La parte superior de la paleta de colores muestra los colores de Material Design principales mientras que la parte inferior de la paleta muestra un intervalo de tonos de color principal seleccionado. Por ejemplo, cuando selecciona **Indigo**, una colección de **Indigo** matices se muestra en la parte inferior del cuadro de diálogo.
Cuando se selecciona un tono, se cambia el color de la propiedad para el matiz seleccionado. En el ejemplo siguiente, la `Background Tint` del botón cambia a *500 Indigo*:

[![Choose Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` se establece en el código de color para *500 Indigo* (`#ff3f51b5`), y el diseñador actualiza el color de fondo del botón para reflejar este cambio:

[![Cambios de tono en segundo plano](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Para obtener más información acerca de la paleta de colores de Material Design, consulte el Material Design [Guía de la paleta de colores](https://material.io/design/color/).

## <a name="typographic-scale"></a>Escala tipográfica

El **apariencia del texto** sección de la **propiedad** pad **estilo** pestaña tiene un icono que le permite seleccione uno de un `TextAppearance` estilo que se ajusta al diseño de Material especificación de:

[![Pestaña estilos](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Al hacer clic en este icono, se abre el **escala tipográficas** elemento flotante cuadro de diálogo, que presenta una lista de estilos de texto preconfigurados que puede elegir entre:

[![Selector de estilo de texto](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

En el ejemplo siguiente, al hacer clic en **pantalla 1** cambia el texto del botón a la fuente más grande de **pantalla 1**:

[![Estilo de presentación 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

El estilo de texto en el **escala tipográficas** diálogo sigue el **tema** configuración. Por ejemplo, si la **luz** se elige el tema en el diseñador, la lista de los reflejos de estilos de texto disponible el **luz** tema:

[![Tema claro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>Editor de temas

El **Theme Editor** le permite personalizar la información de color para un subconjunto de atributos de tema. Para abrir el **Theme Editor**, haga clic en el icono de pincel en la barra de herramientas:

[![Icono de Editor de temas](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

Aunque el **Theme Editor** es accesible desde la barra de herramientas para todos los destino las versiones de Android y niveles de API, solo un subconjunto de las funcionalidades descritas a continuación están disponibles si el nivel de API de destino es anterior a la API 21 (Android 5.0 Círculo).

El panel izquierdo de la **Theme Editor** muestra la lista de colores que constituyen el tema seleccionado actualmente (en este ejemplo, usamos el `Default Theme`):

[![Editor de temas](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

Cuando se selecciona un color de la izquierda, el panel derecho proporciona las siguientes pestañas para ayudarle a editar ese color:

-   **Heredar** &ndash; muestra un diagrama de herencia de estilo para el color seleccionado y muestra el color de resolver y el código de color asignado a ese color de tema.

-   **Selector de color** &ndash; le permite cambiar el color seleccionado a cualquier valor arbitrario.

-   **Paleta de materiales** &ndash; permite cambiar el color seleccionado en un valor que se ajusta al diseño de materiales.

-   **Recursos** &ndash; le permite cambiar el color seleccionado a uno de los otros recursos de color existente en el tema.

Echemos un vistazo a cada una de estas pestañas en detalle.

### <a name="inherit-tab"></a>Heredar de pestaña

Tal como se muestra en el ejemplo siguiente, la **heredar** pestaña muestra la herencia de estilo para el **en segundo plano** color de la **tema predeterminado**:

[![Heredar de pestaña](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

En este ejemplo, el **tema predeterminado** hereda de un estilo que usa `@color/background_material_dark` pero la reemplaza con `color/material_grey_850`, que tiene un valor de código de color de `#ff303030`.
Para obtener más información sobre la herencia de estilo, consulte [estilos y temas](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selector de colores

Captura de pantalla siguiente se ilustra la **selector de colores**:

[![Selector de colores](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)


En este ejemplo, el **en segundo plano** color puede cambiarse a cualquier valor de varias formas:

-   Al hacer clic directamente en un color.
-   Especificar valores de matiz, saturación y luminosidad.
-   Escriba sus valores RGB (rojos, verdes y azules) en formato decimal.
-   Configuración de la versión alfa (opacidad) correspondiente al color seleccionado.
-   Escribir directamente el código de color hexadecimal.

Es el color que elija en el selector de colores *no* restringen las directrices de Material Design o al conjunto de recursos de color disponibles.

### <a name="resources"></a>Recursos

El **recursos** ficha ofrece una lista de recursos de color que ya están presentes en el tema:

[![Recursos](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

Mediante el **recursos** ficha restringe las opciones de esta lista de colores. Tenga en cuenta que si elige un recurso de color que ya está asignado a otra parte del tema, dos elementos adyacentes de la interfaz de usuario "funcionen juntos" (porque tienen el mismo color) y resultar difícil distinguir el usuario.

### <a name="material-palette"></a>Paleta de materiales

El **Material paleta** se abrirá la pestaña el **paleta de colores de diseño de Material** descrito [anteriores](#material_palette). Elegir un valor de color en esta paleta restringe la selección de color para que sea coherente con las directrices de Material Design.

[![Paleta de materiales](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>Crear un nuevo tema

En el ejemplo siguiente, vamos a usar la paleta de Material para crear un nuevo tema personalizado. En primer lugar, cambiaremos la **en segundo plano** al color *900 azul*:

[![Cambiar el fondo a 900 azul](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

Cuando se cambia un recurso de color, aparece un mensaje con el mensaje, *del tema actual tiene cambios sin guardar*:

[![Advertencia de cambios no guardados](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

Se ha realizado el cambio de color en el diseñador, pero aún no se ha guardado este cambio. En este momento, puede realizar una de las siguientes acciones:

-   Haga clic en **descartar cambios** para descartar la nueva opción de color (o las opciones) y revertir el tema a su estado original. 

-   Presione  **&#8984; + S** para guardar los cambios en un nuevo tema denominado **personalizado**. 


## <a name="summary"></a>Resumen

En este tema se describe las características de Material Design disponibles en el Diseñador de Xamarin.Android. Explica cómo habilitar y configurar la cuadrícula de diseño de Material, cómo utilizar la paleta de colores de diseño de Material para editar las propiedades de color y cómo usar el selector de escala tipográficas para configurar las propiedades de texto. También muestra cómo usar el Editor de temas para crear nuevos temas personalizados que cumplan las directrices de Material Design. Para obtener más información sobre la compatibilidad con Xamarin.Android Material Design, consulte [Material tema](~/android/user-interface/material-theme.md).

-----


## <a name="related-links"></a>Vínculos relacionados

- [Tema de materiales](~/android/user-interface/material-theme.md)
- [Introducción al diseño de materiales](https://material.io/design/introduction)
