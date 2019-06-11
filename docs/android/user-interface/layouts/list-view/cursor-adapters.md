---
title: Uso de CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/25/2017
ms.openlocfilehash: 42b9bd528459d8ee941cc293372bf5662a493342
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827617"
---
# <a name="using-cursoradapters"></a>Uso de CursorAdapters


## <a name="overview"></a>Información general

Android proporciona clases de adaptador específicamente para mostrar los datos de una consulta de base de datos de SQLite:

 **SimpleCursorAdapter** : Similar a una `ArrayAdapter` porque se puede usar sin la creación de subclases. Basta con proporcionar los parámetros necesarios (por ejemplo, un cursor e información de diseño) en el constructor y, a continuación, asignar a un `ListView`.

 **CursorAdapter** : valores de una clase base que se puede heredar de cuando se necesita más control sobre el enlace de datos a controles de diseño (por ejemplo, ocultar o mostrar los controles o cambiar sus propiedades).

Adaptadores de cursor proporcionan una manera de alto rendimiento para desplazarse por las listas largas de datos que están almacenados en SQLite. El código usado debe definir una consulta SQL en un `Cursor` de objetos y, a continuación, se describe cómo crear y rellenar las vistas para cada fila.


## <a name="creating-an-sqlite-database"></a>Creación de una base de datos de SQLite

Para demostrar los adaptadores de cursor requiere una implementación sencilla de base de datos de SQLite. El código en **SimpleCursorTableAdapter/VegetableDatabase.cs** contiene el código y SQL para crear una tabla y rellenarla con algunos datos.
La completa `VegetableDatabase` clase se muestra aquí:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
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
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

El `VegetableDatabase` se crearán instancias de clase en el `OnCreate` método de la `HomeScreen` actividad. El `SQLiteOpenHelper` administra la configuración del archivo de base de datos de clase base y garantiza que el código SQL en su `OnCreate` método sólo se ejecuta una vez. Esta clase se utiliza en los dos ejemplos siguientes para `SimpleCursorAdapter` y `CursorAdapter`.

La consulta de cursor *debe* tiene una columna de enteros `_id` para el `CursorAdapter` funcione. Si la tabla subyacente no tiene una columna de enteros denominada `_id` , a continuación, utilice un alias de columna para otro entero único en la `RawQuery` que conforma el cursor. Hacer referencia a la [docs Android](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) para obtener más información.


### <a name="creating-the-cursor"></a>Crear el Cursor

Los ejemplos utilizan un `RawQuery` para activar una consulta SQL en un `Cursor` objeto. La lista de columnas que se devuelve desde la posición del cursor define las columnas de datos que están disponibles para su presentación en el adaptador de cursor. El código que crea la base de datos en el **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` método se muestra aquí:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Cualquier código que llama a `StartManagingCursor` también debe llamar a `StopManagingCursor`. Los ejemplos usan `OnCreate` para iniciar, y `OnDestroy` para cerrar el cursor. El `OnDestroy` método contiene este código:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Una vez que una aplicación tiene una base de datos de SQLite disponible y ha creado un objeto cursor tal como se muestra, puede usar ya sea un `SimpleCursorAdapter` o una subclase de `CusorAdapter` para mostrar las filas en un `ListView`.


## <a name="using-simplecursoradapter"></a>Uso de SimpleCursorAdapter

`SimpleCursorAdapter` es similar a la `ArrayAdapter`, pero especializadas para su uso con SQLite. No requieren la creación de subclases: basta con establecer algunos parámetros simples al crear el objeto y, a continuación, asígnelo a un `ListView`del `Adapter` propiedad.

Los parámetros del constructor SimpleCursorAdapter son:

 **Contexto** – una referencia a la actividad contenedora.

 **Diseño** : el identificador de recurso de la vista de la fila para usar.

 **ICursor** : un cursor que contiene la consulta de SQLite para los datos que se va a mostrar.

 **Desde** matriz de cadenas: una matriz de cadenas que corresponden a los nombres de columnas del cursor.

 **Para** matriz de enteros: una matriz de identificadores de diseño que se corresponden con los controles en el diseño de la fila. El valor de la columna especificada en el `from` matriz se enlazará a la ControlID especificado en la matriz en el mismo índice.

El `from` y `to` matrices deben tener el mismo número de entradas porque forman una asignación desde el origen de datos a los controles de diseño en la vista.

El **SimpleCursorTableAdapter/HomeScreen.cs** hilos de código de ejemplo de un `SimpleCursorAdapter` similar al siguiente:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` es una manera rápida y sencilla para mostrar los datos de SQLite en un `ListView`. La principal limitación es que sólo se puede enlazar los valores de columna se muestran los controles, no permite cambiar otros aspectos del diseño de fila (por ejemplo, mostrar y ocultar los controles o cambiar las propiedades).


## <a name="subclassing-cursoradapter"></a>Creación de subclases de CursorAdapter

Un `CursorAdapter` subclase tiene las mismas ventajas de rendimiento que el `SimpleCursorAdapter` para mostrar los datos de SQLite, pero también le ofrece control completo sobre la creación y diseño de la vista de cada fila. El `CursorAdapter` implementación es muy diferente de la creación de subclases `BaseAdapter` porque no invalida `GetView`, `GetItemId`, `Count` o `this[]` indizador.

Dado un funcionamiento SQLite de base de datos, solo tiene que invalidar dos métodos para crear un `CursorAdapter` subclase:

- **BindView** – proporcionado una visión, actualícelo para mostrar los datos en el cursor proporcionado.

- **NewView** : se le llama cuando el `ListView` requiere una nueva vista para mostrar. El `CursorAdapter` se encargará de reciclaje de vistas (a diferencia de la `GetView` método en adaptadores normales).

Las subclases de adaptador en ejemplos anteriores tienen métodos para devolver el número de filas y recuperar el elemento actual – el `CursorAdapter` no requiere estos métodos porque se puede deducir esa información desde el mismo cursor. Mediante la división de la creación y el llenado de cada vista en estos dos métodos, el `CursorAdapter` exige la reutilización de vista. Esto es en contraste a un adaptador normal donde es posible pasar por alto el `convertView` parámetro de la `BaseAdapter.GetView` método.


### <a name="implementing-the-cursoradapter"></a>Implementar el CursorAdapter

El código en **CursorTableAdapter/HomeScreenCursorAdapter.cs** contiene un `CursorAdapter` subclase. Almacena una referencia de contexto que se pasó al constructor para que pueda acceder a un `LayoutInflater` en el `NewView` método. La clase completa tiene este aspecto:

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```


### <a name="assigning-the-cursoradapter"></a>Asignar el CursorAdapter

En el `Activity` que mostrará el `ListView`, crea el cursor y `CursorAdapter` , a continuación, asignarlo a la vista de lista.

El código que realiza esta acción en el **CursorTableAdapter/HomeScreen.cs** `OnCreate` método se muestra aquí:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

El `OnDestroy` método contiene el `StopManagingCursor` llamada al método se ha descrito anteriormente.



## <a name="related-links"></a>Vínculos relacionados

- [SimpleCursorTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (ejemplo)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
