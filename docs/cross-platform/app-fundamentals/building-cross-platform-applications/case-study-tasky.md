---
title: 'Caso práctico de aplicación multiplataforma: Tasky'
description: Este documento describe cómo la aplicación de ejemplo Tasky Portable diseñada y creada como una aplicación móvil multiplataforma. Describe la aplicación los requisitos, interfaz, modelo de datos, funcionalidad básica, implementación y mucho más.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 23deddae61452d532a87c51cc1ec3bc53eb91c9f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61279114"
---
# <a name="cross-platform-app-case-study-tasky"></a>Caso práctico de aplicación multiplataforma: Tasky

*Tasky* *Portable* es una aplicación de lista de tareas pendientes sencilla. Este documento describe cómo se diseñada y creada siguiendo las instrucciones de la [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento. El análisis abarca las siguientes áreas:

<a name="Design_Process" />

## <a name="design-process"></a>Proceso de diseño

Es aconsejable crear un mapa de carreteras-lo que se desea lograr antes de comenzar a codificar. Esto es especialmente cierto para el desarrollo multiplataforma, que va a crear la funcionalidad que se expondrán de varias maneras. A partir de una idea clara de qué va a compilar ahorra tiempo y esfuerzo más adelante en el ciclo de desarrollo.

 <a name="Requirements" />

### <a name="requirements"></a>Requisitos

Es el primer paso para diseñar una aplicación identificar las características deseadas. Puede tratarse de objetivos de alto nivel o detallada de casos de uso. Tasky tiene requisitos funcionales sencillo:

 -  Ver una lista de tareas
 -  Agregar, editar y eliminar tareas
 -  Establecer el estado de una tarea a 'done'

Debe considerar el uso de características específicas de plataforma.  ¿Puede Tasky aprovechar el perímetro de iOS o Windows Phone Live Tiles? Incluso si no usa características específicas de plataforma en la primera versión, debe planear con antelación para asegurarse de que su negocio y capas de datos pueden adaptarse a ellos.

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>Diseño de la interfaz de usuario

Comience con un diseño de alto nivel que se puede implementar en todas las plataformas de destino. Tenga cuidado a restricciones de interfaz de usuario específico de la plataforma de nota. Por ejemplo, un `TabBarController` en iOS puede mostrar más de cinco botones, mientras que el equivalente de Windows Phone puede mostrar hasta cuatro.
Dibuje el flujo de pantalla con la herramienta que prefiera (papel funciona).

 [![](case-study-tasky-images/taskydesign.png "Dibuje el flujo de pantalla con la herramienta de su elección funciona el papel")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>Modelo de datos

Saber qué datos necesitan almacenarse le ayudará a determinar qué mecanismo de persistencia. Consulte [multiplataforma Data Access](~/cross-platform/app-fundamentals/index.md) para obtener información acerca de los mecanismos de almacenamiento disponible y ayuda para decidir entre ellos. Para este proyecto, vamos a usar SQLite.NET.

Tasky necesita almacenar tres propiedades para cada 'TaskItem':

 -  **Nombre** : cadena
 -  **Notas de la** : cadena
 -  **Realiza** : booleano

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>Funcionalidad principal

Considere la posibilidad de la API de la interfaz de usuario deberá consumir para cumplir los requisitos. Una lista de tareas pendientes requiere las siguientes funciones:

 -   **Muestra todas las tareas** : para mostrar la lista de la pantalla principal de todas las tareas disponibles
 -  **Obtener una tarea** : cuando se toca una fila de la tarea
 -  **Guardar una tarea** : cuando se edita una tarea
 -  **Eliminar una tarea** : cuando se elimina una tarea
 -  **Crear tarea vacía** : cuando se crea una nueva tarea

Para lograr la reutilización de código, se debe implementar esta API una vez en el *biblioteca de clases Portable*.

 <a name="Implementation" />

### <a name="implementation"></a>Implementación

Una vez que haya acordado el diseño de la aplicación, tenga en cuenta cómo se podría implementar como una aplicación multiplataforma. Esto se convertirá en la arquitectura de la aplicación. Siguiendo las instrucciones de la [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento, el código de aplicación debe ser interrumpido profundidad de las siguientes partes:

 -   **El código común** : un proyecto común que contiene código puedan volver a utilizar para almacenar los datos de la tarea; expone una clase de modelo y una API para administrar el almacenamiento y carga de datos.
 -   **Código específico de plataforma** – proyectos específicos de plataforma que implementan una interfaz de usuario nativa para cada sistema operativo, utilizando el código común como 'back-end'.

 [![](case-study-tasky-images/taskypro-architecture.png "Proyectos específicos de la plataforma de implementan una interfaz de usuario nativa para cada sistema operativo, utilizando el código común como el back-end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Estas dos partes se describen en las secciones siguientes.

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>Código común (PCL)

Tasky Portable utiliza la estrategia de biblioteca de clases Portable para compartir código común. Consulte la [opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obtener una descripción de las opciones de uso compartido de código.

Todo el código común, incluyendo la capa de acceso a datos, el código de la base de datos y contratos, se coloca en el proyecto de biblioteca.

El proyecto PCL completo se muestra a continuación. Todo el código en la biblioteca portable es compatible con cada plataforma de destino. Cuando se implementa, cada aplicación nativa hará referencia a esa biblioteca.

![](case-study-tasky-images/portable-project.png "Cuando se implementa, cada aplicación nativa hará referencia a dicha biblioteca")

El diagrama de clase siguiente muestra las clases agrupadas por capa. La `SQLiteConnection` clase es código reutilizable del paquete Sqlite-NET. El resto de las clases son código personalizado para Tasky. El `TaskItemManager` y `TaskItem` clases representan la API que se expone a las aplicaciones específicas de la plataforma.

 [![](case-study-tasky-images/classdiagram-core.png "Las clases TaskItemManager y TaskItem representen la API que se expone a las aplicaciones específicas de la plataforma")](case-study-tasky-images/classdiagram-core.png#lightbox)

Utilizar espacios de nombres para separar las capas ayuda a administrar las referencias entre cada capa. Los proyectos específicos de plataforma solo debe incluir un `using` instrucción para la capa de negocio. La capa de acceso a datos y la capa de datos se deben encapsular por la API que se expone mediante `TaskItemManager` en la capa de negocio.

 <a name="References" />

### <a name="references"></a>Referencias

Bibliotecas de clases portables deben utilizarse en varias plataformas, cada uno con distintos niveles de compatibilidad con las características de plataformas y marcos. Por este motivo, existen limitaciones en el que se pueden usar los paquetes y bibliotecas de framework. Por ejemplo, Xamarin.iOS no admite c# `dynamic` palabra clave, por lo que una biblioteca de clases portable no puede usar cualquier paquete que depende de código dinámico, aunque dicho código podría funcionar en Android. Visual Studio para Mac, no podrá agregar referencias y los paquetes incompatibles, pero desea tener en cuenta para evitar sorpresas más adelante limitaciones.

Nota: Verá que los proyectos hacen referencia a bibliotecas de framework que no ha usado. Estas referencias se incluyen como parte de las plantillas de proyecto de Xamarin. Cuando se compilan aplicaciones, el proceso de vinculación quitará el código sin referencia, así que aunque `System.Xml` ha sido hace referencia, no se incluirá en la aplicación final porque no estamos usando las funciones de Xml.

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>Capa de datos (DL)

La capa de datos contiene el código que realiza el almacenamiento físico de datos, ya sea en una base de datos, archivos sin formato u otro mecanismo. La capa de datos Tasky consta de dos partes: la biblioteca de SQLite-NET y el código personalizado agregado al conectarla.

Tasky se basa en el paquete de nuget de Sqlite-net (publicado por Frank Kreuger) para incrustar código SQLite-NET que proporciona una interfaz de la base de datos de asignación relacional de objetos (ORM). El `TaskItemDatabase` clase hereda de `SQLiteConnection` y agrega los subdominios necesario creación, lectura, actualización, los métodos de eliminación (CRUD) para leer y escribir datos en SQLite. Es una implementación de modelo sencillo de los métodos CRUD genéricos que se puede volver a utilizar en otros proyectos.

El `TaskItemDatabase` es un singleton, lo que garantiza que todo el acceso se produce en la misma instancia. Se usa un bloqueo para impedir el acceso simultáneo de varios subprocesos.

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>SQLite en Windows Phone

IOS y Android se entregan con SQLite como parte del sistema operativo, Windows Phone no incluye un motor de base de datos compatible. Para compartir código entre las tres plataformas se requiere una versión de Windows phone nativos de SQLite. Consulte [trabajar con una base de datos Local](~/xamarin-forms/app-fundamentals/databases.md) para obtener más información sobre cómo configurar el proyecto de Windows Phone para Sqlite.

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>Mediante una interfaz para generalizar el acceso a datos

La capa de datos toma una dependencia en `BL.Contracts.IBusinessIdentity` para que puedan implementar métodos de acceso de datos abstracto que requieren una clave principal. A continuación, se puede conservar cualquier clase de capa de negocio que implementa la interfaz en la capa de datos.

La interfaz sólo especifica una propiedad de entero para que actúe como la clave principal:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La clase base implementa la interfaz y agrega los atributos de SQLite-NET para marcarla como una clave principal de incremento automático. Cualquier clase en la capa de negocio que implementa esta clase base, a continuación, puede conservarse en la capa de datos:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

¿Es un ejemplo de los métodos genéricos en la capa de datos que usan la interfaz `GetItem<T>` método:

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>El bloqueo para impedir el acceso simultáneo

Un [bloqueo](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) se implementa dentro de la `TaskItemDatabase` clase para evitar el acceso simultáneo a la base de datos. Esto es para asegurarse de que se serializa el acceso simultáneo desde subprocesos diferentes (en caso contrario, un componente de interfaz de usuario podría intentar leer la base de datos al mismo tiempo lo está actualizando un subproceso en segundo plano). Aquí se muestra un ejemplo de cómo se implementa el bloqueo:

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

La mayoría del código de capa de datos podría volver a utilizarse en otros proyectos. El código solo específicos de la aplicación de la capa es la `CreateTable<TaskItem>` llamar el `TaskItemDatabase` constructor.

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>Capa de acceso a datos (DAL)

El `TaskItemRepository` clase encapsula el mecanismo de almacenamiento de datos con una API fuertemente tipado que permite `TaskItem` objetos que se creará, eliminar, recuperar y actualizado.

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>Compilación condicional

La clase utiliza la compilación condicional para establecer la ubicación del archivo: este es un ejemplo de la implementación de divergencia de plataforma. La propiedad que devuelve la ruta de acceso se compila a código diferente en cada plataforma. El código y las directivas de compilador específica de la plataforma se muestran aquí:

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

Según la plataforma, el resultado será "<app
path>/Library/TaskDB.db3" para iOS, "<app
path>/Documents/TaskDB.db3" para Android o simplemente "TaskDB.db3" para Windows Phone.

### <a name="business-layer-bl"></a>Capa de negocio (BL)

La capa de negocio implementa las clases del modelo y una fachada para administrarlos.
En Tasky el modelo es el `TaskItem` clase y `TaskItemManager` implementa el patrón de fachada para proporcionar una API para administrar `TaskItems`.

 <a name="Façade" />

#### <a name="faade"></a>Façade

 `TaskItemManager` ajusta el `DAL.TaskItemRepository` para proporcionar la operación Get, guardar y eliminar los métodos que se hará referencia a la aplicación y las capas de interfaz de usuario.

Lógica y las reglas de negocios se colocarían aquí si es necesario, por ejemplo, las reglas de validación que deben cumplirse antes de que se guarda un objeto.

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>API de código específico de plataforma

Una vez que se ha escrito el código común, se debe generar la interfaz de usuario para recopilar y mostrar los datos expuestos por ella. La `TaskItemManager` clase implementa el patrón de fachada para proporcionar una API simple para el código de aplicación para tener acceso a.

El código escrito en cada proyecto específico de plataforma estarán generalmente estrechamente acopladas al SDK nativo de ese dispositivo y solo acceder al código comunes mediante la API definido por el `TaskItemManager`. Esto incluye los métodos y empresariales las clases expone, como `TaskItem`.

Las imágenes no se comparte entre plataformas pero agregan de forma independiente a cada proyecto. Esto es importante porque cada plataforma trata las imágenes de manera diferente, con resoluciones, directorios y nombres de archivo diferentes.

Las secciones restantes explican los detalles de implementación específicos de la plataforma de la interfaz de usuario Tasky.

 <a name="iOS_App" />

## <a name="ios-app"></a>Aplicación de iOS

Hay sólo una serie de clases necesarias para implementar la aplicación Tasky mediante el proyecto PCL común para almacenar y recuperar datos de iOS. El proyecto de Xamarin.iOS iOS completa se muestra a continuación:

 ![](case-study-tasky-images/taskyios-solution.png "aquí se muestra el proyecto de iOS")

Las clases se muestran en este diagrama, se agrupan en capas.

 [![](case-study-tasky-images/classdiagram-android.png "Las clases se muestran en este diagrama, se agrupan en capas")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referencias

La aplicación de iOS hace referencia a las bibliotecas SDK específico de la plataforma: por ejemplo. Xamarin.iOS y MonoTouch.Dialog-1.

Debe hacer referencia el `TaskyPortableLibrary` proyecto PCL.
Aquí se muestra la lista de referencias:

 ![](case-study-tasky-images/taskyios-references.png "Aquí se muestra la lista de referencias")

La capa de aplicación y capa de interfaz de usuario se implementan en este proyecto usando estas referencias.

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Capa de aplicación (AL)

La capa de aplicación contiene clases específicas de la plataforma necesarias para los objetos expuestos por la PCL para la interfaz de usuario "enlazar". La aplicación específica de iOS tiene dos clases para ayudar a mostrar tareas:

 -   **EditingSource** : esta clase se utiliza para enlazar las listas de tareas a la interfaz de usuario. Dado que `MonoTouch.Dialog` se usó para la lista de tareas, es preciso implementar esta aplicación auxiliar para habilitar la funcionalidad de pasar el dedo para eliminar en el `UITableView` . Deslice el dedo para eliminar es habitual en iOS, pero no en Android o Windows Phone, por lo que el proyecto específico de iOS es el único que lo implementa.
 -   **TaskDialog** : esta clase se utiliza para enlazar una única tarea a la interfaz de usuario. Usa el `MonoTouch.Dialog` API de reflexión para "encapsular" la `TaskItem` objeto con una clase que contiene los atributos correctos para permitir que la pantalla de entrada se tiene el formato correcto.

El `TaskDialog` clase utiliza `MonoTouch.Dialog` atributos para crear una pantalla basan en Propiedades de la clase. La clase tiene este aspecto:

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

Tenga en cuenta la `OnTap` atributos requieren un nombre de método: estos métodos deben existir en la clase donde el `MonoTouch.Dialog.BindingContext` se crea (en este caso, el `HomeScreen` clase descrita en la sección siguiente).

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>Capa de interfaz de usuario (UI)

La capa de interfaz de usuario consta de las clases siguientes:

1.   **AppDelegate** – contiene las llamadas a la API de apariencia para definir el estilo de las fuentes y colores usados en la aplicación. Tasky es una sencilla aplicación por lo que no hay ningún otras tareas de inicialización que se ejecutan en `FinishedLaunching` .
2.   **Las pantallas** : las subclases de `UIViewController` que definen cada pantalla y su comportamiento. Las pantallas vinculan juntos la interfaz de usuario con las clases de nivel de aplicación y la API comunes ( `TaskItemManager` ). En este ejemplo se crean las pantallas en código, pero se podría han diseñado mediante Interface Builder de Xcode o el Diseñador de guión gráfico.
3.   **Imágenes** : elementos visuales son una parte importante de cada aplicación. Tasky tiene imágenes pantalla y el icono de presentación, que para iOS, deben proporcionarse en normal y resolución Retina.

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Pantalla principal

La pantalla es un `MonoTouch.Dialog` pantalla que muestra una lista de tareas desde la base de datos de SQLite. Hereda de `DialogViewController` e implementa código para establecer el `Root` para contener una colección de `TaskItem` objetos que desea mostrar.

 [![](case-study-tasky-images/ios-taskylist.png "Hereda de DialogViewController e implementa código para establecer la raíz para que contenga una colección de objetos TaskItem para mostrar")](case-study-tasky-images/ios-taskylist.png#lightbox)

Los dos métodos principales relacionados con la visualización e interacción con la lista de tareas son:

1.   **PopulateTable** : usa la capa de negocio `TaskManager.GetTasks` método para recuperar una colección de `TaskItem` objetos que se va a mostrar.
2.   **Selecciona** : cuando se toca una fila, la tarea se muestra en una nueva pantalla.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Pantalla de detalles de tarea

Detalles de la tarea es una pantalla de entrada que permite que las tareas se deben editar o eliminar.

Usa tasky `MonoTouch.Dialog`de API de reflexión para mostrar la pantalla, por lo que no hay ningún `UIViewController` implementación. En su lugar, el `HomeScreen` crea una instancia de clase y se muestra un `DialogViewController` utilizando la `TaskDialog` clase a partir de la capa de aplicación.

Esta captura de pantalla muestra una pantalla vacía que se muestra el `Entry` atributo establecimiento del texto de marca de agua en el **nombre** y **notas** campos:

 [![](case-study-tasky-images/ios-taskydetail.png "Esta captura de pantalla muestra una pantalla vacía que se muestra el atributo de entrada al establecer el texto de marca de agua en los campos nombre y notas")](case-study-tasky-images/ios-taskydetail.png#lightbox)

La funcionalidad de la **detalles de la tarea** pantalla (por ejemplo, guardar o eliminar una tarea) debe implementarse en el `HomeScreen` clase, porque es donde el `MonoTouch.Dialog.BindingContext` se crea. La siguiente `HomeScreen` métodos admiten la pantalla de detalles de la tarea:

1.   **ShowTaskDetails** – crea un `MonoTouch.Dialog.BindingContext` para representar una pantalla. Crea la pantalla de entrada mediante la reflexión para recuperar los nombres de propiedad y tipos a partir de la `TaskDialog` clase. Información adicional, como el texto de marca de agua para los cuadros de entrada, se implementa con los atributos de propiedades.
2.   **SaveTask** : este método se hace referencia en el `TaskDialog` clase a través de un `OnTap` atributo. Se llama cuando **guardar** presionado y usa un `MonoTouch.Dialog.BindingContext` para recuperar los datos introducidos por el usuario antes de guardar los cambios mediante `TaskItemManager` .
3.   **DeleteTask** : este método se hace referencia en el `TaskDialog` clase a través de un `OnTap` atributo. Lo usa `TaskItemManager` para eliminar los datos mediante la clave principal (propiedad de Id.).

 <a name="Android_App" />

## <a name="android-app"></a>Aplicación de Android

El proyecto de Xamarin.Android completo se muestra a continuación:

 ![](case-study-tasky-images/taskyandroid-solution.png "Aquí se muestra el proyecto de Android")

El diagrama de clases, con las clases agrupadas por capa:

 [![](case-study-tasky-images/classdiagram-android.png "El diagrama de clases, con las clases agrupadas por nivel")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referencias

El proyecto de aplicación de Android debe hacer referencia el ensamblado de específicos de la plataforma Xamarin.Android para tener acceso a clases de Android SDK.

También debe hacer referencia el proyecto PCL (p ej. TaskyPortableLibrary) para tener acceso el código de capa de datos y empresariales comunes.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary para acceder al código de capa empresarial y datos comunes")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Capa de aplicación (AL)

Similar a la versión de iOS que vimos anteriormente, la capa de aplicación en la versión de Android contiene clases específicas de la plataforma necesarias para los objetos expuestos por núcleo de la interfaz de usuario "enlazar".

 **TaskListAdapter** : para mostrar una lista<T> de objetos, es preciso implementar un adaptador para mostrar los objetos personalizados en un `ListView`. El adaptador controla qué diseño se utiliza para cada elemento de la lista: en este caso, el código usa un diseño de Android integrado `SimpleListItemChecked`.

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaz de usuario (UI)

Capa de interfaz de usuario de la aplicación Android es una combinación de código y marcado XML.

 -   **Recursos y diseño** – diseños de pantalla y la fila de diseño que se implementan como archivos AXML de celda. Puede ser el AXML escrito a mano o dispuesto visualmente mediante el Diseñador de la interfaz de usuario de Xamarin para Android.
 -   **Los recursos/Drawable** : imágenes (iconos) y botón personalizado.
 -   **Las pantallas** : las subclases de la actividad que definen cada pantalla y su comportamiento. Une la interfaz de usuario con las clases de nivel de aplicación y la API comunes (`TaskItemManager`).

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Pantalla principal

La pantalla principal consta de una subclase de actividad `HomeScreen` y `HomeScreen.axml` archivo que define el diseño (posición de la lista de tareas y botón). La pantalla tiene un aspecto similar al siguiente:

 [![](case-study-tasky-images/android-taskylist.png "La pantalla tiene un aspecto similar al siguiente")](case-study-tasky-images/android-taskylist.png#lightbox)

El código de la pantalla principal define los controladores para hacer clic en el botón y al hacer clic en elementos de la lista, así como rellenar la lista en el `OnResume` método (por lo que TI refleja los cambios realizados en la pantalla de detalles de tarea). Datos se cargan con la capa de negocio `TaskItemManager` y `TaskListAdapter` del nivel de aplicación.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Pantalla de detalles de tarea

La pantalla de detalles de la tarea también consta de un `Activity` subclase y un archivo de diseño AXML. El diseño determina la ubicación de los controles de entrada y el C# clase define el comportamiento para cargar y guardar `TaskItem` objetos.

 [![](case-study-tasky-images/android-taskydetail.png "La clase define el comportamiento para cargar y guardar los objetos TaskItem")](case-study-tasky-images/android-taskydetail.png#lightbox)

Todas las referencias a la biblioteca PCL son a través de la `TaskItemManager` clase.

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Aplicación de Windows Phone
El proyecto completo de Windows Phone:

 ![](case-study-tasky-images/taskywp7-solution.png "Aplicación de Windows Phone el proyecto completo de Windows Phone")

El diagrama siguiente presenta las clases que se agrupan en capas:

 [![](case-study-tasky-images/classdiagram-wp7.png "Este diagrama presenta las clases que se agrupan en capas")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referencias

El proyecto específico de plataforma debe hacer referencia a las bibliotecas específicas de la plataforma necesarias (como `Microsoft.Phone` y `System.Windows`) para crear una aplicación de Windows Phone válida.

También debe hacer referencia el proyecto PCL (p ej. `TaskyPortableLibrary`) para usar el `TaskItem` clase y base de datos.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary a utilizar la clase TaskItem y base de datos")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Capa de aplicación (AL)

De nuevo, al igual que con las versiones de Android y iOS, la capa de aplicación consta de los elementos no visuales que ayudan a enlazar datos a la interfaz de usuario.

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

ViewModels ajustar los datos de la PCL ( `TaskItemManager`) y la presenta de forma que puede utilizarse en el enlace de datos de Silverlight y XAML. Esto es un ejemplo del comportamiento específico de la plataforma (como se describe en el documento de las aplicaciones multiplataforma).

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaz de usuario (UI)

XAML tiene una funcionalidad única de enlace de datos que se puede declarar en el marcado y reduce la cantidad de código necesario para mostrar los objetos:

1.   **Páginas** : archivos XAML y su código subyacente, definir la interfaz de usuario y hacer referencia los ViewModel y el proyecto PCL para mostrar y recopilar datos.
2.   **Imágenes** : imágenes de icono, el fondo y la pantalla de presentación son una parte fundamental de la interfaz de usuario.

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

La clase MainPage usa el `TaskListViewModel` para mostrar datos mediante las características de enlace de datos del XAML. La página `DataContext` se establece en el modelo de vista, que se rellena de forma asincrónica. El `{Binding}` sintaxis en el XAML determina cómo se muestran los datos.

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

Cada tarea se muestra al enlazar la `TaskViewModel` para el XAML que se definen en el TaskDetailsPage.xaml. Los datos de la tarea se recuperan a través de la `TaskItemManager` en la capa de negocio.

 <a name="Results" />

## <a name="results"></a>Resultados

Las aplicaciones resultantes tiene este aspecto en cada plataforma:

 <a name="iOS" />

#### <a name="ios"></a>iOS

La aplicación usa el diseño de la interfaz de usuario de iOS estándar, como el botón 'Agregar' que se coloca en la barra de navegación y usa el método integrado **más (+)** icono. También usa el valor predeterminado `UINavigationController` botón "Atrás" comportamiento y admite pasar el dedo para eliminar de la tabla.

 [![](case-study-tasky-images/ios-taskylist.png "También utiliza el comportamiento del botón Atrás de UINavigationController predeterminado y admite deslice el dedo para eliminar en la tabla")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "también usa el valor predeterminado UINavigationController copia el comportamiento del botón y admite deslice el dedo para eliminar en la tabla")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

La aplicación de Android usa controles integrados, incluido el diseño integrado para las filas que requieren una muestra 'marca'. Se admite el hardware o comportamiento del sistema back-además de un botón Atrás en la pantalla.

 [![](case-study-tasky-images/android-taskylist.png "Se admite el hardware o comportamiento del sistema back-además de un botón Atrás en la pantalla")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "se admite el comportamiento de espera del sistema de hardware y además una pantalla botón Atrás")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

La aplicación de Windows Phone usa la distribución estándar, rellenar la barra de la aplicación en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior.

 [![](case-study-tasky-images/wp-taskylist.png "La aplicación de Windows Phone usa el diseño estándar, rellenar la barra en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior de la aplicación")](case-study-tasky-images/wp-taskylist.png#lightbox) [![](case-study-tasky-images/wp-taskylist.png "de Windows Phone de la aplicación utiliza el estándar diseño, rellenar la barra en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior de la aplicación")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Resumen

Este documento proporciona una explicación detallada de cómo se han aplicado los principios de diseño de la aplicación en capas a una aplicación simple para facilitar la reutilización del código en tres plataformas móviles: iOS, Android y Windows Phone.

Se describe el proceso que se usa para diseñar los niveles de aplicación y explica qué código &amp; funcionalidad se ha implementado en cada capa.

El código puede descargarse desde [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Vínculos relacionados

- [Building Cross-Platform Applications (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky Portable aplicación de ejemplo (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
