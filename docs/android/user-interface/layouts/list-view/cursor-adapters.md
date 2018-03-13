---
title: Usar CursorAdapters
ms.topic: article
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: 5cadaf5f41d940a0255113178d018b59b780eabc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="using-cursoradapters"></a>Usar CursorAdapters


## <a name="overview"></a>Información general

Android proporciona clases de adaptador concreto para mostrar los datos de una consulta de base de datos de SQLite:

 **SimpleCursorAdapter** : de manera Similar a una `ArrayAdapter` porque se puede utilizar sin subclases. Basta con proporcionar los parámetros necesarios (por ejemplo, un cursor e información de diseño) en el constructor y, a continuación, asignar a un `ListView`.

 **CursorAdapter** : valores de una clase base que se puede heredar de cuando necesita más control sobre el enlace de datos a controles de diseño (por ejemplo, ocultar o mostrar controles o cambiar sus propiedades).

Adaptadores de cursor proporcionan una manera de alto rendimiento que desplazarse a través de listas largas de datos que se almacenan en SQLite. El código usado debe definir una consulta SQL en un `Cursor` del objeto y, a continuación, se describe cómo crear y rellenar las vistas para cada fila.


## <a name="creating-an-sqlite-database"></a>Crear una base de datos de SQLite

Para mostrar los adaptadores de cursor requiere una implementación simple de base de datos de SQLite. El código en **SimpleCursorTableAdapter/VegetableDatabase.cs** contiene el código y SQL para crear una tabla y rellenarla con algunos datos.
La sección completa `VegetableDatabase` clase se muestra aquí:

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

El `VegetableDatabase` se crearán instancias de clase en el `OnCreate` método de la `HomeScreen` actividad. El `SQLiteOpenHelper` clase base administra la configuración del archivo de base de datos y garantiza que el código SQL en su `OnCreate` método solo se ejecuta una vez. Esta clase se utiliza en los dos ejemplos siguientes para `SimpleCursorAdapter` y `CursorAdapter`.

La consulta de cursor *debe* tiene una columna de enteros `_id` para el `CursorAdapter` para que funcione. Si la tabla subyacente no tiene una columna de enteros denominada `_id` , a continuación, utilice un alias de columna para otro entero único en el `RawQuery` que conforman el cursor. Hacer referencia a la [documentos Android](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) para obtener más información.


### <a name="creating-the-cursor"></a>Crear el Cursor

Los ejemplos utilizan un `RawQuery` para activar una consulta SQL en un `Cursor` objeto. La lista de columnas que se devuelve desde la posición del cursor define las columnas de datos que están disponibles para su presentación en el adaptador de cursor. El código que crea la base de datos en el **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` método se muestra aquí:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Cualquier código que llame `StartManagingCursor` también debe llamar a `StopManagingCursor`. Los ejemplos utilizan `OnCreate` para iniciar, y `OnDestroy` para cerrar el cursor. El `OnDestroy` método contiene este código:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Una vez que una aplicación tiene una base de datos de SQLite disponible y ha creado un objeto cursor tal como se muestra, puede utilizar en una `SimpleCursorAdapter` o una subclase de `CusorAdapter` para mostrar las filas en un `ListView`.


## <a name="using-simplecursoradapter"></a>Usar SimpleCursorAdapter

`SimpleCursorAdapter` es similar a la `ArrayAdapter`, pero especializadas para su uso con el código. No requieren la creación de subclases: basta con establecer algunos parámetros simples al crear el objeto y, a continuación, asígnelo a un `ListView`del `Adapter` propiedad.

Los parámetros para el constructor SimpleCursorAdapter son:

 **Contexto** : una referencia a la actividad que lo contiene.

 **Diseño** : el identificador de recurso de la vista de la fila a la utilice.

 **ICursor** : un cursor que contiene la consulta de SQLite para los datos que se va a mostrar.

 **Desde** matriz de cadenas: una matriz de cadenas que corresponden a los nombres de columnas del cursor.

 **Para** matriz de enteros: una matriz de identificadores de diseño que se corresponden con los controles en el diseño de fila. El valor de la columna especificada en el `from` matriz se enlazarán con el ControlID especificado en la matriz en el mismo índice.

El `from` y `to` matrices deben tener el mismo número de entradas porque forman una asignación desde el origen de datos a los controles de diseño en la vista.

El **SimpleCursorTableAdapter/HomeScreen.cs** cables de código de ejemplo de un `SimpleCursorAdapter` similar a la siguiente:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` es una manera rápida y simple para mostrar los datos de SQLite en un `ListView`. La limitación principal es que solo se pueden enlazar los valores de columna para mostrar los controles, no le permiten cambiar otros aspectos del diseño de fila (por ejemplo, mostrar/ocultar controles o cambiar las propiedades).


## <a name="subclassing-cursoradapter"></a>Creación de subclases CursorAdapter

A `CursorAdapter` subclase tiene las mismas ventajas de rendimiento que el `SimpleCursorAdapter` para mostrar los datos de SQLite, pero también le ofrece un control completo sobre la creación y el diseño de la vista de cada fila. El `CursorAdapter` implementación es muy diferente de la creación de subclases `BaseAdapter` porque no invalida `GetView`, `GetItemId`, `Count` o `this[]` indizador.

Dado un trabajo SQLite de base de datos, solo tiene que invalidar dos métodos para crear un `CursorAdapter` subclase:

- **BindView** : proporciona una vista, debe actualizarlo para mostrar los datos en el cursor proporcionado.

- **NewView** – llama cuando el `ListView` requiere una nueva vista para mostrar. El `CursorAdapter` se encargará de reciclaje vistas (a diferencia de la `GetView` método en adaptadores normales).

Las subclases de adaptador en ejemplos anteriores tienen métodos para devolver el número de filas y recuperar el elemento actual: el `CursorAdapter` no requiere estos métodos porque esa información se puede deducir del mismo cursor. Mediante la división de la creación y el llenado de cada vista en estos dos métodos, el `CursorAdapter` exige volver a usar la vista. Esto es a diferencia de un adaptador normal donde es posible pasar por alto la `convertView` parámetro de la `BaseAdapter.GetView` método.


### <a name="implementing-the-cursoradapter"></a>Implementar el CursorAdapter

El código en **CursorTableAdapter/HomeScreenCursorAdapter.cs** contiene un `CursorAdapter` subclase. Almacena una referencia de contexto pasada al constructor y que puede tener acceso a un `LayoutInflater` en el `NewView` método. La clase completa tiene el siguiente aspecto:

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

- [SimpleCursorTableAdapter (ejemplo)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (ejemplo)](https://developer.xamarin.com/samples/CursorTableAdapter/)
