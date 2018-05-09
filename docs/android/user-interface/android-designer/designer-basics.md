---
title: Conceptos básicos de diseñadores
description: En este tema presenta las características del diseñador, se explica cómo iniciar el diseñador, describe la superficie de diseño y detalla cómo utilizar el panel de propiedades para editar las propiedades de widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a8201301fc0437ecb79a81f40e865f14dc6af020
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="designer-basics"></a>Conceptos básicos de diseñadores

_En este tema presenta las características del diseñador, se explica cómo iniciar el diseñador, describe la superficie de diseño y detalla cómo utilizar el panel de propiedades para editar las propiedades de widget._


## <a name="launching-the-designer"></a>Iniciar el diseñador

El diseñador se inicia automáticamente cuando se crea un diseño, o se puede iniciar haciendo doble clic en un archivo de .axml existente. Por ejemplo, haga doble clic en **Main.axml** en el **recursos > diseño** carpeta cargará el diseñador tal y como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Pantalla de diseñador en Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Pantalla de diseñador en Visual Studio para Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Del mismo modo, puede agregar un nuevo diseño haciendo clic en el **diseño** carpeta en el **el Explorador de soluciones** y seleccionando **Agregar > nuevo elemento... > Diseño Android**:

[![Agregar cuadro de diálogo nuevo elemento](designer-basics-images/vs/02-add-new-layout-sml.w157.png)](designer-basics-images/vs/02-add-new-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Del mismo modo, puede agregar un nuevo diseño haciendo clic en el **diseño** carpeta en el **solución Pad** y seleccionando **Agregar > nuevo archivo > Android > diseño**:

[![Agregar cuadro de diálogo nuevo archivo](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

-----

Esto crea un nuevo archivo .axml y lo carga en la superficie de diseño.



## <a name="designer-features"></a>Características del diseñador

El diseñador se compone de varias secciones que admiten las diferentes características, como se muestra en la captura de pantalla siguiente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagrama de paneles del diseñador](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diagrama de paneles del diseñador](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

-----

Cuando se edita un diseño en el diseñador, utilice las siguientes características para crear y dar forma el diseño:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   **Superficie de diseño** &ndash; facilita la construcción visual de la interfaz de usuario por lo que le ofrece una representación editable de cómo aparecerá el diseño en el dispositivo.

-   **Barra de herramientas** &ndash; muestra una lista de selectores: **dispositivo**, **versión**, **tema**, la configuración de diseño y configuración de la barra de acción. La barra de herramientas también incluye iconos para iniciar el Editor de temas y para habilitar la cuadrícula de diseño de Material.

-   **Cuadro de herramientas** &ndash; proporciona una lista de widgets y los diseños que se pueden arrastrar y colocar en la superficie de diseño.

-   **Ventana propiedades** &ndash; se enumeran las propiedades del widget seleccionado para verla y editarla.

-   **Esquema de documento** &ndash; muestra el árbol de widgets que componen el diseño. Puede hacer clic en un elemento en el árbol para provocar que se pueden seleccionar en el diseñador. Además, al hacer clic en un elemento en el árbol de carga las propiedades del elemento en la ventana Propiedades.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-   **Superficie de diseño** &ndash; facilita la construcción visual de la interfaz de usuario por lo que le ofrece una representación editable de cómo aparecerá el diseño en el dispositivo.

-   **Barra de herramientas** &ndash; muestra una lista de selectores: **dispositivo**, **versión**, **tema**, la configuración de diseño y configuración de la barra de acción. La barra de herramientas también incluye iconos para iniciar el Editor de temas y para habilitar la cuadrícula de diseño de Material.

-   **Cuadro de herramientas** &ndash; proporciona una lista de widgets y los diseños que se pueden arrastrar y colocar en la superficie de diseño.

-   **Panel de propiedades** &ndash; se enumeran las propiedades del widget seleccionado para verla y editarla.

-   **Esquema de documento** &ndash; muestra el árbol de widgets que componen el diseño. Puede hacer clic en un elemento en el árbol para provocar que se pueden seleccionar en el diseñador. Además, al hacer clic en un elemento en el árbol de carga las propiedades del elemento en el panel de propiedades.

-----



## <a name="toolbar"></a>Barra de herramientas

La barra de herramientas (situado por encima de la superficie de diseño) presenta los selectores de configuración y los menús de la herramienta:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagrama de la barra de herramientas del diseñador](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diagrama de la barra de herramientas del diseñador](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

-----


La barra de herramientas proporciona acceso a las siguientes características:

-   **Selector de diseño alternativo** &ndash; le permite seleccionar las versiones de un diseño diferente.

-   **Selector de dispositivo** &ndash; define un conjunto de calificadores asociados con un dispositivo determinado, como el tamaño de la pantalla, la resolución y la disponibilidad de teclado. También puede agregar y eliminar nuevos dispositivos.

-   **Selector de versión de Android** &ndash; Android la versión que tiene como destino el diseño. El diseñador representará el diseño según la versión de Android seleccionada.

-   **Selector de tema** &ndash; selecciona el tema de la interfaz de usuario para el diseño.

-   **Selector de configuración** &ndash; selecciona la configuración del dispositivo, como *vertical* o *horizontal*.

-   **Opciones de calificador de recurso** &ndash; abre un cuadro de diálogo que muestra los menús desplegables para seleccionar *lenguaje*, *modo de interfaz de usuario*, *modo nocturno*, y *Pantalla round* opciones.

-   **Configuración de la barra de acción** &ndash; configura las opciones de la barra de acciones para el diseño.

-   **Editor de temas** &ndash; abre el *Editor de temas*, lo que hace posible que le permiten personalizar elementos del tema seleccionado.

-   **Material de diseño de cuadrícula** &ndash; habilita o deshabilita la *Material de diseño de cuadrícula*. El elemento de menú desplegable situado junto a la cuadrícula de diseño de Material abre un cuadro de diálogo que le permite personalizar la cuadrícula.

Cada una de estas características se explica con más detalle en estos temas:

[Calificadores de recursos y opciones de visualización de](~/android/user-interface/android-designer/resource-qualifiers.md) proporciona información detallada sobre la **dispositivo Selector**, **Android Selector de versión**, **Selector de tema**, **Configuración Selector**, **opciones del recurso de calificaciones**, y **configuración de la barra de acción**.

[Vistas alternativas de diseño](~/android/user-interface/android-designer/alternative-layout-views.md) explica cómo utilizar el **Selector de diseño alternativa**.

[Las características de diseño material](~/android/user-interface/android-designer/material-design-features.md) proporciona una descripción detallada de la **Editor de temas** y la **Material de diseño de cuadrícula**.



## <a name="design-surface"></a>Superficie de diseño

El diseñador permite arrastrar y colocar los widgets del cuadro de herramientas a la superficie de diseño. Cuando se interactúa con widgets en el diseñador (al agregar nuevos widgets o cambiar la posición de los existentes), se muestran líneas verticales y horizontales para marcar los puntos de inserción disponibles. En el ejemplo siguiente, un nuevo `Button` widget se arrastra a la superficie de diseño:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Líneas de inserción de ejemplo en la superficie de diseño](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Líneas de inserción de ejemplo en la superficie de diseño](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

-----

Además, se pueden copiar los widgets: puede usar copiar y pegar para copiar un widget, también puede arrastrar y colocar un widget existente mientras presiona la <kbd>Ctrl</kbd> clave.


### <a name="context-menu-commands"></a>Comandos del menú contextual

Un menú contextual está disponible en la superficie de diseño y en el esquema del documento. Este menú muestra los comandos que están disponibles para el widget seleccionado y su contenedor, lo que facilita para poder realizar operaciones en los contenedores (que no siempre son fáciles de seleccionar en la superficie de diseño). Este es un ejemplo de un menú contextual:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menú contextual de ejemplo cuando con el botón secundario de la superficie de diseño](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

En este ejemplo, con el botón secundario un `TextView` abre un menú contextual que ofrece varias opciones:

-   **LinearLayout** &ndash; abre un submenú para editar la `LinearLayout` primario de la `TextView`.

-   **Eliminar**, **copia**, y **cortar** &ndash; operaciones que se aplican a los hace doble clic `TextView`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menú contextual de ejemplo cuando con el botón secundario de la superficie de diseño](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

En este ejemplo, con el botón secundario un `TextView` abre un menú contextual que ofrece varias opciones:

-   **RelativeLayout** &ndash; abre un submenú para editar la `RelativeLayout` primario de la `TextView`.

-   **LinearLayout** &ndash; abre un submenú para editar la `LinearLayout` primario de la `TextView`.

-   **Eliminar**, **copia**, y **cortar** &ndash; operaciones que se aplican a los hace doble clic `TextView`.

-----

En este ejemplo, con el botón secundario un `TextView` abre un menú contextual que ofrece varias opciones:

-   **LinearLayout** &ndash; abre un submenú para editar la `LinearLayout` primario de la `TextView`.

-   **Eliminar**, **copia**, y **cortar** &ndash; operaciones que se aplican a los hace doble clic `TextView`.



### <a name="zoom-controls"></a>Controles de zoom

La superficie de diseño admite Zoom a través de varios controles, como se muestra:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagrama de los controles de zoom de la superficie de diseño](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diagrama de los controles de zoom de la superficie de diseño](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

-----

Estos controles resulten más fácil ver determinadas áreas de la interfaz de usuario en el diseñador:

-   **Resaltar contenedores** &ndash; resalta los contenedores en la superficie de diseño para que resulten fáciles de localizar al acercar y alejar.

-   **Tamaño normal** &ndash; representa el diseño de píxeles para que puedan ver el aspecto que tendrá el diseño con la resolución del dispositivo seleccionado.

-   **Ajustar a la ventana** &ndash; establece el nivel de zoom para que todo el diseño está visible en la superficie de diseño.

-   **Acercar** &ndash; amplía forma incremental con cada clic, y ampliar el diseño.

-   **Alejar** &ndash; aleja incrementalmente con cada clic, parezca que el diseño más pequeños en la superficie de diseño.

Tenga en cuenta que la configuración del zoom elegido no afecta a la interfaz de usuario de la aplicación en tiempo de ejecución.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="property-pad"></a>Panel de propiedades

El diseñador admite la edición de propiedades de widget a través de la **controlador de propiedad**. Las propiedades enumeradas en el cambio de propiedad Pad dependiendo de qué widget está seleccionado en la superficie del diseñador. Cuando el `Button` está seleccionada en el ejemplo anterior, las propiedades para que `Button` widget se muestran:

[![Captura de pantalla de panel de propiedades](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="properties-window"></a>Ventana Propiedades

El diseñador admite la edición de propiedades de widget a través de la **ventana propiedades**. Las propiedades enumeradas en el cambio de la ventana de propiedades dependiendo de qué widget está seleccionado en la superficie de diseño.
Cuando el `Button` está seleccionada en el ejemplo anterior, las propiedades para que `Button` widget se muestran:

![Captura de pantalla de la ventana Propiedades](designer-basics-images/vs/08-property-pad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="property-pad-sections"></a>Secciones del panel de propiedades

El panel de propiedades se divide en varias secciones que agrupan otras propiedades similares &ndash; resulta más fácil encontrar las propiedades de interés:

-   **Widget de** &ndash; propiedades principales del widget, como `id`, `visibility`, `text`, etcetera. Propiedades para administrar el contenido del widget normalmente se colocan aquí.

-   **Estilo** &ndash; propiedades que cambian el aspecto visual del widget, como `font`, `text color`, `background`, etcetera.

-   **Diseño** &ndash; propiedades que establezca la ubicación y el tamaño del widget.

-   **Desplazamiento** &ndash; propiedades de desplazamiento.

-   **Comportamiento** &ndash; marcas que establece cómo se comporta el widget.

-----



### <a name="default-values"></a>Valores predeterminados

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Las propiedades de la mayoría de widgets se quedan en blanco en el **propiedades** ventana porque sus valores hereden el tema de Android seleccionado.
El **propiedades** ventana sólo mostrará los valores que se establecen explícitamente para el widget seleccionado; no mostrará los valores que se heredan del tema.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Las propiedades de la mayoría de widgets se quedan en blanco en el **controlador de propiedad** porque sus valores hereden el tema de Android seleccionado. El **controlador de propiedad** sólo mostrará los valores que se establecen explícitamente para el widget seleccionado; no mostrará los valores que se heredan del tema.

-----


### <a name="referencing-resources"></a>Hacer referencia a recursos

Algunas propiedades pueden hacer referencia a recursos que se definen en archivos que no sean el diseño **.axml** archivo. Los casos más comunes de este tipo son `string` y `drawable` recursos. Sin embargo, también se pueden utilizar referencias de otros recursos, como `Boolean` valores y las dimensiones.
Cuando una propiedad es compatible con las referencias de recursos, un icono de examinar (un botón de puntos suspensivos, &hellip;) se muestra junto a la entrada de texto para la propiedad.
Este botón abre un selector de recursos al hacer clic en.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por ejemplo, la captura de pantalla siguiente muestra los recursos disponibles al hacer clic en los puntos suspensivos a la derecha del campo de texto para un `Button` widget en el **propiedades** ventana:

[![Captura de pantalla de recursos de ejemplo con dos recursos de la lista](designer-basics-images/vs/09-resources-sml.png)](designer-basics-images/vs/09-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Por ejemplo, la captura de pantalla siguiente muestra los recursos disponibles al hacer clic en los puntos suspensivos a la derecha del campo de texto para un `Button` widget en el **controlador de propiedad**:

[![Captura de pantalla de recursos de ejemplo con dos recursos de la lista](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

-----

En el ejemplo siguiente se muestra el selector de recursos para la `Src` propiedad de un `ImageView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Lista de recurso de icono para un ImageView de selector de recursos](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de recurso de icono para un ImageView de selector de recursos](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

-----



### <a name="boolean-property-references"></a>Referencias de propiedad booleano

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

*Booleano* propiedades normalmente se eligen de un menú desplegable en la ventana Propiedades. Puede seleccionar una `true` o `false` valor, o puede seleccionar una referencia de propiedad haciendo clic en **Seleccionar recurso...** . Se puede escribir directamente un valor como `true` o `false`.

![Ejemplo de configuración de propiedades booleanas](designer-basics-images/vs/11-boolean.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

*Booleano* propiedades normalmente se muestran como una casilla de verificación en el panel de propiedades. Cuando un `Boolean` propiedad es compatible con las referencias de recursos, aparece una pequeña casilla de verificación junto a la propiedad. Una casilla activada significa `true` y un cuadro vacío significa `false`. Se puede escribir directamente un valor como `true` o `false`. Mantenga el puntero del mouse sobre la entrada hará que aparezca un icono de campo de texto pequeño. Si desea volver a introducir el valor manualmente, puede hacer clic en él.

[![Ejemplo de configuración de propiedades booleanas](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>Propiedades agrupadas

Algunos widgets tienen propiedades de varios valores que se agrupan (como `Padding`, por ejemplo). Los valores de propiedad se muestran en la **controlador de propiedad** en una fila única, puede expandir. Algunas de estas propiedades se pueden editar directamente en la fila agrupada, como la `Padding` propiedad que se muestra a continuación:

[![Configuración de ejemplo de la propiedad de relleno](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

-----


## <a name="editing-properties-inline"></a>Editar propiedades insertadas

El Diseñador de Android admite la edición directa de determinadas propiedades de la superficie de diseño (por lo que no tiene que buscar estas propiedades en la lista de propiedades). Propiedades que pueden modificarse directamente incluyen texto, margen y tamaño.

### <a name="text"></a>Texto

Las propiedades de texto de algunos widgets (como `Button` y `TextView`), se puede editar directamente en la superficie de diseño. Haga doble clic en un widget se ponerla en modo de edición, tal y como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recurso de texto de la cadena de hello](designer-basics-images/vs/12-text-resource.png "recurso de texto")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Recurso de texto de la cadena de Hola](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

-----

Puede escribir un nuevo valor de texto o puede escribir una nueva cadena de recurso. En el ejemplo siguiente, la `@string/hello` recursos que se va a reemplazar con el texto, `CLICK THIS BUTTON`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![MAYÚS + ENTRAR para texto se vinculan automáticamente a un nuevo recurso](designer-basics-images/vs/13-shift-enter-resource.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![MAYÚS + ENTRAR para texto se vinculan automáticamente a un nuevo recurso](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

-----

Este cambio se almacena en el widget `text` propiedad; no modifica el valor asignado a la `@string/hello` recursos.
Cuando la clave en una nueva cadena de texto, puede presionar <kbd>MAYÚS</kbd> +
<kbd>ENTRAR</kbd> para el texto escrito se vinculan automáticamente a un nuevo recurso.


### <a name="margin"></a>Margin

Cuando se selecciona un widget, el diseñador muestra los identificadores que le permiten cambiar el tamaño o el margen del widget de forma interactiva. Haga clic en el widget mientras está seleccionada alterna entre el modo de edición de tamaño y modo de edición de margen.

Al hacer clic en un widget por primera vez, se muestran los controladores de margen. Si mueve el mouse a uno de los controladores, el diseñador muestra la propiedad que cambie el identificador (tal y como se muestra a continuación para el `layout_marginLeft` propiedad):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Captura de pantalla muestra el margen de controles en el diseñador](designer-basics-images/vs/15-margin-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Captura de pantalla muestra el margen de controles en el diseñador](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

-----

Si ya se ha establecido un margen, aparecen líneas de puntos que indica el espacio que ocupa el margen:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Ejemplo de las líneas de puntos para marcar el espacio alrededor de un botón](designer-basics-images/vs/16-margins-set.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ejemplo de las líneas de puntos para marcar el espacio alrededor de un botón](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

-----



### <a name="size"></a>Tamaño

Tal y como se mencionó anteriormente, puede cambiar al modo de edición de tamaño haciendo clic en un widget mientras aún está seleccionada. Haga clic en el controlador triangular para establecer el tamaño para la dimensión indicada a `wrap_content`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Identificadores de encapsulado de contenido y de cambio de tamaño](designer-basics-images/vs/17-wrap-content.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Identificadores de encapsulado de contenido y de cambio de tamaño](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

-----

Al hacer clic en el **Ajustar contenido** identificador reduce el widget en esa dimensión por lo que es no más de lo necesario ajustar el contenido entre comillas. En este ejemplo, el texto del botón se reduce horizontalmente tal y como se muestra en la captura de pantalla siguiente.

Cuando se establece el valor de tamaño **Ajustar contenido**, el diseñador muestra un identificador triangular que apunta en la dirección opuesta para cambiar el tamaño a `match_parent`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Identificador de elemento primario de coincidencia](designer-basics-images/vs/18-match-parent.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Identificador de elemento primario de coincidencia](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

-----

Al hacer clic en el **coincidencia primario** identificador restaura el tamaño de la dimensión para que sea el mismo que el widget del elemento primario.

Además, puede arrastrar el controlador de tamaño circular (como se muestra en las capturas de pantalla anterior) para cambiar el tamaño del widget para arbitrario `dp` valor. Al hacerlo, ambos **Ajustar contenido** y **coincidencia primario** identificadores se presentan para esa dimensión:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Controladores de tamaño circular](designer-basics-images/vs/19-resize-dp.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Controladores de tamaño circular](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

-----

No todos los contenedores permiten editar el `Size` de un widget. Por ejemplo, observe que en la captura de pantalla siguiente con el `LinearLayout` seleccionado, los controladores de tamaño no aparecen:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![No hay controladores de tamaño](designer-basics-images/vs/20-no-resize-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![No hay controladores de tamaño](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

-----



## <a name="document-outline"></a>Esquema del documento

El **esquema del documento** muestra la jerarquía del widget del diseño.
En el ejemplo siguiente, la que contiene `LinearLayout` widget está seleccionado:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Esquema del documento](designer-basics-images/vs/21-document-outline.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Esquema del documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

-----

El esquema del widget seleccionado (en este caso, un `LinearLayout`) también se resalta en la superficie de diseño. El widget seleccionado en el esquema del documento permanece sincronizado con su equivalente en la superficie de diseño. Esto es útil para la selección de grupos de vista, que no siempre son fáciles de seleccionar en la superficie de diseño.

El esquema del documento es compatible con copiar y pegar, o puede usar arrastrar y colocar. Arrastrar y colocar se admite desde el esquema del documento a la superficie de diseño, así como de la superficie de diseño para el esquema del documento. Además, con el botón secundario de un elemento en el esquema del documento muestra el menú contextual para ese elemento (el mismo menú de contexto que aparece cuando hace clic en ese mismo widget en la superficie de diseño).
