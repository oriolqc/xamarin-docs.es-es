---
title: Clave de API de mapas de obtener un Google
description: Cómo obtener una clave de API de Google se asigna para agregar asignaciones funcionalidad a la aplicación.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: 365bc56c70ef903622c3a4583a30460f907b4ec9
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935059"
---
# <a name="obtaining-a-google-maps-api-key"></a>Clave de API de mapas de obtener un Google

Para usar la funcionalidad de Google Maps en Android, debe registrar para una clave de API de mapas con Google. Mientras lo haga, solo verá una cuadrícula vacía en lugar de una asignación en las aplicaciones. Debe obtener una clave de API de Google Maps Android v2: las claves de la anterior v1 de clave de API de Google Maps Android no funcionará.

Obtener una clave de API de mapas v2 implica los pasos siguientes:

1.  Recuperar la huella digital de SHA-1 del almacén de claves que se utiliza para firmar la aplicación.
2.  Cree un proyecto en la consola de Google APIs.
3.  Obtener la clave de API.


## <a name="obtaining-your-signing-key-fingerprint"></a>Obtener la huella digital de clave firma

Para solicitar una clave de API de mapas de Google, debe conocer la huella digital de SHA-1 del almacén de claves que se utiliza para firmar la aplicación.
Normalmente, esto significa que tendrá que determinar la huella digital de SHA-1 para el almacén de claves de depuración y, a continuación, mediante huella digital SHA-1 para el almacén de claves que se usa para firmar la aplicación para la versión.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

De forma predeterminada, el almacén de claves que se usa para firmar las versiones de depuración de una aplicación puede ser de Xamarin.Android se encuentra en la siguiente ubicación:

**C:\\usuarios\\[USERNAME]\\AppData\\Local\\Xamarin\\Mono para Android\\debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. Esta herramienta se encuentra normalmente en el directorio bin de Java:

**C:\\archivos de programa (x86)\\Java\\jdk [versión]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

De forma predeterminada, el almacén de claves que se usa para firmar las versiones de depuración de una aplicación puede ser de Xamarin.Android se encuentra en la siguiente ubicación:

**/Users/[username]/.local/Share/Xamarin/mono para Android/debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. Esta herramienta se encuentra normalmente en el directorio bin de Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


Ejecute keytool mediante el comando siguiente (con las rutas de acceso de archivo que se muestra arriba):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Ejemplo de Debug.keystore

Para la clave de depuración predeterminada (que se crea automáticamente para la depuración), use este comando:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>Claves de producción

Al implementar una aplicación en Google Play, debe ser [firmado con una clave privada](~/android/deploy-test/signing/index.md).
El `keytool` tendrá que puede ejecutar con los detalles de la clave privados y la huella digital de SHA-1 resultante utilizado para crear una clave de API de Google Maps de producción. No olvide actualizar el **AndroidManifest.xml** archivo con la clave de API de Google Maps correcta antes de la implementación.

### <a name="keytool-output"></a>Salida de keytool

Debería ver algo parecido a lo siguiente en la ventana de consola:

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

Va a utilizar la huella digital SHA-1 (enumeradas después **SHA1**) más adelante en esta guía.

## <a name="creating-an-api-project"></a>Crear un proyecto de API

Una vez recuperada la huella digital de SHA-1 del almacén de claves de firma, es necesario crear un nuevo proyecto en la consola de Google APIs (o agregar el servicio de API de Google Maps Android v2 a un proyecto existente).

1. En un explorador, navegue hasta la [API de consola de Google a los desarrolladores y panel de servicios](https://console.developers.google.com/apis/dashboard/) y haga clic en **seleccione un proyecto**. Haga clic en un nombre de proyecto o cree uno nuevo haciendo clic en **nuevo proyecto**:

   [![Botón de Google para desarrolladores consola Crear proyecto](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Si ha creado un proyecto nuevo, escriba el nombre del proyecto en el **nuevo proyecto** cuadro de diálogo que se muestra. Este cuadro de diálogo fabrica un identificador único del proyecto que se basa en el nombre del proyecto. A continuación, haga clic en el **crear** botón tal como se muestra en este ejemplo:

   [![Nuevo proyecto se denomina XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Tras un minuto aproximadamente, se crea el proyecto y pasará a la **panel** página del proyecto. Desde allí, haga clic en **habilite las API y los servicios de**:

   [![Haga clic en la API de Android de Google Maps en la sección de la biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Desde el **biblioteca API** página, haga clic en **SDK de mapas para Android**. En la siguiente página, haga clic en **habilitar** para activar el servicio para este proyecto:

   [![Haga clic en el botón Habilitar en la sección de panel](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

En este momento se ha creado el proyecto de API y API de Android de Google Maps v2 se ha agregado a él. Sin embargo, no puede usar esta API en el proyecto hasta que cree credenciales para ella. La siguiente sección explica cómo crear una clave de API y la lista blanca de una aplicación Xamarin.Android por lo que está autorizado para usar esta clave.

## <a name="obtaining-the-api-key"></a>Obtener la clave de API

Después de la **Google Developers Console** API proyecto ha sido creado, es necesario crear una clave de API de Android. Aplicaciones de Xamarin.Android deben tener una clave de API antes de que se les concede acceso a la API de asignación Android v2.

1. En el **SDK de mapas para Android** página que se muestra (después de hacer clic **habilitar** en el paso anterior), vaya a la **credenciales** ficha y haga clic en el **crear credenciales** botón:

   [![SDK de mapas de mensajes de credenciales Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Haga clic en **clave de API**:

   [![Agregue las credenciales en el cuadro de diálogo de proyecto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Después de hacer clic en este botón, se genera la clave de API. A continuación es necesario restringir esta clave para que solo la aplicación puede llamar a las API con esta clave. Haga clic en **clave restringir**:

   [![Haga clic en restringir clave en la página de credenciales](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Cambiar el **nombre** arrastrándolo desde **1 de la clave de API** por un nombre que le ayudará a recordar para qué se usa la clave (**XamarinMapsDemoKey** se utiliza en este ejemplo). A continuación, haga clic en el **aplicaciones Android** botón de opción:

   [![Seleccionar aplicaciones Android en la página de credenciales](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para agregar la huella digital SHA-1, haga clic en **+ agregar el nombre del paquete y la huella digital**:

   [![Al hacer clic en el nombre del paquete de complemento y la huella digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Escriba el nombre del paquete de la aplicación y la huella digital de certificado de SHA-1 (obtenidos mediante `keytool` tal y como se explicó anteriormente en esta guía). En el ejemplo siguiente, el nombre del paquete para `XamarinMapsDemo` está especificado, seguido por la huella digital de certificado de SHA-1 obtenida de **debug.keystore**:

   [![Nombre del paquete especificado es com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Tenga en cuenta que, en orden para su APK tener acceso a Google Maps, debe incluir las huellas digitales de SHA-1 y nombres para cada almacén de claves (debug y release) que se usa para firmar el APK del paquete. Por ejemplo, si usa un equipo para la depuración y otro equipo para generar el APK de versión, debe incluir la huella digital de certificado de SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital de certificado de SHA-1 desde el almacén de claves de la versión de el segundo equipo. Haga clic en **+ agregar el nombre del paquete y la huella digital** para agregar otro nombre de paquete y la huella digital, como se muestra en este ejemplo:

   [![Al agregar otra huella digital crea otro certificado de SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Haga clic en el botón **Guardar** para guardar los cambios. A continuación, se devuelven a la lista de las claves de API. Si tiene otras claves de API que haya creado anteriormente, también aparecerán aquí. En este ejemplo, se muestra solo una clave de API (creada en los pasos anteriores):

   [![XamarinMapsDemoKey se muestra en la lista de claves de API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conectar el proyecto a una cuenta facturable

Partir 11 de junio, de 2018, la clave de API no funcionará si el proyecto no está conectado a una cuenta facturable (incluso si el servicio está todavía disponible para las aplicaciones móviles).

1. Haga clic en el botón de menú de la hamburguesa y seleccione el **facturación** página:

   [![Seleccione la sección de facturación de menú de tres rayas](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Vincular el proyecto con una cuenta de facturación, haga clic en **vincular una cuenta de facturación** seguido **crear cuenta de facturación** en el menú emergente mostrada (si no tiene una cuenta, se le guiará para crear uno nuevo):

   [![Proyecto de vínculo a la cuenta de facturación](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Agregar la clave a un proyecto

Por último, agregue esta clave de API para el **AndroidManifest.XML** archivo de la aplicación Xamarin.Android. En el ejemplo siguiente, `YOUR_API_KEY` reemplazará con la clave de API generada en los pasos anteriores:

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
