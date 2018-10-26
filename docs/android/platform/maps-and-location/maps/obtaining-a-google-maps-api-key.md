---
title: Obtener clave de API un Google Maps
description: Cómo obtener una clave de API de mapas de Google para agregar funcionalidad asigna a la aplicación.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: bfeb9d8fa2a0b5a9b18ab8266500586e2e3b6c68
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120026"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obtener clave de API un Google Maps

Para usar la funcionalidad de Google Maps en Android, deberá registrarse para una clave de API de mapas de Google. Hasta entonces, solo verá una cuadrícula vacía en lugar de un mapa en sus aplicaciones. Debe obtener una clave de API de Android de Google Maps v2: las claves de la anterior v1 de clave de API de Android de Google Maps no funcionará.

Obtención de una clave de API de Maps v2 implica los pasos siguientes:

1.  Recuperar la huella digital de SHA-1 del almacén de claves que se usa para firmar la aplicación.
2.  Cree un proyecto en la consola de Google APIs.
3.  Obtener la clave de API.


## <a name="obtaining-your-signing-key-fingerprint"></a>Obtener la huella digital de clave firma

Para solicitar una clave de API de mapas de Google, deberá saber la huella digital de SHA-1 del almacén de claves que se usa para firmar la aplicación.
Normalmente, esto significa que tendrá que determinar la huella digital de SHA-1 para el almacén de claves de depuración y, a continuación, huella digital SHA-1 para el almacén de claves que se usa para firmar la aplicación para su lanzamiento.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

De forma predeterminada, el almacén de claves que se usa para firmar las versiones de depuración de una aplicación puede ser de Xamarin.Android se encuentra en la siguiente ubicación:

**C:\\usuarios\\[USERNAME]\\AppData\\Local\\Xamarin\\Mono para Android\\debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. Esta herramienta se encuentra normalmente en el directorio bin de Java:

**C:\\archivos de programa (x86)\\Java\\jdk [versión]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

De forma predeterminada, el almacén de claves que se usa para firmar las versiones de depuración de una aplicación puede ser de Xamarin.Android se encuentra en la siguiente ubicación:

**/Users/[username]/.local/Share/Xamarin/mono para Android/debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. Esta herramienta se encuentra normalmente en el directorio bin de Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


Ejecutar keytool mediante el comando siguiente (con las rutas de acceso de archivo que se muestra arriba):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Ejemplo de Debug.keystore

Para la clave de depuración predeterminado (que se crea automáticamente para la depuración), use este comando:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>Claves de producción

Al implementar una aplicación en Google Play, debe ser [firmado con una clave privada](~/android/deploy-test/signing/index.md).
El `keytool` deberá ejecutarse con los detalles de la clave privados y la huella digital de SHA-1 resultante usa para crear una clave de API de Google Maps de producción. No olvide actualizar el **AndroidManifest.xml** archivo con la clave de API de Google Maps correcta antes de la implementación.

### <a name="keytool-output"></a>Salida de keytool

Debería ver algo parecido a la siguiente salida en la ventana de consola:

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

Se usará la huella digital de SHA-1 (enumeradas después **SHA1**) más adelante en esta guía.

## <a name="creating-an-api-project"></a>Crear un proyecto de API

Después de recuperar la huella digital de SHA-1 del almacén de claves de firma, es necesario crear un nuevo proyecto en la consola de Google APIs (o agregar el servicio de API de Android de Google Maps v2 a un proyecto existente).

1. En un explorador, vaya a la [API de consola de desarrolladores de Google y el panel de servicios de](https://console.developers.google.com/apis/dashboard/) y haga clic en **seleccione un proyecto**. Haga clic en un nombre de proyecto o cree uno nuevo haciendo clic en **nuevo proyecto**:

   [![Botón Crear proyecto de Google Developer Console](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Si ha creado un proyecto nuevo, escriba el nombre del proyecto en el **nuevo proyecto** cuadro de diálogo que se muestra. Este cuadro de diálogo fabricará un identificador único del proyecto que se basa en el nombre del proyecto. A continuación, haga clic en el **crear** botón tal como se muestra en este ejemplo:

   [![Nuevo proyecto se denomina XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Tras un minuto aproximadamente, se crea el proyecto y se le dirigirá a la **panel** página del proyecto. Desde allí, haga clic en **habilitar API y servicios**:

   [![Al hacer clic en la API de Android de Google Maps en la sección de biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Desde el **biblioteca API** página, haga clic en **Maps SDK para Android**. En la siguiente página, haga clic en **habilitar** para activar el servicio para este proyecto:

   [![Al hacer clic en el botón Habilitar en la sección del panel](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

En este momento se ha creado el proyecto de API y API de Android de Google Maps v2 se ha agregado a él. Sin embargo, no se puede usar esta API en el proyecto hasta que cree credenciales para ella. La siguiente sección explica cómo crear una clave de API y la lista blanca de una aplicación de Xamarin.Android, por lo que está autorizado para usar esta clave.

## <a name="obtaining-the-api-key"></a>Obtener la clave de API

Después de la **Google Developer Console** proyecto de API ha sido creado, es necesario crear una clave de API de Android. Las aplicaciones de Xamarin.Android deben tener una clave de API antes de que se les concede acceso a la API de Android mapa v2.

1. En el **Maps SDK para Android** página que se muestra (después de hacer clic **habilitar** en el paso anterior), vaya a la **credenciales** ficha y haga clic en el **Create credenciales** botón:

   [![SDK de mapas de mensajes de credenciales de Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Haga clic en **clave de API**:

   [![Agregar credenciales al cuadro de diálogo de proyecto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Una vez que se hace clic en este botón, se genera la clave de API. A continuación es necesario restringir esta clave para que solo la aplicación puede llamar a API con esta clave. Haga clic en **clave RESTRICT**:

   [![Al hacer clic restringir clave en la página de credenciales](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Cambiar el **nombre** arrastrándolo desde **1 de la clave de API** por un nombre que le ayudarán a recordar para qué se usa la clave (**XamarinMapsDemoKey** se usa en este ejemplo). A continuación, haga clic en el **aplicaciones Android** botón de radio:

   [![Seleccionar aplicaciones de Android en la página de credenciales](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para agregar la huella digital de SHA-1, haga clic en **+ Agregar nombre de paquete y la huella digital**:

   [![Al hacer clic en agregar el nombre de paquete y la huella digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Escriba el nombre del paquete de la aplicación y especifique la huella digital de certificado de SHA-1 (obtenidos a través de `keytool` tal como se explicó anteriormente en esta guía). En el ejemplo siguiente, el nombre del paquete para `XamarinMapsDemo` está escrito, seguido por la huella digital de certificado de SHA-1 obtenida **debug.keystore**:

   [![Nombre del paquete especificada es com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Tenga en cuenta que, en orden para el APK tener acceso a Google Maps, debe incluir las huellas digitales de SHA-1 y empaquetar los nombres para cada almacén de claves (debug y release) que usó para firmar el APK. Por ejemplo, si usa un equipo para depuración y otro equipo para generar el APK de lanzamiento, debe incluir la huella digital de certificado de SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital de certificado de SHA-1 desde el almacén de claves de la versión de el segundo equipo. Haga clic en **+ Agregar nombre de paquete y la huella digital** para agregar otro nombre de paquete y la huella digital, tal como se muestra en este ejemplo:

   [![Agregar otra huella crea otro certificado de SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Haga clic en el botón **Guardar** para guardar los cambios. A continuación, se le redirigirá a la lista de las claves de API. Si tiene otras claves de API que ha creado anteriormente, también aparecerán aquí. En este ejemplo, se muestra una sola clave de API (creada en los pasos anteriores):

   [![XamarinMapsDemoKey se muestra en la lista de claves de API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conectar el proyecto a una cuenta facturable

A partir 11 de junio, de 2018, la clave de API no funcionará si el proyecto no está conectado a una cuenta facturable (incluso si el servicio todavía es gratis para las aplicaciones móviles).

1. Haga clic en el botón de menú de hamburguesa y seleccione el **facturación** página:

   [![Seleccione la sección de facturación de menú de hamburguesa](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Vincular el proyecto con una cuenta de facturación, haga clic en **vincular una cuenta de facturación** seguido **crear cuenta de facturación** en el menú emergente mostrado (si no tienes una cuenta, se le guiará para crear una nueva):

   [![Proyecto de vínculo a la cuenta de facturación](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Agregar la clave al proyecto

Por último, agregue esta clave de API para el **AndroidManifest.XML** archivo de la aplicación de Xamarin.Android. En el ejemplo siguiente, `YOUR_API_KEY` debe reemplazarse con la clave de API generada en los pasos anteriores:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>Vínculos relacionados

- [Consola de API de Google](https://code.google.com/apis/console/)
- [La clave de API de Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
