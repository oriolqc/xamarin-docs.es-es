---
title: Trabajar con vistas de tabla en Xamarin de tvOS
description: Este artículo trata el diseño y trabajar con vistas de tabla y los controladores de vista de tabla dentro de una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: a8b31d9f2e9c8921420b1fd50c4092273962f54b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105901"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>Trabajar con vistas de tabla en Xamarin de tvOS

_Este artículo trata el diseño y trabajar con vistas de tabla y los controladores de vista de tabla dentro de una aplicación Xamarin.tvOS._

En tvOS, se presenta una vista de tabla como una sola columna o el desplazamiento de filas que opcionalmente se pueden organizar en grupos o secciones. Vistas de tabla deben usarse cuando se necesita mostrar una gran cantidad de datos eficaz para el usuario, en un claro para entender la forma.

Vistas de tabla se muestran normalmente en un lado de un [vista dividida](~/ios/tvos/user-interface/split-views.md) como navegación, con los detalles del elemento seleccionado que se muestra en el lado opuesto:

[![](table-views-images/intro01.png "Vista de tabla de ejemplo")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Acerca de las vistas de tabla

Un `UITableView` muestra una sola columna de filas desplazables como una lista jerárquica de información que opcionalmente se puede organizar en grupos o secciones: 

[![](table-views-images/table01.png "Un elemento seleccionado")](table-views-images/table01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con tablas:

- **Ser consciente de ancho** -intenta lograr el equilibrio correcto en los anchos de tabla. Si la tabla es demasiado amplia, puede ser difícil de detectar desde una distancia y puede tardar fuera del área de contenido disponible. Si la tabla es demasiado estrecha, puede hacer que la información se trunque o encapsulado, vuelva a esto puede ser difícil para el usuario leer desde a través de la sala.
- **Mostrar tabla de contenido rápidamente** : listas grandes de datos, diferida y carga el contenido e iníciela mostrando información tan pronto como la tabla se presenta al usuario. Si tarda demasiado tiempo en cargar la tabla, el usuario podría perder interés en la aplicación o de reflexión que está bloqueado.
- **Informar al usuario de mucho contenido cargas** : si el tiempo de carga de una tabla grande es inevitable, presente una [barra de progreso o indicador de actividad](~/ios/tvos/user-interface/progress-indicators.md) para que sepan que la aplicación no se ha bloqueado.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipos de celda de vista de tabla

Un `UITableViewCell` se utiliza para representar las filas individuales de datos en la vista de tabla. Apple ha definido varios tipos de celda de tabla predeterminada:

- **Default** : una opción de la imagen en el lado izquierdo de la celda y el título alineado a la izquierda de la derecha que presenta este tipo. 
- **Subtítulo** : este presenta tipo título alineado a la izquierda en la primera línea y una menor alineado a la izquierda subtítulo en la línea siguiente.
- **El valor 1** : este tipo presenta un título alineado a la izquierda con un subtítulo coloreado más ligeras, alineado a la derecha en la misma línea.
- **El valor 2** : este tipo presenta un título alineado a la derecha con un subtítulo coloreado más ligeras, alineado a la izquierda en la misma línea.

Todos los tipos de celda de vista de tabla predeterminada también admiten los elementos gráficos, como indicadores de divulgación o marcas de verificación. 

Además, puede definir un **personalizado** tipo de celda de vista de tabla y presente un _prototipo celda_, que se cree en el Diseñador de interfaz o a través del código.

Apple tiene las siguientes sugerencias para trabajar con las celdas de la vista de tabla:

- **Evitar el recorte de texto** -mantener las líneas individuales de texto breve para que no terminan truncados. Truncado palabras o frases que son difíciles para el usuario analizar a través de la sala.
- **Tenga en cuenta el estado de fila Focused** : porque una fila pasa a ser más grande, más se redondean las esquinas en el foco, debe probar la apariencia de la celda en todos los Estados. Imágenes o texto puede convertirse en recorta o buscar incorrecto en estado Focused.
- **Usar tablas Editable de manera racionalizada** -mover o eliminar filas de tabla consume más tiempo en tvOS a iOS. Deberá decidir cuidadosamente si esta característica agregará o diverge de su aplicación tvOS.
- **Crear personalizado celda tipos donde corresponda** : mientras que los tipos integrados de celda de vista de tabla son excelentes para muchas situaciones, considere la posibilidad de creación de tipos personalizados de celda para la información no estándar para proporcionar un mayor control y la información para mejorar la presentación el usuario.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Trabajar con vistas de tabla

Es la manera más fácil trabajar con vistas de tabla en una aplicación Xamarin.tvOS crear y modificar su apariencia en el Diseñador de interfaz.

Para comenzar, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
1. En Visual Studio para Mac, inicie un nuevo proyecto de aplicación de tvOS y seleccione **tvOS** > **aplicación** > **aplicación de vista única** y haga clic en el  **Siguiente** botón: 

    [![](table-views-images/table02.png "Seleccione la aplicación de una vista")](table-views-images/table02.png#lightbox)
1. Escriba un **nombre** para la aplicación y haga clic en **siguiente**: 

    [![](table-views-images/table03.png "Escriba un nombre para la aplicación")](table-views-images/table03.png#lightbox)
1. Ya sea ajustar la **nombre del proyecto** y **nombre de la solución** o acepte los valores predeterminados y haga clic en el **crear** botón para crear la nueva solución: 

    [![](table-views-images/table04.png "El nombre del proyecto y el nombre de la solución")](table-views-images/table04.png#lightbox)
1. En el **panel de solución**, haga doble clic en el `Main.storyboard` archivo para abrirlo en el Diseñador de iOS: 

    [![](table-views-images/table05.png "Archivo Main.storyboard")](table-views-images/table05.png#lightbox)
1. Seleccione y elimine el **Default View Controller**: 

    [![](table-views-images/table06.png "Seleccione y elimine el controlador de vista predeterminado")](table-views-images/table06.png#lightbox)
1. Seleccione un **Split View Controller** desde el **cuadro de herramientas** y arrástrelo hasta la superficie de diseño.
1. De forma predeterminada, obtendrá un [vista dividida](~/ios/tvos/user-interface/split-views.md) con un **controlador de vista de navegación** y un **controlador de vista de tabla** en el lado izquierdo y un **delcontroladordevista** en el lado derecho. Se trata de uso sugerido de Apple de una vista de tabla en tvOS: 

    [![](table-views-images/table08.png "Agregar una vista dividida")](table-views-images/table08.png#lightbox)
1. Debe seleccionar cada parte de la vista de tabla y asígnele un personalizado **nombre de la clase** en el **Widget** pestaña de la **Explorador de propiedades** para que se puede acceder a él más adelante en C#código. Por ejemplo, el **controlador de vista de tabla**: 

    [![](table-views-images/table09.png "Asigne un nombre de clase")](table-views-images/table09.png#lightbox)
1. Asegúrese de crear una clase personalizada para el **controlador de vista de tabla**, el **vista de tabla** y cualquier **celdas prototipo**. Visual Studio para Mac agregará las clases personalizadas en el árbol del proyecto se crean: 

    [![](table-views-images/table10.png "Las clases personalizadas en el árbol del proyecto")](table-views-images/table10.png#lightbox)
1. A continuación, seleccione la vista de tabla en la superficie de diseño y ajuste sus propiedades según sea necesario. Como el número de **celdas prototipo** y **estilo** (simple o agrupado): 

    [![](table-views-images/table11.png "La pestaña de widget")](table-views-images/table11.png#lightbox)
1. Para cada **prototipo celda**, selecciónelo y asignar un único **identificador** en el **Widget** pestaña de la **Explorador de propiedades**. Este paso es _muy importante_ porque lo necesitará más adelante este identificador al rellenar la tabla. Por ejemplo `AttrCell`: 

    [![](table-views-images/table12.png "La pestaña de Widget")](table-views-images/table12.png#lightbox)
1. También puede seleccionar para presentar la celda como uno de los [tipos de celda de vista de tabla predeterminado](#Table-View-Cell-Types) a través de la **estilo** lista desplegable o establézcalo en **personalizado** y usar la superficie de diseño al diseño de la celda arrastrando en otros widgets de interfaz de usuario desde el **cuadro de herramientas**: 

    [![](table-views-images/table13.png "El diseño de celda")](table-views-images/table13.png#lightbox)
1. Asignar un único **nombre** a cada elemento de interfaz de usuario en el diseño de la celda de prototipo en el **Widget** pestaña de la **Explorador de propiedades** para que pueda acceder a ellos más adelante en C# código: 

    [![](table-views-images/table14.png "Asigne un nombre")](table-views-images/table14.png#lightbox)
1. Repita el paso anterior para todas las celdas de prototipo en la vista de tabla.
1. A continuación, asignar las clases personalizadas para el resto del diseño de interfaz de usuario, la vista de detalles de diseño y asignar único **nombres** a cada elemento de interfaz de usuario en los detalles de la vista para que se pueden acceder a ellas en C# también. Por ejemplo: 

    [![](table-views-images/table15.png "El diseño de interfaz de usuario")](table-views-images/table15.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. En Visual Studio, inicie un nuevo proyecto de aplicación de tvOS y seleccione **tvOS** > **aplicación de vista única** y escriba un nombre para la aplicación. Haga clic en el **bien** botón para crear una nueva solución: 

    [![](table-views-images/table02-vs.png "Seleccione la aplicación de una vista")](table-views-images/table02-vs.png#lightbox)
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo para abrirlo en el Diseñador de iOS: 

    [![](table-views-images/table05-vs.png "Archivo Main.storyboard")](table-views-images/table05-vs.png#lightbox)
1. Seleccione y elimine el **Default View Controller**: 

    [![](table-views-images/table06-vs.png "Seleccione y elimine el controlador de vista predeterminado")](table-views-images/table06-vs.png#lightbox)
1. Seleccione un **Split View Controller** desde el **cuadro de herramientas** y arrástrelo hasta la superficie de diseño: 

    [![](table-views-images/table07-vs.png "Un controlador de vista dividida")](table-views-images/table07-vs.png#lightbox)
1. De forma predeterminada, obtendrá un [vista dividida](~/ios/tvos/user-interface/split-views.md) con un **controlador de vista de navegación** y un **controlador de vista de tabla** en el lado izquierdo y un **delcontroladordevista** en el lado derecho. Se trata de uso sugerido de Apple de una vista de tabla en tvOS: 

    [![](table-views-images/table08-vs.png "Diseño de la interfaz de usuario")](table-views-images/table08-vs.png#lightbox)
1. Debe seleccionar cada parte de la vista de tabla y asígnele un personalizado **nombre de la clase** en el **Widget** pestaña de la **Explorador de propiedades** para que se puede acceder a él más adelante en C#código. Por ejemplo, el **controlador de vista de tabla**: 

    [![](table-views-images/table09-vs.png "La pestaña de Widget")](table-views-images/table09-vs.png#lightbox)
1. Asegúrese de crear una clase personalizada para el **controlador de vista de tabla**, el **vista de tabla** y cualquier **celdas prototipo**. Visual Studio para Mac agregará las clases personalizadas en el árbol del proyecto se crean: 

    [![](table-views-images/table10-vs.png "Las clases personalizadas en el árbol del proyecto")](table-views-images/table10-vs.png#lightbox)
1. A continuación, seleccione la vista de tabla en la superficie de diseño y ajuste sus propiedades según sea necesario. Como el número de **celdas prototipo** y **estilo** (simple o agrupado): 

    [![](table-views-images/table11-vs.png "La pestaña de Widget")](table-views-images/table11-vs.png#lightbox)
1. Para cada **prototipo celda**, selecciónelo y asignar un único **identificador** en el **Widget** pestaña de la **Explorador de propiedades**. Este paso es _muy importante_ porque lo necesitará más adelante este identificador al rellenar la tabla. Por ejemplo `AttrCell`: 

    [![](table-views-images/table12-vs.png "Asignar un identificador")](table-views-images/table12-vs.png#lightbox)
1. También puede seleccionar para presentar la celda como uno de los [tipos de celda de vista de tabla predeterminado](#Table-View-Cell-Types) a través de la **estilo** lista desplegable o establézcalo en **personalizado** y usar la superficie de diseño al diseño de la celda arrastrando en otros widgets de interfaz de usuario desde el **cuadro de herramientas**: 

    [![](table-views-images/table13-vs.png "El menú desplegable de estilos")](table-views-images/table13-vs.png#lightbox)
1. Asignar un único **nombre** a cada elemento de interfaz de usuario en el diseño de la celda de prototipo en el **Widget** pestaña de la **Explorador de propiedades** para que pueda acceder a ellos más adelante en C# código: 

    [![](table-views-images/table14-vs.png "La pestaña de Widget")](table-views-images/table14-vs.png#lightbox)
1. Repita el paso anterior para todas las celdas de prototipo en la vista de tabla.
1. A continuación, asignar las clases personalizadas para el resto del diseño de interfaz de usuario, la vista de detalles de diseño y asignar único **nombres** a cada elemento de interfaz de usuario en los detalles de la vista para que se pueden acceder a ellas en C# también. Por ejemplo: 

    [![](table-views-images/table15.png "El diseño de interfaz de usuario")](table-views-images/table15.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Diseñar un modelo de datos

Para facilitar el trabajo con la información que se mostrará la vista de tabla sea más fácil y facilitar la presentación de información detallada (como el usuario selecciona o resalta las filas de la vista de tabla), cree una clase personalizada o las clases para que actúe como el modelo de datos para obtener la información presentada .

Tome como ejemplo una aplicación de reservas de viajes que contiene una lista de **ciudades**, cada una que contiene una lista única de **atracciones** que el usuario puede seleccionar. El usuario podrá marcar una atracción como un *favorito*, seleccione esta opción para obtener *direcciones* a una atracción y *reservar un vuelo* a una ciudad determinada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para crear el modelo de datos para un **atracción**, haga doble clic en el nombre del proyecto en el **panel de solución** y seleccione **agregar** > **nuevo archivo...** . Escriba `AttractionInformation` para el **nombre** y haga clic en el **New** botón: 

[![](table-views-images/data01.png "Escriba el nombre AttractionInformation")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para crear el modelo de datos para un **atracción**, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo elemento ...** . Seleccione **clase** y escriba `AttractionInformation` para el **nombre** y haga clic en el **agregar** botón: 

[![](table-views-images/data01-vs.png "Seleccione la clase y escriba AttractionInformation para el nombre")](table-views-images/data01-vs.png#lightbox)

-----

Editar el `AttractionInformation.cs` de archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

Esta clase proporciona las propiedades para almacenar la información sobre un determinado **atracción**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A continuación, haga doble clic en el nombre del proyecto en el **panel de solución** nuevo y seleccione **agregar** > **nuevo archivo...** . Escriba `CityInformation` para el **nombre** y haga clic en el **New** botón: 

[![](table-views-images/data02.png "Escriba el nombre CityInformation")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A continuación, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones** nuevo y seleccione **agregar** > **nuevo elemento...** . Escriba `CityInformation` para el **nombre** y haga clic en el **agregar** botón: 

[![](table-views-images/data02-vs.png "Escriba el nombre CityInformation")](table-views-images/data02-vs.png#lightbox)

-----

Editar el `CityInformation.cs` de archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

Esta clase contiene toda la información sobre un destino **Ciudad**, una colección de **atracciones** de dicha ciudad y proporciona dos métodos auxiliares (`AddAttraction`) para que sea más fácil agregar atracciones a la Ciudad.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>El origen de datos de la vista de tabla

Cada vista de tabla requiere un origen de datos (`UITableViewDataSource`) proporcionar los datos de la tabla y generar las filas necesarias como requerido por la vista de tabla.

Para el ejemplo anterior, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones**, seleccione **agregar** > **nuevo archivo...**  y llámelo `AttractionTableDatasource` y haga clic en el **New** botón Crear. A continuación, edite el `AttractionTableDatasource.cs` de archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

Echemos un vistazo a algunas secciones de la clase en detalle.

En primer lugar, hemos definido por una constante para contener el identificador único de la celda de prototipo (Esto es el mismo identificador asignado en el Diseñador de la interfaz anterior), volver a agregar un acceso directo al controlador de vista de tabla y creado el almacenamiento de los datos:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

A continuación, guardamos el controlador de vista de tabla, a continuación, crear y rellenar nuestro origen de datos (con los modelos de datos definidos anteriormente) cuando se crea la clase:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Por ejemplo, el `PopulateCities` método simplemente crea objetos de modelo de datos en memoria sin embargo, estos podrían leer fácilmente desde una base de datos o servicio web en una aplicación real:

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

El `NumberOfSections` método devuelve el número de secciones en la tabla:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Para **Plain** con el estilo de las vistas de tabla, siempre devuelven 1.

El `RowsInSection` método devuelve el número de filas en la sección actual:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Nuevamente, por **Plain** vistas de tabla, se devuelve el número total de elementos del origen de datos.

El `TitleForHeader` método devuelve el título de sección:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Para un **sin formato** vista de tabla de tipo, deje el título en blanco (`""`).

Por último, cuando se solicita la vista de tabla, crear y rellenar una celda de prototipo usando el `GetCell` método: 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

Para obtener más información sobre cómo trabajar con un `UITableViewDatasource`, consulte Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) documentación.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>El delegado de la vista de tabla

Cada vista de tabla requiere un delegado (`UITableViewDelegate`) para responder a interacción del usuario o de otros eventos del sistema en la tabla.

Para el ejemplo anterior, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones**, seleccione **agregar** > **nuevo archivo...**  y llámelo `AttractionTableDelegate` y haga clic en el **New** botón Crear. A continuación, edite el `AttractionTableDelegate.cs` de archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

Echemos un vistazo a varias secciones de esta clase en detalles.

En primer lugar, creamos un acceso directo al controlador de vista de tabla cuando se crea la clase:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

A continuación, cuando se selecciona una fila (el usuario hace clic en la superficie de interacción de la Apple remotos) desea marcar la **atracción** representado por la fila seleccionada como favorito:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

A continuación, cuando el usuario resalta una fila (asignándole el foco mediante la superficie de interacción remota de Apple) desee presentar los detalles de la **atracción** representado por esa fila en la sección de detalles de nuestro controlador de vista de división:

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

El `CanFocusRow` se llama al método para cada fila que se va a obtener el foco en la vista de tabla. Devolver `true` lo contrario, devuelve si la fila puede obtener el foco, `false`. En el caso de este ejemplo, hemos creado un personalizado `AttractionHighlighted` eventos que se generarán en cada fila como lo recibe el foco.

Para obtener más información sobre cómo trabajar con un `UITableViewDelegate`, consulte Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) documentación.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>La celda de vista de tabla

Para cada celda de prototipo que ha agregado a la vista de tabla en el Diseñador de interfaz, también se crea una instancia personalizada de la celda de vista de tabla (`UITableViewCell`) para que pueda rellenar la nueva celda (fila) que se crean.

La aplicación de ejemplo, haga doble clic en el `AttractionTableCell.cs` archivo para abrirlo y editarlo y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

Esta clase proporciona almacenamiento para el objeto de modelo de datos de atracción (`AttractionInformation` definidos anteriormente) aparece en la fila especificada:

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

El `UpdateUI` método rellena los Widgets de interfaz de usuario (que se agregaron al prototipo de la celda en el Diseñador de la interfaz) según sea necesario:

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

Para obtener más información sobre cómo trabajar con un `UITableViewCell`, consulte Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) documentación.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>El controlador de vista de tabla

Un controlador de vista de tabla (`UITableViewController`) administra una vista de tabla que se ha agregado a un guión gráfico mediante el Diseñador de la interfaz.

La aplicación de ejemplo, haga doble clic en el `AttractionTableViewController.cs` archivo para abrirlo y editarlo y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

Echemos un vistazo a esta clase. En primer lugar, hemos creado métodos abreviados para facilitar el acceso a la vista de tabla `DataSource` y `TableDelegate`. Vamos a usarlas versiones posteriores para la comunicación entre la vista de tabla en el lado izquierdo de la vista en dos paneles y la vista de detalles de la derecha.

Por último, cuando se carga la vista de tabla en memoria, creamos instancias de la `AttractionTableDatasource` y `AttractionTableDelegate` (ambos creado anteriormente) y adjuntarlos a la vista de tabla.

Para obtener más información sobre cómo trabajar con un `UITableViewController`, consulte Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) documentación.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Extracción de todos los elementos

Como se indicó al principio de este documento, las vistas de tabla se muestran normalmente en un lado de un [vista dividida](~/ios/tvos/user-interface/split-views.md) como navegación, con los detalles del elemento seleccionado que se muestra en el lado opuesto. Por ejemplo: 

[![](table-views-images/intro01.png "Ejecución de la aplicación de ejemplo")](table-views-images/intro01.png#lightbox)

Puesto que este es un patrón estándar de tvOS, echemos un vistazo a los pasos finales para tenerlo todo junto y dispone de los lados izquierdos y derecho de la vista dividida interactúan entre sí.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>La vista de detalles

Para obtener el ejemplo de la aplicación de viajes descrito anteriormente, una clase personalizada (`AttractionViewController`) se ha definido para el controlador de vista estándar presenta en el lado derecho de la vista en dos paneles como la vista de detalle:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

En este caso, hemos proporcionado el **atracción** (`AttractionInformation`) que se muestran como una propiedad y se crea un `UpdateUI` método que rellena los Widgets de interfaz de usuario se agrega a la vista en el Diseñador de interfaz.

También hemos definido un acceso directo hacia el controlador de vista de división (`SplitView`) que se usará para comunicarse cambia a la vista de tabla (`AcctractionTableView`).

Por último, las acciones personalizadas (eventos) se agregaron a las tres `UIButton` instancias creadas en el Diseñador de interfaz, que permite al usuario marcar una atracción como un _favorito_, obtener _direcciones_ a un atracción y _reservar un vuelo_ a una ciudad determinada.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>El controlador de vista de navegación

Dado que el controlador de vista de tabla está anidado en un controlador de vista de navegación en el lado izquierdo de la vista en dos paneles, el controlador de vista de navegación se asignó una clase personalizada (`MasterNavigationController`) en el Diseñador de interfaz y se define como sigue:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Nuevamente, esta clase solo define algunos accesos directos para que sea más fácil para comunicarse a través de los dos lados de la división View Controller:

* `SplitView` : Es un vínculo al controlador de vista de división (`MainSpiltViewController`) que pertenece el controlador de vista de navegación.
* `TableController` : Obtiene el controlador de vista de tabla (`AttractionTableViewController`) que se presenta como la vista de la parte superior del controlador de vista de navegación.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>El controlador de vista dividida

Dado que el controlador de vista de división es la base de nuestra aplicación, hemos creado una clase personalizada (`MasterSplitViewController`) para él en el Diseñador de la interfaz y se define del siguiente modo:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

En primer lugar, creamos los accesos directos a la **detalles** en paralelo de la vista dividida (`AttractionViewController`) y a la **Master** lado (`MasterNavigationController`). Nuevamente, esto facilita para la comunicación entre los dos lados más adelante.

A continuación, cuando la vista dividida se cargan en memoria, asociar el controlador de vista dividida a ambos lados de la vista en dos paneles y responder al usuario resaltado un atracción en la vista de tabla (`AttractionHighlighted`) mostrando la atracción nuevo en el **detalles**  en paralelo de la vista en dos paneles.

Consulte la [tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/) aplicación de ejemplo para una implementación completa de vistas de tabla dentro de una vista en dos paneles.

## <a name="table-views-in-detail"></a>Vistas de tabla en detalle

Puesto que tvOS se basa en iOS, las vistas de tabla y los controladores de vista de tabla están diseñados y se comportan de manera similar. Para obtener más información sobre cómo trabajar con la vista de tabla en una aplicación de Xamarin, consulte nuestra iOS [trabajar con tablas y celdas](~/ios/user-interface/controls/tables/index.md) documentación.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con vistas de tabla dentro de una aplicación Xamarin.tvOS. Y ha presentado un ejemplo de cómo trabajar con una vista de tabla dentro de una vista dividida, que es el uso típico de una vista de tabla en una aplicación de tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
