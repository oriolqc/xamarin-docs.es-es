---
title: Introducción a las bibliotecas de clases portables
description: Este artículo presenta los proyectos de biblioteca de clases portables (PCL) y le guía a través de la creación y uso de proyectos PCL en Visual Studio para Mac y Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: d80a4125d7447b19f001c349aff006dc4744f4a6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919051"
---
# <a name="introduction-to-portable-class-libraries"></a>Introducción a las bibliotecas de clases portables

_Este artículo presenta los proyectos de biblioteca de clases portables (PCL) y le guía a través de la creación y uso de proyectos PCL en Visual Studio para Mac y Visual Studio._


Un componente clave de la creación de aplicaciones multiplataforma es poder compartir código entre varios proyectos para plataformas específicas. Sin embargo, esto se complica por el hecho de que a menudo usan un conjunto diferente de subcarpetas de la biblioteca de clases de Base (BCL) de .NET distintas plataformas y, por lo tanto, se generan para un perfil de biblioteca de núcleo de .NET distinto. Esto significa que cada plataforma solo puede utilizar bibliotecas de clases que tienen como destino el mismo perfil, por lo que aparecen solicitar los proyectos de biblioteca de clases independiente para cada plataforma.

Existen tres maneras principales para uso compartido de código que solucionan este problema: **proyectos .NET estándar**, **proyectos de biblioteca de clases portables (PCL)**, y **proyectos de recurso compartido**.

- **Proyectos de .NET estándar** [.NET estándar](~/cross-platform/app-fundamentals/net-standard.md).
-  **PCL** perfiles específicos que admiten un conjunto conocido de las clases BCL/características de destino de los proyectos. Sin embargo, el lado hacia abajo para PCL es que a menudo requiere esfuerzo adicional arquitectura separar código específico de perfil en sus propias bibliotecas. Para obtener una explicación más detallada sobre estos dos enfoques, consulte el [opciones de uso compartido de código guía](~/cross-platform/app-fundamentals/code-sharing.md) .
-  **Proyectos activos compartidos** usar un único conjunto de archivos y ofrece una forma rápida y sencilla en la que se va a compartir el código dentro de una solución y generalmente utiliza directivas de compilación condicional para especificar las rutas de código para varias plataformas que se usarán (para obtener más información información, consulte el [artículo proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) y [configurar una guía de solución de Xamarin multiplataforma](~/cross-platform/app-fundamentals/code-sharing.md) ).


Esta página explica cómo crear un **PCL** proyecto que tenga como destino un perfil específico, que, a continuación, hacer referencia a varios proyectos para plataformas específicas.


## <a name="what-is-a-portable-class-library"></a>¿Qué es una biblioteca de clases Portable?

Cuando se crea un proyecto de aplicación o un proyecto de biblioteca, está restringida para que funcione en la plataforma concreta para que se ha creado el archivo DLL resultante. Esto evita tener que escribir un ensamblado para una aplicación de Windows y, a continuación, volver a usarlo en Xamarin.iOS y Xamarin.Android.

Sin embargo, cuando se crea una biblioteca de clases portables, puede elegir una combinación de las plataformas que desea que su código para que se ejecute. Las opciones de compatibilidad que elegir al crear una biblioteca de clases Portable se traducen a un identificador de "Perfil", que describe qué plataformas es compatible con la biblioteca.

La siguiente tabla muestra algunas de las características que varían según la plataforma. NET. Para escribir un ensamblado PCL que se ejecuta en plataformas de dispositivos específicas siempre elegir que admiten es necesario cuando se crea el proyecto.

|Característica|.NET Framework|Aplicaciones para UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Principal|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|Serialización|Y|Y|Y|Y|Y|
|Anotaciones de datos|4.0.3 +|Y|Y||Y|

La columna Xamarin refleja el hecho de que Xamarin.iOS y Xamarin.Android es compatible con todos los perfiles incluidos con Visual Studio y la disponibilidad de características en las bibliotecas que cree sólo se verá limitada por las otras plataformas que elija para admitir.

Esto incluye perfiles que son combinaciones de:

-  .NET 4 o .NET 4.5
-  Silverlight 5
-  Windows Phone 8
-  Aplicaciones para UWP

Puede leer más acerca de las capacidades de los perfiles diferentes en [sitio Web de Microsoft](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) y ver otro miembro de la Comunidad [resumen del perfil PCL](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) que incluye información del marco de trabajo y otras notas compatibles.



Crear una PCL para compartir el código tiene una serie de ventajas y desventajas en comparación con la alternativa de vinculación de archivo:


**Beneficios**

1. Uso compartido del código centralizada: escribir y probar el código en un proyecto único que puede ser utilizado por otras bibliotecas o aplicaciones.
1. Las operaciones de refactorización afectará a todo el código cargado en la solución (la biblioteca de clases Portable y los proyectos específicos de plataforma).
1. El proyecto PCL puede hacer referencia fácilmente en otros proyectos de una solución o el ensamblado de salida se puede compartir para que otros usuarios hacer referencia en sus soluciones.


**Desventajas**

1. Dado que la misma biblioteca de clases Portable se comparte entre varias aplicaciones, bibliotecas específicas de plataforma no puede hacer referencia (p. ej. Community.CsharpSqlite.WP7).
1. El subconjunto de la biblioteca de clases Portable no puede incluir las clases que estarían disponibles en MonoTouch y Mono para Android (por ejemplo, DllImport o System.IO.File).



Hasta cierto punto dos desventajas pueden evitarse mediante el patrón de proveedor o la inserción de dependencias para código de la implementación real en los proyectos de plataforma en una interfaz o clase base que se define en la biblioteca de clases Portable.



Este diagrama muestra la arquitectura de una aplicación multiplataforma mediante una biblioteca de clases Portable para compartir código, sino también mediante inserción de dependencias para pasar de características depende de la plataforma:



[![](pcl-images/image1.png "Este diagrama muestra la arquitectura de una aplicación multiplataforma mediante una biblioteca de clases Portable para compartir código, sino también mediante inserción de dependencias para pasar de características depende de la plataforma")](pcl-images/image1.png#lightbox)



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Tutorial de Visual Studio para Mac


Esta sección se explica cómo crear y utilizar una biblioteca de clases Portable con Visual Studio para Mac. Consulte la sección de ejemplo de PCL para una implementación completa.



### <a name="creating-a-pcl"></a>Crear una PCL


Agregar una biblioteca de clases Portable a la solución es muy similar a la adición de un proyecto de biblioteca normal.


1. En el cuadro de diálogo nuevo proyecto, seleccione el `Multiplatform > Library > Portable Library` opción


    ![](pcl-images/image2.png "Multiplataforma > biblioteca > biblioteca Portable")


1. Cuando se crea una PCL en Visual Studio para Mac se configura automáticamente con un perfil que funciona con Xamarin.iOS y Xamarin.Android. El proyecto PCL aparecerá como se muestra en esta captura de pantalla:

    ![](pcl-images/image3.png "El proyecto PCL aparecerá como se muestra en esta captura de pantalla")

La PCL ahora está lista para el código va a agregar. También pueden hacer referencia otros proyectos (proyectos de aplicaciones, proyectos de biblioteca e incluso otros proyectos PCL).



### <a name="editing-pcl-settings"></a>Editar configuración de PCL


Para ver y cambiar la configuración de PCL para este proyecto, haga clic en el proyecto y elija **Opciones > compilar > General** para ver la pantalla se muestra aquí:



[![](pcl-images/image4.png "Para ver y cambiar la configuración de PCL para este proyecto, haga clic en el proyecto y elija Opciones de compilación General para ver la pantalla que se muestra aquí")](pcl-images/image4.png#lightbox)



Las opciones en esta pantalla controlan qué plataformas es compatible con esta PCL determinado. Si se cambia cualquiera de estas opciones, modifica el perfil usado por este PCL, que a su vez controla qué características se pueden usar en el código portable.



Si se cambia cualquiera de los `Target Framework` opciones actualiza automáticamente el `Current Profile`; la pantalla también mostrará una advertencia si se seleccionan opciones incompatibles.



[![](pcl-images/image5.png "Cambiar cualquiera de las opciones de .NET Framework de destino automáticamente, actualiza el perfil actual de la pantalla también mostrará una advertencia si se seleccionan opciones incompatibles")](pcl-images/image5.png#lightbox)



Si se cambia después de que ya se ha añadido código en la PCL, es posible que la biblioteca ya no se compilará si el código hace referencia a características que no forman parte del perfil recién seleccionado.


## <a name="working-with-a-pcl"></a>Trabajar con una PCL


Cuando se escribe código en una biblioteca PCL, Visual Studio para el editor de Mac reconocerá las limitaciones del perfil seleccionado y ajustar las opciones de Autocompletar en consecuencia. Por ejemplo, esta captura de pantalla muestra las opciones de Autocompletar para System.IO utilizando el perfil predeterminado (Profile136) que se usa en Visual Studio para Mac: tenga en cuenta la barra de desplazamiento que indica aproximadamente la mitad de las clases disponibles se muestran (de hecho hay solo 14 clases disponibles).



[![](pcl-images/image6.png "E/S utilizando el perfil predeterminado Profile136 usa en Visual Studio para la barra de desplazamiento que indica aproximadamente la mitad de las clases disponibles aparecen en realidad solo están 14 clases disponibles de aviso de Mac")](pcl-images/image6.png#lightbox)



Comparar que con la System.IO Autocompletar de un proyecto Xamarin.iOS o Xamarin.Android: hay 40 clases incluidas disponible normalmente usan clases como `File` y `Directory` que no están en ningún perfil de PCL.



[![](pcl-images/image7.png "40 clases incluidas disponible normalmente usan clases como archivos y directorios que no están en ningún perfil de PCL")](pcl-images/image7.png#lightbox)



Esto refleja las ventajas y desventajas subyacente del uso de PCL: significa que la capacidad de compartir código sin problemas en muchas plataformas de ciertas API no está disponible porque no tienen implementaciones comparables en todas las plataformas posibles.



### <a name="using-pcl"></a>Uso de PCL


Una vez que se ha creado un proyecto PCL, puede agregar una referencia a él desde cualquier aplicación compatible o biblioteca de proyectos de la misma manera normalmente agregar referencias. En Visual Studio para Mac, haga doble clic en el nodo referencias y elija `Edit References…` , a continuación, cambie a la ficha proyectos tal como se muestra:



[![](pcl-images/image8.png "En Visual Studio para Mac, haga doble clic en el nodo referencias y elija Editar referencias, a continuación, cambie a la ficha de proyectos, como se muestra")](pcl-images/image8.png#lightbox)



Captura de pantalla siguiente muestra el panel de la solución para la aplicación de ejemplo TaskyPortable, que muestra la biblioteca PCL, en la parte inferior y una referencia a dicha biblioteca PCL en el proyecto Xamarin.iOS.



[![](pcl-images/image9.png "El panel de la solución para la aplicación de ejemplo TaskyPortable")](pcl-images/image9.png#lightbox)



El resultado de una PCL (ie. la DLL del ensamblado resultante) también puede agregarse como una referencia a la mayoría de los proyectos. Esto hace que una forma ideal de distribuir componentes entre plataformas y bibliotecas PCL.




# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)




## <a name="visual-studio-walkthrough"></a>Tutorial de Visual Studio


Esta sección le guía por el proceso de crear y utilizar una biblioteca de clases Portable con Visual Studio. Consulte la sección de ejemplo de PCL para una implementación completa.



### <a name="creating-a-pcl"></a>Crear una PCL


Agregar una PCL a la solución en Visual Studio es ligeramente diferente a la adición de un proyecto normal.



1. En la pantalla Agregar nuevo proyecto, seleccione el `Portable Class Library` opción


    ![](pcl-images/image10.png "Biblioteca de clases Portable")


1. Visual Studio le pedirá inmediatamente con el siguiente cuadro de diálogo para que se puede configurar el perfil.
 Active la casilla de las plataformas que necesita para admitir y haga clic en Aceptar.


    ![](pcl-images/image11.png "Marque las plataformas que necesita para admitir y haga clic en Aceptar")


1. El proyecto PCL aparecerá como se muestra en el Explorador de soluciones. El nodo de referencias se indicará que la biblioteca utiliza un subconjunto de .NET Framework (definida por el perfil PCL).

    ![](pcl-images/image12.png "NET Framework definidas por el perfil PCL")

La PCL ahora está lista para el código va a agregar. También pueden hacer referencia otros proyectos (proyectos de aplicaciones, proyectos de biblioteca e incluso otros proyectos PCL).



### <a name="editing-pcl-settings"></a>Editar configuración de PCL


Se pueden ver y cambiar, haga doble clic en el proyecto y elija la configuración de PCL **Propiedades > biblioteca** , tal y como se muestra en esta captura de pantalla:



[![](pcl-images/image13.png "La configuración de PCL se pueden ver y cambiar, haga doble clic en el proyecto y elija Propiedades biblioteca, como se muestra en esta captura de pantalla")](pcl-images/image13.png#lightbox)



Si se cambia después de que ya se ha añadido código en la PCL, es posible que la biblioteca ya no se compilará si el código hace referencia a características que no forman parte del perfil recién seleccionado.



### <a name="working-with-a-pcl"></a>Trabajar con una PCL


Cuando se escribe código en una biblioteca PCL, Visual Studio reconoce las limitaciones del perfil seleccionado y ajustar las opciones de Intellisense en consecuencia. Por ejemplo, esta captura de pantalla muestra las opciones de Autocompletar para System.IO utilizando el perfil predeterminado (Profile136): tenga en cuenta la barra de desplazamiento que indica aproximadamente la mitad de las clases disponibles se muestran (de hecho hay solo 14 clases disponibles).



[![](pcl-images/image14.png "E/S utilizando el perfil predeterminado Profile136")](pcl-images/image14.png#lightbox)



Comparar que con la System.IO Autocompletar en un proyecto normal: hay 40 clases incluidas disponible normalmente usan clases como `File` y `Directory` que no están en ningún perfil de PCL.



[![](pcl-images/image15.png "Autocompletar en un proyecto normal")](pcl-images/image15.png#lightbox)



Esto refleja las ventajas y desventajas subyacente del uso de PCL: significa que la capacidad de compartir código sin problemas en muchas plataformas de ciertas API no está disponible porque no tienen implementaciones comparables en todas las plataformas posibles.



### <a name="using-pcl"></a>Uso de PCL


Una vez que se ha creado un proyecto PCL, puede agregar una referencia a él desde cualquier aplicación compatible o biblioteca de proyectos de la misma manera normalmente agregar referencias. En Visual Studio, haga doble clic en el nodo referencias y elija `Add Reference...` , a continuación, cambie a la **solución: proyectos** pestaña tal como se muestra:



[![](pcl-images/image16.png "Ficha de proyectos, como se muestra")](pcl-images/image16.png#lightbox)



Captura de pantalla siguiente muestra el panel de la solución para la aplicación de ejemplo TaskyPortable, que muestra la biblioteca PCL, en la parte inferior y una referencia a dicha biblioteca PCL en el proyecto Xamarin.iOS.



[![](pcl-images/image17.png "El panel de la solución para la aplicación de ejemplo TaskyPortable")](pcl-images/image17.png#lightbox)



El resultado de una PCL (ie. la DLL del ensamblado resultante) también puede agregarse como una referencia a la mayoría de los proyectos.
Esto hace que una forma ideal de distribuir componentes entre plataformas y bibliotecas PCL.




-----



## <a name="pcl-example"></a>Ejemplo PCL


El [TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/) aplicación de ejemplo muestra cómo se puede utilizar una biblioteca de clases Portable con Xamarin.
Estas son algunas capturas de pantalla de las aplicaciones resultantes que se ejecutan en iOS, Android y Windows Phone:



[![](pcl-images/image18.png "Estas son algunas capturas de pantalla de las aplicaciones resultantes que se ejecutan en iOS, Android y Windows Phone")](pcl-images/image18.png#lightbox)



Comparte una serie de clases de datos y la lógica que son puramente portable código y también muestra cómo incorporar requisitos específicos de la plataforma mediante la inserción de dependencia para la implementación de la base de datos de SQLite.




A continuación se muestra la estructura de la solución (en Visual Studio para Mac y Visual Studio respectivamente):



[![](pcl-images/image19.png "La estructura de la solución se muestra aquí en Visual Studio para Mac y Visual Studio respectivamente")](pcl-images/image19.png#lightbox)



Dado que el código de SQLite NET tiene partes específicas de la plataforma (para trabajar con las implementaciones de SQLite en cada sistema operativo diferente) para la demostración con fines se ha refactorizado en un resumen de la clase que se puede compilar en una biblioteca de clases Portable, y el código real que se implementan como subclases iOS y Android proyectos.



### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

La biblioteca de clases Portable está limitada en las características de .NET que puede admitir. Puesto que se compila para ejecutarse en varias plataformas, no puede hacer uso de `[DllImport]` funcionalidad que se usa en la red de SQLite. En su lugar SQLite NET se implementa como una clase abstracta y, a continuación, al que hace referencia a través del resto del código compartido. A continuación se muestra un extracto de la API abstracta:


```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```


El resto del código compartido, usa la clase abstracta para "store" y "recuperar" objetos de la base de datos. En cualquier aplicación que utilice esta clase abstracta que debemos pasamos en una implementación completa que proporciona la funcionalidad de base de datos real.



### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid y TaskyiOS


Los proyectos de aplicación de Android y iOS contienen la interfaz de usuario y otro código específico de la plataforma usado en el cable telefónico al código compartido en la PCL.



Estos proyectos también contienen una implementación de la base de datos abstracto API que funciona en esa plataforma. En iOS y Android el Sqlite motor de base de datos está integrado en el sistema operativo, por lo que puede usar la implementación `[DllImport]` tal como se muestra para proporcionar la implementación concreta de conectividad de base de datos. A continuación se muestra un extracto del código de implementación específica de la plataforma:


```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```


La implementación completa se puede ver en el código de ejemplo.

### <a name="taskywinphone"></a>TaskyWinPhone


La aplicación de Windows Phone tiene su interfaz de usuario integrada con XAML y contiene otro código específico de la plataforma para conectar los objetos compartidos con la interfaz de usuario.



A diferencia de la implementación en el que se usa para iOS y Android, debe crear y usar una instancia de la aplicación de Windows Phone la `Community.Sqlite.dll` como parte de su resumen de la API de base de datos. En lugar de usar `DllImport`, se implementan métodos como abierto en el ensamblado Community.Sqlite que se hace referencia en el `TaskWinPhone` proyecto. Se muestra un extracto aquí, comparación con iOS y Android versión anterior


```csharp
public static Result Open(string filename, out Sqlite3.sqlite3 db)
{
    db = new Sqlite3.sqlite3();
    return (Result)Sqlite3.sqlite3_open(filename, ref db);
}

public static Result Close(Sqlite3.sqlite3 db)
{
    return (Result)Sqlite3.sqlite3_close(db);
}
```


## <a name="summary"></a>Resumen


En este artículo brevemente tiene explica las ventajas e inconvenientes de bibliotecas de clases Portable, se ha mostrado cómo crear y consumir PCLs desde dentro de Visual Studio para Mac y Visual Studio; y finalmente introdujo una aplicación de ejemplo completo – TaskyPortable – que muestra una PCL en acción.


## <a name="related-links"></a>Vínculos relacionados

- [TaskyPortable (ejemplo)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Portable Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opciones de código compartido](~/cross-platform/app-fundamentals/code-sharing.md)
- [El desarrollo multiplataforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
