---
title: 'Caso práctico: Tasky'
description: Este documento describe cómo se han aplicado los principios de creación de aplicaciones entre plataformas en la aplicación de ejemplo Tasky Portable. Modifica en el diseño de aplicaciones móviles, escribir código común para volver a usar e implementar proyectos para plataformas específicas que tienen como destino el iOS, Android y Windows Phone plataformas.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 1f8325bbc6b3f8aa40a08ca2c57605544ab0bc85
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="case-study-tasky"></a>Caso práctico: Tasky

_Este documento describe cómo se han aplicado los principios de creación de aplicaciones entre plataformas en la aplicación de ejemplo Tasky Portable. Modifica en el diseño de aplicaciones móviles, escribir código común para volver a usar e implementar proyectos para plataformas específicas que tienen como destino el iOS, Android y Windows Phone plataformas._

*Tasky* *Portable* es una aplicación de lista de tareas pendientes simple. Este documento describen cómo se diseñada y creada, siguiendo las instrucciones de la [creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento. La descripción tratan las áreas siguientes:

<a name="Design_Process" />

## <a name="design-process"></a>Proceso de diseño

Es aconsejable crear un mapa de carreteras: lo que se desea lograr antes de comenzar a codificar. Esto es especialmente cierto para el desarrollo multiplataforma, donde se está compilando la funcionalidad que se expondrán de varias maneras. A partir de una idea clara de lo que se va a compilar ahorra tiempo y esfuerzo más adelante en el ciclo de desarrollo.

 <a name="Requirements" />

### <a name="requirements"></a>Requisitos

Es el primer paso para diseñar una aplicación identificar características que desee. Puede tratarse de objetivos de alto nivel o detallados casos de uso. Tasky tiene requisitos funcionales sencillos:

 -  Ver una lista de tareas
 -  Agregar, editar y eliminar tareas
 -  Establecer el estado de una tarea a 'done'

Debe considerar el uso de características específicas de la plataforma.  ¿Tasky puede aprovechar las ventajas de perímetro de iOS o Windows Phone Live Tiles? Incluso si no usa características específicas de la plataforma en la primera versión, debe planear con antelación para asegurarse de que su negocio y capas de datos pueden adaptarse a ambos.

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>Diseño de la interfaz de usuario

Comience con un diseño de alto nivel que se pueden implementar en todas las plataformas de destino. Tenga cuidado a las restricciones de interfaz de usuario de nota específico de plataforma. Por ejemplo, un `TabBarController` en iOS puede mostrar más de cinco botones, mientras que el equivalente de Windows Phone puede mostrar hasta cuatro.
Dibuje el flujo de pantalla mediante la herramienta de su elección (papel funciona).

 [![](case-study-tasky-images/taskydesign.png "Dibuje el flujo de pantalla mediante la herramienta de los trabajos de papel elección")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>Modelo de datos

Saber qué datos deben almacenarse le ayudará a determinar qué mecanismo de persistencia que se utiliza. Vea [multiplataforma Data Access](~/cross-platform/app-fundamentals/index.md) para obtener información acerca de los mecanismos de almacenamiento disponible y ayuda para decidir entre ellos. Para este proyecto, usaremos SQLite.NET.

Tasky necesita almacenar tres propiedades para cada 'TaskItem':

 -  **Nombre de** : cadena
 -  **Notas de** : cadena
 -  **Realiza** : booleano

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>Funcionalidad principal

Considere la posibilidad de la API de la interfaz de usuario necesario que va a utilizar para cumplir los requisitos. Una lista de tareas requiere las siguientes funciones:

 -   **Enumerar todas las tareas** : para mostrar la lista de la pantalla principal de todas las tareas disponibles
 -  **Obtener una tarea** : cuando una fila de la tarea se toca
 -  **Guardar una tarea** : cuando se edita una tarea
 -  **Eliminar una tarea** : cuando se elimina una tarea
 -  **Crear tarea vacía** : cuando se crea una nueva tarea

Para lograr la reutilización del código, se debe implementar esta API una vez en el *biblioteca de clases Portable*.

 <a name="Implementation" />

### <a name="implementation"></a>Implementación

Una vez que se haya acordado el diseño de la aplicación, tenga en cuenta cómo se puede implementar como una aplicación multiplataforma. Esto se convertirá en la arquitectura de la aplicación. Las instrucciones de la [creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento, el código de la aplicación debe interrumpirse en profundidad en las siguientes partes:

 -   **El código común** : un proyecto común que contiene código puedan volver a utilizar para almacenar los datos de la tarea; expone una clase de modelo y una API para administrar el almacenamiento y la carga de datos.
 -   **Código específico de la plataforma** : proyectos para plataformas específicas que implementan una interfaz de usuario nativa para cada sistema operativo, utilizando el código común como 'back-end'.

 [![](case-study-tasky-images/taskypro-architecture.png "Proyectos específicos de plataforma implementan una interfaz de usuario nativa para cada sistema operativo, utilizando el código común como el back-end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Estas dos partes se describen en las secciones siguientes.

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>Código común (PCL)

Tasky Portable utiliza la estrategia de biblioteca de clases Portable para compartir código común. Consulte la [opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obtener una descripción de las opciones de uso compartido de código.

Todo el código común, incluyendo la capa de acceso a datos, código de base de datos y contratos, se coloca en el proyecto de biblioteca.

El proyecto PCL completo se muestra a continuación. Todo el código en la biblioteca portable es compatible con cada plataforma de destino. Cuando se implementa, cada aplicación nativa hará referencia a dicha biblioteca.

![](case-study-tasky-images/portable-project.png "Cuando se implementa, cada aplicación nativa hará referencia a dicha biblioteca")

El diagrama de clase siguiente muestra las clases agrupadas por capa. La `SQLiteConnection` clase es código reutilizable en el paquete de red de Sqlite. El resto de las clases son código personalizado para Tasky. El `TaskItemManager` y `TaskItem` clases que representan la API que se expone a las aplicaciones específicas de la plataforma.

 [![](case-study-tasky-images/classdiagram-core.png "Las clases TaskItemManager y TaskItem representan la API que se expone a las aplicaciones específicas de la plataforma")](case-study-tasky-images/classdiagram-core.png#lightbox)

Utilizar espacios de nombres para separar los niveles de ayuda a administrar las referencias entre las capas. Los proyectos específicos de plataforma solo deberían ser necesario incluir un `using` instrucción para la capa del negocio. La capa de acceso a datos y la capa de datos se deben encapsular por la API que se expone mediante `TaskItemManager` en la capa del negocio.

 <a name="References" />

### <a name="references"></a>Referencias

Bibliotecas de clases portables deben ser utilizable en varias plataformas, cada uno con distintos niveles de compatibilidad con las características de .NET framework y plataforma. Por eso, existen limitaciones en el que se pueden usar paquetes y bibliotecas de framework. Por ejemplo, Xamarin.iOS no es compatible con c# `dynamic` palabra clave, por lo que una biblioteca de clases portables no puede usar cualquier paquete que depende de código dinámico, aunque dicho código podría funcionar en Android. Visual Studio para Mac impedir agregar referencias y los paquetes incompatibles, pero desea limitaciones, tenga en cuenta para evitar las sorpresas más adelante.

Nota: Verá que los proyectos hacen referencia a las bibliotecas de framework que no has utilizado. Estas referencias se incluyen como parte de las plantillas de proyecto de Xamarin. Cuando se compilan aplicaciones, el proceso de vinculación quitará el código sin referencia, así que, aunque `System.Xml` ha sido al que hace referencia, no se incluirá en la aplicación final porque no estamos utilizando las funciones de Xml.

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>Capa de datos (DL)

La capa de datos contiene el código que realiza el almacenamiento físico de los datos; si para una base de datos, archivos sin formato u otro mecanismo. La capa de datos Tasky consta de dos partes: la biblioteca de red de SQLite y el código personalizado agregado al vincularla.

Tasky se basa en el paquete de nuget de Sqlite net (publicado por Frank Kreuger) para incrustar código SQLite NET que proporciona una interfaz de la base de datos de asignación relacional de objetos (ORM). El `TaskItemDatabase` clase hereda de `SQLiteConnection` y agrega los subdominios necesario creación, lectura, actualización, métodos de eliminación (CRUD) para leer y escribir datos en el código. Es una implementación simple reutilizable de métodos CRUD genéricos que se pueden volver a usar en otros proyectos.

El `TaskItemDatabase` es un singleton, asegurándose de que todo el acceso se realiza en la misma instancia. Un bloqueo se utiliza para evitar el acceso simultáneo de varios subprocesos.

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>SQLite en Windows Phone

Mientras iOS y Android se entregan con código como parte del sistema operativo, Windows Phone no incluye un motor de base de datos compatible. Para compartir código entre las tres plataformas se requiere una versión de Windows phone nativo de SQLite. Vea [trabajar con una base de datos Local](~/xamarin-forms/app-fundamentals/databases.md) para obtener más información acerca de cómo configurar el proyecto de Windows Phone de Sqlite.

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>Mediante una interfaz para generalizar el acceso a datos

La capa de datos tiene una dependencia en `BL.Contracts.IBusinessIdentity` para que puedan implementar métodos de acceso de datos abstracto que requieren una clave principal. Cualquier clase de capa de negocio que implementa la interfaz, a continuación, se conservan en la capa de datos.

La interfaz solo especifica una propiedad de entero para que actúe como la clave principal:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La clase base implementa la interfaz y agrega los atributos de SQLite NET para marcarla como una clave principal de incremento automático. Cualquier clase en el nivel de negocio que implementa esta clase base, a continuación, se conservan en la capa de datos:

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

#### <a name="locking-to-prevent-concurrent-access"></a>El bloqueo para evitar el acceso simultáneo

A [bloqueo](http://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) se implementa en el `TaskItemDatabase` clase para evitar el acceso simultáneo a la base de datos. Esto sirve para asegurarse de que se serializa un acceso simultáneo desde subprocesos diferentes (en caso contrario, un componente de interfaz de usuario puede intentar leer la base de datos a la vez que está actualizando un subproceso en segundo plano). A continuación se muestra un ejemplo de cómo se implementa el bloqueo:

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

La mayoría del código de capa de datos puede volver a utilizar en otros proyectos. El código solo específica de la aplicación de la capa es la `CreateTable<TaskItem>` llamar a en el `TaskItemDatabase` constructor.

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>Capa de acceso a datos (DAL)

El `TaskItemRepository` clase encapsula el mecanismo de almacenamiento de datos con una API fuertemente tipadas que permite `TaskItem` objetos va a crear, eliminar, recuperar y actualizar.

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>Compilación condicional

La clase usa la compilación condicional para establecer la ubicación del archivo; éste es un ejemplo de implementación de divergencia de plataforma. La propiedad que devuelve la ruta de acceso se compila a código diferente en cada plataforma. Aquí se muestran el código y las directivas de compilador específica de la plataforma:

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

La capa empresarial implementa las clases del modelo y una fachada para administrarlos.
En Tasky el modelo es el `TaskItem` clase y `TaskItemManager` implementa el modelo de fachada para proporcionar una API para administrar `TaskItems`.

 <a name="Façade" />

#### <a name="faade"></a>Fachada

 `TaskItemManager` ajusta el `DAL.TaskItemRepository` para proporcionar la instrucción Get, guardar y eliminar los métodos que se hará referencia por la capa de interfaz de usuario y la aplicación.

Lógica y reglas de negocios se colocarían aquí si es necesario, por ejemplo, las reglas de validación que deben cumplirse antes de que se guarda un objeto.

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>API para código específico de plataforma

Una vez que se ha escrito el código común, debe generarse la interfaz de usuario para recopilar y mostrar los datos expuestos por él. La `TaskItemManager` clase implementa el patrón de fachada para proporcionar una API simple para el código de aplicación para tener acceso.

El código escrito en cada proyecto específico de plataforma estarán generalmente estrechamente acopladas al SDK nativo de dichos dispositivos y solo tener acceso al código común mediante la API definida por el `TaskItemManager`. Esto incluye los métodos y empresariales las clases expone, como `TaskItem`.

Imágenes no se comparte entre plataformas pero agregar de manera independiente para cada proyecto. Esto es importante porque cada plataforma trata las imágenes de forma diferente, con diferentes nombres de archivo, directorios y resoluciones.

Las secciones restantes describen los detalles de implementación específica de la plataforma de la interfaz de usuario Tasky.

 <a name="iOS_App" />

## <a name="ios-app"></a>Aplicación iOS

Hay solo un conjunto de clases necesarias para implementar la aplicación Tasky con el proyecto PCL comunes para almacenar y recuperar datos de iOS. El proyecto de Xamarin.iOS iOS completo se muestra a continuación:

 ![](case-study-tasky-images/taskyios-solution.png "a continuación se muestra el proyecto de iOS")

En este diagrama, que se agrupan en las capas se muestran las clases.

 [![](case-study-tasky-images/classdiagram-android.png "Las clases se muestran en este diagrama, que se agrupan en capas")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referencias

La aplicación de iOS hace referencia a las bibliotecas SDK específicos de la plataforma – p. ej. Xamarin.iOS y MonoTouch.Dialog-1.

También debe hacer referencia el `TaskyPortableLibrary` proyecto PCL.
La lista de referencias se muestra aquí:

 ![](case-study-tasky-images/taskyios-references.png "Aquí se muestra la lista de referencias")

El nivel de aplicación y la capa de interfaz de usuario se implementan en este proyecto con estas referencias.

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Nivel de aplicación (AL)

El nivel de aplicación contiene clases específicas de la plataforma necesarias para los objetos expuestos por la PCL a la interfaz de usuario 'enlazar'. La aplicación específica de iOS consta de dos clases para ayudar a mostrar tareas:

 -   **EditingSource** : esta clase se utiliza para enlazar las listas de tareas a la interfaz de usuario. Dado que `MonoTouch.Dialog` se utilizó para la lista de tareas, es necesario implementar esta aplicación auxiliar para habilitar la funcionalidad de eliminación de deslice el dedo en el `UITableView` . Deslice el dedo para eliminar es habitual en iOS, pero no Android o Windows Phone, por lo que el proyecto específico de iOS es la única persona que lo implementa.
 -   **TaskDialog** : esta clase se utiliza para enlazar una sola tarea a la interfaz de usuario. Usa el `MonoTouch.Dialog` API de reflexión para 'wrap' el `TaskItem` objeto con una clase que contiene los atributos correctos para permitir que la pantalla de entrada se tienen el formato correcto.

El `TaskDialog` clase utiliza `MonoTouch.Dialog` atributos para crear una pantalla basados en Propiedades de la clase. La clase tiene el siguiente aspecto:

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

Observe el `OnTap` atributos requieren un nombre de método: estos métodos deben existir en la clase donde el `MonoTouch.Dialog.BindingContext` se crea (en este caso, el `HomeScreen` clase descrita en la sección siguiente).

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>Capa de interfaz de usuario (UI)

La capa de interfaz de usuario consta de las clases siguientes:

1.   **AppDelegate** – contiene las llamadas a la API de apariencia para definir el estilo de las fuentes y colores usados en la aplicación. Tasky es una sencilla aplicación por lo que no hay ningún otras tareas de inicialización que se ejecuta en `FinishedLaunching` .
2.   **Pantallas** – subclases de `UIViewController` que definen cada pantalla y su comportamiento. Unir pantallas de la interfaz de usuario con las clases de nivel de aplicación y la API comunes ( `TaskItemManager` ). En este ejemplo se crean las pantallas en el código, pero podría se han diseñado mediante el generador de interfaz de Xcode o el diseñador del guión gráfico.
3.   **Imágenes** : elementos visuales son una parte importante de cada aplicación. Tasky tiene imágenes pantalla y el icono de presentación, que para iOS, deben proporcionarse en normal y la resolución de la Retina.

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Pantalla principal

La pantalla de inicio es un `MonoTouch.Dialog` pantalla que muestra una lista de tareas desde la base de datos de SQLite. Hereda de `DialogViewController` e implementa código para establecer el `Root` para que contenga una colección de `TaskItem` objetos que desea mostrar.

 [![](case-study-tasky-images/ios-taskylist.png "Hereda de DialogViewController e implementa código para establecer la raíz para que contenga una colección de objetos TaskItem para su presentación")](case-study-tasky-images/ios-taskylist.png#lightbox)

Los dos métodos principales relacionados con la visualización e interacción con la lista de tareas son:

1.   **PopulateTable** : usa la capa de negocio `TaskManager.GetTasks` método para recuperar una colección de `TaskItem` objetos que se mostrarán.
2.   **Selecciona** : cuando una fila se toca, muestra la tarea en una nueva pantalla.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Pantalla de detalles de tarea

Detalles de la tarea es una pantalla de entrada que permite que las tareas pueden modificar ni eliminar.

Usa tasky `MonoTouch.Dialog`de API de reflexión para mostrar la pantalla, por lo que no hay ningún `UIViewController` implementación. En su lugar, el `HomeScreen` crea una instancia de clase y se muestra un `DialogViewController` mediante la `TaskDialog` clase a partir de la capa de aplicación.

Esta captura de pantalla muestra una pantalla vacía que muestra la `Entry` atributo establecimiento del texto de marca de agua en el **nombre** y **notas** campos:

 [![](case-study-tasky-images/ios-taskydetail.png "Esta captura de pantalla muestra una pantalla vacía que se muestra el atributo de entrada al establecer el texto de marca de agua en los campos nombre y notas")](case-study-tasky-images/ios-taskydetail.png#lightbox)

La funcionalidad de la **detalles de tarea** pantalla (por ejemplo, guardar o eliminar una tarea) debe implementarse en el `HomeScreen` de la clase, porque es donde el `MonoTouch.Dialog.BindingContext` se crea. El siguiente `HomeScreen` métodos admiten la pantalla de detalles de la tarea:

1.   **ShowTaskDetails** – crea un `MonoTouch.Dialog.BindingContext` para representar una pantalla. Crea la pantalla de entrada utilizando la reflexión para recuperar los nombres de propiedad y los tipos de la `TaskDialog` clase. Información adicional, como el texto de marca de agua para los cuadros de entrada, se implementa con los atributos en las propiedades.
2.   **SaveTask** : este método se hace referencia en el `TaskDialog` clase a través de un `OnTap` atributo. Se llama cuando **guardar** se presiona y usa un `MonoTouch.Dialog.BindingContext` para recuperar los datos introducidos por el usuario antes de guardar los cambios mediante `TaskItemManager` .
3.   **DeleteTask** : este método se hace referencia en el `TaskDialog` clase a través de un `OnTap` atributo. Usa `TaskItemManager` para eliminar los datos mediante la clave principal (propiedad de Id.).

 <a name="Android_App" />

## <a name="android-app"></a>Aplicación de Android

El proyecto Xamarin.Android completo se muestra a continuación:

 ![](case-study-tasky-images/taskyandroid-solution.png "Aquí se muestra el proyecto de Android")

El diagrama de clases, con las clases agrupadas por capa:

 [![](case-study-tasky-images/classdiagram-android.png "El diagrama de clases, con las clases agrupadas por capas")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referencias

El proyecto de aplicación de Android debe hacer referencia el ensamblado de Xamarin.Android específico de la plataforma para tener acceso a clases del SDK de Android.

También debe hacer referencia el proyecto PCL (p. ej. TaskyPortableLibrary) para obtener acceso al código de capa de datos y business comunes.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary para acceder al código de capa de datos y business comunes")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Nivel de aplicación (AL)

Similar a la versión de iOS que explicamos anteriormente, la capa de aplicación en la versión de Android contiene clases específicas de la plataforma necesarias para los objetos expuestos por el núcleo de la interfaz de usuario 'enlazar'.

 **TaskListAdapter** : para mostrar una lista<T> de objetos es necesario implementar un adaptador para mostrar los objetos personalizados de un `ListView`. El adaptador controla qué diseño se utiliza para cada elemento de la lista: en este caso, el código usa un diseño integrado Android `SimpleListItemChecked`.

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaz de usuario (UI)

Capa de interfaz de usuario de la aplicación Android es una combinación de código y marcado XML.

 -   **Diseño derecursos/** – diseños de pantalla y la fila de diseño que se implementan como archivos AXML de celda. Puede ser el AXML escrito a mano o dispuesto en visualmente mediante el Diseñador de la interfaz de usuario de Xamarin para Android.
 -   **Recursos/Drawable** – imágenes (iconos) y botón personalizado.
 -   **Pantallas** – subclases de actividad que definen cada pantalla y su comportamiento. Une la interfaz de usuario con las clases de nivel de aplicación y la API comunes (`TaskItemManager`).

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Pantalla principal

La pantalla Inicio está formada por una subclase de la actividad `HomeScreen` y `HomeScreen.axml` archivo que define el diseño (posición de la lista de tareas y el botón). La pantalla tiene el siguiente aspecto:

 [![](case-study-tasky-images/android-taskylist.png "La pantalla tiene el siguiente aspecto")](case-study-tasky-images/android-taskylist.png#lightbox)

El código de la pantalla principal define los controladores para hacer clic en el botón y haga clic en elementos de la lista, así como rellenar la lista en la `OnResume` método (por lo que TI refleja los cambios realizados en la pantalla de detalles de tarea). Datos se cargan con la capa de negocio `TaskItemManager` y `TaskListAdapter` de la capa de aplicación.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Pantalla de detalles de tarea

La pantalla de detalles de la tarea también consta de un `Activity` subclase y un archivo de diseño AXML. El diseño determina la ubicación de los controles de entrada y la clase de C# define el comportamiento para cargar y guardar `TaskItem` objetos.

 [![](case-study-tasky-images/android-taskydetail.png "La clase define el comportamiento para cargar y guardar objetos TaskItem")](case-study-tasky-images/android-taskydetail.png#lightbox)

Todas las referencias a la biblioteca PCL estén a través de la `TaskItemManager` clase.

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Aplicación de Windows Phone
El proyecto completo de Windows Phone:

 ![](case-study-tasky-images/taskywp7-solution.png "Aplicación de Windows Phone el proyecto completo de Windows Phone")

El diagrama siguiente muestra las clases que se agrupan en capas:

 [![](case-study-tasky-images/classdiagram-wp7.png "Este diagrama muestra las clases que se agrupan en capas")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>Referencias

El proyecto específico de plataforma debe hacer referencia a las bibliotecas de especificidad de plataforma necesarias (como `Microsoft.Phone` y `System.Windows`) para crear una aplicación de Windows Phone válida.

También debe hacer referencia el proyecto PCL (p. ej. `TaskyPortableLibrary`) para que se usen los `TaskItem` clase y base de datos.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary para utilizar la base de datos y los TaskItem (clase)")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Nivel de aplicación (AL)

Una vez más, al igual que con las versiones de Android y iOS, el nivel de aplicación consta de los elementos no visuales que ayudan a enlazar datos a la interfaz de usuario.

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

ViewModels ajustar datos en la PCL ( `TaskItemManager`) y la presenta de forma que puede ser utilizado por el enlace de datos de Silverlight y XAML. Este es un ejemplo de comportamiento específico de la plataforma (como se describe en el documento de aplicaciones multiplataforma).

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaz de usuario (UI)

XAML tiene una capacidad de enlace de datos única que se puede declarar en el marcado y reduce la cantidad de código necesario para mostrar los objetos:

1.   **Páginas** : archivos XAML y su código subyacente define la interfaz de usuario y hacer referencia a la ViewModels y el proyecto PCL para mostrar y recopilar datos.
2.   **Imágenes** – imágenes de icono, el fondo y la pantalla de presentación son una parte fundamental de la interfaz de usuario.

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

La clase MainPage utiliza el `TaskListViewModel` para mostrar los datos de uso de características de enlace de datos del XAML. La página `DataContext` se establece en el modelo de vista, que se rellena de forma asincrónica. El `{Binding}` sintaxis en el código XAML determina cómo se muestran los datos.

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

Cada tarea se muestra mediante el enlace de la `TaskViewModel` al XAML definido en el TaskDetailsPage.xaml. Los datos de la tarea se recuperan a través de la `TaskItemManager` en la capa del negocio.

 <a name="Results" />

## <a name="results"></a>Resultados

Las aplicaciones resultantes tiene este aspecto en cada plataforma:

 <a name="iOS" />

#### <a name="ios"></a>iOS

La aplicación usa el diseño de la interfaz de usuario de iOS estándar, por ejemplo, el botón 'Agregar' se coloca en la barra de navegación y el uso integrado **signo más (+)** icono. También usa el valor predeterminado `UINavigationController` botón "Atrás" comportamiento y admite deslice el dedo para eliminar de la tabla.

 [![](case-study-tasky-images/ios-taskylist.png "También utiliza el comportamiento del botón Atrás de UINavigationController predeterminado y admite deslice el dedo para eliminar en la tabla") ](case-study-tasky-images/ios-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/ios-taskylist.png "también usa el valor predeterminado UINavigationController copia el comportamiento del botón y admite deslice el dedo para eliminar en la tabla")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

La aplicación de Android usa controles integrados, incluido el diseño integrado para las filas que requieren una 'marca' de muestra. Se admite el comportamiento de retroceso/sistema de hardware además de un botón Atrás en la pantalla.

 [![](case-study-tasky-images/android-taskylist.png "Se admite el comportamiento de retroceso/sistema de hardware además de un botón Atrás en la pantalla")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "se admite el comportamiento de retroceso de hardware/sistema además una pantalla botón Atrás")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

La aplicación de Windows Phone utiliza el diseño estándar, rellenar la barra en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior de la aplicación.

 [![](case-study-tasky-images/wp-taskylist.png "La aplicación de Windows Phone usa el diseño estándar, rellenar la barra en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior de la aplicación") ](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/wp-taskylist.png "de Windows Phone de la aplicación utiliza el estándar diseño, rellenar la barra en la parte inferior de la pantalla en lugar de una barra de navegación en la parte superior de la aplicación")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Resumen

Este documento proporciona una explicación detallada de cómo se han aplicado los principios de diseño de la aplicación superpuesto a una aplicación simple para facilitar la reutilización de código de tres plataformas móviles: iOS, Android y Windows Phone.

Se describe el proceso que se utiliza para diseñar las capas de aplicación y se describe qué código &amp; funcionalidad se ha implementado en cada capa.

El código puede descargarse desde [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones multiplataforma (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Aplicación de ejemplo Portable tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
