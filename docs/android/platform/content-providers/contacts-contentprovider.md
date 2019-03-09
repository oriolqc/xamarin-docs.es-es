---
title: Uso de ContentProvider de contactos
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: 48bb334e7e400d57e7eddc23b0b4ff183a7eba9b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669419"
---
# <a name="using-the-contacts-contentprovider"></a>Uso de ContentProvider de contactos

Código que usa acceso a los datos expuestos por un `ContentProvider` no requiere una referencia a la `ContentProvider` clase en absoluto. En su lugar, se usa un Uri para crear un cursor a través de los datos expuestos por el `ContentProvider`. Android usa el Uri para buscar el sistema para la aplicación que expone un `ContentProvider` con ese identificador. El Uri es una cadena, normalmente en un formato DNS inversa como `com.android.contacts/data`.

En lugar de realizar los desarrolladores recordar esta cadena, el Android *contactos* proveedor expone sus metadatos en el `android.provider.ContactsContract` clase. Esta clase se utiliza para determinar el identificador Uri de la `ContentProvider` , así como los nombres de las tablas y columnas que se pueden consultar.

Algunos tipos de datos también requieren el permiso especial para tener acceso a. La lista de contactos integrada requiere la `android.permission.READ_CONTACTS` permiso en el **AndroidManifest.xml** archivo.

Hay tres formas de crear un cursor desde el Uri:

1. **ManagedQuery()** &ndash; el enfoque preferido en Android 2.3 (API nivel 10) y versiones anteriores, un `ManagedQuery` devuelve un cursor y administra también automáticamente la actualización de los datos y cerrar el cursor. Este método está en desuso en Android 3.0 (API nivel 11).

1. **ContentResolver.Query()** &ndash; devuelve un cursor no administrado, lo que significa que debe poder actualizarlo y cierre explícitamente en el código.

1. **CursorLoader(). LoadInBackground()** &ndash; introducido en 3.0 Android (API nivel 11), `CursorLoader` ahora es el modo preferido para consumir un `ContentProvider` . `CursorLoader` las consultas un `ContentResolver` en un subproceso en segundo plano, por lo que no se bloquea la interfaz de usuario.
   Esta clase se puede acceder en versiones anteriores de Android con la biblioteca de compatibilidad de v4.


Cada uno de estos métodos tiene el mismo conjunto básico de entradas:

-  **URI** &ndash; el nombre completo de la `ContentProvider` .
-  **Proyección** &ndash; especificación de las columnas que seleccione para el cursor.
-  **Selección** &ndash; Similar a una instancia de SQL `WHERE` cláusula.
-  **SelectionArgs** &ndash; parámetros que se sustituirá en la selección.
-  **SortOrder** &ndash; columnas para ordenar por.



## <a name="creating-inputs-for-a-query"></a>Crear entradas para una consulta

El `ContactsProvider` código de ejemplo realiza una consulta muy sencilla en el proveedor de contactos integrado de Android. No es necesario saber el Uri o una columna nombres reales - toda la información necesaria para consultar los contactos `ContentProvider` está disponible como constantes expuestas por el `ContactsContract` clase.

Independientemente de qué método se usa para recuperar el cursor, se utilizan estos mismos objetos como parámetros, tal como se muestra en el *ContactsProvider/ContactsAdapter.cs* archivo:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

En este ejemplo, el `selection`, `selectionArgs` y `sortOrder` se omitirá si se establecen en `null`.



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Creación de un Cursor desde un Uri del proveedor de contenido

Una vez que se han creado los objetos de parámetro, se puede usar en una de las tres formas siguientes:



### <a name="using-a-managed-query"></a>Mediante una consulta administrada

Las aplicaciones destinadas a Android 2.3 (API nivel 10) o anterior, debe utilizar este método:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Android administrará este cursor no es necesario que lo cierre.



### <a name="using-contentresolver"></a>Uso de ContentResolver

Acceso a `ContentResolver` directamente para obtener un cursor con un `ContentProvider` se puede realizar del siguiente modo:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Este cursor no está administrado, por lo que se debe cerrar cuando ya no es necesario.
Asegúrese de que el código cierra un cursor que está abierto, en caso contrario, se producirá un error.

```csharp
cursor.Close();
```

Como alternativa, puede llamar a `StartManagingCursor()` y `StopManagingCursor()` ' Administrar ' el cursor. Cursores administrados se desactiva y consultados de nuevo cuando las actividades se detiene y se reinicia automáticamente.



### <a name="using-cursorloader"></a>Uso de CursorLoader

Compilan aplicaciones para Android 3.0 (API nivel 11) o posterior debe utilizar este método:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

El `CursorLoader` garantiza que todas las operaciones de cursor se realizan en un subproceso en segundo plano y pueden inteligentemente volver a usar un cursor existente entre las instancias de actividad cuando una actividad se reinicia (por ejemplo, debido a un cambio de configuración) en su lugar que volver a cargar los datos de nuevo.

También pueden usar las versiones anteriores de Android el `CursorLoader` clase utilizando el [v4 compatibilidad con bibliotecas](https://developer.android.com/tools/support-library/index.html).



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Mostrar los datos de Cursor con un adaptador personalizado

Para mostrar la imagen de contacto vamos a usar un adaptador personalizado, por lo que podemos resolver manualmente el `PhotoId` referencia a una ruta de acceso del archivo de imagen.

Para mostrar datos con un adaptador personalizado, el ejemplo se usa un `CursorLoader` para recuperar todos los datos de contacto en una colección local en el **FillContacts** método desde **ContactsProvider/ContactsAdapter.cs**:

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

A continuación, implemente los métodos de BaseAdapter mediante el `contactList` colección. El adaptador está implementado solo como se haría con cualquier otra colección &ndash; no hay ningún control especial aquí porque los datos se originan un `ContentProvider`:

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

Se muestra la imagen (si existe) con el Uri para el archivo de imagen en el dispositivo. La aplicación tendrá este aspecto:

[![Captura de pantalla de la aplicación Mostrar contactos en un ListView; se muestra una imagen a la izquierda de una entrada](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Con un modelo de código similar, la aplicación puede acceder a una amplia variedad de datos del sistema como el usuario fotos, vídeos y música.
Algunos tipos de datos requieren permisos especiales que se soliciten en el proyecto **AndroidManifest.xml**.



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Mostrar los datos de Cursor con un SimpleCursorAdapter

También podría mostrarse el cursor con un `SimpleCursorAdapter` (aunque se mostrará solo el nombre, no la foto). Este código muestra cómo usar un `ContentProvider` con `SimpleCursorAdapter` (este código no aparece en el ejemplo):

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

Hacer referencia a la [ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre la implementación de `SimpleCursorAdapter`.


## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
