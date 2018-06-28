---
title: Solucionar problemas de configuración del emulador
description: En este artículo se explica cómo diagnosticar y solucionar los problemas que pueden producirse al usar Android Device Manager.
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: 4cbd7d7626d114856d6c68fe7bc9feb7c2a5abc2
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733833"
---
# <a name="troubleshooting-emulator-setup-problems"></a>Solucionar problemas de configuración del emulador

_En este artículo se explica cómo diagnosticar y solucionar los problemas que pueden producirse al usar Android Device Manager para configurar Android Emulator. Para obtener información sobre cómo solucionar problemas mientras se ejecuta Android Emulator, vea [Solución de problemas de Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md)._

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="android-sdk-in-non-standard-location"></a>Android SDK en ubicación no estándar

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

## <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

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

## <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

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

## <a name="generating-a-bug-report"></a>Generación de informes de errores

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Si encuentra algún problema con Android Device Manager que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error. Para ello, haga clic con el botón derecho en la barra del título y seleccione **Generate Bug Report** (Generar informe de errores):

[![Ubicación del elemento de menú para informar de un error](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Si encuentra algún problema con Android Device Manager que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error. Para ello, haga clic en **Ayuda > Generate Bug Report** (Generar informe de errores):

[![Ubicación del elemento de menú para informar de un error](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)

-----
