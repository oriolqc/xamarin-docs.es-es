---
title: Administrador de emuladores de Google
description: "Cómo crear y administrar dispositivos virtuales Android mediante el Administrador de emuladores de Google"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0C0BBEC0-C84A-4558-B905-4EF81FCD62F9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1e90ebe55dbec9fdcc57b9eaab99e614fdc5cbbc
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="google-emulator-manager"></a>Administrador de emuladores de Google

Después de comprobar que la aceleración de hardware está habilitada (como se describe en [Aceleración de hardware de Android Emulator](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), el siguiente paso consiste en crear dispositivos virtuales que se usarán para probar y depurar la aplicación. Puede usar el Administrador de emuladores de Google heredado, también conocido como *Administrador de dispositivos virtuales Android (AVD)*, para crear dispositivos virtuales para que los use el emulador de Android SDK.

> [!NOTE]
> Si tiene como destino Android 8.0 Oreo, debe usar el [Administrador de dispositivos Android de Xamarin](~/android/get-started/installation/android-emulator/xamarin-device-manager.md) para crear y configurar dispositivos virtuales.


## <a name="installing-system-images"></a>Instalación de imágenes del sistema

En función de los niveles de API de Android que quiera establecer como destino, debe descargar e instalar las imágenes del sistema específicas del nivel de API que use el emulador de Android SDK. Para cada nivel de API de Android, hay un conjunto de imágenes del sistema **x86** que deberá descargar e instalar para crear dispositivos virtuales.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para instalar las imágenes del sistema necesarias, inicie Android SDK Manager (**Herramientas > Android > Android SDK Manager**) y desplácese hasta los niveles de API que quiera admitir. Por cada nivel de API, active la marca de verificación situada junto a las siguientes imágenes del sistema:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para instalar las imágenes del sistema necesarias, inicie Android SDK Manager (**Herramientas > SDK Manager**) y desplácese hasta los niveles de API que quiera admitir. Por cada nivel de API, active la marca de verificación situada junto a las siguientes imágenes del sistema:

-----

-   **Imagen del sistema Intel x86 Atom**
-   **Imagen del sistema Intel x86 Atom de API de Google**

La segunda imagen del sistema agrega API de Google (por ejemplo, API de Google Maps) en el dispositivo virtual. 

En la siguiente captura de pantalla, se instalarán imágenes **Intel x86 Atom** para que se puedan crear dispositivos virtuales que ejecutan Android 6.0:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selección de imágenes del sistema x86 de Android 6.0 para el emulador de Android](google-emulator-manager-images/win/03-select-x86-images-sml.png)](google-emulator-manager-images/win/03-select-x86-images.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Selección de imágenes del sistema x86 de Android 6.0 para el emulador de Android](google-emulator-manager-images/mac/02-select-x86-images-sml.png)](google-emulator-manager-images/mac/02-select-x86-images.png#lightbox)

-----

Si está desarrollando aplicaciones de 64 bits, instale en su lugar las siguientes imágenes del sistema:

-   **Imagen del sistema Intel x86 Atom_64**
-   **Imagen del sistema Intel x86 Atom_64 de API de Google**

Puede usar estas imágenes del sistema de 64 bits para ejecutar aplicaciones de 32 bits, pero la **imagen del sistema Intel x86 Atom** de 32 bits se ejecuta un poco más rápido en el emulador de Android SDK.

Si está desarrollando aplicaciones para Android Wear, instale las siguientes imágenes del sistema:

-   **Imagen del sistema Intel x86 Atom de Android Wear**
-   **Imagen del sistema Intel x86 Atom de API de Google**

Después de instalar estas imágenes del sistema, puede crear dispositivos virtuales Android basados en **x86**. Para ello, seleccione el nivel de API y las opciones de CPU/ABI adecuados durante la configuración del dispositivo virtual (esto se describe a continuación).


## <a name="configuring-virtual-devices"></a>Configuración de dispositivos virtuales

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Los dispositivos virtuales se configuran a través del **administrador del emulador de Android** (también denominado _administrador de dispositivos virtuales Android_ o _administrador AVD_). Para iniciar el administrador del emulador de Android desde Visual Studio, haga clic en el icono del **administrador del emulador de Android** en la barra de herramientas:

[![Ubicación del icono AVD](google-emulator-manager-images/win/04-avd-icon-sml.png)](google-emulator-manager-images/win/04-avd-icon.png#lightbox)

También puede iniciar el administrador del emulador de Android desde la barra de menús. Para ello, seleccione **Herramientas > Android > Administrador del emulador de Android**:

[![Ubicación del elemento de menú Administrador del emulador de Android](google-emulator-manager-images/win/05-avd-manager-menu-item-sml.png)](google-emulator-manager-images/win/05-avd-manager-menu-item.png#lightbox)

En el cuadro de diálogo **Android Virtual Device (AVD) Manager** se muestra la lista de dispositivos virtuales Android existentes:

[![Administrador de dispositivos virtuales Android](google-emulator-manager-images/win/06-virtual-device-manager-sml.png)](google-emulator-manager-images/win/06-virtual-device-manager.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Los dispositivos virtuales se configuran a través del **administrador del emulador de Android** (también denominado _administrador de dispositivos virtuales Android_ o _administrador AVD_). 

Puede iniciar Android Emulator Manager desde la barra de menús. Para ello, seleccione **Herramientas > Google Emulator Manager**:

[![Ubicación del elemento de menú Administrador del emulador de Android](google-emulator-manager-images/mac/03-avd-manager-menu-item-sml.png)](google-emulator-manager-images/mac/03-avd-manager-menu-item.png#lightbox)

En el cuadro de diálogo **Android Virtual Device (AVD) Manager** se muestra la lista de dispositivos virtuales Android existentes:

[![Administrador de dispositivos virtuales Android](google-emulator-manager-images/mac/05-virtual-device-manager-sml.png)](google-emulator-manager-images/mac/05-virtual-device-manager.png#lightbox)

-----

Puede crear imágenes del dispositivo virtual con características de dispositivo y niveles de API diferentes. En la sección siguiente se explica cómo crear definiciones personalizadas del dispositivo y dispositivos virtuales.


### <a name="creating-a-custom-device-definition"></a>Creación de una definición personalizada del dispositivo

Para crear una definición personalizada del dispositivo, haga clic en **Create... (Crear...)** en **Android Virtual Device (AVD) Manager**. Se abre el cuadro de diálogo **Create new Android Virtual Device (AVD)** (Crear dispositivo virtual Android (AVD)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Definición personalizada del dispositivo basada en Nexus 6](google-emulator-manager-images/win/07-custom-device-sml.png)](google-emulator-manager-images/win/07-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Definición personalizada del dispositivo basada en Nexus 6](google-emulator-manager-images/mac/06-custom-device-sml.png)](google-emulator-manager-images/mac/06-custom-device.png#lightbox)

-----

En este cuadro de diálogo, configure las opciones siguientes:

-   **AVD Name** (Nombre de AVD): nombre único de la definición del dispositivo. En la captura de pantalla de ejemplo anterior, el nombre es **MyNexus**. Tenga en cuenta que el nombre de AVD no puede contener espacios. Si intenta usar espacios en el nombre de AVD, se deshabilitará el botón **Aceptar**.

-   **Dispositivo**: seleccione el perfil de hardware que quiere emular (por ejemplo, **Nexus 5** o **Nexus 6**).

-   **Destino**: seleccione el nivel de API de Android del dispositivo virtual. Este valor debe ser mayor o igual que la versión mínima de Android de la aplicación.

-   **CPU/ABI**: seleccione **Google APIs Intel Atom (x86)** (Intel Atom de API de Google (x86)) para que las API de Google estén disponibles en la definición del dispositivo.

-   **Máscara**: seleccione la apariencia del dispositivo virtual. En la captura de pantalla de ejemplo anterior, está seleccionada la máscara **HVGA** (la captura de pantalla del emulador incluida al final de este artículo es un ejemplo de la máscara **HVGA**).

-   **Opciones de memoria**: por lo general, la configuración de RAM predeterminada es demasiado alta y, en Windows, provoca la siguiente advertencia: **emulating RAM greater than 768M may fail** (La emulación de RAM superior a 768 MB puede producir un error). Para la mayoría de los usuarios, se recomienda configurar la RAM en 768 MB (como se muestra en la captura de pantalla anterior). Los valores grandes de RAM pueden ralentizar el emulador.

-   **Use Host GPU** (Usar GPU de host): esta opción hace que el emulador use la Unidad de procesamiento de gráficos (GPU) del equipo host para realizar operaciones de gráficos. Se recomienda que habilite esta opción para aumentar aún más el rendimiento del emulador. Para obtener más información sobre la sección de **opciones de emulación**, vea [What are Snapshot and Use Host GPU emulation options used for?](https://android.stackexchange.com/questions/51739/what-is-snapshot-and-use-host-gpu-emulation-options-for) (¿Para qué se usan las opciones de emulación Snapshot (Instantánea) y Use Host GPU (Usar GPU de host)?).


Puede dejar las demás opciones en sus valores predeterminados. Cuando esté listo, haga clic en **Aceptar** para crear el dispositivo virtual. Los resultados de la nueva configuración del dispositivo virtual se indican en el siguiente cuadro de diálogo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cuadro de diálogo de resultados después de crear un AVD](google-emulator-manager-images/win/08-create-results.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Cuadro de diálogo de resultados después de crear un AVD](google-emulator-manager-images/mac/07-create-results.png)

-----

Para obtener una explicación detallada de las propiedades de configuración enumeradas en este cuadro de diálogo, vea [Propiedades de perfiles de hardware](https://developer.android.com/studio/run/managing-avds.html#hpproperties).
Al hacer clic en **Aceptar**, la nueva configuración del dispositivo se mostrará en la lista de dispositivos virtuales Android existentes. En la siguiente captura de pantalla, se ha agregado **MyNexus** a la lista:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus agregado a la lista de dispositivos](google-emulator-manager-images/win/09-added-to-list-sml.png)](google-emulator-manager-images/win/09-added-to-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![MyNexus agregado a la lista de dispositivos](google-emulator-manager-images/mac/08-added-to-list-sml.png)](google-emulator-manager-images/mac/08-added-to-list.png#lightbox)

-----

También se agrega el nuevo dispositivo virtual personalizado al menú desplegable de dispositivos:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus agregado al menú desplegable de dispositivos](google-emulator-manager-images/win/10-available-custom-device-sml.png)](google-emulator-manager-images/win/10-available-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![MyNexus agregado al menú desplegable de dispositivos](google-emulator-manager-images/mac/09-available-custom-device-sml.png)](google-emulator-manager-images/mac/09-available-custom-device.png#lightbox)

-----



### <a name="cloning-a-device-definition"></a>Clonación de definiciones del dispositivo

Es posible seleccionar una definición de un dispositivo existente y *clonarla* para crear una definición personalizada del dispositivo. Se trata de una buena estrategia para los casos en los que hay una definición de un dispositivo existente que solo necesita algunos ajustes menores para cumplir sus requisitos. En la pestaña **Device Definitions (Definiciones de dispositivos)** de **Android Virtual Device (AVD) Manager** se enumeran todas las definiciones de dispositivos disponibles:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Lista de definiciones de dispositivos disponibles](google-emulator-manager-images/win/11-device-definitions-sml.png)](google-emulator-manager-images/win/11-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de definiciones de dispositivos disponibles](google-emulator-manager-images/mac/10-device-definitions-sml.png)](google-emulator-manager-images/mac/10-device-definitions.png#lightbox)

-----

Los dispositivos preconfigurados de esta lista no se pueden modificar, y solamente se pueden editar los dispositivos virtuales creados por el usuario. Es posible derivar una nueva definición de dispositivo a partir de una definición de dispositivo preconfigurado. Para ello, seleccione la definición del dispositivo y haga clic en **Clonar**. Por ejemplo, al seleccionar la definición del **Nexus 5** y hacer clic en **Clonar** aparece el siguiente cuadro de diálogo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cuadro de diálogo para clonar dispositivo](google-emulator-manager-images/win/12-clone-device-sml.png)](google-emulator-manager-images/win/12-clone-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cuadro de diálogo para clonar dispositivo](google-emulator-manager-images/mac/11-clone-device-sml.png)](google-emulator-manager-images/mac/11-clone-device.png#lightbox)

-----

En la siguiente captura de pantalla, se cambia el nombre por **Nexus 5 personalizado** y se modifican los parámetros del dispositivo para crear una nueva definición de dispositivo personalizada:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![AVD Nexus 5 personalizado](google-emulator-manager-images/win/13-custom-nexus.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![AVD de Nexus 5 personalizado](google-emulator-manager-images/mac/12-custom-nexus-sml.png)](google-emulator-manager-images/mac/12-custom-nexus.png#lightbox)

-----

Al hacer clic en **Clone Device (Clonar dispositivo)** se crea la nueva definición del dispositivo, que ahora aparece en la lista **Device Definitions (Definiciones de dispositivos)**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Nexus 5 personalizado aparece como nueva definición del dispositivo de usuario](google-emulator-manager-images/win/14-new-definition-sml.png)](google-emulator-manager-images/win/14-new-definition.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Nexus 5 personalizado aparece como nueva definición del dispositivo de usuario](google-emulator-manager-images/mac/13-new-definition-sml.png)](google-emulator-manager-images/mac/13-new-definition.png#lightbox)

-----

Tenga en cuenta que cada definición de dispositivo creada por el usuario se muestra con un icono verde, como se indica más arriba. Esta nueva definición del dispositivo se puede usar para crear un nuevo AVD. Para ello, seleccione la definición y haga clic en **Crear AVD**. A continuación se muestra el cuadro de diálogo **Create new Android Virtual Device (AVD) (Crear nuevo Android Virtual Device (AVD))**. En el ejemplo siguiente, se ha generado automáticamente el nombre **AVD\_for\_Nexus\_5\_Custom** para el nuevo dispositivo virtual:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Crear AVD a partir de una definición de dispositivo de usuario de Nexus 5 personalizado](google-emulator-manager-images/win/15-create-avd-sml.png)](google-emulator-manager-images/win/15-create-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Crear AVD a partir de una definición de dispositivo de usuario de Nexus 5 personalizado](google-emulator-manager-images/mac/14-create-avd-sml.png)](google-emulator-manager-images/mac/14-create-avd.png#lightbox)

-----

Al hacer clic en **Aceptar**, la configuración personalizada del dispositivo se mostrará en la lista de los dispositivos virtuales de Android existentes. Además, se agregará al menú desplegable de dispositivos:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Nuevo AVD personalizado agregado al menú desplegable de dispositivos](google-emulator-manager-images/win/16-new-avd-sml.png)](google-emulator-manager-images/win/16-new-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Nuevo AVD personalizado agregado al menú desplegable de dispositivos](google-emulator-manager-images/mac/15-new-avd-sml.png)](google-emulator-manager-images/mac/15-new-avd.png#lightbox)

-----

