---
title: "Características de diseño de materiales"
description: "Este tema describe las características del diseñador que facilitan a los desarrolladores crear diseños conforme a Material diseño. Esta sección presenta y explica cómo utilizar la cuadrícula de Material, la paleta de colores de Material, la escala tipográfico y el Editor de temas."
ms.topic: article
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: c0b5fa3e7eacb9f7fd8aa133a290d0e7654972ce
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="material-design-features"></a>Características de diseño de materiales

_Este tema describe las características del diseñador que facilitan a los desarrolladores crear diseños conforme a Material diseño. Esta sección presenta y explica cómo utilizar la cuadrícula de Material, la paleta de colores de Material, la escala tipográfico y el Editor de temas._

<a name="overview" />

## <a name="overview"></a>Información general

El Diseñador de Xamarin.Android incluye características que facilitan la crear diseños compatibles con Material-diseño. Si no está familiarizado con el Material de diseño, vea la [Material de diseño Introducción](https://www.google.com/design/spec/material-design/introduction.html).

En esta guía, tendremos un vistazo a las siguientes características de diseñador:

-   *Cuadrícula material* &ndash; una superposición en la superficie de diseño que muestra una cuadrícula, el espaciado y líneas delimitadoras para ayudarle a colocar los widgets de diseño según las directrices de diseño de Material.

-   *Paleta de colores material* &ndash; cuadro de diálogo Panel de una propiedad que le ayuda a elegir un color de la paleta de diseño de Material oficial.

-   *Escala tipográfico* &ndash; cuadro de diálogo Panel de una propiedad que proporciona una opción de configuración de compatibilidad con Material diseño para la `textAppearance` propiedades de campos de texto.

-   *Editor de temas* &ndash; un editor de recursos de color pequeñas que le permite definir información de color para un subconjunto de un tema. Por ejemplo, puede obtener una vista previa y modificar los colores del Material como `colorPrimary`, `colorPrimaryDark`, y `colorAccent`.

Comenzaremos que tenga una apariencia en cada una de estas características y se proporcionan ejemplos de cómo usarlas.


<a name="material_grid" />

## <a name="material-design-grid"></a>Cuadrícula de diseño de materiales

El menú de la cuadrícula de diseño de Material está disponible en la barra de herramientas en la parte superior del diseñador:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cuadrícula de diseño de materiales](material-design-features-images/vs/01-material-design-grid-sml.png)](material-design-features-images/vs/01-material-design-grid.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cuadrícula de diseño de materiales](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png)

-----

Al hacer clic en el icono de la cuadrícula de diseño de Material, el diseñador muestra una superposición en la superficie de diseño que incluye los siguientes elementos:

-   Líneas delimitadoras (líneas de color naranja)

-   Espaciado (áreas en verde)

-   Una cuadrícula (líneas azules)

Estos elementos se pueden ver en la captura de pantalla siguiente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Línea delimitadora, el espaciado y la cuadrícula](material-design-features-images/vs/02-grid-and-keylines-sml.png)](material-design-features-images/vs/02-grid-and-keylines.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Línea delimitadora, el espaciado y la cuadrícula](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Cada uno de estos elementos de la superposición es configurable. Al hacer clic en el botón de puntos suspensivos situado junto al menú de la cuadrícula de diseño de Material, abre un popover del cuadro de diálogo que le permite habilitar o deshabilitar la cuadrícula, configure la colocación de líneas delimitadoras y establecer los espaciados. Tenga en cuenta que todos los valores se expresan en `dp` (independiente de la densidad de píxeles):

![Cuadrícula, línea delimitadora y la configuración de espaciado](material-design-features-images/vs/03-grid-configuration.png)

Para agregar una línea delimitadora nueva, escriba un nuevo valor de desplazamiento en el **desplazamiento** , seleccione una ubicación (**izquierdo**, **arriba**, **derecho**, o  **inferior**) y haga clic en el icono para agregar la nueva línea de delimitadora +.

De forma similar, para agregar una nueva configuración de espacio, escriba el tamaño y la posición de desplazamiento (en el punto de distribución) en el **tamaño** y **desplazamiento** cuadros, respectivamente. Seleccione una ubicación (**izquierdo**, **superior**, **derecho**, o **inferior**) y haga clic en el icono para agregar el nuevo espaciado +.

Cuando cambie estos valores de configuración, se guardan en el archivo XML de diseño y volver a usar cuando se vuelve a abre el diseño.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Cada uno de estos elementos de la superposición es configurable. Al hacer clic en el triángulo hacia abajo situada junto al menú de la cuadrícula de diseño de Material, abre un popover del cuadro de diálogo que le permite habilitar o deshabilitar la cuadrícula, configure la colocación de líneas delimitadoras y establecer los espaciados. Tenga en cuenta que todos los valores se expresan en `dp` (independiente de la densidad de píxeles):

[![Cuadrícula, línea delimitadora y la configuración de espaciado](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png)

Para agregar una línea delimitadora nueva, escriba un nuevo valor de desplazamiento en el **desplazamiento** , seleccione una ubicación (**izquierdo**, **arriba**, **derecho**, o  **inferior**) y haga clic en el icono para agregar la nueva línea de delimitadora +.

De forma similar, para agregar una nueva configuración de espacio, escriba el tamaño y la posición de desplazamiento (en el punto de distribución) en el **tamaño** y **desplazamiento** cuadros, respectivamente. Seleccione una ubicación (**izquierdo**, **superior**, **derecho**, o **inferior**) y haga clic en el icono para agregar el nuevo espaciado +.

Cuando cambie estos valores de configuración, se guardan en el archivo XML de diseño y volver a usar cuando se vuelve a abre el diseño.


## <a name="material-design-color-palette"></a>Paleta de colores de material de diseño

Cada elemento del panel de propiedad que acepta un color ahora tiene un icono adicional que puede usar para abrir la paleta de colores de Material de diseño, como se muestra en esta captura de pantalla:

[![Icono de color](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png)

Al hacer clic en este icono, se abre un popover del cuadro de diálogo que permite configurar el color de esa propiedad de la paleta de colores de diseño de Material:

[![Paleta de colores material de diseño](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png)

La parte superior de la paleta de colores muestra los colores de diseño de Material principales mientras que la parte inferior de la paleta muestra un intervalo de tonos para el color principal seleccionado. Por ejemplo, cuando selecciona **Indigo**, una colección de **Indigo** matices se muestra en la parte inferior del cuadro de diálogo.
Cuando se selecciona un matiz, se cambia el color de la propiedad para el matiz seleccionado. En el ejemplo siguiente, la `Background Tint` del botón cambia a *500 Indigo*:

[![Elija Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png)

`Background Tint` se establece en el código de color de *500 Indigo* (`#ff3f51b5`), y el diseñador actualiza el color de fondo del botón para reflejar este cambio:

[![Cambios de tono de fondo](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png)

Para obtener más información acerca de la paleta de colores de diseño de Material, ver el diseño de Material [Guía de la paleta de colores](http://www.google.com/design/spec/style/color.html#color-color-palette).

## <a name="typographic-scale"></a>Escala tipográfico

El **apariencia del texto** sección de la **propiedad** panel **estilo** ficha tiene un icono que le permite seleccione uno de un `TextAppearance` estilo que se ajusta al diseño de Material especificación de:

[![Pestaña estilos](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png)

Al hacer clic en este icono, se abre la **escala tipográfico** popover del cuadro de diálogo, que presenta una lista de estilos de texto configurado previamente que puede elegir entre:

[![Selector de estilo de texto](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png)

En el ejemplo siguiente, haga clic en **pantalla 1** cambia el texto del botón a la fuente más grande de **pantalla 1**:

[![Estilo de presentación 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png)

El estilo de texto en el **escala tipográfico** diálogo sigue el **tema** configuración. Por ejemplo, si la **luz** tema se haya elegido en el diseñador, la lista de reflejos de estilos de texto disponible el **luz** tema:

[![Tema claro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png)

-----


<a name="theme_editor" />

## <a name="theme-editor"></a>Editor de temas

El **Editor de temas** le permite personalizar la información de color de un subconjunto de atributos de tema. Para abrir el **Editor de temas**, haga clic en el icono de pincel en la barra de herramientas:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Icono de Editor de temas](material-design-features-images/vs/04-theme-editor-icon.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[ ![Icono de Editor de temas](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png)

-----

Aunque la **Editor de temas** es accesible desde la barra de herramientas para todos los destino versiones de Android y niveles de API, solo un subconjunto de las capacidades descritas a continuación están disponibles si el nivel de API de destino es anterior a la API 21 (Android 5.0 Círculo).

El panel izquierdo de la **Editor de temas** muestra la lista de colores que forman el tema seleccionado actualmente (en este ejemplo, estamos utilizando la `Default Theme`):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Editor de temas](material-design-features-images/vs/05-theme-editor-sml.png)](material-design-features-images/vs/05-theme-editor.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Editor de temas](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png)

-----

Cuando se selecciona un color de la izquierda, el panel derecho proporciona las siguientes pestañas para ayudarle a editar ese color:

-   **Heredar** &ndash; muestra un diagrama de herencia de estilo para el color seleccionado y muestra el color resuelto y el código de color asignado a ese color del tema.

-   **Selector de color** &ndash; permite cambiar el texto seleccionado en color para cualquier valor arbitrario.

-   **Paleta de material** &ndash; permite cambiar el texto seleccionado en color en un valor que se ajusta al diseño del Material.

-   **Recursos** &ndash; recursos en el tema de color permite cambiar al color seleccionado a una de las otras existentes.

Echemos un vistazo a cada una de estas pestañas en detalle.


<a name="theme_edit_inherit_tab" />

### <a name="inherit-tab"></a>Heredar de pestaña

Tal como se muestra en el ejemplo siguiente, la **heredar** pestaña muestra la herencia de estilo para el **fondo** color de la **tema predeterminado**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Heredar de pestaña](material-design-features-images/vs/06-inherit-tab-sml.png)](material-design-features-images/vs/06-inherit-tab.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Heredar de pestaña](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png)

-----

En este ejemplo, el **tema predeterminado** hereda de un estilo que usa `@color/background_material_dark` pero la reemplaza con `color/material_grey_850`, que tiene un valor de código de color de `#ff303030`.
Para obtener más información acerca de la herencia de estilo, consulte [estilos y temas](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).


<a name="theme_edit_color_picker" />

### <a name="color-picker"></a>Selector de colores

Captura de pantalla siguiente muestra la **selector de colores**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selector de colores](material-design-features-images/vs/07-color-picker-sml.png)](material-design-features-images/vs/07-color-picker.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Selector de colores](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png)

-----

En este ejemplo, el **fondo** color puede cambiarse a cualquier valor a través de varios medios:

-   Al hacer clic directamente en un color.
-   Especificar valores de matiz, saturación y luminosidad.
-   Especificar los valores RGB (rojos, verdes, azul) en formato decimal.
-   Establecer el canal alfa (opacidad) correspondiente al color seleccionado.
-   Escribir directamente el código hexadecimal del color.

Es el color que elija en el selector de colores *no* restringido instrucciones de diseño de Material o para el conjunto de recursos de color disponibles.

<a name="theme_edit_resources" />

### <a name="resources"></a>Recursos

El **recursos** ficha ofrece una lista de recursos de color que ya están presentes en el tema:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Recursos](material-design-features-images/vs/08-resources-sml.png)](material-design-features-images/vs/08-resources.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Recursos](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png)

-----

Mediante el **recursos** ficha restringe las opciones para esta lista de colores. Tenga en cuenta que si elige un recurso de color que ya está asignado a otra parte del tema, dos elementos adyacentes de la interfaz de usuario pueden "ejecutar juntos" (porque tienen el mismo color) y ser difícil para el usuario distinguir.


<a name="theme_edit_material_pallette" />

### <a name="material-palette"></a>Paleta de material

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

El **Material paleta** pestaña abre la **paleta de colores de diseño de Material**. Elegir un valor de color de esta paleta restringe la selección de color para que sea coherente con las directrices de diseño de Material.

[![Paleta de material](material-design-features-images/vs/09-material-palette-sml.png)](material-design-features-images/vs/09-material-palette.png)

La parte superior de la paleta de colores muestra los colores de diseño de Material principales mientras que la parte inferior de la paleta muestra un intervalo de tonos para el color principal seleccionado. Por ejemplo, cuando selecciona **Indigo**, una colección de **Indigo** matices se muestra en la parte inferior del cuadro de diálogo.
Cuando se selecciona un matiz, se cambia el color de la propiedad para el matiz seleccionado. En el ejemplo siguiente, la `Background Tint` del botón cambia a *500 Indigo*:

![Seleccione Indigo 500](material-design-features-images/vs/10-indigo.png)

`Background Tint` se establece en el código de color de *500 Indigo* (`#ff3f51b5`), y el diseñador actualiza el color de fondo para reflejar este cambio:

[![Cambiar un tono de fondo](material-design-features-images/vs/11-background-tint-sml.png)](material-design-features-images/vs/11-background-tint.png)

Para obtener más información acerca de la paleta de colores de diseño de Material, ver el diseño de Material [Guía de la paleta de colores](http://www.google.com/design/spec/style/color.html#color-color-palette).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El **Material paleta** pestaña abre el **paleta de colores de diseño de Material** descritos [anteriores](#material_palette). Elegir un valor de color de esta paleta restringe la selección de color para que sea coherente con las directrices de diseño de Material.

[![Paleta de material](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png)

-----


<a name="theme_create" />

### <a name="creating-a-new-theme"></a>Crear un nuevo tema

En el ejemplo siguiente, vamos a usar la paleta de Material para crear un nuevo tema personalizado. En primer lugar, cambiaremos la **fondo** color se *900 azul*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cambiar el fondo a 900 azul](material-design-features-images/vs/12-change-background-to-blue.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cambiar el fondo a 900 azul](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png)

-----


Cuando se cambia un recurso de color, un mensaje aparece con el mensaje, *del tema actual tiene cambios sin guardar*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cambios no guardados de advertencia](material-design-features-images/vs/13-unsaved-changes-sml.png)](material-design-features-images/vs/13-unsaved-changes.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cambios no guardados de advertencia](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png)

-----


El **fondo** color en el diseñador ha cambiado a la nueva selección de color, pero aún no se ha guardado este cambio. Se ofrecen dos opciones para que indicar cómo manejar el cambio:

-   **Descartar cambios** &ndash; descarta la nueva opción de color (o alternativas) y revierte el tema a su estado original.

-   **Crear nuevo tema** &ndash; crea un nuevo tema que se deriva del tema seleccionado actualmente y usa las invalidaciones de color realizadas en el **Editor de temas**.

Al hacer clic en **crear nuevo tema**, se producirá una de las acciones siguientes, según el tema seleccionado:

-   Si el tema seleccionado actualmente en el diseñador no es un tema de proyecto, se le presentará la opción para crear un nuevo archivo de recursos con el tema personalizado (mediante el tema seleccionado como el elemento primario del tema personalizado). El diseñador se activa en el tema personalizado después de crearlo.

-   Si el tema seleccionado actualmente es un tema de proyecto que se define en una ubicación, se le presentará la **tema de actualización** opción; al hacer clic en esta opción actualiza el tema actualmente seleccionado, en lugar de crear uno nuevo.

-   Si el tema seleccionado actualmente es un tema de proyecto que se define en varias ubicaciones (por ejemplo, en forma diferente-lleva el sufijo **valores** carpetas como **v21 valores**), aparecerá un cuadro de diálogo que pregunta para una nueva ubicación para guardar el tema personalizado.

Siguiendo con el ejemplo anterior, haga clic en **crear nuevo tema** resultados en la creación de un nuevo tema denominan **personalizado**:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Tema personalizado agregado](material-design-features-images/vs/14-custom-theme-sml.png)](material-design-features-images/vs/14-custom-theme.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Tema personalizado agregado](material-design-features-images/xs/19-custom-theme-sml.png)](material-design-features-images/xs/19-custom-theme.png)

-----


Dado que el tema seleccionado actualmente no es un tema de proyecto, no hay ningún cuadro de diálogo para actualizar el tema seleccionado o para especificar una nueva ubicación.

<a name="summary" />

## <a name="summary"></a>Resumen

En este tema se describe las características de diseño de Material disponibles en el Diseñador de Xamarin.Android. Explica cómo habilitar y configurar la cuadrícula de diseño de Material, cómo utilizar la paleta de colores de Material de diseño para editar las propiedades de color y cómo usar el selector de escala tipográfico para configurar las propiedades de texto. También muestra cómo utilizar el Editor de temas para crear nuevos temas personalizados que cumplan las directrices de diseño de Material. Para obtener más información sobre la compatibilidad con Xamarin.Android Material de diseño, vea [Material tema](~/android/user-interface/material-theme.md).



## <a name="related-links"></a>Vínculos relacionados

- [Tema de materiales](~/android/user-interface/material-theme.md)
- [Introducción de material de diseño](https://www.google.com/design/spec/material-design/introduction.html)
