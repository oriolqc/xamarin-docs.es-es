---
title: Permisos de Xamarin.Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: b8a8005c69c8aaee5d92bdabb3429bd52fc76b4a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770240"
---
# <a name="permissions-in-xamarinandroid"></a>Permisos de Xamarin.Android


## <a name="overview"></a>Información general

Ejecutan aplicaciones de Android en su propio espacio aislado y para la seguridad de motivos no tienen acceso a ciertos recursos del sistema o el hardware en el dispositivo. El usuario debe conceder explícitamente permiso a la aplicación antes de que pueden usar estos recursos. Por ejemplo, una aplicación no puede tener acceso el GPS en un dispositivo sin permiso explícito del usuario. Android producirá una `Java.Lang.SecurityException` si una aplicación intenta tener acceso a un recurso protegido sin permiso.

Los permisos se declaran en el **AndroidManifest.xml** por el desarrollador de aplicaciones cuando se desarrolló la aplicación. El sistema Android tiene dos diferentes flujos de trabajo para obtener el consentimiento del usuario para esos permisos:
 
* Para las aplicaciones que el destino Android 5.1 (API nivel 22) o inferior, se produce la solicitud de permiso cuando se instaló la aplicación. Si el usuario no haya concedido los permisos, la aplicación no se instalará. Una vez que la aplicación está instalada, no hay ninguna manera para revocar los permisos excepto al desinstalar la aplicación.
* A partir de Android 6.0 (nivel de API 23), los usuarios se han proporcionado más control sobre los permisos; puede conceder o revocar permisos, siempre y cuando la aplicación está instalada en el dispositivo. Esta captura de pantalla muestra la configuración de permisos para la aplicación de contactos de Google. Esta pestaña muestra los distintos permisos y permite al usuario habilitar o deshabilitar permisos:

![Pantalla de permisos de ejemplo](permissions-images/01-permissions-check.png) 

Aplicaciones de Android deben comprobar en tiempo de ejecución para ver si tienen permiso para tener acceso a un recurso protegido. Si la aplicación no tiene permiso, debe realizar las solicitudes mediante las nuevas API proporcionadas por el SDK de Android para el usuario para conceder los permisos. Los permisos se dividen en dos categorías:

* **Permisos de normal** &ndash; son los permisos que suponen poco riesgo de seguridad para la seguridad o privacidad del usuario. Android 6.0 automáticamente concederá permisos normales en el momento de la instalación. Consulte la documentación de Android para un [lista completa de permisos normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Permisos peligrosos** &ndash; en comparación con los permisos normales, permisos peligrosos son los que proteger la seguridad o la privacidad del usuario. Estas deben ser concedido explícitamente por el usuario. Enviar o recibir un mensaje SMS es un ejemplo de una acción que necesite un permiso peligroso.

> [!IMPORTANT]
> La categoría a la que pertenece un permiso puede cambiar con el tiempo.  Es posible que un permiso que se ha clasificado como puede ser un permiso "normal" en el futuro elevados niveles de API para un permiso peligroso.

Permisos peligrosos se Sub-dividen aún más [ _grupos de permisos_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Un grupo de permisos contendrá los permisos que se relacionan lógicamente. Cuando el usuario concede el permiso a un miembro de un grupo de permisos, Android concede automáticamente permiso a todos los miembros de ese grupo. Por ejemplo, el [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) grupo de permisos contiene tanto el `WRITE_EXTERNAL_STORAGE` y `READ_EXTERNAL_STORAGE` permisos. Si el usuario concede el permiso para `READ_EXTERNAL_STORAGE`, la `WRITE_EXTERNAL_STORAGE` permiso se concede automáticamente al mismo tiempo.

Antes de solicitar uno o varios permisos, es una práctica recomendada para proporcionar un motivo en cuanto a por qué la aplicación requiere el permiso antes de solicitar el permiso. Una vez que el usuario entienda el motivo, la aplicación puede solicitar permiso del usuario. Si comprende la lógica, el usuario puede tomar una decisión informada si desean conceder el permiso y entender las repercusiones si no lo hace. 

El flujo de trabajo completo de comprobación y la solicitud de permisos se conoce como un _permisos en tiempo de ejecución_ Compruebe y se puede resumir en el diagrama siguiente: 

[![Diagrama de flujo de comprobación de permiso de tiempo de ejecución](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

La biblioteca de compatibilidad con Android backports algunas de las nuevas API para los permisos a versiones anteriores de Android. Estas API utilizados comprobará automáticamente la versión de Android en el dispositivo por lo que no es necesario realizar una comprobación de nivel de API cada vez.  

Este documento describe cómo agregar permisos a una aplicación Xamarin.Android y cómo las aplicaciones destinadas a Android 6.0 (nivel de API 23) o posterior, debe realizar una comprobación de permiso de tiempo de ejecución.


> [!NOTE]
> Es posible que los permisos para el hardware pueden afectar a cómo la aplicación se filtra por Google Play. Por ejemplo, si la aplicación requiere el permiso de la cámara, a continuación, Google Play no se mostrará la aplicación en Google Play Store en un dispositivo que no tiene una cámara instalada.


<a name="requirements" />

## <a name="requirements"></a>Requisitos

Se recomienda encarecidamente que los proyectos de Xamarin.Android incluyen el [Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) paquete NuGet. Este permiso de paquete will atrás API específicas para las versiones anteriores de Android, proporcionar comunes de una interfaz constantemente sin necesidad de comprobar la versión de Android que esté ejecutando la aplicación.


## <a name="requesting-system-permissions"></a>Solicitar permisos de sistema

El primer paso para trabajar con permisos Android es declarar que los permisos en el Android el archivo de manifiesto. Esto debe hacerse con independencia del nivel de API que la aplicación tiene como destino.

Aplicaciones que destino Android 6.0 o versiones posteriores no pueden suponer que porque el usuario permiso en algún momento en el pasado, que el permiso se aplicará la próxima vez. Una aplicación cuyo destino es Android 6.0 siempre debe realizar una comprobación de permiso en tiempo de ejecución. Aplicaciones destinadas a Android 5.1 o inferior no es necesario realizar una comprobación de permiso de tiempo de ejecución.

> [!NOTE]
> Las aplicaciones solo deben solicitar los permisos que necesitan.


### <a name="declaring-permissions-in-the-manifest"></a>Declarar los permisos en el manifiesto

Los permisos se agregan a la **AndroidManifest.xml** con el `uses-permission` elemento. Por ejemplo, si una aplicación consiste en ubicar la posición del dispositivo, requiere bien y permisos de la ubicación del curso. Los dos elementos siguientes se agregan al manifiesto: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Es posible declarar los permisos mediante la compatibilidad con herramientas integrada en Visual Studio:

1. Haga doble clic en **propiedades** en el **el Explorador de soluciones** y seleccione la **manifiesto Android** ficha en la ventana Propiedades:

    [![Permisos necesarios en la ficha manifiesto de Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Si la aplicación no tiene ya un AndroidManifest.xml, haga clic en **AndroidManifest.xml No encontrado. Haga clic para agregar uno** tal y como se muestra a continuación:

    [![No hay ningún mensaje AndroidManifest.xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Seleccione los permisos que necesita la aplicación desde el **los permisos necesarios** lista y guardar:

    [![Permisos de cámara de ejemplo seleccionados](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Es posible declarar los permisos mediante la compatibilidad con herramientas integrada en Visual Studio para Mac:

1. Haga doble clic en el proyecto en el **solución Pad** y seleccione **Opciones > compilar > aplicación Android**:

    [![Sección de permisos necesarios que se muestra](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Haga clic en el **agregar manifiesto Android** botón si el proyecto no tiene ya una **AndroidManifest.xml**:

    [![Manifiesto de Android del proyecto es que faltan](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Seleccione los permisos que necesita la aplicación desde el **los permisos necesarios** lista y haga clic en **Aceptar**:

    [![Permisos de cámara de ejemplo seleccionados](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android agregará automáticamente algunos permisos en tiempo de compilación para las compilaciones de depuración. Esto hará que la depuración de la aplicación sea más fácil. En concreto, dos permisos importantes son `INTERNET` y `READ_EXTERNAL_STORAGE`. Estos permisos automáticamente set no aparecerán esté habilitado en el **los permisos necesarios** lista. Versiones de lanzamiento, sin embargo, utilice solo los permisos que se establecen explícitamente en el **los permisos necesarios** lista. 

Para las aplicaciones destinadas a Android 5.1 (API nivel 22) o inferior, no hay nada más que debe hacerse. Aplicaciones que se ejecutarán en Android 6.0 (23 de nivel de API 23) o posterior deben continuar con la siguiente sección sobre cómo realizar comprobaciones de permisos de tiempo de ejecución. 


### <a name="runtime-permission-checks-in-android-60"></a>Permiso en tiempo de ejecución comprueba en Android 6.0

El `ContextCompat.CheckSelfPermission` (método) (disponible en la biblioteca de compatibilidad de Android) se usa para comprobar si se ha concedido un permiso concreto. Este método devolverá un [ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/) enum que tiene uno de dos valores:

* **`Permission.Granted`** &ndash; Se ha concedido el permiso especificado.
* **`Permission.Denied`** &ndash; No se ha concedido el permiso especificado.

Este fragmento de código es un ejemplo de cómo comprobar el permiso de la cámara en una actividad: 

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

Es un procedimiento recomendado para informar al usuario en cuanto a por qué un permiso es necesario para una aplicación por lo que puede llevar a cabo una decisión informada para conceder el permiso. Un ejemplo de esto sería una aplicación que toma fotografías y etiquetas geográfica ellos. Es evidente para el usuario que es necesario el permiso de la cámara, pero que no esté claro por qué la aplicación también necesita la ubicación del dispositivo. El análisis razonado debe mostrar un mensaje para ayudar al usuario a entender por qué el permiso de ubicación es deseable y que se requiere el permiso de la cámara.

El `ActivityCompat.ShouldShowRequestPermissionRational` método se utiliza para determinar si la lógica que se debe mostrar al usuario. Este método devolverá `true` si debe mostrarse el motivo de un determinado permiso. Esta captura de pantalla muestra un ejemplo de un Snackbar mostrado por una aplicación que explica por qué la aplicación necesita conocer la ubicación del dispositivo:

![Motivo de ubicación](permissions-images/07-rationale-snackbar.png) 

Si el usuario concede el permiso, el `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` se debería llamar al método. Este método requiere los siguientes parámetros:

* **actividad** &ndash; se trata de la actividad que solicita los permisos y se informará Android de los resultados.
* **permisos** &ndash; una lista de los permisos que se ha solicitado.
* **requestCode** &ndash; un valor entero que se utiliza para que coincida con los resultados de la solicitud de permiso para un `RequestPermissions` llamar. Este valor debería ser mayor que cero.

Este fragmento de código es un ejemplo de los dos métodos que se han analizado. En primer lugar, se realiza una comprobación para determinar si debe mostrarse el razonamiento de permiso. Si el motivo es que se mostrará, se muestra un Snackbar con la lógica. Si el usuario hace clic en **Aceptar** en Snackbar, a continuación, la aplicación solicitará los permisos. Si el usuario no acepta el motivo, la aplicación no debe continuar para solicitar permisos. Si no se muestra el motivo, la actividad solicitará el permiso siguiente:

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

`RequestPermission` puede llamarse incluso si el usuario ya ha concedido permiso. Las llamadas subsiguientes no son necesarias, pero ofrezcan al usuario la oportunidad de confirmar (o revocar) el permiso. Cuando `RequestPermission` es llama, control se entregue en el sistema operativo, que se mostrará una interfaz de usuario para aceptar los permisos:  

![Cuadro de diálogo de permiso](permissions-images/08-location-permission-dialog.png)

Cuando el usuario haya finalizado, Android devolverá los resultados a la actividad a través de un método de devolución de llamada, `OnRequestPermissionResult`. Este método es una parte de la interfaz `ActivityCompat.IOnRequestPermissionsResultCallback` que debe ser implementada por la actividad. Esta interfaz tiene un único método, `OnRequestPermissionsResult`, que se invocará Android para informar a la actividad de las opciones del usuario. Si el usuario dispone del permiso, la aplicación puede seguir adelante y usar el recurso protegido. Un ejemplo de cómo implementar `OnRequestPermissionResult` se muestra a continuación: 

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
            Snackbar.Make(layout, Resource.String.permision_available_camera, Snackbar.LengthShort).Show();            
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

Esta guía describe cómo agregar y comprobar los permisos en un dispositivo Android. Las diferencias en cómo funcionan los permisos entre las aplicaciones de Android antiguas (API nivel < 23) y nuevas aplicaciones de Android (API nivel 22 >). Describe cómo realizar comprobaciones de permisos de tiempo de ejecución en Android 6.0.


## <a name="related-links"></a>Vínculos relacionados

- [Lista de permisos normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Aplicación de ejemplo de permisos en tiempo de ejecución](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Permisos de control de Xamarin.Android](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest)
