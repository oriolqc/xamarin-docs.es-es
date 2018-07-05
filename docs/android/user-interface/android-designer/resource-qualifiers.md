---
title: Calificadores de recursos y las opciones de visualización
description: En este tema se explica cómo definir los recursos que se usará solo cuando se comparan algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena completo del idioma. Un recurso de cadena puede definirse de forma predeterminada, con otros recursos alternativos definidos que se usará para idiomas adicionales. Pueden calificar todos los tipos de recursos, incluido el diseño propio.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: 7bc8c1066e557085c1bf34f77765edbb2259ba7a
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403304"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Calificadores de recursos y las opciones de visualización

_En este tema se explica cómo definir los recursos que se usará solo cuando se comparan algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena completo del idioma. Un recurso de cadena puede definirse de forma predeterminada, con otros recursos alternativos definidos que se usará para idiomas adicionales. Pueden calificar todos los tipos de recursos, incluido el diseño propio._


## <a name="custom-device-configurations"></a>Configuraciones de dispositivo personalizado

Android está disponible en una gran cantidad de dispositivos y resoluciones de pantalla.
Para ayudar a diseñar interfaces de usuario que tienen como destino varios dispositivos, el diseñador incluye una variedad de configuraciones de dispositivos integrados. También permite agregar configuraciones de dispositivo adicionales; Estas configuraciones se basan en *calificadores* que especifique para distinguir una configuración de dispositivo de otro. Hay muchos tipos diferentes de calificadores. Para obtener más información acerca de estos tipos de recursos, consulte [recursos de Android](~/android/app-fundamentals/resources-in-android/index.md).

En la parte inferior del Selector de dispositivos de menú es un **personalizar** opción tal como se muestra a continuación:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menú Selector de dispositivos](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menú Selector de dispositivos](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


Seleccionar **personalizar** muestra un cuadro de diálogo que puede usar para examinar las configuraciones de dispositivo disponible. Al hacer clic en el **las definiciones de dispositivos** pestaña, se presenta una lista de todas las definiciones de dispositivos conocidos:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![AVD Manager](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![AVD Manager](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


Los dispositivos preconfigurados en el diseñador no pueden modificarse. Sin embargo, puede hacer clic en **crear dispositivo...**  para definir una definición personalizada del dispositivo. Como alternativa, puede seleccionar una definición existente y haga clic en **Clone...**  para usarlo como punto de partida para una nueva definición.
Por ejemplo, al seleccionar la definición del **Nexus 5** y hacer clic en **Clone... (Clonar...)** se presenta el siguiente cuadro de diálogo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Clonar dispositivo](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Clonar dispositivo](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


En la captura de pantalla siguiente se cambia el nombre a **Nexus 5 personalizado** y se modifican los parámetros de dispositivo para crear una nueva definición personalizada del dispositivo. En este ejemplo, **vertical** está deshabilitado para que la definición del dispositivo es de sólo horizontal:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


Al hacer clic en **Clone Device (Clonar dispositivo)** se crea la nueva definición del dispositivo, que ahora aparece en la lista **Device Definitions (Definiciones de dispositivos)**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Definiciones de dispositivos actualizada](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Definiciones de dispositivos actualizada](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


Tenga en cuenta que cada definición de dispositivo creado por el usuario se muestra con un icono verde, como se indicó anteriormente. Cuando vuelva a la **dispositivo** menú selector, se presenta la nueva definición de dispositivo personalizado en la sección superior de la lista (si no ve la configuración del dispositivo personalizado en esta lista, pruebe a reiniciar el IDE):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado aparece en la lista de dispositivos](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado aparece en la lista de dispositivos](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


Seleccionar configuración del dispositivo, modifica el diseño para que se ajuste a las personalizaciones que creó anteriormente (en este caso, Panorama modo):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado en uso](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado en uso](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>Opciones del calificador de recursos

**Opciones del calificador de recursos** puede tener acceso haciendo clic en los tres puntos situados a la derecha de la **configuración del dispositivo** opciones:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Opciones del calificador de recursos](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Opciones del calificador de recursos](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


Este cuadro de diálogo presenta los menús desplegables para los siguientes calificadores de recursos:

-   **Lenguaje** &ndash; muestra los recursos de idioma disponibles y ofrece una opción para agregar nuevos recursos de idioma y región.

-   **Modo de interfaz de usuario** &ndash; listas de los modos de presentación (como **Dock para coche** y **Dock de escritorio**), así como las direcciones de diseño.

Cada uno de estos menús desplegables abre nuevos cuadros de diálogo donde puede seleccionar y configurar los calificadores de recursos (como se explica a continuación).



### <a name="language"></a>Lenguaje

El **lenguaje** menú desplegable muestra sólo los idiomas que tengan los recursos definidos (o **todos los idiomas**, que es el valor predeterminado). Sin embargo, hay también un **Agregar idioma o región...**  opción que le permite agregar un nuevo idioma a la lista:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Agregar idioma o región](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Agregar idioma o región](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


Al hacer clic en **Agregar idioma o región...** , **Seleccionar idioma** abrirá el cuadro de diálogo para mostrar listas desplegables de idiomas y regiones:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lista de idiomas](resource-qualifiers-images/vs/10-languages.png "lista de idiomas")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de idiomas](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


En este ejemplo, hemos elegido **fr (francés)** para el idioma y **BE** (Bélgica) para el dialecto regional de francés. Tenga en cuenta que el **región** campo es opcional porque muchos lenguajes pueden especificarse sin tener en cuenta para regiones específicas. Cuando el **lenguaje** menú desplegable se abre de nuevo, se muestra el recurso de idioma y región recién agregada:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Idioma y región que eligió](resource-qualifiers-images/vs/11-language-region-added.png "elegido de idioma y región")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Idioma y región elegida](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


Tenga en cuenta que si agrega un nuevo lenguaje pero no crear nuevos recursos para él, usará idioma agregado ya no se muestra la próxima vez que abra el proyecto.



### <a name="ui-mode"></a>Modo de interfaz de usuario

Al hacer clic en el **modo UI** muestra menú desplegable, una lista de modos, como **Normal**, **Dock para coche**, **Dock de escritorio**, **Televisión**, **dispositivo**, y **inspección**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menú modo de interfaz de usuario](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

A continuación de esta lista son los modos de noche **no noche** y **noche**, seguido de las instrucciones de diseño **de izquierda a derecha** y **de derecha a izquierda** (para información sobre **de izquierda a derecha** y **de derecha a izquierda** opciones, vea [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).
Los elementos de la última de la **opciones del calificador de recursos** diálogo son el **pantallas de ida y vuelta** (para su uso con Android Wear) o **pantallas de ida y vuelta no** (para obtener información acerca de ida y vea las pantallas que no son round, [diseños](https://developer.android.com/training/wearables/ui/layouts.html)).
Para obtener más información acerca de los modos de interfaz de usuario de Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menú modo de interfaz de usuario](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

A continuación de esta lista son los modos de noche **no noche** y **noche**, seguido de las instrucciones de diseño **de izquierda a derecha** y **de derecha a izquierda**. Para obtener más información acerca de los modos de interfaz de usuario de Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Para obtener información acerca de **de izquierda a derecha** y **de derecha a izquierda** opciones, vea [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).

### <a name="round-screen"></a>Pantalla redonda

El último elemento en el **opciones del calificador de recursos** cuadro de diálogo es la **de ida y vuelta pantalla** menú. Este menú le permite seleccionar **pantallas de ida y vuelta** (para su uso con Android Wear) o **pantallas rectangulares**:

[![Menú de la pantalla redonda](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>Configuración de la barra de acción

El **configuración de la barra de acción** icono está disponible a la izquierda del icono de pincel (Editor de temas):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Configuración de la barra de acción](resource-qualifiers-images/vs/14-action-bar.png "configuración de la barra de acción")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Configuración de la barra de acción](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


Este icono abre un elemento flotante cuadro de diálogo que proporciona una manera de seleccionar uno de tres modos de barra de acciones:

-   **Estándar** &ndash; consta de un logotipo o el icono y el título de texto con un subtítulo (opcional).

-   **Lista** &ndash; modo de navegación de la lista. En lugar de texto de título estáticos, este modo presenta un menú de lista para la navegación dentro de la actividad (es decir, se puede presentar al usuario como una lista desplegable).

-   **Pestañas** &ndash; modo de navegación de la ficha. En lugar de texto de título estáticos, este modo presenta una serie de pestañas de navegación dentro de la actividad.



## <a name="themes"></a>Temas

El **tema** menú desplegable muestra todos los temas definidos en el proyecto. Seleccionar **más temas** abre un cuadro de diálogo con una lista de todos los temas disponibles en el SDK de Android instalado, como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Más temas de la lista](resource-qualifiers-images/vs/15-theme-menu-sml.png "más temas de la lista")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Más temas de la lista](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


Cuando se selecciona un tema, la superficie de diseño se actualiza para mostrar el efecto del nuevo tema. Tenga en cuenta que este cambio se hacen permanente solo si el **Aceptar** hace clic en botón el **tema** cuadro de diálogo. Una vez que se ha seleccionado un tema, se incluirá en el **tema** menú desplegable como se muestra a continuación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Ahora está disponible el tema claro](resource-qualifiers-images/vs/16-light-theme.png "ahora está disponible el tema claro")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ahora está disponible el tema claro](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Versión de Android

Android **versión** selector establece la versión de Android que se usa para representar el diseño en el diseñador. El selector de muestra todas las versiones que son compatibles con la versión de framework de destino del proyecto:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lista de las versiones de Android](resource-qualifiers-images/vs/17-android-version.png "versiones de lista de Android")

La versión de framework de destino se puede establecer en la configuración del proyecto en **Propiedades > aplicaciones > compilar con la versión Android**. Para obtener más información acerca de la versión de framework de destino, vea [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas viene determinada por la versión de framework de destino del proyecto. Esto también es cierto para las propiedades disponibles en el **ventana propiedades**. Es la lista de widgets disponibles *no* determinado por el valor seleccionado en el **versión** selector de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto en Android 4.4, aún puede seleccionar Android 6.0 en el selector de versión de la barra de herramientas para ver el aspecto que tiene el proyecto en Android 6.0, pero no podrá agregar widgets que son específicos a Android 6.0 &ndash;  todavía estará limitado a los widgets que están disponibles en Android 4.4.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de las versiones de Android](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

La versión de framework de destino se puede establecer en la configuración del proyecto en el **opciones de proyecto > compilar > General** sección. Para obtener más información acerca de la versión de framework de destino, vea [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas viene determinada por la versión de framework de destino del proyecto. Esto también es cierto para las propiedades disponibles en el **panel propiedad**. Es la lista de widgets disponibles *no* determinado por el valor seleccionado en el **versión** selector de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto en Android 4.4, aún puede seleccionar Android 6.0 en el selector de versión de la barra de herramientas para ver el aspecto que tiene el proyecto en Android 6.0, pero no podrá agregar widgets que son específicos a Android 6.0 &ndash;  todavía estará limitado a los widgets que están disponibles en Android 4.4.

-----
