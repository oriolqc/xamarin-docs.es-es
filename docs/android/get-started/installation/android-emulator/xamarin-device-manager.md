---
title: Administrador de dispositivos Android de Xamarin
description: El Administrador de dispositivos Android de Xamarin, actualmente en versión preliminar, reemplaza el Administrador de dispositivos de Google heredado. En esta guía se explica cómo usar el Administrador de dispositivos Android de Xamarin para crear y configurar dispositivos virtuales Android (AVD) que emulen dispositivos Android. Puede usar estos dispositivos virtuales para ejecutar y probar la aplicación sin tener que depender de un dispositivo físico.
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 94f82c9f893e22074ba95c052b57ce6ff18eaa1e
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="xamarin-android-device-manager"></a>Administrador de dispositivos Android de Xamarin

_El Administrador de dispositivos Xamarin Android, actualmente en versión preliminar, reemplaza el Administrador de dispositivos de Google heredado. En esta guía se explica cómo usar el Administrador de dispositivos Android de Xamarin para crear y configurar dispositivos virtuales Android (AVD) que emulen dispositivos Android. Puede usar estos dispositivos virtuales para ejecutar y probar la aplicación sin tener que depender de un dispositivo físico._

![Actualmente en versión preliminar](~/media/shared/preview.png)
 
## <a name="overview"></a>Información general

Después de comprobar que la aceleración de hardware está habilitada (como se describe en [Hardware Acceleration](~/android/get-started/installation/android-emulator/hardware-acceleration.md) (Aceleración de hardware)), el siguiente paso consiste en crear dispositivos virtuales que se usarán para probar y depurar la aplicación. Puede usar el Administrador de dispositivos Android de Xamarin a fin de crear dispositivos virtuales para que los use el emulador de Android SDK.

¿Por qué le interesa usar el Administrador de dispositivos Android de Xamarin en lugar del [Administrador de dispositivos de Google](~/android/get-started/installation/android-emulator/google-emulator-manager.md)?
A partir de Android SDK Tools versión 26.0.1, Google ha quitado la compatibilidad con los administradores de AVD y SDK basados en interfaz de usuario en favor de las herramientas nuevas de CLI (interfaz de línea de comandos). Debido a este cambio, debe usar [Android SDK Manager](~/android/get-started/installation/android-sdk.md) y el Administrador de dispositivos Android de Xamarin cuando actualice a Android SDK Tools 26.0.1 y versiones posteriores (necesario para el desarrollo de Android 8.0 Oreo).


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En esta guía se explica cómo instalar y usar el Administrador de dispositivos Android de Xamarin para Visual Studio en Windows (o [Mac](?tabs=vsmac)):

[![Captura de pantalla del Administrador de dispositivos Android de Xamarin en la pestaña Dispositivos](xamarin-device-manager-images/win/01-devices-dialog-sml.png)](xamarin-device-manager-images/win/01-devices-dialog.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En esta guía se explica cómo instalar y usar el Administrador de dispositivos Android de Xamarin para Visual Studio en Mac (o [Windows](?tabs=vswin)):

[![Captura de pantalla del Administrador de dispositivos Android de Xamarin en la pestaña Dispositivos](xamarin-device-manager-images/mac/01-devices-dialog-sml.png)](xamarin-device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Esta guía solo se aplica a Visual Studio para Mac.
Xamarin Studio no es compatible con el Administrador de dispositivos Android de Xamarin.

-----

El Administrador de dispositivos Android de Xamarin se usa para crear y configurar *dispositivos virtuales Android* (AVD) que se ejecutan en el [emulador de Android SDK](~/android/deploy-test/debugging/android-sdk-emulator/index.md).
Cada AVD es una configuración del emulador que simula un dispositivo Android físico. Esto permite ejecutar y probar la aplicación en diversas configuraciones que simulan diferentes dispositivos Android físicos. El Administrador de dispositivos Android de Xamarin reemplaza al administrador AVD independiente de Google (que está en desuso).

En esta guía se describe cómo instalar e iniciar el Administrador de dispositivos Android. Obtendrá información sobre cómo crear, duplicar, personalizar e iniciar dispositivos virtuales. También se explica cómo configurar las propiedades de cada dispositivo virtual (como el nivel de API, la CPU, la memoria y la resolución), cómo habilitar o deshabilitar los sensores simulados (como el acelerómetro, el GPS, la orientación y el sensor de luz) y cómo configurar el tipo de aceleración de hardware usada por ese dispositivo virtual.

## <a name="requirements"></a>Requisitos

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para usar el Administrador de dispositivos Android de Xamarin, necesita lo siguiente:

- Visual Studio 2017 versión 15.5 o posterior. Se admite Visual Studio Community Edition y versiones posteriores.

- Xamarin para Visual Studio versión 4.8 o posterior. Para obtener información sobre cómo actualizar Xamarin, vea [Change the Updates Channel](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) (Cambiar el canal de actualizaciones).

- La versión más reciente del [instalador del Administrador de dispositivos de Xamarin](https://go.microsoft.com/fwlink/?linkid=865528) para Windows.

- **Android SDK**: es necesario instalar Android SDK (vea [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)) y SDK Tools versión 26.0 como se describe en la sección siguiente. Asegúrese de instalar Android SDK en la siguiente ubicación (si todavía no está instalado): **C:\\Archivos de programa (x86)\\Android\\android-sdk**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

- Visual Studio para Mac 7.4 o posterior.

- La versión más reciente del [instalador del Administrador de dispositivos de Xamarin](https://go.microsoft.com/fwlink/?linkid=865527) para macOS.

- **Android SDK**: es necesario instalar Android SDK 8.0 (API 26) o posterior mediante SDK Manager.

-----

## <a name="installing-the-device-manager"></a>Instalar el Administrador de dispositivos

Siga estos pasos para instalar el Administrador de dispositivos Android de Xamarin:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Descargue el [instalador del Administrador de dispositivos de Xamarin](https://go.microsoft.com/fwlink/?linkid=865528) para Windows.

2. Haga doble clic en **Xamarin.DeviceManager.msi** y siga las instrucciones de instalación: 

    ![Asistente para la instalación del Administrador de dispositivos Android de Xamarin](xamarin-device-manager-images/win/30-installer.png)


> [!NOTE]
> A partir de [Visual Studio 2017 Preview 5](https://www.visualstudio.com/vs/preview/), Android Device Manager se distribuirá como parte del instalador de VS2017. No es necesario descargar un instalador independiente para obtener Xamarin Android Device Manager con Visual Studio 2017 Preview 5.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Descargue el [instalador del Administrador de dispositivos de Xamarin](https://go.microsoft.com/fwlink/?linkid=865527) para macOS.

2. Haga doble clic en **AndroidDevices.pkg** y siga las instrucciones de instalación: 

    [![Asistente para la instalación del Administrador de dispositivos Android de Xamarin](xamarin-device-manager-images/mac/30-installer-sml.png)](xamarin-device-manager-images/mac/30-installer.png#lightbox)

-----
## <a name="launching-the-device-manager"></a>Iniciar el Administrador de dispositivos

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En la versión preliminar 3 de Visual Studio 15.6 y posteriores, puede iniciar el Administrador de dispositivos Android de Xamarin desde el menú **Herramientas**. Si usa la versión preliminar 3 de Visual Studio 15.6 o posteriores, haga clic en **Herramientas > Administrador de Android Emulator** para iniciar el Administrador de dispositivos:

[![Inicio desde el menú Herramientas](xamarin-device-manager-images/win/04-tools-menu-sml.png)](xamarin-device-manager-images/win/04-tools-menu.png#lightbox)

Si usa una versión anterior de Visual Studio, es necesario iniciar el Administrador de dispositivos Android de Xamarin desde el menú **Inicio** de Windows.

![Administrador de dispositivos Android de Xamarin en el menú Inicio](xamarin-device-manager-images/win/31-start-menu.png)

Haga clic con el botón derecho en **Xamarin Android Device Manager** (Administrador de dispositivos Android de Xamarin) y seleccione **Más > Ejecutar como administrador**. Si ve el cuadro de diálogo de error siguiente al inicio, consulte la sección [Solución de problemas](#troubleshooting) para obtener soluciones alternativas:

![Error de instancia de Android SDK](xamarin-device-manager-images/win/32-sdk-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En la versión preliminar 3 de Visual Studio para Mac 7.6, actualmente en el canal alfa, o bien versiones posteriores, puede iniciar el Administrador de dispositivos Android de Xamarin en **Herramientas > Administrador de emuladores**:

[![Inicio desde el menú Herramientas](xamarin-device-manager-images/mac/16-tools-menu-sml.png)](xamarin-device-manager-images/mac/16-tools-menu.png#lightbox)

Si usa una versión anterior de Visual Studio para Mac, es necesario iniciar el Administrador de dispositivos Android de Xamarin de forma independiente. Busque **Dispositivos Android** en la carpeta **Aplicaciones** y haga doble clic en él para iniciarlo:

[![Ubicación del Administrador de dispositivos Android de Xamarin en Finder](xamarin-device-manager-images/mac/31-location-in-finder-sml.png)](xamarin-device-manager-images/mac/31-location-in-finder.png#lightbox)

-----

Para poder usar el Administrador de dispositivos Android, debe instalar Android SDK Tools versión 26.0.0 o posterior. Si no está instalado, verá este cuadro de diálogo de error en el inicio:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cuadro de diálogo de error de instancia de Android SDK](xamarin-device-manager-images/win/02-sdk-instance-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Cuadro de diálogo de error de instancia de Android SDK](xamarin-device-manager-images/mac/02-sdk-instance-error.png)

-----

Si ve este cuadro de diálogo de error, haga clic en **Aceptar** para abrir Android SDK Manager. En Android SDK Manager, haga clic en la pestaña **Herramientas** e instale **Android SDK Tools 26.0.2** o posterior, **Android SDK Platform-Tools 26.0.0** o posterior y **Android SDK Build-Tools 26.0.0** o posterior:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Instalar Android SDK Tools 26.0](xamarin-device-manager-images/win/03-sdk-tools-sml.png)](xamarin-device-manager-images/win/03-sdk-tools.png#lightbox)

Después de instalar estos paquetes, puede cerrar SDK Manager y volver a iniciar el Administrador de dispositivos Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Instalar Android SDK Tools 26.0](xamarin-device-manager-images/mac/03-sdk-tools-sml.png)](xamarin-device-manager-images/mac/03-sdk-tools.png#lightbox)

-----

## <a name="main-screen"></a>Pantalla principal

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Cuando inicie el Administrador de dispositivos Android por primera vez, aparecerá una pantalla con todos los dispositivos virtuales configurados. Para cada dispositivo, se muestran el **nombre**, el **sistema operativo** (nivel de API de Android), la **CPU**, el tamaño de **memoria** y la resolución de pantalla:

[![Lista de los dispositivos instalados y sus parámetros](xamarin-device-manager-images/win/05-installed-list-sml.png)](xamarin-device-manager-images/win/05-installed-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Cuando inicie el Administrador de dispositivos Android por primera vez, aparecerá una pantalla con todos los dispositivos virtuales configurados. Para cada dispositivo, se muestran el **nombre**, la **imagen de sistema** (nivel de API de Android), la **CPU**, el tamaño de **memoria** y la resolución de pantalla:

[![Lista de los dispositivos instalados y sus parámetros](xamarin-device-manager-images/mac/05-devices-list-sml.png)](xamarin-device-manager-images/mac/05-devices-list.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Al hacer clic en un dispositivo de la lista, aparece el botón **Iniciar** a la derecha. Puede hacer clic en el botón **Iniciar** para iniciar el emulador con este dispositivo virtual:

[![Botón Iniciar de una imagen de dispositivo](xamarin-device-manager-images/win/06-start-button-sml.png)](xamarin-device-manager-images/win/06-start-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Haga clic en el botón **Reproducir** para iniciar el emulador con el dispositivo virtual de su elección:

[![Botón Iniciar de una imagen de dispositivo](xamarin-device-manager-images/mac/06-start-button-sml.png)](xamarin-device-manager-images/mac/06-start-button.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Una vez que el emulador se haya iniciado con el dispositivo virtual seleccionado, el botón **Iniciar** cambiará al botón **Detener**, que puede usar para detener el emulador:

[![Botón Detener del dispositivo en ejecución](xamarin-device-manager-images/win/07-stop-button-sml.png)](xamarin-device-manager-images/win/07-stop-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Una vez que el emulador se haya iniciado con el dispositivo virtual seleccionado, el botón **Reproducir** cambiará al botón **Detener**, que puede usar para detener el emulador:

[![Botón Detener del dispositivo en ejecución](xamarin-device-manager-images/mac/07-stop-button-sml.png)](xamarin-device-manager-images/mac/07-stop-button.png#lightbox)

-----

### <a name="new-device"></a>Dispositivo nuevo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para crear un dispositivo, haga clic en el botón **Nuevo** (situado en la parte superior derecha de la pantalla):

[![Botón Nuevo para crear un dispositivo](xamarin-device-manager-images/win/08-new-button-sml.png)](xamarin-device-manager-images/win/08-new-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para crear un dispositivo, haga clic en el botón **Nuevo dispositivo** (situado en la parte superior derecha de la pantalla):

[![Botón Nuevo para crear un dispositivo](xamarin-device-manager-images/mac/08-new-button-sml.png)](xamarin-device-manager-images/mac/08-new-button.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Al hacer clic en **Nuevo**, aparece la pantalla **Nuevo dispositivo**:

[![Pantalla Nuevo dispositivo del Administrador de dispositivos](xamarin-device-manager-images/win/09-new-device-editor-sml.png)](xamarin-device-manager-images/win/09-new-device-editor.png#lightbox)

Para configurar un nuevo dispositivo en la pantalla **Nuevo dispositivo**, siga estos pasos:

1. Seleccione el dispositivo físico que quiera emular. Para ello, haga clic en el menú desplegable **Dispositivo**:

    [![Menú desplegable Dispositivo](xamarin-device-manager-images/win/10-device-menu-sml.png)](xamarin-device-manager-images/win/10-device-menu.png#lightbox)

2. Seleccione la imagen de sistema que quiera usar con este dispositivo virtual. Para ello, haga clic en el menú desplegable **Imagen de sistema**. En este menú aparecen las imágenes de sistema instaladas en la sección **Instalado**. En la sección **Descargar** se muestran las imágenes de sistema que no están disponibles actualmente en el equipo de desarrollo, pero que se pueden instalar de forma automática:

    [![Menú desplegable Imagen de sistema](xamarin-device-manager-images/win/11-system-image-menu-sml.png)](xamarin-device-manager-images/win/11-system-image-menu.png#lightbox)

3. Asigne un nuevo nombre al dispositivo. En el ejemplo siguiente, el nuevo dispositivo se denomina **Nexus 5 API 25**:

    [![Asignar un nombre al nuevo dispositivo](xamarin-device-manager-images/win/12-device-name-sml.png)](xamarin-device-manager-images/win/12-device-name.png#lightbox)

4. Edite las propiedades que necesite modificar. Para realizar cambios en las propiedades, vea [Propiedades de perfil](#properties) más adelante en esta guía.

5. Agregue las propiedades adicionales que necesite establecer explícitamente. En la pantalla **Nuevo dispositivo** solo se muestran las propiedades que se modifican con más frecuencia, pero puede hacer clic en el menú desplegable **Agregar propiedad** (en la esquina inferior izquierda) para agregar propiedades adicionales. En el ejemplo siguiente, se agrega la propiedad `hw.lcd.backlight`:

    [![Menú desplegable Agregar propiedad](xamarin-device-manager-images/win/13-add-property-menu-sml.png)](xamarin-device-manager-images/win/13-add-property-menu.png#lightbox)

6. Haga clic en el botón **Crear** (esquina inferior derecha) para crear el dispositivo:

    ![Botón Crear](xamarin-device-manager-images/win/14-create-button.png)

7. Es posible que aparezca la pantalla **Aceptación de licencia**. Haga clic en **Aceptar** si está de acuerdo con los términos de licencia:

    ![Pantalla de aceptación de licencia](xamarin-device-manager-images/win/15-license-acceptance.png)

8. El Administrador de dispositivos Android agrega el nuevo dispositivo a la lista de dispositivos virtuales instalados, con el indicador de progreso **Creando** mientras crea el dispositivo:

    [![Indicador de progreso de creación](xamarin-device-manager-images/win/16-creating-the-device-sml.png)](xamarin-device-manager-images/win/16-creating-the-device.png#lightbox)

9. Una vez finalizado el proceso de creación, el nuevo dispositivo se muestra en la lista de dispositivos virtuales instalados con el botón **Iniciar**, listo para su inicio:

   [![Dispositivo recién creado listo para su inicio](xamarin-device-manager-images/win/17-created-device-sml.png)](xamarin-device-manager-images/win/17-created-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Al hacer clic en **Nuevo dispositivo**, aparece la pantalla **Nuevo dispositivo**:

[![Pantalla Nuevo dispositivo del Administrador de dispositivos](xamarin-device-manager-images/mac/09-new-device-editor-sml.png)](xamarin-device-manager-images/mac/09-new-device-editor.png#lightbox)

Siga estos pasos para configurar un nuevo dispositivo en la pantalla **Nuevo dispositivo**:

1. Seleccione el dispositivo físico que quiera emular. Para ello, haga clic en el menú desplegable **Dispositivo**:

    [![Menú desplegable Dispositivo](xamarin-device-manager-images/mac/10-device-menu-sml.png)](xamarin-device-manager-images/mac/10-device-menu.png#lightbox)

2. Seleccione la imagen de sistema que quiera usar con este dispositivo virtual. Para ello, haga clic en el menú desplegable **Imagen de sistema**. En este menú aparecen las imágenes de sistema instaladas en la sección **Instalado**. En la sección **Descargar** (si aparece) se muestran las imágenes de sistema que no están disponibles actualmente en el equipo de desarrollo, pero que se pueden instalar de forma automática:

    [![Menú desplegable Imagen de sistema](xamarin-device-manager-images/mac/11-system-image-menu-sml.png)](xamarin-device-manager-images/mac/11-system-image-menu.png#lightbox)

3. Asigne un nuevo nombre al dispositivo. En el ejemplo siguiente, el nuevo dispositivo se denomina **Nexus 5X API 25**:

    [![Asignar un nombre al nuevo dispositivo](xamarin-device-manager-images/mac/12-device-name-sml.png)](xamarin-device-manager-images/mac/12-device-name.png#lightbox)

4. Edite las propiedades que necesite modificar. Para realizar cambios en las propiedades, vea [Propiedades de perfil](#properties) más adelante en esta guía.

5. Agregue las propiedades adicionales que necesite establecer explícitamente. En la pantalla **Nuevo dispositivo** solo se muestran las propiedades que se modifican con más frecuencia, pero puede hacer clic en el menú desplegable **Agregar propiedad** (en la esquina inferior izquierda) para agregar propiedades adicionales:

    [![Menú desplegable Agregar propiedad](xamarin-device-manager-images/mac/13-add-property-menu-sml.png)](xamarin-device-manager-images/mac/13-add-property-menu.png#lightbox)

6. También puede hacer clic en **Personalizar** para definir una nueva propiedad para el dispositivo:

    ![Botón Crear](xamarin-device-manager-images/mac/14-custom-button.png)

7. Haga clic en el botón **Crear** (esquina inferior derecha) para crear el dispositivo:

    ![Botón Crear](xamarin-device-manager-images/mac/15-create-button.png)

8. Es posible que aparezca la pantalla **Aceptación de licencia**. Haga clic en **Aceptar** si está de acuerdo con los términos de licencia.

9. Mientras crea el dispositivo, el Administrador de dispositivos Android agrega el nuevo dispositivo a la lista de dispositivos, con el indicador de progreso **Creando**:

    [![Indicador de progreso de creación](xamarin-device-manager-images/mac/17-creating-the-device-sml.png)](xamarin-device-manager-images/mac/17-creating-the-device.png#lightbox)

10. Una vez finalizado el proceso de creación, el nuevo dispositivo se muestra en la lista de dispositivos con el botón **Reproducir**, listo para su inicio:

   [![Dispositivo recién creado listo para su inicio](xamarin-device-manager-images/mac/18-created-device-sml.png)](xamarin-device-manager-images/mac/18-created-device.png#lightbox)

-----


<a name="device-edit" />


### <a name="edit-device"></a>Editar dispositivo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para editar un dispositivo virtual existente, selecciónelo y haga clic en el botón **Editar** (situado en la esquina superior derecha de la pantalla):

[![Botón Editar para modificar un nuevo dispositivo](xamarin-device-manager-images/win/19-edit-button-sml.png)](xamarin-device-manager-images/win/19-edit-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para editar un dispositivo virtual existente, seleccione el menú desplegable **Opciones adicionales** (icono de engranaje) y haga clic en **Editar**:
 
[![Selección del menú Editar para modificar un nuevo dispositivo](xamarin-device-manager-images/mac/19-edit-button-sml.png)](xamarin-device-manager-images/mac/19-edit-button.png#lightbox)
 
-----

Al hacer clic en **Editar**, se inicia el editor de dispositivos para el dispositivo virtual seleccionado:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Pantalla del editor de dispositivos](xamarin-device-manager-images/win/20-device-editor-sml.png)](xamarin-device-manager-images/win/20-device-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
[![Pantalla del editor de dispositivos](xamarin-device-manager-images/mac/20-device-editor-sml.png)](xamarin-device-manager-images/mac/20-device-editor.png#lightbox)
 
-----

En la pantalla del **editor de dispositivos** se muestran las propiedades del dispositivo virtual en la primera columna, con los valores correspondientes de cada propiedad en la segunda columna. Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por ejemplo, en la siguiente captura de pantalla, la propiedad `hw.lcd.density` se cambia de **420** a **240**:

[![Ejemplo de edición de dispositivo](xamarin-device-manager-images/win/21-device-editing-sml.png)](xamarin-device-manager-images/win/21-device-editing.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Por ejemplo, en la siguiente captura de pantalla, la propiedad `hw.lcd.density` se cambia de **320** a **240** y la propiedad `hw.ramSize` se cambia a **768**:
 
[![Ejemplo de edición de dispositivo](xamarin-device-manager-images/mac/21-device-editing-sml.png)](xamarin-device-manager-images/mac/21-device-editing.png#lightbox)
 
-----

Después de realizar los cambios de configuración necesarios, haga clic en el botón **Guardar**.
Para más información sobre cómo cambiar las propiedades del dispositivo virtual, vea [Propiedades de perfil](#properties) más adelante en esta guía.


 
### <a name="additional-options"></a>Opciones adicionales

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En el menú &hellip;, en la esquina superior derecha, hay opciones adicionales para trabajar con dispositivos:

[![Ubicación del menú de opciones adicionales](xamarin-device-manager-images/win/22-overflow-menu-sml.png)](xamarin-device-manager-images/win/22-overflow-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En el menú desplegable situado a la izquierda del botón **Reproducir** hay opciones adicionales para trabajar con dispositivos:

[![Ubicación del menú de opciones adicionales](xamarin-device-manager-images/mac/22-overflow-menu-sml.png)](xamarin-device-manager-images/mac/22-overflow-menu.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

El menú de opciones adicionales contiene los elementos siguientes:

-   **Duplicate and Edit** (Duplicar y editar): duplica el dispositivo seleccionado actualmente y lo abre en la pantalla **Nuevo dispositivo** con otro nombre exclusivo. Por ejemplo, al seleccionar **VisualStudio_android 23_x86_phone** y hacer clic en **Duplicate and Edit** (Duplicar y editar), se anexa un contador al nombre:

    [![Pantalla para duplicar y editar](xamarin-device-manager-images/win/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **Mostrar en el Explorador**: se abre una ventana del Explorador de Windows en la carpeta que contiene los archivos del dispositivo virtual. Por ejemplo, al seleccionar **Nexus 5X API 25** y hacer clic en **Mostrar en el Explorador**, se abre una ventana como la siguiente:

    [![Resultados de hacer clic en Mostrar en el Explorador](xamarin-device-manager-images/win/24-reveal-in-explorer-sml.png)](xamarin-device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **Restablecimiento de fábrica**: restablece el dispositivo seleccionado a sus valores predeterminados, para lo cual borra los cambios de usuario realizados en el estado interno del dispositivo mientras estaba en ejecución. Este cambio no altera las modificaciones que realice en el dispositivo virtual durante la creación y la edición. Aparecerá un cuadro de diálogo que le recordará que este restablecimiento no se puede deshacer. Haga clic en **Wipe user data** (Borrar los datos de usuario) para confirmar el restablecimiento.

-   **Eliminar**: elimina de forma permanente el dispositivo virtual seleccionado.
    Aparecerá un cuadro de diálogo que le recordará que la eliminación de un dispositivo no se puede deshacer. Haga clic en **Eliminar** si está seguro de que quiere eliminar el dispositivo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El menú de opciones adicionales contiene los elementos siguientes:

-   **Editar**: abre el dispositivo seleccionado actualmente en el editor de dispositivos, tal y como se ha descrito anteriormente en [Editar dispositivo](#device-edit).

-   **Duplicate and Edit** (Duplicar y editar): duplica el dispositivo seleccionado actualmente y lo abre en la pantalla **Nuevo dispositivo** con otro nombre exclusivo.
    Por ejemplo, al seleccionar **Nexus 5X API 25** y hacer clic en **Duplicate and Edit** (Duplicar y editar), se anexa un contador al nombre:

    [![Pantalla para duplicar y editar](xamarin-device-manager-images/mac/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **Mostrar en Finder**: se abre una ventana del Finder de macOS en la carpeta que contiene los archivos del dispositivo virtual. Por ejemplo, al seleccionar **Nexus 5X API 25** y hacer clic en **Mostrar en Finder**, se abre una ventana como la siguiente:

    [![Resultados de hacer clic en Mostrar en el Explorador](xamarin-device-manager-images/mac/24-reveal-in-finder-sml.png)](xamarin-device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **Restablecimiento de fábrica**: restablece el dispositivo seleccionado a sus valores predeterminados, para lo cual borra los cambios de usuario realizados en el estado interno del dispositivo mientras estaba en ejecución. Este cambio no altera las modificaciones que realice en el dispositivo virtual durante la creación y la edición. Aparecerá un cuadro de diálogo que le recordará que este restablecimiento no se puede deshacer. Haga clic en **Wipe user data** (Borrar los datos de usuario) para confirmar el restablecimiento.

-   **Eliminar**: elimina de forma permanente el dispositivo virtual seleccionado.
    Aparecerá un cuadro de diálogo que le recordará que la eliminación de un dispositivo no se puede deshacer. Haga clic en **Eliminar** si está seguro de que quiere eliminar el dispositivo.

-----

<a name="properties" />
 

## <a name="profile-properties"></a>Propiedades de perfil

En las pantallas **Nuevo dispositivo** y **Edición de dispositivos** se muestran las propiedades del dispositivo virtual en la primera columna, con los valores correspondientes de cada propiedad en la segunda columna. Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha. Puede modificar sus *propiedades de perfil de hardware* y sus *propiedades de AVD*.
Las propiedades de perfil de hardware (como `hw.ramSize` y `hw.accelerometer`) describen las características físicas del dispositivo emulado. Estas características incluyen el tamaño de pantalla, la cantidad de memoria RAM disponible y si hay un acelerómetro o no. Las propiedades de AVD especifican el funcionamiento del AVD durante su ejecución. Por ejemplo, se pueden configurar las propiedades de AVD para especificar la forma en que el AVD usa la tarjeta gráfica del equipo de desarrollo para la representación.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Puede cambiar las propiedades de la manera siguiente:

-   Para cambiar una propiedad booleana, haga clic en la marca de verificación situada a la derecha de la propiedad booleana:

    ![Cambiar una propiedad booleana](xamarin-device-manager-images/win/25-boolean-value.png)

-   Para cambiar una propiedad *enum* (enumerada), haga clic en la flecha abajo situada a la derecha de la propiedad y seleccione un nuevo valor.

    ![Cambiar una propiedad enum](xamarin-device-manager-images/win/27-enum-value.png)

-   Para cambiar una propiedad de cadena o entero, haga doble clic en la configuración actual de la cadena o del entero en la columna de valor y especifique un nuevo valor.

    ![Cambiar una propiedad de entero](xamarin-device-manager-images/win/26-integer-value.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Puede cambiar las propiedades de la manera siguiente:

-   Para cambiar una propiedad booleana, haga clic en la marca de verificación situada a la derecha de la propiedad booleana:

    ![Cambiar una propiedad booleana](xamarin-device-manager-images/mac/25-boolean-value.png)

-   Para cambiar una propiedad *enum* (enumerada), haga clic en el menú desplegable situado a la derecha de la propiedad y seleccione un nuevo valor.

    ![Cambiar una propiedad enum](xamarin-device-manager-images/mac/27-enum-value.png)

-   Para cambiar una propiedad de cadena o entero, haga doble clic en la configuración actual de la cadena o del entero en la columna de valor y especifique un nuevo valor.

    ![Cambiar una propiedad de entero](xamarin-device-manager-images/mac/26-integer-value.png)


-----

En la tabla siguiente se proporciona una explicación detallada de las propiedades enumeradas en las pantallas **Nuevo dispositivo** y **Edición de dispositivos**:

[!include[](~/android/includes/emulator-properties.md)]

Para más información sobre estas propiedades, vea [Propiedades de perfil de hardware](https://developer.android.com/studio/run/managing-avds.html#hpproperties).


<a name="troubleshooting" />

## <a name="troubleshooting"></a>Solución de problemas

A continuación se describen los problemas más comunes del Administrador de dispositivos Android de Xamarin y soluciones alternativas:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK en ubicación no estándar

Normalmente, Android SDK se instala en la siguiente ubicación:

**C:\\Archivos de programa (x86)\\Android\\android-sdk**

Si el SDK no está instalado en esta ubicación, puede obtener este error en el inicio:

![Error de instancia de Android SDK](xamarin-device-manager-images/win/32-sdk-error.png)

Para solucionar este problema, haga lo siguiente:

1. En el escritorio de Windows, vaya a **C:\\Usuarios\\*nombre de usuario*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Ubicación del archivo de registro del Administrador de dispositivos de Xamarin](xamarin-device-manager-images/win/33-log-files.png)

2. Haga doble clic para abrir uno de los archivos de registro y busque la **ruta de acceso del archivo de configuración**. Por ejemplo:

    [![Ruta de acceso del archivo de configuración en el archivo de registro](xamarin-device-manager-images/win/34-config-file-path-sml.png)](xamarin-device-manager-images/win/34-config-file-path.png#lightbox)

3. Vaya a esta ubicación y haga doble clic en **user.config** para abrirlo. 

4. En **user.config**, busque el elemento **&lt;UserSettings&gt;** y agréguele un atributo **AndroidSdkPath**. Establezca este atributo en la ruta de acceso en la que está instalado Android SDK en el equipo y guarde el archivo. Por ejemplo, **&lt;UserSettings&gt;** tendrá el aspecto siguiente si Android SDK se ha instalado en **C:\\Programas\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Después de realizar este cambio en **user.config**, podrá iniciar el Administrador de dispositivos Android de Xamarin.

### <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

Si tiene un AVD configurado para Android Oreo con acceso Wi-Fi simulado, el reinicio del AVD después de una instantánea puede provocar la deshabilitación del acceso Wi-Fi.

Para evitar este problema:

1. Seleccione el AVD en el Administrador de dispositivos de Xamarin.

2. En el menú de opciones adicionales, haga clic en **Mostrar en el Explorador**.

3. Vaya a **snapshots > default_boot**.

4. Elimine el archivo **snapshot.pb**:

    [![Ubicación del archivo snapshot.pb](xamarin-device-manager-images/win/36-delete-snapshot-sml.png)](xamarin-device-manager-images/win/36-delete-snapshot.png#lightbox)

5. Reinicie el AVD. 

Una vez realizados estos cambios, se reiniciará el AVD en un estado que permita que la conexión Wi-Fi vuelva a funcionar.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

Si tiene un AVD configurado para Android Oreo con acceso Wi-Fi simulado, el reinicio del AVD después de una instantánea puede provocar la deshabilitación del acceso Wi-Fi.

Para evitar este problema:

1. Seleccione el AVD en el Administrador de dispositivos de Xamarin.

2. En el menú de opciones adicionales, haga clic en **Mostrar en Finder**.

3. Vaya a **snapshots > default_boot**.

4. Elimine el archivo **snapshot.pb**:

    [![Ubicación del archivo snapshot.pb](xamarin-device-manager-images/mac/36-delete-snapshot-sml.png)](xamarin-device-manager-images/mac/36-delete-snapshot.png#lightbox)

5. Reinicie el AVD. 

Una vez realizados estos cambios, se reiniciará el AVD en un estado que permita que la conexión Wi-Fi vuelva a funcionar.

-----


### <a name="generating-a-bug-report"></a>Generación de informes de errores

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Si encuentra algún problema con el Administrador de dispositivos Android de Xamarin que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error haciendo clic con el botón derecho en la barra del título y seleccionando **Generar informe de errores**:

![Ubicación del elemento de menú para informar de un error](xamarin-device-manager-images/win/35-bug-report.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Si encuentra algún problema con el Administrador de dispositivos Android de Xamarin que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error haciendo clic **Ayuda > Generate Bug Report (Generar informe de errores)**:

![Ubicación del elemento de menú para informar de un error](xamarin-device-manager-images/mac/35-bug-report.png)

-----

 
 
## <a name="summary"></a>Resumen

En esta guía se presenta el Administrador de dispositivos Android de Xamarin disponible en Visual Studio para Mac y Xamarin para Visual Studio. Se explican características esenciales, como iniciar y detener el emulador de Android, seleccionar un dispositivo virtual Android (AVD) para su ejecución, crear dispositivos virtuales y editar un dispositivo virtual. También se describe cómo editar las propiedades de perfil de hardware para una mayor personalización.


## <a name="related-links"></a>Vínculos relacionados

- [Cambios en las Herramientas de Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Depuración con el emulador de Android SDK](~/android/deploy-test/debugging/android-sdk-emulator/index.md)
- [Notas de la versión de SDK Tools (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
