---
title: Administración de dispositivos virtuales con Android Device Manager
description: En este artículo se explica cómo usar Android Device Manager para crear y configurar dispositivos virtuales Android (AVD) que emulen dispositivos Android físicos. Puede usar estos dispositivos virtuales para ejecutar y probar la aplicación sin tener que depender de un dispositivo físico.
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a7c1aeafd94d7e2639617cda13312ee8a09e2c94
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935333"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Administración de dispositivos virtuales con Android Device Manager

_En este artículo se explica cómo usar Android Device Manager para crear y configurar dispositivos virtuales Android (AVD) que emulen dispositivos Android físicos. Puede usar estos dispositivos virtuales para ejecutar y probar la aplicación sin tener que depender de un dispositivo físico._

## <a name="overview"></a>Información general

Después de comprobar que la aceleración de hardware está habilitada (como se explica en [Aceleración de hardware para el rendimiento del emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), el siguiente paso es usar _Android Device Manager_ (también denominado _Xamarin Android Device Manager_) para crear dispositivos virtuales que se puedan usar para probar y depurar la aplicación.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En este artículo se explica cómo se usa Android Device Manager para crear, duplicar, personalizar e iniciar dispositivos virtuales Android.

[![Captura de pantalla de Android Device Manager en la pestaña Dispositivos](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

Android Device Manager se usa para crear y configurar _dispositivos virtuales Android_ (AVD) que se ejecutan en [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD es una configuración del emulador que simula un dispositivo Android físico. Esto permite ejecutar y probar la aplicación en diversas configuraciones que simulan diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

Para usar Android Device Manager, necesita lo siguiente:

-   Se necesita Visual Studio 2017 versión 15.7 o posterior. Se admiten las ediciones Community, Professional y Enterprise de Visual Studio.

-   Visual Studio Tools para Xamarin versión 4.9 o posterior.

-   Es necesario instalar Android SDK (vea [Configuración de Android SDK para Xamarin.Android](~/android/get-started/installation/android-sdk.md)) y SDK Tools versión 26.1.1 o posterior como se explica en la sección siguiente. Asegúrese de instalar Android SDK en la siguiente ubicación (si todavía no está instalado): **C:\\Archivos de programa (x86)\\Android\\android-sdk**.


## <a name="launching-the-device-manager"></a>Iniciar el Administrador de dispositivos

Inicie Android Device Manager desde el menú **Herramientas**. Para ello, haga clic en **Herramientas > Android > Android Device Manager**:

[![Inicio desde el menú Herramientas](device-manager-images/win/04-tools-menu-sml.png)](device-manager-images/win/04-tools-menu.png#lightbox)

Si ve el cuadro de diálogo de error siguiente al inicio, vea [Solución de problemas de Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md) para obtener soluciones alternativas:

![Error de instancia de Android SDK](device-manager-images/win/32-sdk-error.png)

Para poder usar Android Device Manager, debe instalar Android SDK Tools versión 26.1.1 o posterior. Si no está instalado, podría ver este cuadro de diálogo de error en el inicio:

![Cuadro de diálogo de error de instancia de Android SDK](device-manager-images/win/02-sdk-instance-error.png)

Si ve este cuadro de diálogo de error, haga clic en **Abrir SDK Manager** para abrir Android SDK Manager. En Android SDK Manager, haga clic en la pestaña **Herramientas** e instale lo siguiente:

-   **Android SDK Tools 26.1.1** o posterior 
-   **Herramientas de plataforma de Android SDK 27.0.1** o posterior  
-   **Herramientas de compilación de Android SDK 27.0.3** o posterior

Estos paquetes deberían aparecer con el estado **Instalado**, como se muestra en la captura de pantalla siguiente:

[![Instalar Android SDK Tools 27.0](device-manager-images/win/03-sdk-tools-sml.png)](device-manager-images/win/03-sdk-tools.png#lightbox)

Después de instalar estos paquetes, puede cerrar SDK Manager y volver a iniciar el Administrador de dispositivos Android.

## <a name="main-screen"></a>Pantalla principal

Cuando inicie el Administrador de dispositivos Android por primera vez, aparecerá una pantalla con todos los dispositivos virtuales configurados. Para cada dispositivo, se muestran el **nombre**, el **sistema operativo** (nivel de API de Android), la **CPU**, el tamaño de **memoria** y la resolución de pantalla:

[![Lista de los dispositivos instalados y sus parámetros](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Al hacer clic en un dispositivo de la lista, aparece el botón **Iniciar** a la derecha. Puede hacer clic en el botón **Iniciar** para iniciar el emulador con este dispositivo virtual:

[![Botón Iniciar de una imagen de dispositivo](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

Una vez que el emulador se haya iniciado con el dispositivo virtual seleccionado, el botón **Iniciar** cambiará al botón **Detener**, que puede usar para detener el emulador:

[![Botón Detener del dispositivo en ejecución](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Dispositivo nuevo

Para crear un dispositivo, haga clic en el botón **Nuevo** (situado en la parte superior derecha de la pantalla):

[![Botón Nuevo para crear un dispositivo](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

Al hacer clic en **Nuevo**, aparece la pantalla **Nuevo dispositivo**:

[![Pantalla Nuevo dispositivo del Administrador de dispositivos](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Para configurar un nuevo dispositivo en la pantalla **Nuevo dispositivo**, siga estos pasos:

1. Seleccione el dispositivo físico que quiera emular. Para ello, haga clic en el menú desplegable **Dispositivo**:

    [![Menú desplegable Dispositivo](device-manager-images/win/10-device-menu-sml.png)](device-manager-images/win/10-device-menu.png#lightbox)

2. Seleccione la imagen de sistema que quiera usar con este dispositivo virtual. Para ello, haga clic en el menú desplegable **Imagen de sistema**. En este menú aparecen las imágenes del administrador de dispositivos de sistema instaladas en la sección **Instalado**. En la sección **Descargar** se muestran las imágenes de administrador de dispositivos de sistema que no están disponibles actualmente en el equipo de desarrollo, pero que se pueden instalar de forma automática:

    [![Menú desplegable Imagen de sistema](device-manager-images/win/11-system-image-menu-sml.png)](device-manager-images/win/11-system-image-menu.png#lightbox)

3. Asigne un nuevo nombre al dispositivo. En el ejemplo siguiente, el nuevo dispositivo se denomina **Nexus 5 API 25**:

    [![Asignar un nombre al nuevo dispositivo](device-manager-images/win/12-device-name-sml.png)](device-manager-images/win/12-device-name.png#lightbox)

4. Edite las propiedades que necesite modificar. Para realizar cambios en las propiedades, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).

5. Agregue las propiedades adicionales que necesite establecer explícitamente. En la pantalla **Nuevo dispositivo** solo se muestran las propiedades que se modifican con más frecuencia, pero puede hacer clic en el menú desplegable **Agregar propiedad** (en la esquina inferior izquierda) para agregar propiedades adicionales. En el ejemplo siguiente, se agrega la propiedad `hw.lcd.backlight`:

    [![Menú desplegable Agregar propiedad](device-manager-images/win/13-add-property-menu-sml.png)](device-manager-images/win/13-add-property-menu.png#lightbox)

6. Haga clic en el botón **Crear** (esquina inferior derecha) para crear el dispositivo:

    ![Botón Crear](device-manager-images/win/14-create-button.png)

7. Es posible que aparezca la pantalla **Aceptación de licencia**. Haga clic en **Aceptar** si está de acuerdo con los términos de licencia:

    ![Pantalla de aceptación de licencia](device-manager-images/win/15-license-acceptance.png)

8. Android Device Manager agrega el nuevo dispositivo a la lista de dispositivos virtuales instalados, con el indicador de progreso **Creando** mientras crea el dispositivo:

    [![Indicador de progreso de creación](device-manager-images/win/16-creating-the-device-sml.png)](device-manager-images/win/16-creating-the-device.png#lightbox)

9. Una vez finalizado el proceso de creación, el nuevo dispositivo se muestra en la lista de dispositivos virtuales instalados con el botón **Iniciar**, listo para su inicio:

   [![Dispositivo recién creado listo para su inicio](device-manager-images/win/17-created-device-sml.png)](device-manager-images/win/17-created-device.png#lightbox)


### <a name="edit-device"></a>Editar dispositivo

Para editar un dispositivo virtual existente, selecciónelo y haga clic en el botón **Editar** (situado en la esquina superior derecha de la pantalla):

[![Botón Editar para modificar un nuevo dispositivo](device-manager-images/win/19-edit-button-sml.png)](device-manager-images/win/19-edit-button.png#lightbox)

Al hacer clic en **Editar**, se inicia el editor de dispositivos para el dispositivo virtual seleccionado:

[![Pantalla del editor de dispositivos](device-manager-images/win/20-device-editor-sml.png)](device-manager-images/win/20-device-editor.png#lightbox)

En la pantalla del **editor de dispositivos** se muestran las propiedades del dispositivo virtual en la primera columna, con los valores correspondientes de cada propiedad en la segunda columna. Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha.

Por ejemplo, en la siguiente captura de pantalla, la propiedad `hw.lcd.density` se cambia de **420** a **240**:

[![Ejemplo de edición de dispositivo](device-manager-images/win/21-device-editing-sml.png)](device-manager-images/win/21-device-editing.png#lightbox)

Después de realizar los cambios de configuración necesarios, haga clic en el botón **Guardar**.
Para obtener más información sobre cómo cambiar las propiedades del dispositivo virtual, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).

 
### <a name="additional-options"></a>Opciones adicionales

En el menú &hellip;, en la esquina superior derecha, hay opciones adicionales para trabajar con dispositivos:

[![Ubicación del menú de opciones adicionales](device-manager-images/win/22-overflow-menu-sml.png)](device-manager-images/win/22-overflow-menu.png#lightbox)

El menú de opciones adicionales contiene los elementos siguientes:

-   **Duplicate and Edit** (Duplicar y editar): duplica el dispositivo seleccionado actualmente y lo abre en la pantalla **Nuevo dispositivo** con otro nombre exclusivo. Por ejemplo, al seleccionar **VisualStudio_android 23_x86_phone** y hacer clic en **Duplicate and Edit** (Duplicar y editar), se anexa un contador al nombre:

    [![Pantalla para duplicar y editar](device-manager-images/win/23-dupe-and-edit-sml.png)](device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **Mostrar en el Explorador**: se abre una ventana del Explorador de Windows en la carpeta que contiene los archivos del dispositivo virtual. Por ejemplo, al seleccionar **Nexus 5X API 25** y hacer clic en **Mostrar en el Explorador**, se abre una ventana como la siguiente:

    [![Resultados de hacer clic en Mostrar en el Explorador](device-manager-images/win/24-reveal-in-explorer-sml.png)](device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **Restablecimiento de fábrica**: restablece el dispositivo seleccionado a sus valores predeterminados, para lo cual borra los cambios de usuario realizados en el estado interno del dispositivo mientras estaba en ejecución (esto también borra la captura de pantalla [Arranque rápido](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) actual, si existe). Este cambio no altera las modificaciones que realice en el dispositivo virtual durante la creación y la edición. Aparecerá un cuadro de diálogo que le recordará que este restablecimiento no se puede deshacer. Haga clic en **Wipe user data** (Borrar los datos de usuario) para confirmar el restablecimiento.

-   **Eliminar**: elimina de forma permanente el dispositivo virtual seleccionado.
    Aparecerá un cuadro de diálogo que le recordará que la eliminación de un dispositivo no se puede deshacer. Haga clic en **Eliminar** si está seguro de que quiere eliminar el dispositivo.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En este artículo se explica cómo se usa Android Device Manager para crear, duplicar, personalizar e iniciar dispositivos virtuales Android.

[![Captura de pantalla de Android Device Manager en la pestaña Dispositivos](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Esta guía solo se aplica a Visual Studio para Mac.
Xamarin Studio no es compatible con Android Device Manager.

Android Device Manager se usa para crear y configurar *dispositivos virtuales Android* (AVD) que se ejecutan en [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD es una configuración del emulador que simula un dispositivo Android físico. Esto permite ejecutar y probar la aplicación en diversas configuraciones que simulan diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

- Visual Studio para Mac 7.5 o posterior.

- Es necesario instalar Android SDK 8.0 (API 26) o posterior mediante Android SDK Manager.


## <a name="launching-the-device-manager"></a>Iniciar el Administrador de dispositivos

Para iniciar Android Device Manager, haga clic en **Herramientas > Device Manager**:

[![Inicio desde el menú Herramientas](device-manager-images/mac/16-tools-menu-sml.png)](device-manager-images/mac/16-tools-menu.png#lightbox)

Para poder usar Android Device Manager, debe instalar Android SDK Tools versión 26.0.2 o posterior. Si no está instalado, verá este cuadro de diálogo de error en el inicio:

![Cuadro de diálogo de error de instancia de Android SDK](device-manager-images/mac/02-sdk-instance-error.png)

Si ve este cuadro de diálogo de error, haga clic en **Aceptar** para abrir Android SDK Manager. En Android SDK Manager, haga clic en la pestaña **Herramientas** e instale lo siguiente:

-   **Android SDK Tools 26.0.2** o posterior 
-   **Herramientas de plataforma de Android SDK 26.0.0** o posterior 
-   **Herramientas de compilación de Android SDK 26.0.0** o posterior

Estos paquetes deberían aparecer con el estado **Instalado**, como se muestra en la captura de pantalla siguiente:

[![Instalar Android SDK Tools 26.0](device-manager-images/mac/03-sdk-tools-sml.png)](device-manager-images/mac/03-sdk-tools.png#lightbox)

## <a name="main-screen"></a>Pantalla principal

Cuando inicie el Administrador de dispositivos Android por primera vez, aparecerá una pantalla con todos los dispositivos virtuales configurados. Para cada dispositivo, se muestran el **nombre**, la **imagen de sistema** (nivel de API de Android), la **CPU**, el tamaño de **memoria** y la resolución de pantalla:

[![Lista de los dispositivos instalados y sus parámetros](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Haga clic en el botón **Reproducir** para iniciar el emulador con el dispositivo virtual de su elección:

[![Botón Iniciar de una imagen de dispositivo](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

Una vez que el emulador se haya iniciado con el dispositivo virtual seleccionado, el botón **Reproducir** cambiará al botón **Detener**, que puede usar para detener el emulador:

[![Botón Detener del dispositivo en ejecución](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

### <a name="new-device"></a>Dispositivo nuevo

Para crear un dispositivo, haga clic en el botón **Nuevo dispositivo** (situado en la parte superior derecha de la pantalla):

[![Botón Nuevo para crear un dispositivo](device-manager-images/mac/08-new-button-sml.png)](device-manager-images/mac/08-new-button.png#lightbox)

Al hacer clic en **Nuevo dispositivo**, aparece la pantalla **Nuevo dispositivo**:

[![Pantalla Nuevo dispositivo del Administrador de dispositivos](device-manager-images/mac/09-new-device-editor-sml.png)](device-manager-images/mac/09-new-device-editor.png#lightbox)

Siga estos pasos para configurar un nuevo dispositivo en la pantalla **Nuevo dispositivo**:

1. Seleccione el dispositivo físico que quiera emular. Para ello, haga clic en el menú desplegable **Dispositivo**:

    [![Menú desplegable Dispositivo](device-manager-images/mac/10-device-menu-sml.png)](device-manager-images/mac/10-device-menu.png#lightbox)

2. Seleccione la imagen de sistema que quiera usar con este dispositivo virtual. Para ello, haga clic en el menú desplegable **Imagen de sistema**. En este menú aparecen las imágenes del administrador de dispositivos de sistema instaladas en la sección **Instalado**. En la sección **Descargar** (si aparece) se muestran las imágenes de administrador de dispositivos de sistema que no están disponibles actualmente en el equipo de desarrollo, pero que se pueden instalar de forma automática:

    [![Menú desplegable Imagen de sistema](device-manager-images/mac/11-system-image-menu-sml.png)](device-manager-images/mac/11-system-image-menu.png#lightbox)

3. Asigne un nuevo nombre al dispositivo. En el ejemplo siguiente, el nuevo dispositivo se denomina **Nexus 5X API 25**:

    [![Asignar un nombre al nuevo dispositivo](device-manager-images/mac/12-device-name-sml.png)](device-manager-images/mac/12-device-name.png#lightbox)

4. Edite las propiedades que necesite modificar. Para realizar cambios en las propiedades, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).

5. Agregue las propiedades adicionales que necesite establecer explícitamente. En la pantalla **Nuevo dispositivo** solo se muestran las propiedades que se modifican con más frecuencia, pero puede hacer clic en el menú desplegable **Agregar propiedad** (en la esquina inferior izquierda) para agregar propiedades adicionales:

    [![Menú desplegable Agregar propiedad](device-manager-images/mac/13-add-property-menu-sml.png)](device-manager-images/mac/13-add-property-menu.png#lightbox)

6. También puede hacer clic en **Personalizar** para definir una nueva propiedad para el dispositivo:

    ![Botón Crear](device-manager-images/mac/14-custom-button.png)

7. Haga clic en el botón **Crear** (esquina inferior derecha) para crear el dispositivo:

    ![Botón Crear](device-manager-images/mac/15-create-button.png)

8. Es posible que aparezca la pantalla **Aceptación de licencia**. Haga clic en **Aceptar** si está de acuerdo con los términos de licencia.

9. Android Device Manager agrega el nuevo dispositivo a la lista de dispositivos virtuales instalados, con el indicador de progreso **Creando** mientras crea el dispositivo:

    [![Indicador de progreso de creación](device-manager-images/mac/17-creating-the-device-sml.png)](device-manager-images/mac/17-creating-the-device.png#lightbox)

10. Una vez finalizado el proceso de creación, el nuevo dispositivo se muestra en la lista de dispositivos con el botón **Reproducir**, listo para su inicio:

   [![Dispositivo recién creado listo para su inicio](device-manager-images/mac/18-created-device-sml.png)](device-manager-images/mac/18-created-device.png#lightbox)


### <a name="edit-device"></a>Editar dispositivo

Para editar un dispositivo virtual existente, seleccione el menú desplegable **Opciones adicionales** (icono de engranaje) y haga clic en **Editar**:
 
[![Selección del menú Editar para modificar un nuevo dispositivo](device-manager-images/mac/19-edit-button-sml.png)](device-manager-images/mac/19-edit-button.png#lightbox)

Al hacer clic en **Editar**, se inicia el editor de dispositivos para el dispositivo virtual seleccionado:
 
[![Pantalla del editor de dispositivos](device-manager-images/mac/20-device-editor-sml.png)](device-manager-images/mac/20-device-editor.png#lightbox)

En la pantalla del **editor de dispositivos** se muestran las propiedades del dispositivo virtual en la primera columna, con los valores correspondientes de cada propiedad en la segunda columna. Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha.

Por ejemplo, en la siguiente captura de pantalla, la propiedad `hw.lcd.density` se cambia de **320** a **240** y la propiedad `hw.ramSize` se cambia a **768**:
 
[![Ejemplo de edición de dispositivo](device-manager-images/mac/21-device-editing-sml.png)](device-manager-images/mac/21-device-editing.png#lightbox)

Después de realizar los cambios de configuración necesarios, haga clic en el botón **Guardar**.
Para obtener más información sobre cómo cambiar las propiedades del dispositivo virtual, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).

 
### <a name="additional-options"></a>Opciones adicionales

En el menú desplegable situado a la izquierda del botón **Reproducir** hay opciones adicionales para trabajar con dispositivos:

[![Ubicación del menú de opciones adicionales](device-manager-images/mac/22-overflow-menu-sml.png)](device-manager-images/mac/22-overflow-menu.png#lightbox)

El menú de opciones adicionales contiene los elementos siguientes:

-   **Editar**: abre el dispositivo seleccionado actualmente en el editor de dispositivos, tal como se ha descrito anteriormente.

-   **Duplicate and Edit** (Duplicar y editar): duplica el dispositivo seleccionado actualmente y lo abre en la pantalla **Nuevo dispositivo** con otro nombre exclusivo.
    Por ejemplo, al seleccionar **Nexus 5X API 25** y hacer clic en **Duplicate and Edit** (Duplicar y editar), se anexa un contador al nombre:

    [![Pantalla para duplicar y editar](device-manager-images/mac/23-dupe-and-edit-sml.png)](device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **Mostrar en Finder**: se abre una ventana del Finder de macOS en la carpeta que contiene los archivos del dispositivo virtual. Por ejemplo, al seleccionar **Nexus 5X API 25** y hacer clic en **Mostrar en Finder**, se abre una ventana como la siguiente:

    [![Resultados de hacer clic en Mostrar en el Explorador](device-manager-images/mac/24-reveal-in-finder-sml.png)](device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **Restablecimiento de fábrica**: restablece el dispositivo seleccionado a sus valores predeterminados, para lo cual borra los cambios de usuario realizados en el estado interno del dispositivo mientras estaba en ejecución. Este cambio no altera las modificaciones que realice en el dispositivo virtual durante la creación y la edición. Aparecerá un cuadro de diálogo que le recordará que este restablecimiento no se puede deshacer. Haga clic en **Wipe user data** (Borrar los datos de usuario) para confirmar el restablecimiento.

-   **Eliminar**: elimina de forma permanente el dispositivo virtual seleccionado.
    Aparecerá un cuadro de diálogo que le recordará que la eliminación de un dispositivo no se puede deshacer. Haga clic en **Eliminar** si está seguro de que quiere eliminar el dispositivo.

-----

## <a name="troubleshooting"></a>Solución de problemas

En las secciones siguientes se explica cómo diagnosticar y solucionar los problemas que pueden producirse al usar Android Device Manager para configurar dispositivos virtuales.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK en ubicación no estándar

Normalmente, Android SDK se instala en la siguiente ubicación:

**C:\\Archivos de programa (x86)\\Android\\android-sdk**

Si el SDK no está instalado en esta ubicación, puede obtener este error al iniciar Android Device Manager:

![Error de instancia de Android SDK](troubleshooting-images/win/01-sdk-error.png)

Para solucionar este problema, haga lo siguiente:

1. En el escritorio de Windows, vaya a **C:\\Usuarios\\*nombre de usuario*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Ubicación del archivo de registro de Android Device Manager](troubleshooting-images/win/02-log-files.png)

2. Haga doble clic para abrir uno de los archivos de registro y busque la **ruta de acceso del archivo de configuración**. Por ejemplo:

    [![Ruta de acceso del archivo de configuración en el archivo de registro](troubleshooting-images/win/03-config-file-path-sml.png)](troubleshooting-images/win/03-config-file-path.png#lightbox)

3. Vaya a esta ubicación y haga doble clic en **user.config** para abrirlo. 

4. En **user.config**, busque el elemento **&lt;UserSettings&gt;** y agréguele un atributo **AndroidSdkPath**. Establezca este atributo en la ruta de acceso en la que está instalado Android SDK en el equipo y guarde el archivo. Por ejemplo, **&lt;UserSettings&gt;** tendrá el aspecto siguiente si Android SDK se ha instalado en **C:\\Programas\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Después de realizar este cambio en **user.config**, podrá iniciar Android Device Manager.

### <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

Si tiene un AVD configurado para Android Oreo con acceso Wi-Fi simulado, el reinicio del AVD después de una instantánea puede provocar la deshabilitación del acceso Wi-Fi.

Para evitar este problema:

1. Seleccione el AVD en Android Device Manager.

2. En el menú de opciones adicionales, haga clic en **Mostrar en el Explorador**.

3. Vaya a **snapshots > default_boot**.

4. Elimine el archivo **snapshot.pb**:

    ![Ubicación del archivo snapshot.pb](troubleshooting-images/win/05-delete-snapshot.png)

5. Reinicie el AVD. 

Una vez realizados estos cambios, se reiniciará el AVD en un estado que permita que la conexión Wi-Fi vuelva a funcionar.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

Si tiene un AVD configurado para Android Oreo con acceso Wi-Fi simulado, el reinicio del AVD después de una instantánea puede provocar la deshabilitación del acceso Wi-Fi.

Para evitar este problema:

1. Seleccione el AVD en Android Device Manager.

2. En el menú de opciones adicionales, haga clic en **Mostrar en Finder**.

3. Vaya a **snapshots > default_boot**.

4. Elimine el archivo **snapshot.pb**:

    [![Ubicación del archivo snapshot.pb](troubleshooting-images/mac/02-delete-snapshot-sml.png)](troubleshooting-images/mac/02-delete-snapshot.png#lightbox)

5. Reinicie el AVD. 

Una vez realizados estos cambios, se reiniciará el AVD en un estado que permita que la conexión Wi-Fi vuelva a funcionar.

-----

### <a name="generating-a-bug-report"></a>Generación de informes de errores

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Si encuentra algún problema con Android Device Manager que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error. Para ello, haga clic con el botón derecho en la barra del título y seleccione **Generate Bug Report** (Generar informe de errores):

[![Ubicación del elemento de menú para informar de un error](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Si encuentra algún problema con Android Device Manager que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error. Para ello, haga clic en **Ayuda > Generate Bug Report** (Generar informe de errores):

[![Ubicación del elemento de menú para informar de un error](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)


-----

## <a name="summary"></a>Resumen

En esta guía se presenta la versión de Android Device Manager disponible en Visual Studio para Mac y Visual Studio Tools para Xamarin. Se explican características esenciales, como iniciar y detener el emulador de Android, seleccionar un dispositivo virtual Android (AVD) para su ejecución, crear dispositivos virtuales y editar un dispositivo virtual. Se explica cómo editar propiedades de hardware de generación de perfiles para aumentar la personalización, y se proporcionan sugerencias para solucionar problemas comunes.


## <a name="related-links"></a>Vínculos relacionados

- [Cambios en las Herramientas de Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
- [Notas de la versión de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
