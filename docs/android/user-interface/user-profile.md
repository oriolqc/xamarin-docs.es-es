---
title: Perfil de usuario
ms.topic: article
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2017
ms.openlocfilehash: cf8230c5832104fd17b14532f1d32822a1fc0097
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="user-profile"></a>Perfil de usuario

Android admitió enumerar contactos con la `ContactsContract` proveedor desde el 5 de nivel de API. Por ejemplo, a la lista de contactos es tan simple como utilizar el `ContactContracts.Contacts` de la clase, como se muestra en el código siguiente:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
           
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id,
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);
           
if (cursor.MoveToFirst ()) {
    do {
        Console.WriteLine ("Contact ID: {0}, Contact Name: {1}",
            cursor.GetString (cursor.GetColumnIndex (projection [0])),
            cursor.GetString (cursor.GetColumnIndex (projection [1])));
                   
    } while (cursor.MoveToNext());
}
```

Android 4 (API nivel 14), un nuevo `ContactsContact.Profile` clase está disponible a través del proveedor de ContactsContract. El `ContactsContact.Profile` proporciona acceso a un perfil de personal para el propietario de un dispositivo, lo que incluye datos de contacto, como del propietario del dispositivo nombre y número de teléfono.


## <a name="required-permissions"></a>Permisos necesarios

Para leer y escribir datos de contacto, las aplicaciones deben solicitar la `Read_Contacts` y `Write_Contacts` permisos, respectivamente. Además, para leer y editar el perfil de usuario, las aplicaciones deben solicitar la `Read_Profile` y `Write_Profile` permisos.


## <a name="updating-profile-data"></a>Actualización de datos de perfil

Una vez que estos permisos se han establecido, una aplicación puede usar técnicas de Android normales para interactuar con los datos del perfil de usuario. Por ejemplo, para actualizar el nombre para mostrar del perfil se llamaría a `ContentResolver.Update` con un `Uri` recuperados a través de la `ContactsContract.Profile.ContentRawContactsUri` propiedad, tal y como se muestra a continuación:

```csharp
var values = new ContentValues ();
          
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName,
    "John Doe");
           
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri,
    values, null, null);
```


## <a name="reading-profile-data"></a>Leer datos de perfil

Emitir una consulta a la `ContactsContact.Profile.ContentUri` lecturas realizar copias de los datos de perfil. Por ejemplo, el siguiente código leerá el nombre para mostrar del perfil de usuario:

```csharp
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);

if (cursor.MoveToFirst ()) {
    Console.WriteLine(
        cursor.GetString (cursor.GetColumnIndex (projection [0])));
}
```


## <a name="navigating-to-the-people-app"></a>Vaya a la aplicación de personas

Por último, para navegar hasta el perfil de usuario en la nueva aplicación de personas que viene con Android 4, basta con crear un intento con un `ActionView` acción y un `ContactsContract.Profile.ContentUri`y lo pasa a la `StartActivity` método similar al siguiente:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

Cuando se ejecuta el código anterior, la aplicación de personas se cargará en el perfil de usuario, como se muestra en la siguiente captura de pantalla:

[![Aplicación de captura de pantalla de personas mostrar el perfil de usuario de Juan García](user-profile-images/15-people-app.png)](user-profile-images/15-people-app.png#lightbox)

Trabajar con el perfil de usuario ahora es similar a la interacción con otros datos en Android y ofrece un nivel adicional de personalización del dispositivo.



## <a name="related-links"></a>Vínculos relacionados

- [ContactsProviderDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Introducción a helado Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
