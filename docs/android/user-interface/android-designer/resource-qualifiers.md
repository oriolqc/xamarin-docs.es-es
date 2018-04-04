---
title: Los calificadores de recursos y opciones de visualización
description: En este tema se explica cómo definir los recursos que se usará cuando se comparan algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena de idioma completo. Un recurso de cadena se puede definir como el valor predeterminado, con otros recursos alternativos definidos para su uso en otros idiomas. Pueden calificar todos los tipos de recurso, incluido el diseño propio.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: bc9eb145b6d9ed7bd441d625f533c5cbbd87fccd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="resource-qualifiers-and-visualization-options"></a>Los calificadores de recursos y opciones de visualización

_En este tema se explica cómo definir los recursos que se usará cuando se comparan algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena de idioma completo. Un recurso de cadena se puede definir como el valor predeterminado, con otros recursos alternativos definidos para su uso en otros idiomas. Pueden calificar todos los tipos de recurso, incluido el diseño propio._


## <a name="custom-device-configurations"></a>Configuraciones de dispositivo personalizado

Android está disponible en una gran cantidad de dispositivos y resoluciones de pantalla.
Para ayudar a las interfaces de usuario de diseño que muchos de los dispositivos de destino, el diseñador incluye una variedad de configuraciones de dispositivos integrados. También permite agregar configuraciones de dispositivo adicionales; Estas configuraciones se basan en *calificadores* que especifique para distinguir una configuración de dispositivo de otra. Hay muchos tipos diferentes de calificadores. Para obtener más información acerca de estos tipos de recursos, consulte [recursos Android](~/android/app-fundamentals/resources-in-android/index.md).

En la parte inferior del Selector de dispositivo de menú es un **personalizar** opción tal y como se muestra a continuación:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menú del Selector de dispositivo](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menú del Selector de dispositivo](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


Seleccionar **personalizar** muestra un cuadro de diálogo que puede usar para examinar las configuraciones de dispositivo disponible. Al hacer clic en el **dispositivo definiciones** ficha, se presenta una lista de todas las definiciones de dispositivo conocido:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Administrador AVD](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Administrador AVD](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


No se puede modificar dispositivos preconfigurados en el diseñador. Sin embargo, puede hacer clic en **crear un dispositivo...**  para definir una definición de dispositivo personalizado. Como alternativa, puede seleccionar una definición existente y haga clic en **clon...**  para utilizarlo como punto de partida para una nueva definición.
Por ejemplo, al seleccionar la definición del **Nexus 5** y hacer clic en **Clone... (Clonar...)** se presenta el siguiente cuadro de diálogo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo de clon](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo de clon](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


En la captura de pantalla siguiente, se cambia el nombre a **Nexus 5 personalizado** y se modifican los parámetros del dispositivo para crear una nueva definición de dispositivo personalizado. En este ejemplo, **vertical** está deshabilitado para que la definición del dispositivo es de solo horizontal:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


Al hacer clic en **Clone Device (Clonar dispositivo)** se crea la nueva definición del dispositivo, que ahora aparece en la lista **Device Definitions (Definiciones de dispositivos)**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Definiciones de dispositivo actualizados](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Definiciones de dispositivo actualizados](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


Tenga en cuenta que cada definición de dispositivo creada por el usuario se muestra con un icono verde como se indicó anteriormente. Cuando vuelva a la **dispositivo** menú selector, se presenta la nueva definición de dispositivo personalizado en la sección superior de la lista (si no ve la configuración de dispositivo personalizado en esta lista, pruebe a reiniciar el IDE):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado aparece en la lista de dispositivos](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado aparece en la lista de dispositivos](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


Selección de esta configuración de dispositivo, modifica el diseño para que se ajuste a las personalizaciones que creó anteriormente (en este caso, horizontal modo):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado en uso](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado en uso](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>Opciones del recurso de calificador

**Opciones de calificador de recurso** puede obtenerse acceso haciendo clic en el icono de triángulo hacia abajo a la derecha de la **configuración del dispositivo** opciones:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Opciones del recurso de calificador](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Opciones del recurso de calificador](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


Este cuadro de diálogo presenta los menús desplegables para los calificadores de recursos siguientes:

-   **Idioma** &ndash; muestra los recursos de idioma disponibles y ofrece una opción para agregar nuevos recursos de idioma y región.

-   **Modo de interfaz de usuario** &ndash; listas de los modos de presentación (como **automóvil acoplar** y **acoplar del departamento de soporte**), así como instrucciones de diseño.

Cada uno de estos menús desplegables abre nuevos cuadros de diálogo donde puede seleccionar y configurar los calificadores de recursos (como se explica a continuación).



### <a name="language"></a>Lenguaje

El **lenguaje** menú desplegable muestra sólo los idiomas que tienen recursos definidos (o **todos los idiomas**, que es el valor predeterminado). Sin embargo, hay también un **Agregar idioma y región...**  opción que le permite agregar un nuevo idioma a la lista:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Agregar el idioma y región](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Agregar el idioma y región](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


Al hacer clic en **Agregar idioma y región...** , **Seleccionar idioma** abrirá el cuadro de diálogo para mostrar listas desplegables de regiones e idiomas disponibles:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lista de idiomas](resource-qualifiers-images/vs/10-languages.png "lista de idiomas")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de idiomas](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


En este ejemplo, que hemos elegido **fr (francés)** para el idioma y **BE** (Bélgica) para el dialecto regional de francés. Tenga en cuenta que la **región** campo es opcional porque muchos idiomas pueden especificarse sin tener en cuenta para regiones específicas. Cuando el **lenguaje** menú desplegable se vuelve a abrir, se muestra el recurso de idioma y región agregadas recientemente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Idioma y región que eligió](resource-qualifiers-images/vs/11-language-region-added.png "elegida de idioma y región")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Idioma y región elegido](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


Tenga en cuenta que si agrega un nuevo lenguaje pero no podrá crear nuevos recursos para él, usará idioma agregado ya no se mostrará la próxima vez que abra el proyecto.



### <a name="ui-mode"></a>Modo de interfaz de usuario

Al hacer clic en el **modo de interfaz de usuario** muestra menú desplegable, una lista de modos, como **Normal**, **automóvil acoplar**, **acoplar del departamento de soporte**, **Televisión**, **dispositivo**, y **inspección**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menú modo de interfaz de usuario](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

A continuación de esta lista se muestran los modos de noche **no noche** y **noche**, seguido de las instrucciones de diseño **de izquierda a derecha** y **de derecha a izquierda** (para información sobre **de izquierda a derecha** y **de derecha a izquierda** opciones, consulte [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).
Los últimos elementos de la **opciones de calificador de recurso** diálogo son el **pantallas de ida y vuelta** (para su uso con desgaste Android) o **pantallas de ida y vuelta no** (para obtener información acerca de ida y pantallas no round, consulte [diseños](https://developer.android.com/training/wearables/ui/layouts.html)).
Para obtener más información acerca de los modos de interfaz de usuario de Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menú modo de interfaz de usuario](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

A continuación de esta lista se muestran los modos de noche **no noche** y **noche**, seguido de las instrucciones de diseño **de izquierda a derecha** y **de derecha a izquierda**. Para obtener más información acerca de los modos de interfaz de usuario de Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Para obtener información acerca de **de izquierda a derecha** y **de derecha a izquierda** opciones, consulte [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).

### <a name="round-screen"></a>Pantalla de redondeo

El último elemento en el **opciones de calificador de recurso** cuadro de diálogo es el **de ida y vuelta pantalla** menú. Este menú le permite seleccionar **pantallas de ida y vuelta** (para su uso con desgaste Android) o **pantallas Rectangular**:

[![Menú de pantalla Round](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>Configuración de la barra de acción

El **configuración de la barra de acción** icono está disponible a la izquierda del icono de pincel (Editor de temas):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Configuración de la barra de acción](resource-qualifiers-images/vs/14-action-bar.png "configuración de la barra de acción")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Configuración de la barra de acción](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


Este icono abre un popover de cuadro de diálogo que proporciona una manera para seleccionar uno de tres modos de barra de acciones:

-   **Estándar** &ndash; está formada por un logotipo o el icono y el título de texto con un subtítulo (opcional).

-   **Lista** &ndash; modo de exploración de la lista. En lugar de texto de título estático, este modo presenta un menú de lista para la navegación dentro de la actividad (es decir, se puede presentar al usuario como una lista desplegable).

-   **Pestañas** &ndash; modo de exploración de pestaña. En lugar de texto de título estático, este modo presenta una serie de pestañas de navegación dentro de la actividad.



## <a name="themes"></a>Temas

El **tema** menú desplegable muestra todos los temas definidos en el proyecto. Seleccionar **más temas** abre un cuadro de diálogo con una lista de todos los temas disponibles en el SDK de Android instalado, tal y como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Lista de temas más](resource-qualifiers-images/vs/15-theme-menu-sml.png "más temas de la lista")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Más temas de la lista](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


Cuando se selecciona un tema, la superficie de diseño se actualiza para mostrar el efecto del nuevo tema. Tenga en cuenta que este cambio se hace permanente solo si la **Aceptar** botón se hace clic en el **tema** cuadro de diálogo. Una vez que se ha seleccionado un tema, se incluirá en el **tema** menú desplegable tal como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Ahora está disponible el tema claro](resource-qualifiers-images/vs/16-light-theme.png "ahora está disponible el tema claro")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ahora está disponible el tema claro](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Versión de Android

El Android **versión** selector establece la versión de Android que se usa para representar el diseño en el diseñador. El selector muestra todas las versiones que son compatibles con la versión de framework de destino del proyecto:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lista de versiones de Android](resource-qualifiers-images/vs/17-android-version.png "versiones de lista de Android")

La versión de framework de destino se puede establecer en la configuración del proyecto en **Propiedades > aplicaciones > compilar con la versión Android**. Para obtener más información acerca de la versión de framework de destino, vea [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas se determina por la versión de framework de destino del proyecto. Esto también es cierto para las propiedades disponibles en la **ventana propiedades**. La lista disponible de widgets es *no* determinado por el valor seleccionado en el **versión** selector de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto para Android 4.4, aún puede seleccionar Android 6.0 en el selector de versión de la barra de herramientas para ver el aspecto que tiene el proyecto en Android 6.0, pero no podrá agregar widgets que son específicas de Android 6.0 &ndash;  todavía se limitará a los widgets que están disponibles en Android 4.4.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de versiones de Android](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

La versión de framework de destino se puede establecer en la configuración del proyecto en el **Project Options > compilar > General** sección. Para obtener más información acerca de la versión de framework de destino, vea [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas se determina por la versión de framework de destino del proyecto. Esto también es cierto para las propiedades disponibles en la **controlador de propiedad**. La lista disponible de widgets es *no* determinado por el valor seleccionado en el **versión** selector de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto para Android 4.4, aún puede seleccionar Android 6.0 en el selector de versión de la barra de herramientas para ver el aspecto que tiene el proyecto en Android 6.0, pero no podrá agregar widgets que son específicas de Android 6.0 &ndash;  todavía se limitará a los widgets que están disponibles en Android 4.4.

-----
