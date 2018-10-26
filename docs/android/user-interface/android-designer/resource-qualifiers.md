---
title: Calificadores de recursos y las opciones de visualización
description: En este tema se explica cómo definir los recursos que se usará solo cuando se comparan algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena completo del idioma. Un recurso de cadena puede definirse de forma predeterminada, con otros recursos alternativos definidos que se usará para idiomas adicionales. Pueden calificar todos los tipos de recursos, incluido el diseño propio.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 9d99e6a59b57b59d585b32befdadc0890d41448c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115638"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Calificadores de recursos y las opciones de visualización

_En este tema se explica cómo definir los recursos que se usará solo cuando se comparan algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena completo del idioma. Un recurso de cadena puede definirse de forma predeterminada, con otros recursos alternativos definidos que se usará para idiomas adicionales. Pueden calificar todos los tipos de recursos, incluido el diseño propio._


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>Opciones del calificador de recursos

**Opciones del calificador de recursos** puede tener acceso haciendo clic en el icono de puntos suspensivos a la derecha de la **horizontal** botón modo:

[![Opciones del calificador de recursos](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

Este cuadro de diálogo presenta los menús desplegables para los siguientes calificadores de recursos:

-   **Lenguaje** &ndash; muestra los recursos de idioma disponibles y ofrece una opción para agregar nuevos recursos de idioma y región.

-   **Modo de interfaz de usuario** &ndash; listas de los modos de presentación (como **Dock para coche** y **Dock de escritorio**), así como las direcciones de diseño.

Cada uno de estos menús desplegables abre nuevos cuadros de diálogo donde puede seleccionar y configurar los calificadores de recursos (como se explica a continuación).

### <a name="language"></a>Lenguaje

El **lenguaje** menú desplegable muestra sólo los idiomas que tengan los recursos definidos (o **todos los idiomas**, que es el valor predeterminado). Sin embargo, hay también un **Agregar idioma o región...**  opción que le permite agregar un nuevo idioma a la lista:

[![Agregar idioma o región](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

Al hacer clic en **Agregar idioma o región...** , **Seleccionar idioma** abrirá el cuadro de diálogo para mostrar listas desplegables de idiomas y regiones:

![Lista de idiomas](resource-qualifiers-images/vs/10-languages.png "lista de idiomas")

En este ejemplo, hemos elegido **fr (francés)** para el idioma y **BE** (Bélgica) para el dialecto regional de francés. Tenga en cuenta que el **región** campo es opcional porque muchos lenguajes pueden especificarse sin tener en cuenta para regiones específicas. Cuando el **lenguaje** menú desplegable se abre de nuevo, se muestra el recurso de idioma y región recién agregada:

![Idioma y región que eligió](resource-qualifiers-images/vs/11-language-region-added.png "elegido de idioma y región")

Tenga en cuenta que si agrega un nuevo lenguaje pero no crear nuevos recursos para él, usará idioma agregado ya no se muestra la próxima vez que abra el proyecto.

### <a name="ui-mode"></a>Modo de interfaz de usuario

Al hacer clic en el **modo UI** muestra menú desplegable, una lista de modos, como **Normal**, **Dock para coche**, **Dock de escritorio**, **Televisión**, **dispositivo**, y **inspección**:


[![Menú modo de interfaz de usuario](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

A continuación de esta lista son los modos de noche **no noche** y **noche**, seguido de las instrucciones de diseño **de izquierda a derecha** y **de derecha a izquierda** (para información sobre **de izquierda a derecha** y **de derecha a izquierda** opciones, vea [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)).
Los elementos de la última de la **opciones del calificador de recursos** diálogo son el **pantallas de ida y vuelta** (para su uso con Android Wear) o **pantallas no redonda**.
Para obtener información acerca de las pantallas round y que no son round, consulte [diseños](https://developer.android.com/training/wearables/ui/layouts.html).
Para obtener más información acerca de los modos de interfaz de usuario de Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

## <a name="action-bar-settings"></a>Configuración de la barra de acción

El **configuración de la barra de acción** icono está disponible a la izquierda del icono de pincel (Editor de temas):

![Configuración de la barra de acción](resource-qualifiers-images/vs/14-action-bar.png "configuración de la barra de acción")

Este icono abre un elemento flotante cuadro de diálogo que proporciona una manera de seleccionar uno de tres modos de barra de acciones:

-   **Estándar** &ndash; consta de un logotipo o un icono y el título de texto con un subtítulo (opcional).

-   **Lista** &ndash; modo de navegación de la lista. En lugar de texto de título estáticos, este modo presenta un menú de lista para la navegación dentro de la actividad (es decir, se puede presentar al usuario como una lista desplegable).

-   **Pestañas** &ndash; modo de navegación de la ficha. En lugar de texto de título estáticos, este modo presenta una serie de pestañas de navegación dentro de la actividad.

## <a name="themes"></a>Temas

El **tema** menú desplegable muestra todos los temas definidos en el proyecto. Seleccionar **más temas** abre un cuadro de diálogo con una lista de todos los temas disponibles en el SDK de Android instalado, como se muestra a continuación:

[![Más temas de la lista](resource-qualifiers-images/vs/15-theme-menu-sml.png "más temas de la lista")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

Cuando se selecciona un tema, la superficie de diseño se actualiza para mostrar el efecto del nuevo tema. Tenga en cuenta que este cambio se hacen permanente solo si el **Aceptar** hace clic en botón el **tema** cuadro de diálogo. Una vez que se ha seleccionado un tema, se incluirá en el **tema** menú desplegable como se muestra a continuación:

![Ahora está disponible el tema claro](resource-qualifiers-images/vs/16-light-theme.png "ahora está disponible el tema claro")

## <a name="android-version"></a>Versión de Android

Android **versión** selector establece la versión de Android que se usa para representar el diseño en el diseñador. El selector de muestra todas las versiones que son compatibles con la versión de framework de destino del proyecto:

![Lista de las versiones de Android](resource-qualifiers-images/vs/17-android-version.png "versiones de lista de Android")

La versión de framework de destino se puede establecer en la configuración del proyecto en **Propiedades > aplicaciones > compilar con la versión Android**. Para obtener más información acerca de la versión de framework de destino, vea [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas viene determinada por la versión de framework de destino del proyecto. Esto también es cierto para las propiedades disponibles en el **ventana propiedades**. Es la lista de widgets disponibles *no* determinado por el valor seleccionado en el **versión** selector de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto en Android 4.4, aún puede seleccionar Android 6.0 en el selector de versión de la barra de herramientas para ver el aspecto que tiene el proyecto en Android 6.0, pero no podrá agregar widgets que son específicos a Android 6.0 &ndash;  todavía estará limitado a los widgets que están disponibles en Android 4.4.

Para obtener más información acerca de los tipos de recursos, consulte [recursos de Android](~/android/app-fundamentals/resources-in-android/index.md).



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>Opciones del calificador de recursos

**Opciones del calificador de recursos** puede tener acceso haciendo clic en el icono de puntos suspensivos a la derecha de la **horizontal** botón modo:

[![Opciones del calificador de recursos](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

Este cuadro de diálogo presenta los menús desplegables para los siguientes calificadores de recursos:

-   **Lenguaje** &ndash; muestra los recursos de idioma disponibles y ofrece una opción para agregar nuevos recursos de idioma y región.

-   **Modo de interfaz de usuario** &ndash; listas de los modos de presentación (como **Dock para coche** y **Dock de escritorio**), así como las direcciones de diseño.

Cada uno de estos menús desplegables abre nuevos cuadros de diálogo donde puede seleccionar y configurar los calificadores de recursos (como se explica a continuación).

### <a name="language"></a>Lenguaje

El **lenguaje** menú desplegable muestra sólo los idiomas que tengan los recursos definidos (o **todos los idiomas**, que es el valor predeterminado). Sin embargo, hay también un **Agregar idioma o región...**  opción que le permite agregar un nuevo idioma a la lista:

[![Agregar idioma o región](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

Al hacer clic en **Agregar idioma o región...** , **Seleccionar idioma** abrirá el cuadro de diálogo para mostrar listas desplegables de idiomas y regiones:

[![Lista de idiomas](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

En este ejemplo, hemos elegido **fr (francés)** para el idioma y **BE** (Bélgica) para el dialecto regional de francés. Tenga en cuenta que el **región** campo es opcional porque muchos lenguajes pueden especificarse sin tener en cuenta para regiones específicas. Cuando el **lenguaje** menú desplegable se abre de nuevo, se muestra el recurso de idioma y región recién agregada:

[![Idioma y región elegida](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

Tenga en cuenta que si agrega un nuevo lenguaje pero no crear nuevos recursos para él, usará idioma agregado ya no se muestra la próxima vez que abra el proyecto.

### <a name="ui-mode"></a>Modo de interfaz de usuario

Al hacer clic en el **modo UI** muestra menú desplegable, una lista de modos, como **Normal**, **Dock para coche**, **Dock de escritorio**, **Televisión**, **dispositivo**, y **inspección**:

[![Menú modo de interfaz de usuario](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

A continuación de esta lista son los modos de noche **no noche** y **noche**, seguido de las instrucciones de diseño **de izquierda a derecha** y **de derecha a izquierda**. El último par de opciones le permite seleccionar **pantallas de ida y vuelta** o **pantallas rectangulares** (útil para dispositivos Android Wear).

Para obtener más información acerca de los modos de interfaz de usuario de Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Para obtener información acerca de **de izquierda a derecha** y **de derecha a izquierda** opciones, vea [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).


## <a name="action-bar-settings"></a>Configuración de la barra de acción

El **configuración de la barra de acción** icono está disponible a la izquierda del icono de pincel (Editor de temas):

[![Configuración de la barra de acción](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

Este icono abre un elemento flotante cuadro de diálogo que proporciona una manera de seleccionar uno de tres modos de barra de acciones:

-   **Estándar** &ndash; consta de un logotipo o el icono y el título de texto con un subtítulo (opcional).

-   **Lista** &ndash; modo de navegación de la lista. En lugar de texto de título estáticos, este modo presenta un menú de lista para la navegación dentro de la actividad (es decir, se puede presentar al usuario como una lista desplegable).

-   **Pestañas** &ndash; modo de navegación de la ficha. En lugar de texto de título estáticos, este modo presenta una serie de pestañas de navegación dentro de la actividad.

## <a name="themes"></a>Temas

El **tema** menú desplegable muestra todos los temas definidos en el proyecto. Seleccionar **más temas** abre un cuadro de diálogo con una lista de todos los temas disponibles en el SDK de Android instalado, como se muestra a continuación:

[![Más temas de la lista](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

Cuando se selecciona un tema, la superficie de diseño se actualiza para mostrar el efecto del nuevo tema. Tenga en cuenta que este cambio se hacen permanente solo si el **Aceptar** hace clic en botón el **tema** cuadro de diálogo. Una vez que se ha seleccionado un tema, se incluirá en el **tema** menú desplegable como se muestra a continuación:

[![Ahora está disponible el tema claro](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Versión de Android

Android **versión** selector establece la versión de Android que se usa para representar el diseño en el diseñador. El selector de muestra todas las versiones que son compatibles con la versión de framework de destino del proyecto:

[![Lista de las versiones de Android](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

La versión de framework de destino se puede establecer en la configuración del proyecto en el **opciones de proyecto > compilar > General** sección. Para obtener más información acerca de la versión de framework de destino, vea [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas viene determinada por la versión de framework de destino del proyecto. Esto también es cierto para las propiedades disponibles en el **panel propiedad**. Es la lista de widgets disponibles *no* determinado por el valor seleccionado en el **versión** selector de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto en Android 4.4, aún puede seleccionar Android 6.0 en el selector de versión de la barra de herramientas para ver el aspecto que tiene el proyecto en Android 6.0, pero no podrá agregar widgets que son específicos a Android 6.0 &ndash;  todavía estará limitado a los widgets que están disponibles en Android 4.4.

Para obtener más información acerca de los tipos de recursos, consulte [recursos de Android](~/android/app-fundamentals/resources-in-android/index.md).

-----
