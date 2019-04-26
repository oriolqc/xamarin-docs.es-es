---
title: Conceptos básicos de Xamarin.Android Designer
description: En este tema se presenta las características del Diseñador de Xamarin.Android, se explica cómo iniciar el diseñador, describe la superficie de diseño y detalla cómo usar el panel de propiedades para editar las propiedades del widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/05/2018
ms.openlocfilehash: fe909d72f3c6d6733318b5dcbd1858a1a9e28b37
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61165695"
---
# <a name="xamarinandroid-designer-basics"></a>Conceptos básicos de Xamarin.Android Designer

_En este tema se presenta las características del Diseñador de Xamarin.Android, se explica cómo iniciar el diseñador, describe la superficie de diseño y detalla cómo usar el panel de propiedades para editar las propiedades del widget._


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>Iniciar el diseñador

El diseñador se inicia automáticamente cuando se crea un diseño, o se puede iniciar haciendo doble clic en un archivo de diseño existente. Por ejemplo, haga doble clic en **activity_main.axml** en el **recursos > diseño** carpeta cargará el diseñador tal como se muestra en esta captura de pantalla:

[![Pantalla de diseñador en Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

Del mismo modo, puede agregar un nuevo diseño haciendo clic con el **diseño** carpeta en el **el Explorador de soluciones** y seleccionando **Agregar > nuevo elemento... > Diseño de Android**:

[![Agregar cuadro de diálogo nuevo elemento](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

Esto crea un nuevo **.axml** archivo de diseño y los carga en el diseñador.

## <a name="designer-features"></a>Características del diseñador

El diseñador se compone de varias secciones que admiten sus diversas características, como se muestra en la captura de pantalla siguiente:

[![Diagrama de los paneles del diseñador](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

Cuando se edita un diseño en el diseñador, utilice las siguientes características para crear y dar forma a su diseño:

-   **Superficie de diseño** &ndash; facilita la construcción visual de la interfaz de usuario, lo que le ofrece una representación editable de cómo aparecerá el diseño en el dispositivo. El **superficie de diseño** se muestra dentro de la **panel de diseño** (con el **barra de herramientas del diseñador** situado por encima de él).

-   **Panel de origen** &ndash; proporciona una vista del origen de XML subyacente que se corresponde con el diseño que se presentan en el **superficie de diseño**.

-   **Barra de herramientas del diseñador** &ndash; muestra una lista de selectores: **Dispositivo**, **versión**, **tema**, diseño y configuración de la barra de acciones. El **barra de herramientas del diseñador** también incluye iconos para iniciar el Editor de temas y para habilitar la cuadrícula de diseño de materiales.

-   **Cuadro de herramientas** &ndash; proporciona una lista de widgets y los diseños que puede arrastrar y colocar en el **superficie de diseño**.

-   **Ventana propiedades** &ndash; se enumeran las propiedades del widget seleccionado para verla y editarla.

-   **Esquema de documento** &ndash; muestra el árbol de widgets que componen el diseño. Puede hacer clic en un elemento en el árbol para provocar que se pueden seleccionar en el **superficie de diseño**. Además, al hacer clic en un elemento en el árbol de la carga las propiedades del elemento en el **propiedades** ventana.

## <a name="design-surface"></a>Superficie de diseño

El diseñador hace posible para que pueda arrastrar y colocar widgets desde el cuadro de herramientas hasta la **superficie de diseño**. Al interactuar con los widgets en el diseñador (al agregar widgets nuevos o cambiar la posición de los existentes), aparecen líneas verticales y horizontales para marcar los puntos de inserción disponibles. En el ejemplo siguiente, un nuevo `Button` widget se arrastra a la **superficie de diseño**:

[![Líneas de inserción de ejemplo en la superficie de diseño](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

Además, se pueden copiar los widgets: puede usar la copia y pegado para copiar un widget, o bien puede arrastrar y colocar un widget existente mientras presiona la <kbd>CTRL</kbd> clave.

### <a name="designer-toolbar"></a>Barra de herramientas del diseñador

El **barra de herramientas del diseñador** (situado encima de la **superficie de diseño**) presenta los selectores de configuración y los menús de la herramienta:

[![Diagrama de la barra de herramientas del diseñador](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

El **barra de herramientas del diseñador** proporciona acceso a las siguientes características:

-   **Selector de diseño alternativo** &ndash; le permite seleccionar desde las versiones de un diseño diferente.

-   **Selector de dispositivos** &ndash; define un conjunto de calificadores (por ejemplo, el tamaño de la pantalla, resolución y disponibilidad de teclado) asociado con un dispositivo determinado. También puede agregar y eliminar nuevos dispositivos.

-   **Selector de versión de Android** &ndash; The Android versión que tiene como destino el diseño. El diseñador representará el diseño según la versión de Android seleccionado.

-   **Selector de tema** &ndash; selecciona el tema de la interfaz de usuario para el diseño.

-   **Selector de configuración** &ndash; selecciona la configuración del dispositivo, como *vertical* o *horizontal*.

-   **Opciones del calificador de recursos** &ndash; abre un cuadro de diálogo que muestra los menús desplegables para seleccionar *lenguaje*, *modo UI*, *modo nocturno*, y *Pantalla redonda* opciones.

-   **Configuración de la barra de acción** &ndash; configura las opciones de la barra de acciones para el diseño.

-   **Editor de temas** &ndash; abre el *Theme Editor*, lo que hace posible que le permiten personalizar los elementos del tema seleccionado.

-   **Cuadrícula de diseño de materiales** &ndash; habilita o deshabilita la *Material de diseño de cuadrícula*. El elemento de menú desplegable junto a la cuadrícula de diseño de Material abre un cuadro de diálogo que le permite personalizar la cuadrícula.

Cada una de estas características se explica con más detalle en estos temas:

-   [Calificadores de recursos y las opciones de visualización](~/android/user-interface/android-designer/resource-qualifiers.md) proporciona información detallada sobre la **Selector de dispositivos**, **Selector de versión de Android**, **Selector de tema**, **Selector de configuración**, **recursos calificaciones opciones**, y **configuración de la barra de acción**.

-   [Vistas alternativas de diseño](~/android/user-interface/android-designer/alternative-layout-views.md) explica cómo utilizar el **Selector de diseño alternativa**.

-   [Las características de diseño de Xamarin.Android Designer Material](~/android/user-interface/android-designer/material-design-features.md) proporciona una visión integral de la **Theme Editor** y **Material de diseño de cuadrícula**.

### <a name="context-menu-commands"></a>Comandos de menú contextual

Un menú contextual está disponible tanto en el **superficie de diseño** y en el **esquema del documento**. Este menú muestra los comandos que están disponibles para el widget seleccionado y su contenedor, facilitando la tarea para realizar operaciones en contenedores (que no son siempre fáciles de seleccionar en el **superficie de diseño**). Este es un ejemplo de un menú contextual:

[![Menú contextual de ejemplo cuando haciendo clic en la superficie de diseño](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

En este ejemplo, haciendo clic en un `TextView` abre un menú contextual que ofrece varias opciones:

-   **LinearLayout** &ndash; abre un submenú para editar el `LinearLayout` primario de la `TextView`.

-   **Eliminar**, **copia**, y **cortar** &ndash; operaciones que se aplican a la que se hace `TextView`.


### <a name="zoom-controls"></a>Controles de zoom

El **superficie de diseño** admite Zoom a través de varios controles, como se muestra:

[![Diagrama de los controles de zoom de la superficie de diseño](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

Estos controles que sea más fácil ver ciertas áreas de la interfaz de usuario en el diseñador:

-   **Resaltar contenedores** &ndash; resalta los contenedores en el **superficie de diseño** para que sean más fáciles de localizar al acercar y alejar.

-   **Tamaño normal** &ndash; representa el diseño de píxeles para que puedan ver cuál será la apariencia del diseño en la resolución del dispositivo seleccionado.

-   **Ajustar a la ventana** &ndash; establece el nivel de zoom para que todo el diseño está visible en la superficie de diseño.

-   **Acercar** &ndash; hace zoom forma incremental con cada clic, ampliar el diseño.

-   **Alejar** &ndash; aleja de forma incremental con cada clic, parezca que el diseño más pequeños en la superficie de diseño.

Tenga en cuenta que la configuración del zoom elegido no afecta a la interfaz de usuario de la aplicación en tiempo de ejecución.

## <a name="switching-between-design-and-source-panes"></a>Cambiar entre paneles de diseño y código fuente

En la franja de center entre el **diseño** y **origen** paneles, hay varios botones que se usan para modificar el modo **diseño** y **origen**se muestran los paneles:

[![Ubicaciones de botón Mostrar panel](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

Estos botones realice lo siguiente:

-   **Diseño** &ndash; este botón superior, **diseño**, selecciona el **diseño** panel. Cuando se hace clic en este botón, el **cuadro de herramientas** y **propiedades** paneles están habilitados y el **barra de herramientas del Editor de texto** no se muestra. Cuando el **contraer** se hace clic en botón (ver abajo), el **diseño** panel se presenta por sí solo, sin la **origen** panel.

-   **Intercambiar paneles** &ndash; este botón (que es similar a dos flechas opuestas) intercambia la **diseño** y **origen** paneles para que la **origen** panel se encuentra en la izquierda y la **diseño** panel está a la derecha. Hacer clic de nuevo intercambios estos paneles la copia en sus ubicaciones originales.

-   **Origen** &ndash; selecciona este botón (que es similar a dos corchetes angulares opuestos) el **origen** panel. Cuando se hace clic en este botón, el **cuadro de herramientas** y **propiedades** paneles están deshabilitados y **barra de herramientas del Editor de texto** se hace visible en la parte superior de Visual Studio. Cuando el **contraer** botón se hizo clic (consulte más abajo), al hacer clic en el **origen** botón muestra el **origen** panel en lugar de la **diseño** panel.

-   **División vertical** &ndash; este botón (que es similar a una barra vertical), se muestra el **diseño** y **origen** paneles side-by-side. Se trata de la organización predeterminada.

-   **División horizontal** &ndash; este botón (que es similar a una barra horizontal), se muestra el **diseño** situado encima del **origen** panel. **Intercambiar paneles** se puede hacer clic para colocar el **origen** situado encima del **diseño** panel.

-   **Contraer panel** &ndash; la presentación de dos paneles de este botón (que es similar a dos corchetes angulares hacia la derecha) "contrae" **diseño** y **origen** en una sola vista de uno de Estos paneles.
    Este botón se convierte en el **Expandir panel** botón (similar a dos corchetes angulares hacia la izquierda), que se puede hacer clic para devolver la vista a dos paneles (**diseño** y **origen**) modo de presentación.

Cuando **Contraer panel** se hizo clic, sino la **diseño** se muestra el panel. Sin embargo, puede hacer clic en el **origen** botón para ver en su lugar, solo el **origen** panel. Haga clic en el **diseño** botón para volver a la **diseño** panel.

## <a name="source-pane"></a>Panel de código fuente

El **origen** panel muestra el origen XML subyacente del diseño se muestra en el **superficie de diseño**. Dado que las dos vistas están disponibles al mismo tiempo, es posible crear un diseño de interfaz de usuario desde y hacia atrás entre una representación visual del diseño y el origen XML subyacente para el diseño:

[![Origen XML de ejemplo en el panel de código fuente](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

Los cambios realizados en el origen de XML que se presentan inmediatamente en el **superficie de diseño**; los cambios realizados en el **superficie de diseño** hacer que el origen XML que se muestra en el **origen** panel a se actualiza en consecuencia. Cuando realice cambios en el XML en el **origen** panel, finalización automática y las características de IntelliSense están disponibles para la velocidad de desarrollo de la interfaz de usuario basada en XML como se explica a continuación.

Para mayor facilidad la navegación al trabajar con archivos XML largos, el **origen** panel es compatible con la barra de desplazamiento de Visual Studio (tal y como se muestra a la derecha en la captura de pantalla anterior). Para obtener más información acerca de la barra de desplazamiento, vea [cómo el seguimiento del código personalizando la barra de desplazamiento](https://msdn.microsoft.com/library/dn237345.aspx).


### <a name="autocompletion"></a>Finalización automática

Cuando comienza a escribir el nombre de un atributo para un widget, puede presionar <kbd>CTRL+BARRA espaciadora</kbd> para ver una lista de finalizaciones posibles. Por ejemplo, después de escribir `android:lay` en el ejemplo siguiente (seguir escribiendo <kbd>CTRL+BARRA espaciadora</kbd>), se presenta en la lista siguiente:

[![Finalización automática del atributo de diseño](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

Presione <kbd>ENTRAR</kbd> para aceptar la primera lista de finalización, o use las teclas de flecha para desplazarse a la finalización deseada y presione <kbd>ENTRAR</kbd>. Como alternativa, puede usar el mouse para desplazarse a y haga clic en la finalización deseada.

### <a name="intellisense"></a>IntelliSense

Después de escribir un nuevo atributo para un widget y empezar a asignarle un valor, IntelliSense aparece después de un carácter desencadenador es de tipo y proporciona una lista de valores válidos que se usará para ese atributo. Por ejemplo, una vez se haya especificado la primera comilla doble para `android:layout_width` en el ejemplo siguiente, un selector de Autocompletar Emerge para proporcionar la lista de opciones válidas para este ancho:

[![Ejemplo de IntelliSense para el ancho de diseño](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

En la parte inferior de este menú emergente hay dos botones (tal como se describe en rojo en la captura de pantalla anterior). Al hacer clic en el **los recursos del proyecto** botón de la izquierda restringe la lista a los recursos que forman parte del proyecto de aplicación, mientras hace clic en el **Framework Resources** situado a la derecha restringe la lista para Mostrar los recursos disponibles en el marco de trabajo.
Estos botones activar o desactivar: puede hacer clic en nuevo para deshabilitar la acción de filtrado que proporciona cada.



## <a name="properties-pane"></a>Panel de propiedades

El diseñador admite la edición de propiedades de widget a través de la **propiedades** panel: 

![Captura de pantalla de la ventana Propiedades](designer-basics-images/vs/08-property-pad.png)

Las propiedades enumeradas en la **propiedades** cambio panel dependiendo de qué widget está seleccionado en el **superficie de diseño**.

### <a name="default-values"></a>Valores predeterminados

Las propiedades de la mayoría de widgets estará en blanco en el **propiedades** ventana porque sus valores heredan el tema seleccionado Android.
El **propiedades** ventana solo mostrará los valores que se establecen explícitamente para el widget seleccionado; no mostrará los valores que se heredan del tema.

### <a name="referencing-resources"></a>Hacer referencia a recursos

Algunas propiedades pueden hacer referencia a los recursos que se definen en archivos que no sean el diseño **.axml** archivo. Los casos más comunes de este tipo son `string` y `drawable` recursos. Sin embargo, también se pueden utilizar referencias de otros recursos, tales como `Boolean` valores y las dimensiones. Cuando una propiedad es compatible con las referencias de recursos, se muestra un icono de exploración (un cuadrado) junto a la entrada de texto para la propiedad. Este botón abre al hacer clic en un selector de recursos.

Por ejemplo, la captura de pantalla siguiente muestra las opciones disponibles al hacer clic en el cuadrado oscura a la derecha del campo de texto para un `Text` widget en el **propiedades** ventana:

[![Ejemplo de lista de opciones de texto](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

Cuando **recursos...**  se hace clic en, el **Seleccionar recurso** cuadro de diálogo:

[![Captura de pantalla de los recursos de ejemplo con varios recursos que se muestran](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

En esta lista, puede seleccionar un recurso de texto que se usará para ese widget, en lugar de codificar el texto en el **propiedades** panel. En el ejemplo siguiente se muestra el selector de recursos para la `Src` propiedad de un `ImageView`:

[![Lista de recursos de icono para un ImageView de selector de recursos](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

Al hacer clic en el cuadrado en blanco a la derecha de la `Src` propiedad abre el **Seleccionar recurso** cuadro de diálogo con una lista de recursos comprendido entre colores (como se indicó anteriormente) y recursos drawable.


### <a name="boolean-property-references"></a>Referencias de propiedad booleano

*Booleano* normalmente están seleccionadas las propiedades como marcas de verificación junto a una propiedad en la ventana Propiedades. Puede designar un `true` o `false` valor activando o desactivando la casilla de verificación o puede seleccionar una referencia de propiedad haciendo clic en el cuadrado relleno oscuro a la derecha de la propiedad. En el ejemplo siguiente, se cambia texto a mayúsculas, haga clic en el **texto todas mayúsculas** referencia de propiedad booleano asociado seleccionado `TextView`:

![Ejemplo de cómo configurar las propiedades booleanas](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>Editar propiedades insertadas

El Diseñador de Android es compatible con la edición directa de ciertas propiedades en el **superficie de diseño** (por lo que no tiene que buscar estas propiedades en la lista de propiedades). Las propiedades que se pueden editar directamente incluyen texto, margen y tamaño.

### <a name="text"></a>Texto

Las propiedades de texto de algunos de los widgets (como `Button` y `TextView`), se pueden editar directamente en el **superficie de diseño**. Haga doble clic en un widget colocaremos en modo de edición, como se muestra a continuación:

![Recurso de texto de la cadena hello](designer-basics-images/vs/12-text-resource.png "recurso de texto")

Puede escribir un nuevo valor de texto o puede escribir una nueva cadena de recurso. En el ejemplo siguiente, la `@string/hello` recurso se ha sustituido por el texto, `CLICK THIS BUTTON`:

![MAYÚS + ENTRAR para vincular el texto automáticamente a un nuevo recurso](designer-basics-images/vs/13-shift-enter-resource.png)

Este cambio se almacena en el widget `text` propiedad; no modifica el valor asignado a la `@string/hello` recursos.
Cuando la clave en una nueva cadena de texto, puede presionar <kbd>MAYÚS</kbd> +
<kbd>ENTRAR</kbd> para el texto escrito se vinculan automáticamente a un nuevo recurso.

### <a name="margin"></a>Margen

Cuando se selecciona un widget, el diseñador muestra los identificadores que le permiten cambiar el tamaño o el margen del widget de forma interactiva. Al hacer clic en el widget cuando se selecciona éste alterna entre el modo de edición de margen y el modo de edición de tamaño.

Al hacer clic en un widget por primera vez, se muestran los controladores de margen. Si mueve el mouse a uno de los controladores, el diseñador muestra la propiedad que cambiará el identificador (como se muestra a continuación para el `layout_marginLeft` propiedad):

![Captura de pantalla muestra el margen se controla en el diseñador](designer-basics-images/vs/15-margin-handles.png)

Si ya se ha establecido un margen, aparecen las líneas de puntos que indica el espacio que ocupa el margen:

![Ejemplo de las líneas de puntos para marcar el espacio alrededor de un botón](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>Tamaño

Como se mencionó anteriormente, puede cambiar al modo de edición de tamaño haciendo clic en un widget mientras aún está seleccionada. Haga clic en el controlador para establecer el tamaño de la dimensión indicada para triangular `wrap_content`:

![Identificadores de contenido encapsulado y cambio de tamaño](designer-basics-images/vs/17-wrap-content.png)

Al hacer clic en el **Ajustar contenido** identificador reduce el widget de la dimensión para que sea no más de lo necesario ajustar el contenido incluido. En este ejemplo, el texto del botón se reduce horizontalmente como se muestra en la captura de pantalla siguiente.

Cuando se establece el valor de tamaño en **Ajustar contenido**, el diseñador muestra un identificador triangular que apunta en la dirección opuesta para cambiar el tamaño de `match_parent`:

![Identificador de elemento primario de coincidencia](designer-basics-images/vs/18-match-parent.png)

Al hacer clic en el **coincidencia primario** identificador restaura el tamaño de la dimensión para que sea igual al widget primario.

Además, puede arrastrar el controlador de tamaño circular (como se muestra en las capturas de pantalla anterior) para cambiar el tamaño del widget para arbitrario `dp` valor. Al hacerlo, ambos **Ajustar contenido** y **coincidencia primario** se presentan identificadores para esa dimensión:

![Controladores de tamaño circular](designer-basics-images/vs/19-resize-dp.png)

No todos los contenedores permiten editar el `Size` de un widget. Por ejemplo, observe que en la siguiente captura de pantalla con la `LinearLayout` seleccionada, los controladores de tamaño no aparecen:

![No hay controladores de tamaño](designer-basics-images/vs/20-no-resize-handles.png)


## <a name="document-outline"></a>Esquema del documento

El **esquema del documento** muestra la jerarquía de widgets del diseño.
En el ejemplo siguiente, la que contiene `LinearLayout` widget está seleccionado:

![Ejemplo de esquema de documento](designer-basics-images/vs/21-document-outline.png)

El esquema del widget seleccionado (en este caso, un `LinearLayout`) también se resalta en el **superficie de diseño**. El widget seleccionado en el esquema del documento permanece sincronizado con su homólogo en el **superficie de diseño**. Esto es útil para seleccionar los grupos de vista, que no siempre son fáciles de seleccionar en el **superficie de diseño**.

El **esquema del documento** admite copiar y pegar o puede usar arrastrar y colocar. Se admite arrastrar y colocar desde el **esquema del documento** a la **superficie de diseño** , así como de la **superficie de diseño** a la **esquema del documento**. Además, con el botón secundario en un elemento del **esquema del documento** muestra el menú contextual para ese elemento (el mismo menú contextual que aparece cuando hace clic en ese mismo widget en el **superficie de diseño**).




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="launching-the-designer"></a>Iniciar el diseñador

El diseñador se inicia automáticamente cuando se crea un diseño, o se puede iniciar haciendo doble clic en un archivo .axml existente. Por ejemplo, haga doble clic en **Main.axml** en el **recursos > diseño** carpeta cargará el diseñador tal como se muestra a continuación:

[![Pantalla de diseñador en Visual Studio para Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

Del mismo modo, puede agregar un nuevo diseño haciendo clic con el **diseño** carpeta en el **panel de solución** y seleccionando **Agregar > nuevo archivo > Android > diseño**:

[![Agregar cuadro de diálogo nuevo archivo](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

Esto crea un nuevo archivo .axml y lo carga en la superficie de diseño.

## <a name="designer-features"></a>Características del diseñador

El diseñador se compone de varias secciones que admiten sus diversas características, como se muestra en la captura de pantalla siguiente:

[![Diagrama de los paneles del diseñador](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

Cuando se edita un diseño en el diseñador, utilice las siguientes características para crear y dar forma a su diseño:

-   **Superficie de diseño** &ndash; facilita la construcción visual de la interfaz de usuario, lo que le ofrece una representación editable de cómo aparecerá el diseño en el dispositivo.

-   **Barra de herramientas** &ndash; muestra una lista de selectores: **Dispositivo**, **versión**, **tema**, diseño y configuración de la barra de acciones. La barra de herramientas también incluye iconos para iniciar el Editor de temas y para habilitar la cuadrícula de diseño de materiales.

-   **Cuadro de herramientas** &ndash; proporciona una lista de widgets y los diseños que puede arrastrar y colocar en la superficie de diseño.

-   **Panel propiedad** &ndash; se enumeran las propiedades del widget seleccionado para verla y editarla.

-   **Esquema de documento** &ndash; muestra el árbol de widgets que componen el diseño. Puede hacer clic en un elemento en el árbol para hacer que se selecciona en el diseñador. Además, al hacer clic en un elemento en el árbol de la carga las propiedades del elemento en el panel de propiedades.

## <a name="toolbar"></a>Barra de herramientas

La barra de herramientas (situado por encima de la superficie de diseño) presenta los selectores de configuración y los menús de la herramienta:

[![Diagrama de la barra de herramientas del diseñador](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

La barra de herramientas proporciona acceso a las siguientes características:

-   **Selector de diseño alternativo** &ndash; le permite seleccionar desde las versiones de un diseño diferente.

-   **Selector de dispositivos** &ndash; define un conjunto de calificadores asociado con un dispositivo determinado, como el tamaño de la pantalla, la resolución y la disponibilidad de teclado. También puede agregar y eliminar nuevos dispositivos.

-   **Selector de versión de Android** &ndash; The Android versión que tiene como destino el diseño. El diseñador representará el diseño según la versión de Android seleccionado.

-   **Selector de tema** &ndash; selecciona el tema de la interfaz de usuario para el diseño.

-   **Selector de configuración** &ndash; selecciona la configuración del dispositivo, como *vertical* o *horizontal*.

-   **Opciones del calificador de recursos** &ndash; abre un cuadro de diálogo que muestra los menús desplegables para seleccionar *lenguaje*, *modo UI*, *modo nocturno*, y *Pantalla redonda* opciones.

-   **Configuración de la barra de acción** &ndash; configura las opciones de la barra de acciones para el diseño.

-   **Editor de temas** &ndash; abre el *Theme Editor*, lo que hace posible que le permiten personalizar los elementos del tema seleccionado.

-   **Cuadrícula de diseño de materiales** &ndash; habilita o deshabilita la *Material de diseño de cuadrícula*. El elemento de menú desplegable junto a la cuadrícula de diseño de Material abre un cuadro de diálogo que le permite personalizar la cuadrícula.

Cada una de estas características se explica con más detalle en estos temas:

[Calificadores de recursos y las opciones de visualización](~/android/user-interface/android-designer/resource-qualifiers.md) proporciona información detallada sobre la **Selector de dispositivos**, **Selector de versión de Android**, **Selector de tema**, **Selector de configuración**, **recursos calificaciones opciones**, y **configuración de la barra de acción**.

[Vistas alternativas de diseño](~/android/user-interface/android-designer/alternative-layout-views.md) explica cómo utilizar el **Selector de diseño alternativa**.

[Material de las características de diseño](~/android/user-interface/android-designer/material-design-features.md) proporciona una visión integral de la **Theme Editor** y **Material de diseño de cuadrícula**.

## <a name="design-surface"></a>Superficie de diseño

El diseñador permite arrastrar y colocar widgets desde el cuadro de herramientas a la superficie de diseño. Al interactuar con los widgets en el diseñador (al agregar widgets nuevos o cambiar la posición de los existentes), aparecen líneas verticales y horizontales para marcar los puntos de inserción disponibles. En el ejemplo siguiente, un nuevo `Button` widget se arrastra a la superficie de diseño:

[![Líneas de inserción de ejemplo en la superficie de diseño](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

Además, se pueden copiar los widgets: puede usar la copia y pegado para copiar un widget, o bien puede arrastrar y colocar un widget existente mientras presiona la <kbd>Ctrl</kbd> clave.

### <a name="context-menu-commands"></a>Comandos de menú contextual

Un menú contextual está disponible en la superficie de diseño y en el esquema del documento. Este menú muestra los comandos que están disponibles para el widget seleccionado y su contenedor, facilitando la tarea para realizar operaciones en contenedores (que no siempre son fáciles de seleccionar en la superficie de diseño). Este es un ejemplo de un menú contextual:

[![Menú contextual de ejemplo cuando haciendo clic en la superficie de diseño](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

En este ejemplo, haciendo clic en un `Button` abre un menú contextual que ofrece varias opciones:

-   **LinearLayout** &ndash; abre un submenú para editar el `LinearLayout` primario de la `Button`.

-   **Cortar**, **copia**, y **eliminar** &ndash; operaciones que se aplican a la que se hace `Button`.

### <a name="zoom-controls"></a>Controles de zoom

La superficie de diseño admite Zoom a través de varios controles, como se muestra:

[![Diagrama de los controles de zoom de la superficie de diseño](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

Estos controles que sea más fácil ver ciertas áreas de la interfaz de usuario en el diseñador:

-   **Resaltar contenedores** &ndash; resalta los contenedores en la superficie de diseño para que sean más fáciles de localizar al acercar y alejar.

-   **Tamaño normal** &ndash; representa el diseño de píxeles para que puedan ver cuál será la apariencia del diseño en la resolución del dispositivo seleccionado.

-   **Ajustar a la ventana** &ndash; establece el nivel de zoom para que todo el diseño está visible en la superficie de diseño.

-   **Acercar** &ndash; hace zoom forma incremental con cada clic, ampliar el diseño.

-   **Alejar** &ndash; aleja de forma incremental con cada clic, parezca que el diseño más pequeños en la superficie de diseño.

Tenga en cuenta que la configuración del zoom elegido no afecta a la interfaz de usuario de la aplicación en tiempo de ejecución.

## <a name="property-pad"></a>Panel de propiedades

El diseñador admite la edición de propiedades de widget a través de la **panel propiedad**. Las propiedades enumeradas en el cambio de propiedad Pad dependiendo de qué widget está seleccionado en la superficie del diseñador. Cuando el `Button` está seleccionada en el ejemplo anterior, las propiedades para que `Button` widget se muestran:

[![Captura de pantalla del panel de la propiedad](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>Secciones del panel de propiedades

El panel de propiedades se divide en varias secciones que agrupan propiedades similares &ndash; Esto facilita la localización de propiedades de interés:

-   **Widget** &ndash; propiedades principales del widget, tales como `id`, `visibility`, `text`, etcetera. Normalmente se colocan aquí las propiedades para administrar el contenido del widget.

-   **Estilo** &ndash; propiedades que cambian la apariencia visual del widget, como `font`, `text color`, `background`, etcetera.

-   **Diseño** &ndash; propiedades que establecer la ubicación y tamaño del widget.

-   **Desplazamiento** &ndash; las propiedades de desplazamiento.

-   **Comportamiento** &ndash; marcas que establece cómo se comporta el widget.

### <a name="default-values"></a>Valores predeterminados

Las propiedades de la mayoría de widgets estará en blanco en el **panel propiedad** porque sus valores heredan el tema seleccionado Android. El **panel propiedad** sólo mostrará los valores que se establecen explícitamente para el widget seleccionado; no mostrará los valores que se heredan del tema.

### <a name="referencing-resources"></a>Hacer referencia a recursos

Algunas propiedades pueden hacer referencia a los recursos que se definen en archivos que no sean el diseño **.axml** archivo. Los casos más comunes de este tipo son `string` y `drawable` recursos. Sin embargo, también se pueden utilizar referencias de otros recursos, tales como `Boolean` valores y las dimensiones.
Cuando una propiedad es compatible con las referencias de recursos, un icono de exploración (un botón de puntos suspensivos &hellip;) se muestra junto a la entrada de texto para la propiedad.
Al hacer clic, este botón abre un selector de recursos.

Por ejemplo, la captura de pantalla siguiente muestra los recursos disponibles al hacer clic en el botón de puntos suspensivos a la derecha del campo de texto para un `Button` widget en el **panel propiedad**:

[![Captura de pantalla de los recursos de ejemplo con dos recursos enumerados](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

En el ejemplo siguiente se muestra el selector de recursos para la `Src` propiedad de un `ImageView`:

[![Lista de recursos de icono para un ImageView de selector de recursos](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>Referencias de propiedad booleano

*Booleano* propiedades normalmente se muestran como una casilla en el panel de propiedades. Cuando un `Boolean` propiedad admite referencias de recursos, aparece un pequeño cuadro de verificación junto a la propiedad. Una casilla activada significa `true` y un cuadro vacío significa `false`. También puede especificar un valor como `true` o `false`. Mantiene el mouse sobre la entrada aparece un icono de campo de texto pequeño. Si desea escribir manualmente el valor, puede hacer clic en él.

[![Ejemplo de cómo configurar las propiedades booleanas](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>Propiedades agrupadas

Algunos widgets tienen propiedades de varios valores que se agrupan (como `Padding`, por ejemplo). Estos valores de propiedad se muestran en el **panel propiedad** en una fila única y expansible. Algunas de estas propiedades se pueden editar directamente en la fila agrupada, como el `Padding` propiedad que se muestra a continuación:

[![Ejemplo de configuración para la propiedad Padding](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>Editar propiedades insertadas

El Diseñador de Android admite la edición directa de ciertas propiedades en la superficie de diseño (por lo que no tiene que buscar estas propiedades en la lista de propiedades). Las propiedades que se pueden editar directamente incluyen texto, margen y tamaño.

### <a name="text"></a>Texto

Las propiedades de texto de algunos de los widgets (como `Button` y `TextView`), se puede editar directamente en la superficie de diseño. Haga doble clic en un widget colocaremos en modo de edición, como se muestra a continuación:

[![Recurso de texto de la cadena hello](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

Puede escribir un nuevo valor de texto o puede escribir una nueva cadena de recurso. En el ejemplo siguiente, la `@string/hello` recurso se ha sustituido por el texto, `CLICK THIS BUTTON`:

[![MAYÚS + ENTRAR para vincular el texto automáticamente a un nuevo recurso](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

Este cambio se almacena en el widget `text` propiedad; no modifica el valor asignado a la `@string/hello` recursos.
Cuando la clave en una nueva cadena de texto, puede presionar <kbd>MAYÚS</kbd> +
<kbd>ENTRAR</kbd> para el texto escrito se vinculan automáticamente a un nuevo recurso.

### <a name="margin"></a>Margen

Cuando se selecciona un widget, el diseñador muestra los identificadores que le permiten cambiar el tamaño o el margen del widget de forma interactiva. Al hacer clic en el widget cuando se selecciona éste alterna entre el modo de edición de margen y el modo de edición de tamaño.

Al hacer clic en un widget por primera vez, se muestran los controladores de margen. Si mueve el mouse a uno de los controladores, el diseñador muestra la propiedad que cambiará el identificador (como se muestra a continuación para el `layout_marginLeft` propiedad):

[![Captura de pantalla muestra el margen se controla en el diseñador](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

Si ya se ha establecido un margen, aparecen las líneas de puntos que indica el espacio que ocupa el margen:

[![Ejemplo de las líneas de puntos para marcar el espacio alrededor de un botón](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>Tamaño

Como se mencionó anteriormente, puede cambiar al modo de edición de tamaño haciendo clic en un widget mientras aún está seleccionada. Haga clic en el controlador para establecer el tamaño de la dimensión indicada para triangular `wrap_content`:

[![Identificadores de contenido encapsulado y cambio de tamaño](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

Al hacer clic en el **Ajustar contenido** identificador reduce el widget de la dimensión que no más de lo necesario ajustar el contenido incluido. En este ejemplo, el texto del botón se reduce horizontalmente como se muestra en la captura de pantalla siguiente.

Cuando se establece el valor de tamaño en **Ajustar contenido**, el diseñador muestra un identificador triangular que apunta en la dirección opuesta para cambiar el tamaño de `match_parent`:

[![Identificador de elemento primario de coincidencia](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

Al hacer clic en el **coincidencia primario** identificador restaura el tamaño de la dimensión para que sea igual al widget primario.

Además, puede arrastrar el controlador de tamaño circular (como se muestra en las capturas de pantalla anterior) para cambiar el tamaño del widget para arbitrario `dp` valor. Al hacerlo, ambos **Ajustar contenido** y **coincidencia primario** se presentan identificadores para esa dimensión:

[![Controladores de tamaño circular](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

No todos los contenedores permiten editar el `Size` de un widget. Por ejemplo, observe que en la siguiente captura de pantalla con la `LinearLayout` seleccionada, los controladores de tamaño no aparecen:

[![No hay controladores de tamaño](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>Esquema del documento

El **esquema del documento** muestra la jerarquía de widgets del diseño.
En el ejemplo siguiente, la que contiene `LinearLayout` widget está seleccionado:

[![Esquema del documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

El esquema del widget seleccionado (en este caso, un `LinearLayout`) también se resalta en la superficie de diseño. El widget seleccionado en el esquema del documento permanece sincronizado con su equivalente en la superficie de diseño. Esto es útil para seleccionar los grupos de vista, que no siempre son fáciles de seleccionar en la superficie de diseño.

El esquema del documento admite copiar y pegar, o puede usar arrastrar y colocar. Arrastrar y colocar es compatible desde el esquema del documento a la superficie de diseño, así como de la superficie de diseño para el esquema del documento. Además, se muestra el menú contextual para ese elemento (el mismo menú de contexto que aparece cuando hace clic en ese mismo widget en la superficie de diseño) haciendo clic en un elemento en el esquema del documento.

-----
