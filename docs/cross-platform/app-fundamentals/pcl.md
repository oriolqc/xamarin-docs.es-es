---
title: Introducción a las bibliotecas de clases portables (PCL)
description: En este artículo se presenta los proyectos de biblioteca de clases portables (PCL) y le guía a través de la creación y consumo de los proyectos PCL en Visual Studio para Mac y Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: a1e7152ca0f24d1ac660742e97907ea9300df9a7
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666884"
---
# <a name="portable-class-libraries-pcl"></a>Bibliotecas de clases portables (PCL)

> [!WARNING]
> Bibliotecas de clases portables (PCL) se consideran en desuso en las versiones más recientes de Visual Studio.
> Aunque todavía puede abrir, editar y compilar las PCL, para los nuevos proyectos se recomienda usar [bibliotecas de .NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

Un componente clave de la creación de aplicaciones multiplataforma es poder compartir código entre varios proyectos específicos de plataforma. Sin embargo, esto se complica por el hecho de que a menudo usan un conjunto diferente de subcarpetas de la biblioteca de clases de Base (BCL) de .NET distintas plataformas y, por lo tanto, se generan a .NET Core Library perfil diferente. Esto significa que cada plataforma solo puede usar las bibliotecas de clases que tienen como destino el mismo perfil aparecerán exigir los proyectos de biblioteca de clases independiente para cada plataforma.

Existen tres enfoques principales para uso compartido de código que solucionan este problema: **proyectos de .NET Standard**, **proyectos de activos compartidos**, y **proyectos de biblioteca de clases portables (PCL)**.

- **Proyectos de .NET standard** son el método preferido para compartir código. NET, obtenga más información sobre [proyectos de .NET Standard y Xamarin](~/cross-platform/app-fundamentals/net-standard.md).
- **Los proyectos de activos compartidos** usar un único conjunto de archivos y ofrece una forma rápida y sencilla en la que se va a compartir el código dentro de una solución y generalmente emplea directivas de compilación condicional para especificar las rutas de código para varias plataformas que se usará (para obtener más información información, consulte el [artículo proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)).
- **PCL** proyectos apuntan a perfiles específicos que admiten un conjunto conocido de características o las clases BCL. Sin embargo, el lado negativo a PCL es que a menudo requieren esfuerzo extra de arquitectura para separar código específico de perfil en sus propias bibliotecas.

Esta página explica cómo crear un **PCL** proyecto destinado a un perfil específico, que, a continuación, puede hacer referencia a varios proyectos específicos de la plataforma.

## <a name="what-is-a-portable-class-library"></a>¿Qué es una biblioteca de clases portables?

Cuando se crea un proyecto de aplicación o un proyecto de biblioteca, está restringida a trabajar en la plataforma concreta que se creó para la DLL resultante. Esto evita tener que escribir un ensamblado para una aplicación de Windows y, a continuación, volver a usarlo en Xamarin.iOS y Xamarin.Android.

Sin embargo, cuando se crea una biblioteca de clases Portable, puede elegir una combinación de las plataformas que desea que el código para ejecutarse en. Las opciones de compatibilidad que elija al crear una biblioteca de clases Portable se traducen en un identificador de "Perfil", que describe qué plataformas es compatible con la biblioteca.

En la tabla siguiente muestra algunas de las características que varían según la plataforma. NET. Para escribir un ensamblado PCL que se garantiza que se ejecute en dispositivos y plataformas específicas basta con elegir qué soporte se requiere cuando se crea el proyecto.

|Característica|.NET Framework|Aplicaciones para UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Principal|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|Serialización|Y|Y|Y|Y|Y|
|Anotaciones de datos|4.0.3 +|Y|Y||Y|

La columna Xamarin refleja el hecho de que Xamarin.iOS y Xamarin.Android admite todos los perfiles incluidos con Visual Studio, y solo se limitará la disponibilidad de características en las bibliotecas creadas por las otras plataformas que elija para admitir.

Esto incluye los perfiles que son combinaciones de:

- .NET 4 o 4.5 de .NET
- Silverlight 5
- Windows Phone 8
- Aplicaciones para UWP

Puede leer más sobre las capacidades de los distintos perfiles en [sitio Web de Microsoft](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) y vea otro miembro de la Comunidad [resumen de perfiles PCL](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) que incluye información de framework y otras notas compatibles.

**Beneficios**

1. Uso compartido de código centralizados: escribir y probar el código en un único proyecto que puede utilizarse otras bibliotecas o aplicaciones.
2. Las operaciones de refactorización afectará a todo el código cargado en la solución (la biblioteca de clases Portable y los proyectos específicos de la plataforma).
3. El proyecto PCL se puedan encontrar fácilmente los otros proyectos en una solución o el ensamblado de salida se puede compartir para que otros usuarios a hacer referencia en sus soluciones.

**Desventajas**

1. Dado que la misma biblioteca de clases Portable se comparte entre varias aplicaciones, no pueden hacer referencia a bibliotecas específicas de plataforma (p ej. Community.CsharpSqlite.WP7).
2. El subconjunto de la biblioteca de clases Portable no puede incluir las clases que podrían estar disponibles en MonoTouch y Mono para Android (por ejemplo, DllImport o System.IO.File).

> [!NOTE]
> Bibliotecas de clases portables han quedado obsoletas en la versión más reciente de Visual Studio, y [bibliotecas estándar de .NET](net-standard.md) se recomiendan en su lugar.

Hasta cierto punto, ambos desventajas pueden evitarse mediante el patrón de proveedor o la inserción de dependencias para código de la implementación real en los proyectos de plataforma con una interfaz o clase base que se define en la biblioteca de clases Portable.

Este diagrama muestra la arquitectura de una aplicación multiplataforma usando una biblioteca de clases Portable para compartir código, pero también mediante la inserción de dependencia para pasar las características dependientes de la plataforma:

[![](pcl-images/image1.png "Este diagrama muestra la arquitectura de una aplicación multiplataforma usando una biblioteca de clases Portable para compartir código, pero también mediante la inserción de dependencia para pasar las características dependientes de la plataforma")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio para Mac tutorial

Esta sección se explica cómo crear y usar una biblioteca de clases Portable con Visual Studio para Mac. Consulte la sección de ejemplo de PCL para una implementación completa.

### <a name="creating-a-pcl"></a>Creación de una PCL

Agregar una biblioteca de clases Portable en la solución es muy similar a la adición de un proyecto de biblioteca normal.

1. En el **nuevo proyecto** cuadro de diálogo Seleccionar el **Multiplatform > biblioteca > biblioteca Portable** opción:

    ![Cree un nuevo proyecto PCL](pcl-images/image2.png)

1. Cuando se crea una PCL en Visual Studio para Mac se configura automáticamente con un perfil que funciona para Xamarin.iOS y Xamarin.Android. El proyecto PCL aparecerá como se muestra en esta captura de pantalla:

    ![Proyecto PCL en el panel de solución](pcl-images/image3.png)

La PCL ahora está lista para el código que se va a agregar. También pueden hacer referencia otros proyectos (proyectos de aplicación, los proyectos de biblioteca e incluso otros proyectos PCL).

### <a name="editing-pcl-settings"></a>Editar la configuración de PCL

Para ver y cambiar la configuración de PCL para este proyecto, haga clic en el proyecto y elija **Opciones > compilar > General** para ver la pantalla se muestra aquí:

[![Opciones de proyecto de PCL para establecer el perfil](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

Haga clic en **cambios...**  para modificar el perfil de destino para esta biblioteca de clases portable.

Si se cambia el perfil después de código ya se agregó a la PCL, es posible que la biblioteca ya no se compilará si el código hace referencia a las características que no forman parte del perfil recién seleccionado.

## <a name="working-with-a-pcl"></a>Trabajar con una PCL

Cuando se escribe código en una biblioteca PCL, el editor Visual Studio para Mac reconocerá las limitaciones del perfil seleccionado y ajustar las opciones de Autocompletar en consecuencia. Por ejemplo, esta captura de pantalla muestra las opciones de Autocompletar para System.IO utilizando el perfil predeterminado (Profile136) que se usan en Visual Studio para Mac: tenga en cuenta la barra de desplazamiento que indica que se muestran aproximadamente la mitad de las clases disponibles (de hecho hay solo 14 clases disponibles).

[![Lista de IntelliSense de 14 clases de la clase System.IO de una PCL](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

Comparar que con las clases de System.IO Autocompletar en un proyecto de Xamarin.iOS o Xamarin.Android: hay 40 clases incluidas disponible normalmente usan clases como `File` y `Directory` que no están en ningún perfil de PCL.

[![Lista de IntelliSense de 40 clases del espacio de nombres System.IO de .NET Framework](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

Esto refleja el equilibrio subyacente del uso de PCL: la capacidad para compartir código a la perfección en muchas plataformas significa que ciertas API no está disponible porque no tienen implementaciones comparables en todas las plataformas posibles.

### <a name="using-pcl"></a>Uso de PCL

Una vez que se ha creado un proyecto PCL, puede agregar una referencia a él desde cualquier aplicación compatible o biblioteca de proyectos de la misma manera normalmente agregar referencias. En Visual Studio para Mac, haga doble clic en el nodo referencias y elija **editar referencias...**  , a continuación, cambie a la **proyectos** pestaña tal como se muestra:

[![Agregue una referencia a una PCL a través de la opción de editar referencias](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

Captura de pantalla siguiente muestra el panel de solución para la aplicación de ejemplo TaskyPortable, que muestra la biblioteca PCL en la parte inferior y una referencia a esa biblioteca PCL en el proyecto de Xamarin.iOS.

[![Proyecto de PCL de TaskyPortable ejemplo soluciones que muestra](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

El resultado de una PCL (ie. la DLL del ensamblado resultante) también pueden agregarse como una referencia a la mayoría de los proyectos. Esto hace que PCL una forma ideal de enviar las bibliotecas y los componentes de desarrollo multiplataforma.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Tutorial de Visual Studio

Esta sección se explica cómo crear y usar una biblioteca de clases Portable con Visual Studio. Consulte la sección de ejemplo de PCL para una implementación completa.

### <a name="creating-a-pcl"></a>Creación de una PCL

Adición de una PCL a la solución en Visual Studio es ligeramente diferente a la adición de un proyecto normal:

1. En el **Agregar nuevo proyecto** pantalla, seleccione el **biblioteca de clases (portátil heredada)** opción. Tenga en cuenta que este tipo de proyecto en desuso, aconseja la descripción de la derecha.

    [![Ventana nuevo proyecto para crear una biblioteca de clases portable](pcl-images/image10-sml.png "biblioteca de clases Portable")](pcl-images/image10.png#lightbox)

2. Visual Studio le pedirá inmediatamente con el siguiente cuadro de diálogo para que se puede configurar el perfil.
 Marque las plataformas que necesita para admitir y haga clic en Aceptar.

    [![Seleccione las plataformas de destino para la biblioteca](pcl-images/image11-sml.png "las plataformas que necesita para admitir y haga clic en Aceptar de graduación")](pcl-images/image11.png#lightbox)

3. El proyecto PCL aparecerá como se muestra en el Explorador de soluciones &ndash; el texto **(Portable)** aparece junto al nombre del proyecto para indicar es una PCL:

    ![NET Framework definidas por el perfil PCL](pcl-images/image12.png "NET Framework definidas por el perfil PCL")

La PCL ahora está lista para el código que se va a agregar. También pueden hacer referencia otros proyectos (proyectos de aplicación, los proyectos de biblioteca e incluso otros proyectos PCL).

### <a name="editing-pcl-settings"></a>Editar la configuración de PCL

Se puede ver y cambiar con el botón secundario en el proyecto y elija la configuración de PCL **Propiedades > biblioteca** , tal y como se muestra en esta captura de pantalla:

[![Editar las plataformas de destino](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

Si se cambia el perfil después de código ya se agregó a la PCL, es posible que la biblioteca ya no se compilará si el código hace referencia a las características que no forman parte del perfil recién seleccionado.

> [!TIP]
> También hay un mensaje que advierte que **. NETStandard es el método recomendado para compartir código**. Esto es una indicación de que, aunque las PCL, siguen siendo compatibles, se recomienda actualizar a .NET Standard.

### <a name="working-with-a-pcl"></a>Trabajar con una PCL

Cuando se escribe código en una biblioteca PCL, Visual Studio reconocerá las limitaciones del perfil seleccionado y ajustar las opciones de Intellisense en consecuencia. Por ejemplo, esta captura de pantalla muestra las opciones de Autocompletar para System.IO utilizando el perfil predeterminado (Profile136): observe la barra de desplazamiento que indica que se muestran aproximadamente la mitad de las clases disponibles (de hecho existen solo 14 clases disponibles).

[![Reducción del número de clases de E/S disponibles en una PCL](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

Comparar que con las clases de System.IO Autocompletar en un proyecto normal: hay 40 clases incluidas disponible normalmente usan clases como `File` y `Directory` que no están en ningún perfil de PCL.

[![Muchas clases más E/S disponibles en .NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

Esto refleja el equilibrio subyacente del uso de PCL: la capacidad para compartir código a la perfección en muchas plataformas significa que ciertas API no está disponible porque no tienen implementaciones comparables en todas las plataformas posibles.

> [!TIP]
> .NET standard 2.0 representa un API área expuesta mucho mayor que las PCL, incluido el espacio de nombres System.IO. Para proyectos nuevos, .NET Standard es preferible a PCL.

### <a name="using-pcl"></a>Uso de PCL

Una vez que se ha creado un proyecto PCL, puede agregar una referencia a él desde cualquier aplicación compatible o biblioteca de proyectos de la misma manera normalmente agregar referencias. En Visual Studio, haga doble clic en el nodo referencias y elija `Add Reference...` , a continuación, cambie a la **solución > proyectos** pestaña tal como se muestra:

[![Agregue una referencia a una PCL a través de la pestaña agregar proyectos de referencia](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

Captura de pantalla siguiente muestra el panel de solución para la aplicación de ejemplo TaskyPortable, que muestra la biblioteca PCL en la parte inferior y una referencia a esa biblioteca PCL en el proyecto de Xamarin.iOS.

[![Solución de ejemplo TaskyPortable que muestra una biblioteca PCL](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

El resultado de una PCL (ie. la DLL del ensamblado resultante) también pueden agregarse como una referencia a la mayoría de los proyectos.
Esto hace que PCL una forma ideal de enviar las bibliotecas y los componentes de desarrollo multiplataforma.

-----

## <a name="pcl-example"></a>Ejemplo PCL

El [TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/) aplicación de ejemplo muestra cómo se puede usar una biblioteca de clases Portable con Xamarin.
Estas son algunas capturas de pantalla de las aplicaciones resultantes que se ejecutan en iOS y Android:

[![](pcl-images/image18.png "Estas son algunas capturas de pantalla de las aplicaciones resultantes que se ejecutan en iOS, Android y Windows Phone")](pcl-images/image18.png#lightbox)

Comparte una serie de clases de datos y la lógica que son código portable puramente y también muestra cómo incorporar los requisitos específicos de la plataforma mediante la inserción de dependencias para la implementación de la base de datos de SQLite.

A continuación se muestra la estructura de solución (en Visual Studio para Mac y Visual Studio, respectivamente):

[![](pcl-images/image19.png "La estructura de solución se muestra aquí en Visual Studio para Mac y Visual Studio, respectivamente")](pcl-images/image19.png#lightbox)

Dado que el código de SQLite-NET tiene partes específicas de la plataforma (para trabajar con las implementaciones de SQLite en cada sistema operativo diferente) para la demostración de efectos se ha refactorizado en un abstracto de clases que se puede compilar en una biblioteca de clases Portable, y el código real que se implementan como subclases en los proyectos de Android y iOS.

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

La biblioteca de clases Portable está limitada en las características de .NET que puede admitir. Puesto que se compila para ejecutarse en varias plataformas, no se puede hacer uso de `[DllImport]` funcionalidad que se usa en SQLite-NET. En su lugar SQLite-NET se implementa como una clase abstracta y, a continuación, hace referencia a través del resto del código compartido. A continuación se muestra un extracto de la API abstracto:

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

El resto del código compartido, usa la clase abstracta "almacenar" y "recuperar" objetos de la base de datos. En cualquier aplicación que usa esta clase abstracta, debemos pasar en una implementación completa que proporciona la funcionalidad de la base de datos real.

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid y TaskyiOS

Los proyectos de aplicación de Android y iOS contienen la interfaz de usuario y otro código específico de la plataforma utilizado para el código compartido en la PCL de seguridad de conexión.

Estos proyectos también contienen una implementación de la base de datos abstracto API que funciona en esa plataforma. En iOS y Android el Sqlite motor de base de datos está integrado en el sistema operativo, por lo que puede usar la implementación `[DllImport]` tal como se muestra para proporcionar la implementación concreta de conectividad de base de datos. Aquí se muestra un extracto de código de la implementación específica de la plataforma:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

La implementación completa se puede ver en el código de ejemplo.

## <a name="summary"></a>Resumen

En este artículo brevemente tiene tratan las ventajas e inconvenientes de las bibliotecas de clases portables, se muestra cómo crear y consumir PCL desde dentro de Visual Studio para Mac y Visual Studio; y, por último, introdujo una aplicación de ejemplo completo: TaskyPortable: que muestra una PCL en acción.

## <a name="related-links"></a>Vínculos relacionados

- [TaskyPortable (ejemplo)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Portable Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Desarrollo multiplataforma con .NET Framework (Microsoft)](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
