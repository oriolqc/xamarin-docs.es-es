---
title: Permisos de Xamarin.Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 204dd903586164691d068a956e741c406df10b36
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027161"
---
# <a name="permissions-in-xamarinandroid"></a>Permisos de Xamarin.Android


## <a name="overview"></a>Información general

Ejecutan aplicaciones de Android en su propio espacio aislado y para la seguridad de las razones no tiene acceso a ciertos recursos del sistema o el hardware en el dispositivo. El usuario debe conceder explícitamente permiso a la aplicación antes de que pueden usar estos recursos. Por ejemplo, una aplicación no puede tener acceso el GPS en un dispositivo sin permiso explícito del usuario. Android producirá una `Java.Lang.SecurityException` si una aplicación intenta obtener acceso a un recurso protegido sin permiso.

Los permisos se declaran en el **AndroidManifest.xml** por el desarrollador de aplicaciones cuando se desarrolla la aplicación. Android tiene dos flujos de trabajo diferentes para obtener el consentimiento del usuario para esos permisos:
 
* Para las aplicaciones que como destino Android 5.1 (API nivel 22) o inferior, se produjo la solicitud de permiso cuando se instaló la aplicación. Si el usuario no haya concedido los permisos, no se instalaría la aplicación. Una vez que la aplicación está instalada, no hay ninguna manera para revocar los permisos excepto mediante la desinstalación de la aplicación.
* A partir de Android 6.0 (nivel de API 23), los usuarios se les más control sobre los permisos; puede conceder o revocar permisos mientras la aplicación está instalada en el dispositivo. Esta captura de pantalla muestra la configuración de permisos para la aplicación de contactos de Google. Enumera los distintos permisos y permite al usuario habilitar o deshabilitar permisos:

![Pantalla de ejemplo de permisos](permissions-images/01-permissions-check.png) 

Aplicaciones de Android deben comprobar en tiempo de ejecución para ver si tienen permiso para acceder a un recurso protegido. Si la aplicación no tiene permiso, debe realizar las solicitudes mediante las nuevas API proporcionadas por el SDK de Android para el usuario para conceder los permisos. Los permisos se dividen en dos categorías:

* **Permisos normal** &ndash; estos son los permisos que suponen pequeño riesgo de seguridad para la seguridad o privacidad del usuario. Android 6.0 automáticamente concederá permisos normales en el momento de la instalación. Consulte la documentación de Android para un [lista completa de permisos normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Permisos peligrosos** &ndash; en comparación con permisos normales, permisos peligrosos son los que proteger la seguridad o la privacidad del usuario. Deben tener concedido explícitamente por el usuario. Enviar o recibir un mensaje SMS es un ejemplo de una acción que necesite un permiso peligroso.

> [!IMPORTANT]
> Puede cambiar la categoría a la que pertenece un permiso con el tiempo.  Es posible que un permiso que se ha clasificado como puede ser un permiso "normal" en el futuro elevados niveles de API para un permiso peligroso.

Permisos peligrosos se Sub dividen en [ _grupos de permisos_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Un grupo de permisos contendrá los permisos que se relacionan lógicamente. Cuando el usuario concede el permiso a un miembro de un grupo de permisos, Android concede automáticamente permiso a todos los miembros de ese grupo. Por ejemplo, el [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) grupo permiso contiene tanto el `WRITE_EXTERNAL_STORAGE` y `READ_EXTERNAL_STORAGE` permisos. Si el usuario concede el permiso para `READ_EXTERNAL_STORAGE`, el `WRITE_EXTERNAL_STORAGE` permiso se concede automáticamente al mismo tiempo.

Antes de solicitar uno o varios permisos, es una práctica recomendada para proporcionar una justificación sobre por qué la aplicación requiere el permiso antes de solicitar el permiso. Una vez que el usuario comprenda el razonamiento, la aplicación puede solicitar permiso al usuario. Al entender el razonamiento, el usuario puede tomar una decisión informada si desean conceder el permiso y comprender las repercusiones si no lo hace. 

Flujo de trabajo completo de comprobación y la solicitud de permisos se conoce como un _permisos en tiempo de ejecución_ Compruebe y se pueden resumir en el diagrama siguiente: 

[![Diagrama de flujo de comprobación de permiso de tiempo de ejecución](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

La biblioteca de compatibilidad de Android de backports algunas de las nuevas API para los permisos para las versiones anteriores de Android. Por lo que no es necesario realizar una comprobación de nivel de API cada vez, estas API usado comprobará automáticamente la versión de Android en el dispositivo.  

Este documento describe cómo agregar permisos a una aplicación de Xamarin.Android y cómo las aplicaciones destinadas a Android 6.0 (nivel de API 23) o versiones posteriores, debe realizar una comprobación de permiso de tiempo de ejecución.


> [!NOTE]
> Es posible que los permisos de hardware pueden afectar a cómo se filtra la aplicación Google Play. Por ejemplo, si la aplicación requiere permiso para la cámara, a continuación, Google Play no mostrará la aplicación en el Store de Google Play en un dispositivo que no tiene una cámara instalada.


<a name="requirements" />

## <a name="requirements"></a>Requisitos

Se recomienda encarecidamente que los proyectos de Xamarin.Android incluyan el [Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) paquete NuGet. Este permiso de paquete will restituir API específicas para las versiones anteriores de Android, que proporciona un común de interfaz constantemente sin necesidad de comprobar la versión de Android que se está ejecutando la aplicación.


## <a name="requesting-system-permissions"></a>La solicitud de permisos del sistema

El primer paso para trabajar con los permisos de Android es declarar que los permisos de Android el archivo de manifiesto. Esto debe realizarse independientemente del nivel de API que la aplicación tiene como destino.

Aplicaciones que tener como destino Android 6.0 o versiones posteriores no se pueden suponer que porque el usuario permiso en algún momento en el pasado, que el permiso será válido la próxima vez. Una aplicación que tenga como destino Android 6.0 siempre debe realizar una comprobación de permisos en tiempo de ejecución. Las aplicaciones destinadas a Android 5.1 o inferior no es necesario realizar una comprobación de permiso de tiempo de ejecución.

> [!NOTE]
> Las aplicaciones solo deben solicitar los permisos que necesitan.


### <a name="declaring-permissions-in-the-manifest"></a>Declarar los permisos en el manifiesto

Los permisos se agregan a la **AndroidManifest.xml** con el `uses-permission` elemento. Por ejemplo, si es una aplicación buscar la posición del dispositivo, requiere bien y los permisos de ubicación del curso. Los dos elementos siguientes se agregan al manifiesto: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Es posible declarar los permisos mediante la compatibilidad con herramientas integrada en Visual Studio:

1. Haga doble clic en **propiedades** en el **el Explorador de soluciones** y seleccione el **manifiesto de Android** pestaña en la ventana Propiedades:

    [![Permisos necesarios en la pestaña manifiesto de Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Si la aplicación no dispone de un archivo AndroidManifest.xml, haga clic en **AndroidManifest.xml No encontrado. Haga clic para agregar uno** tal como se muestra a continuación:

    [![Ningún mensaje de AndroidManifest.xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Seleccione los permisos que necesita la aplicación desde el **permisos necesarios** lista y guarde:

    [![Permisos de la cámara de ejemplo seleccionados](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Es posible declarar los permisos mediante la compatibilidad con herramientas integrada en Visual Studio para Mac:

1. Haga doble clic en el proyecto en el **panel de solución** y seleccione **Opciones > compilar > aplicación de Android**:

    [![Sección de los permisos necesario que se muestra](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Haga clic en el **agregar manifiesto de Android** botón si el proyecto no tiene todavía un **AndroidManifest.xml**:

    [![Falta el manifiesto de Android del proyecto](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Seleccione los permisos que necesita la aplicación desde el **permisos necesarios** lista y haga clic en **Aceptar**:

    [![Permisos de la cámara de ejemplo seleccionados](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android agregará automáticamente algunos permisos en tiempo de compilación para las compilaciones de depuración. Esto hará que la depuración de la aplicación sea más fácil. En concreto, dos permisos importantes son `INTERNET` y `READ_EXTERNAL_STORAGE`. Estos permisos automáticamente set no se mostrará a habilitarse en el **permisos necesarios** lista. Versiones de lanzamiento, sin embargo, use solo los permisos que se establecen explícitamente en el **permisos necesarios** lista. 

Para las aplicaciones que tienen como destino Android 5.1 (API nivel 22) o inferior, no hay nada más que debe hacerse. Las aplicaciones que se ejecutarán en Android 6.0 (23 de nivel de API 23) o posterior deben continuar con la siguiente sección para realizar comprobaciones de permisos de tiempo de ejecución. 


### <a name="runtime-permission-checks-in-android-60"></a>Comprobaciones de permiso en tiempo de ejecución en Android 6.0

El `ContextCompat.CheckSelfPermission` (método) (disponible con la biblioteca de compatibilidad con Android) se usa para comprobar si se ha concedido un permiso específico. Este método devolverá un [ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/) enum que tiene uno de dos valores:

* **`Permission.Granted`** &ndash; Se ha concedido el permiso especificado.
* **`Permission.Denied`** &ndash; No se ha concedido el permiso especificado.

Este fragmento de código es un ejemplo de cómo se buscan el permiso de la cámara en una actividad: 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

Es una práctica recomendada para informar al usuario sobre por qué un permiso es necesario para una aplicación para que se puede realizar una decisión informada para conceder el permiso. Un ejemplo de esto sería una aplicación que toma fotografías y geográfica etiquetas ellos. Está claro al usuario que es necesario el permiso de la cámara, pero podría no ser claro por qué la aplicación también necesita la ubicación del dispositivo. El razonamiento debe mostrar un mensaje para ayudar al usuario a entender por qué el permiso de ubicación es deseable y que se requiere el permiso de la cámara.

El `ActivityCompat.ShouldShowRequestPermissionRationale` método se usa para determinar si la lógica que se debe mostrar al usuario. Este método devolverá `true` si debe mostrarse el motivo de un permiso determinado. Esta captura de pantalla muestra un ejemplo de un Snackbar mostrado por una aplicación que explica por qué la aplicación necesita conocer la ubicación del dispositivo:

![Justificación de la ubicación](permissions-images/07-rationale-snackbar.png) 

Si el usuario concede el permiso, el `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` método debe llamarse. Este método requiere los siguientes parámetros:

* **actividad** &ndash; se trata de la actividad que solicita los permisos y se informará de Android de los resultados.
* **permisos** &ndash; una lista de los permisos que se solicitan.
* **requestCode** &ndash; un valor entero que se utiliza para comparar los resultados de la solicitud de permiso para un `RequestPermissions` llamar. Este valor debería ser mayor que cero.

Este fragmento de código es un ejemplo de los dos métodos que se han analizado. En primer lugar, se realiza una comprobación para determinar si debe mostrarse el razonamiento de permiso. Si el motivo es que se mostrará, se muestra un Snackbar con el razonamiento. Si el usuario hace clic **Aceptar** en el Snackbar, a continuación, la aplicación solicitará los permisos. Si el usuario no acepta el razonamiento, la aplicación no debe continuar para solicitar permisos. Si no se muestra el motivo, la actividad solicitará el permiso siguiente:

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission` se puede llamar incluso si el usuario ya haya concedido permiso. Las llamadas subsiguientes no son necesarias, pero proporcionan al usuario la oportunidad de confirmar (o revocar) el permiso. Cuando `RequestPermission` es llamado, se entrega el control desactivado en el sistema operativo, que se mostrará una interfaz de usuario para aceptar los permisos:  

![Cuadro de diálogo de permiso](permissions-images/08-location-permission-dialog.png)

Después de que el usuario ha terminado, Android devolverá los resultados a la actividad mediante el método de devolución de llamada, `OnRequestPermissionResult`. Este método es una parte de la interfaz `ActivityCompat.IOnRequestPermissionsResultCallback` que debe implementarse mediante la actividad. Esta interfaz tiene un único método, `OnRequestPermissionsResult`, que se invocará Android para informar a la actividad de las opciones del usuario. Si el usuario dispone del permiso, la aplicación puede avanzar y usar el recurso protegido. Un ejemplo de cómo implementar `OnRequestPermissionResult` se muestra a continuación: 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permission_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  


## <a name="summary"></a>Resumen

Esta guía describe cómo agregar y comprobar los permisos en un dispositivo Android. Las diferencias en cómo funcionan los permisos entre las aplicaciones antiguas de Android (API nivel < 23) y nuevas aplicaciones de Android (API nivel 22 >). Describe cómo realizar las comprobaciones de permisos de tiempo de ejecución en Android 6.0.


## <a name="related-links"></a>Vínculos relacionados

- [Lista de permisos normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Aplicación de ejemplo de permisos en tiempo de ejecución](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Permisos de control de Xamarin.Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
