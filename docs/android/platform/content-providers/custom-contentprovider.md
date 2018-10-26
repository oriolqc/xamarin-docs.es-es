---
title: Creación de un ContentProvider personalizado
description: La sección anterior muestra cómo consumir datos desde una implementación de ContentProvider integrada. En esta sección se explica cómo crear un ContentProvider personalizado y, a continuación, utilizar sus datos.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: da8aacac1f282fefb6b8d0e84cae168cf3a7148b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108020"
---
# <a name="creating-a-custom-contentprovider"></a>Creación de un ContentProvider personalizado

_La sección anterior muestra cómo consumir datos desde una implementación de ContentProvider integrada. En esta sección se explica cómo crear un ContentProvider personalizado y, a continuación, utilizar sus datos._

## <a name="about-contentproviders"></a>Acerca de ContentProviders

Una clase de proveedor de contenido debe heredar de `ContentProvider`. Debe constar de un almacén de datos interno que se usa para responder a las consultas y debe exponer los URI y los tipos MIME como constantes para ayudar a usar código que las solicitudes válidas para los datos.

### <a name="uri-authority"></a>URI (autoridad)

`ContentProviders` se tiene acceso en Android mediante un Uri. Una aplicación que expone un `ContentProvider` establece los identificadores URI que responderá en su **AndroidManifest.xml** archivo. Cuando se instala la aplicación, estos URI se registran para que otras aplicaciones pueden tener acceso a ellos.

En Mono para Android, la clase de proveedor de contenido debe tener un `[ContentProvider]` atributo para especificar el Uri (o URI) que deben agregarse a **AndroidManifest.xml**.


### <a name="mime-type"></a>Tipo MIME

El formato típico de tipos MIME consta de dos partes. Android `ContentProviders` normalmente utilizan estas dos cadenas para la primera parte del tipo MIME:

1. `vnd.android.cursor.item` &ndash; para representar una sola fila, utilice el `ContentResolver.CursorItemBaseType` constante en el código.

1. `vnd.android.cursor.dir` &ndash; para varias filas, use el `ContentResolver.CursorDirBaseType` constante en el código.

La segunda parte del tipo MIME es específica de la aplicación y debe usar un estándar de DNS inversa con un `vnd.` prefijo. El código de ejemplo usa `vnd.com.xamarin.sample.Vegetables`.


### <a name="data-model-metadata"></a>Metadatos del modelo de datos

Aplicaciones de consumo deben construir consultas de Uri para tener acceso a diferentes tipos de datos. El Uri base se puede expandir para hacer referencia a una tabla de datos determinada y también puede incluir parámetros para filtrar los resultados. También se deben declarar las columnas y las cláusulas que se utiliza con el cursor resultante para mostrar los datos.

Para asegurarse de que se construyen solo las consultas Uri válidas, es habitual para proporcionar las cadenas como valores de constantes válidas. Así resulta más fácil para tener acceso a la `ContentProvider` porque hace que los valores que pueda detectar a través de la finalización de código y evita errores tipográficos en las cadenas.

En el ejemplo anterior el `android.provider.ContactsContract` clase expone los metadatos de los datos de contactos. Para nuestras necesidades `ContentProvider` nos va a exponer simplemente las constantes en la propia clase.


## <a name="implementation"></a>Implementación

Hay tres pasos para crear y consumir un personalizado `ContentProvider`:

1. **Cree una clase base de datos** &ndash; implemente `SQLiteOpenHelper`.

2. **Crear un `ContentProvider` clase** &ndash; implemente `ContentProvider` con una instancia de la base de datos, los metadatos se exponen como valores constantes y métodos para tener acceso a los datos.

3. **Acceso a la `ContentProvider` mediante su Uri** &ndash; rellenar un `CursorAdapter` mediante el `ContentProvider`, se accede a través de su Uri.

Como se explicó anteriormente, `ContentProviders` se pueden consumir desde aplicaciones que no sea donde se han definido. En este ejemplo se usan los datos en la misma aplicación, pero tenga en cuenta que otras aplicaciones también pueden acceder a él, siempre sepan el Uri y la información acerca del esquema (que normalmente se expone como valores constantes).


## <a name="create-a-database"></a>Crear una base de datos

La mayoría `ContentProvider` implementaciones se basará en un `SQLite` base de datos. El código de la base de datos de ejemplo en **SimpleContentProvider/VegetableDatabase.cs** crea una base de datos muy sencillo de dos columnas, como se muestra:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

  public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
  public override void OnCreate(SQLiteDatabase db)
  {
    db.ExecSQL(create_table_sql);
    // seed with data
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
  }
  public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
  {
    throw new NotImplementedException();
  }
}
```

La propia implementación de la base de datos no necesita ninguna consideración especial que exponerse con una `ContentProvider`; no obstante, si va a enlazar el `ContentProvider's` datos a un `ListView` , a continuación, controlar una columna de entero único denominada `_id` debe formar parte de la conjunto de resultados. Consulte la [ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) documento para obtener más detalles sobre el uso de la `ListView` control.


## <a name="create-the-contentprovider"></a>Crear el ContentProvider

El resto de esta sección proporciona instrucciones paso a paso sobre cómo el **SimpleContentProvider/VegetableProvider.cs** se creó la clase de ejemplo.


### <a name="initialize-the-database"></a>Inicializar la base de datos

El primer paso es crear subclases `ContentProvider` y agregue la base de datos que va a usar.

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

El resto del código constituirá la implementación del proveedor de contenido real que permite que los datos se detecte y consultar.



## <a name="add-metadata-for-consumers"></a>Agregar metadatos para los consumidores

Hay cuatro tipos diferentes de los metadatos que se van a exponer en el `ContentProvider` clase. Solo se requiere la entidad, el resto se realizan por convención.

- **Autoridad** &ndash; el `ContentProvider` atributo *debe* agregarse a la clase para que se registra con el Android cuando se instala la aplicación.

- **URI** &ndash; el `CONTENT_URI` se expone como una constante para que resulte fácil de usar en el código. Debe coincidir con la entidad, pero incluyen el esquema y la ruta de acceso base.

- **Tipos MIME** &ndash; listas de resultados y solo se tratan como diferentes tipos de contenido, por lo que se definen dos tipos de MIME para representarlos.

- **InterfaceConsts** &ndash; proporcionar un valor constante para cada nombre de columna de datos, para que pueda usar código detectar fácilmente y hacer referencia a ellos sin riesgo de errores tipográficos.

Este código muestra cómo se implementa cada uno de estos elementos, agregar a la definición de la base de datos desde el paso anterior:

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```


## <a name="implement-the-uri-parsing-helper"></a>Implementar la aplicación auxiliar de análisis de URI

Dado que consumen el código usa los URI para realizar solicitudes de un `ContentProvider`, necesitamos poder analizar esas solicitudes para determinar los datos para devolver. El `UriMatcher` clase puede ayudar a analizar los URI, una vez que se ha inicializado con el identificador Uri patrones que el `ContentProvider` admite.

El `UriMatcher` en el ejemplo se inicializará con dos identificadores URI:

1. *"com.xamarin.sample.VegetableProvider/vegetables"* &ndash; solicitud para devolver la lista completa de verduras.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; donde el \# es un marcador de posición para un parámetro numérico (el `_id` de la fila en la base de datos). Marcador de posición de un asterisco ("\*") también puede usarse para que coincida con un parámetro de texto.

En el código, utilizamos las constantes para hacer referencia a valores de metadatos, como la autoridad y la BASE de\_ruta de acceso. Los códigos de retorno se usará en los métodos que realizan análisis para determinar qué datos para devolver el Uri.

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

Este código es todo privado para el `ContentProvider` clase. Consulte [documentación de Google UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/) para obtener más información.


## <a name="implement-the-querymethod"></a>Implementar el QueryMethod

El más sencillo `ContentProvider` es el método para implementar la `Query` método. La implementación siguiente usa el `UriMatcher` para analizar el `uri` parámetro y llame al método base de datos correcta. Si el `uri` contiene un parámetro de identificador, a continuación, se analizaba el entero (mediante `LastPathSegment`) y se usa en la consulta de base de datos.

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

El `GetType` también se debe reemplazar el método. Es posible que se llama a este método para determinar el tipo de contenido que se devolverán para un Uri determinado.
Esto podría indicar a la aplicación consumidora cómo controlar esos datos.

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```


## <a name="implement-the-other-overrides"></a>Implementar las otras invalidaciones

Nuestro ejemplo simple no se permite para la edición o eliminación de datos, pero se deben implementar los métodos Insert, Update y Delete tan agregarlos sin una implementación:

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

Que se haya completado la basic `ContentProvider` implementación. Una vez que se ha instalado la aplicación, los datos estarán disponibles dentro de la aplicación sino también a cualquier otra aplicación que conozca el Uri para hacer referencia a él.


## <a name="access-the-contentprovider"></a>Obtener acceso a la ContentProvider

Una vez el `VegetableProvider` ha sido implementado, tener acceso a él se realiza la misma manera que el proveedor de contactos al principio de este documento: obtener un cursor con el Uri especificado y, a continuación, utilizar un adaptador para tener acceso a los datos.


## <a name="bind-a-listview-to-a-contentprovider"></a>Enlazar un ListView ContentProvider

Para rellenar un `ListView` con los datos se usa el identificador Uri que corresponde a la lista sin filtrar de verduras. En el código se usa el valor constante `VegetableProvider.CONTENT_URI`, lo que sabemos que se resuelve como `com.xamarin.sample.vegetableprovider/vegetables`. Nuestro `VegetableProvider.Query` implementación devolverá un cursor que, a continuación, se puede enlazar a la `ListView`.

El código en `SimpleContentProvider/HomeScreen.cs` muestra lo fácil que es mostrar los datos de un `ContentProvider`:

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

La aplicación resultante tendrá este aspecto:

[![Captura de pantalla de anuncio verduras, frutas, flor yemas, legumbres, bulbos, tubérculos de la aplicación](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperar un único elemento de un ContentProvider

Una aplicación de consumo también puede tener acceso a las filas individuales de datos, lo que pueden realizarse mediante la creación de un Uri diferente al que hace referencia a una fila concreta (por ejemplo).

Use `ContentResolver` directamente para tener acceso a un único elemento, mediante la creación de un Uri con la `Id`.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

El método completo tiene este aspecto:

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```


## <a name="related-links"></a>Vínculos relacionados

- [SimpleContentProvider (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
