---
title: Trabajar con vistas de tabla
description: "Este artículo tratan diseñar y trabajar con vistas de la tabla y controladores de la vista de tabla dentro de una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: af562ac03f2cd5f293f99c7509000499ad5deaa4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-table-views"></a>Trabajar con vistas de tabla

_Este artículo tratan diseñar y trabajar con vistas de la tabla y controladores de la vista de tabla dentro de una aplicación Xamarin.tvOS._

En tvOS, se presenta una vista de tabla como una sola columna de desplazar filas que, opcionalmente, se pueden organizar en grupos o secciones. Vistas de la tabla deben utilizarse cuando se necesitan para mostrar una gran cantidad de datos eficaz para el usuario, como un texto sin cifrar para entender la forma.

Vistas de la tabla se muestran generalmente en un lado de un [vista dividida](~/ios/tvos/user-interface/split-views.md) como navegación, con los detalles del elemento seleccionado aparece en el lado opuesto:

[![](table-views-images/intro01.png "Vista de tabla de ejemplo")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Acerca de las vistas de tabla

Un `UITableView` muestra una sola columna de filas desplazables como una lista jerárquica de información que, opcionalmente, se puede organizar en grupos o secciones: 

[![](table-views-images/table01.png "Un elemento seleccionado")](table-views-images/table01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con tablas:

- **Tenga en cuenta de ancho** -intenta lograr el equilibrio correcto en el ancho de tabla. Si la tabla es demasiado grande, puede resultar difícil analizar desde la distancia y puede tardar fuera del área de contenido disponible. Si la tabla es demasiado estrecha, es posible que la información se trunque o encapsulado, nuevo puede ser difícil para el usuario leer en la misma habitación.
- **Mostrar tabla de contenido rápidamente** : para obtener listas grandes de datos, carga diferida el contenido y mostrar información tan pronto como se presenta al usuario en la tabla. Si tarda demasiado tiempo en cargar la tabla, el usuario podría perder el interés en su aplicación o cree que está bloqueado.
- **Informar al usuario de mucho contenido cargas** : si un tiempo de carga de la tabla grande es inevitable, presente un [barra de progreso o indicador de actividad de](~/ios/tvos/user-interface/progress-indicators.md) para que sepan que la aplicación no se ha bloqueado.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipos de celdas de la vista de tabla

Un `UITableViewCell` se utiliza para representar las filas individuales de datos en la vista de tabla. Apple ha define varios tipos de celda de tabla de forma predeterminada:

- **Predeterminado** : una opción de la imagen en el lado izquierdo de la celda y el título alineado a la izquierda de la derecha que presenta este tipo. 
- **Subtítulo** : este presenta el tipo título alineado a la izquierda en la primera línea y un menor alineado a la izquierda subtítulo en la línea siguiente.
- **Valor de 1** -este tipo presenta un título alineado a la izquierda con un subtítulo coloreado más ligeras, alineado a la derecha en la misma línea.
- **El valor 2** -este tipo presenta un título alineado a la derecha con un subtítulo coloreado más ligeras, alineado a la izquierda en la misma línea.

Todos los tipos de celda de vista de tabla predeterminada también admiten elementos gráficos como indicadores de divulgación de información o marcas de verificación. 

Además, puede definir un **personalizado** tipo de celda de la vista de tabla y presente un _celda prototipo_, que se cree en el Diseñador de la interfaz o a través del código.

Apple tiene las siguientes sugerencias para trabajar con celdas de la vista de tabla:

- **Evitar el recorte de texto** -mantener las líneas individuales de texto breve para que no terminan truncados. Truncado palabras o frases que son difíciles para el usuario analizar desde a través de la sala.
- **Tenga en cuenta el estado de fila enfocado** : porque una fila es mayor, con más redondear esquinas en el foco, debe probar la apariencia de la celda en todos los Estados. Texto o imágenes puede ser recorta o buscar incorrecto en el estado enfocado.
- **Usar tablas Editable de con moderación** -mover o eliminar filas de la tabla es más lento en tvOS a iOS. Debe decidir con cuidado si esta característica se agregue o quede oculta por la aplicación tvOS.
- **Crear personalizado celda tipos donde corresponda** : mientras que los tipos integrados de celda de la vista de tabla son excelentes para muchas situaciones, considere la posibilidad de creación de tipos de celda personalizados para obtener información no estándar para proporcionar un mayor control y la información para mejorar la presentación el usuario.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Trabajar con vistas de tabla

La manera más fácil trabajar con vistas de tabla en una aplicación de Xamarin.tvOS consiste en crear y modificar su apariencia en el Diseñador de interfaz.

Para comenzar, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. En Visual Studio para Mac, inicie un nuevo proyecto de aplicación de tvOS y seleccione **tvOS** > **aplicación** > **ver solo la aplicación** y haga clic en el  **Siguiente** botón: 

    [![](table-views-images/table02.png "Seleccione la aplicación de la vista única")](table-views-images/table02.png#lightbox)
1. Escriba un **nombre** para la aplicación y haga clic en **siguiente**: 

    [![](table-views-images/table03.png "Escriba un nombre para la aplicación")](table-views-images/table03.png#lightbox)
1. En ajustar la **nombre del proyecto** y **nombre de la solución** o acepte los valores predeterminados y haga clic en el **crear** botón para crear la nueva solución: 

    [![](table-views-images/table04.png "El nombre del proyecto y el nombre de la solución")](table-views-images/table04.png#lightbox)
1. En el **solución Pad**, haga doble clic en el `Main.storyboard` archivo para abrirlo en el Diseñador de iOS: 

    [![](table-views-images/table05.png "El archivo Main.storyboard")](table-views-images/table05.png#lightbox)
1. Seleccione y elimine la **predeterminado View Controller**: 

    [![](table-views-images/table06.png "Seleccione y elimine el controlador de vista predeterminado")](table-views-images/table06.png#lightbox)
1. Seleccione un **división View Controller** desde el **cuadro de herramientas** y arrástrelo hasta la superficie de diseño.
1. De forma predeterminada, obtendrá un [vista dividida](~/ios/tvos/user-interface/split-views.md) con un **controlador de vista de navegación** y un **controlador de vista de tabla** en el lado izquierdo y un **View Controller** en el lado derecho. Se trata de uso de sugerencias de Apple de una vista de tabla en tvOS: 

    [![](table-views-images/table08.png "Agregar una vista dividida")](table-views-images/table08.png#lightbox)
1. Debe seleccionar cada parte de la vista de tabla y asignarle un personalizado **nombre de la clase** en el **Widget** pestaña de la **el Explorador de propiedades** para que pueda acceder a él más adelante en C# código. Por ejemplo, el **controlador de vista de tabla**: 

    [![](table-views-images/table09.png "Asignar un nombre de clase")](table-views-images/table09.png#lightbox)
1. Asegúrese de crear una clase personalizada para la **controlador de vista de tabla**, el **vista de tabla** y cualquier **celdas prototipo**. Visual Studio para Mac incluirá las clases personalizadas en el árbol del proyecto que se crean: 

    [![](table-views-images/table10.png "Las clases personalizadas en el árbol del proyecto")](table-views-images/table10.png#lightbox)
1. A continuación, seleccione la vista de tabla en la superficie de diseño y ajustar sus propiedades según sea necesario. Como el número de **celdas prototipo** y **estilo** (simple o agrupadas): 

    [![](table-views-images/table11.png "La pestaña de widget")](table-views-images/table11.png#lightbox)
1. Para cada **celda prototipo**, selecciónelo y asignar un único **identificador** en el **Widget** pestaña de la **el Explorador de propiedades**. Este paso es _muy importante_ ya que lo necesitará más adelante este identificador al rellenar la tabla. Por ejemplo `AttrCell`: 

    [![](table-views-images/table12.png "La pestaña de Widget")](table-views-images/table12.png#lightbox)
1. También puede seleccionar para presentar la celda como uno de los [tipos de celda de la vista de tabla predeterminado](#Table-View-Cell-Types) a través de la **estilo** desplegable o establézcalo en **personalizado** y usar la superficie de diseño al diseño de la celda arrastrando en otros widgets de interfaz de usuario desde el **cuadro de herramientas**: 

    [![](table-views-images/table13.png "El diseño de celda")](table-views-images/table13.png#lightbox)
1. Asignar un único **nombre** a cada elemento de interfaz de usuario en el diseño de celda de prototipo en la **Widget** pestaña de la **el Explorador de propiedades** por lo que puede tener acceso a ellos más adelante en el código de C#: 

    [![](table-views-images/table14.png "Asigne un nombre")](table-views-images/table14.png#lightbox)
1. Repita el paso anterior para todas las celdas de prototipo en la vista de tabla.
1. A continuación, asignar las clases personalizadas para el resto de su diseño de interfaz de usuario, la vista de detalles de diseño y asignar único **nombres** a cada elemento de interfaz de usuario en los detalles de la vista para que se pueda acceder a ellos en C# también. Por ejemplo: 

    [![](table-views-images/table15.png "El diseño de interfaz de usuario")](table-views-images/table15.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. En Visual Studio, inicie un nuevo proyecto de aplicación de tvOS y seleccione **tvOS** > **ver solo la aplicación** y escriba un nombre para la aplicación. Haga clic en el **bien** botón para crear una nueva solución: 

    [![](table-views-images/table02-vs.png "Seleccione la aplicación de la vista única")](table-views-images/table02-vs.png#lightbox)
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo para abrirlo en el Diseñador de iOS: 

    [![](table-views-images/table05-vs.png "El archivo Main.storyboard")](table-views-images/table05-vs.png#lightbox)
1. Seleccione y elimine la **predeterminado View Controller**: 

    [![](table-views-images/table06-vs.png "Seleccione y elimine el controlador de vista predeterminado")](table-views-images/table06-vs.png#lightbox)
1. Seleccione un **división View Controller** desde el **cuadro de herramientas** y arrástrelo hasta la superficie de diseño: 

    [![](table-views-images/table07-vs.png "Un controlador de vista de división")](table-views-images/table07-vs.png#lightbox)
1. De forma predeterminada, obtendrá un [vista dividida](~/ios/tvos/user-interface/split-views.md) con un **controlador de vista de navegación** y un **controlador de vista de tabla** en el lado izquierdo y un **View Controller** en el lado derecho. Se trata de uso de sugerencias de Apple de una vista de tabla en tvOS: 

    [![](table-views-images/table08-vs.png "Diseño de la interfaz de usuario")](table-views-images/table08-vs.png#lightbox)
1. Debe seleccionar cada parte de la vista de tabla y asignarle un personalizado **nombre de la clase** en el **Widget** pestaña de la **el Explorador de propiedades** para que pueda acceder a él más adelante en C# código. Por ejemplo, el **controlador de vista de tabla**: 

    [![](table-views-images/table09-vs.png "La pestaña de Widget")](table-views-images/table09-vs.png#lightbox)
1. Asegúrese de crear una clase personalizada para la **controlador de vista de tabla**, el **vista de tabla** y cualquier **celdas prototipo**. Visual Studio para Mac incluirá las clases personalizadas en el árbol del proyecto que se crean: 

    [![](table-views-images/table10-vs.png "Las clases personalizadas en el árbol del proyecto")](table-views-images/table10-vs.png#lightbox)
1. A continuación, seleccione la vista de tabla en la superficie de diseño y ajustar sus propiedades según sea necesario. Como el número de **celdas prototipo** y **estilo** (simple o agrupadas): 

    [![](table-views-images/table11-vs.png "La pestaña de Widget")](table-views-images/table11-vs.png#lightbox)
1. Para cada **celda prototipo**, selecciónelo y asignar un único **identificador** en el **Widget** pestaña de la **el Explorador de propiedades**. Este paso es _muy importante_ ya que lo necesitará más adelante este identificador al rellenar la tabla. Por ejemplo `AttrCell`: 

    [![](table-views-images/table12-vs.png "Asignar un identificador")](table-views-images/table12-vs.png#lightbox)
1. También puede seleccionar para presentar la celda como uno de los [tipos de celda de la vista de tabla predeterminado](#Table-View-Cell-Types) a través de la **estilo** desplegable o establézcalo en **personalizado** y usar la superficie de diseño al diseño de la celda arrastrando en otros widgets de interfaz de usuario desde el **cuadro de herramientas**: 

    [![](table-views-images/table13-vs.png "La lista desplegable de estilos")](table-views-images/table13-vs.png#lightbox)
1. Asignar un único **nombre** a cada elemento de interfaz de usuario en el diseño de celda de prototipo en la **Widget** pestaña de la **el Explorador de propiedades** por lo que puede tener acceso a ellos más adelante en el código de C#: 

    [![](table-views-images/table14-vs.png "La pestaña de Widget")](table-views-images/table14-vs.png#lightbox)
1. Repita el paso anterior para todas las celdas de prototipo en la vista de tabla.
1. A continuación, asignar las clases personalizadas para el resto de su diseño de interfaz de usuario, la vista de detalles de diseño y asignar único **nombres** a cada elemento de interfaz de usuario en los detalles de la vista para que se pueda acceder a ellos en C# también. Por ejemplo: 

    [![](table-views-images/table15.png "El diseño de interfaz de usuario")](table-views-images/table15.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Diseñar un modelo de datos

Facilitan el trabajo con la información que la vista de tabla se mostrará más fácilmente y facilitar la presentación de información detallada (como el usuario selecciona o resalta las filas en la vista de tabla), cree una clase personalizada o clases para que actúe como el modelo de datos para ver la información presentada .

Tome como ejemplo una aplicación de reservas de viajes que contiene una lista de **ciudades**, cada una que contiene una lista única de **atractivos** que el usuario puede seleccionar. El usuario podrá marcar una atracción como un *favoritos*, seleccione esta opción para obtener *direcciones* a una atracción y *reservar un vuelo* para una ciudad determinada.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para crear el modelo de datos para un **atracción**, haga doble clic en el nombre del proyecto en el **solución Pad** y seleccione **agregar** > **nuevo archivo...** . Escriba `AttractionInformation` para el **nombre** y haga clic en el **New** botón: 

[![](table-views-images/data01.png "Escriba AttractionInformation para el nombre")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para crear el modelo de datos para un **atracción**, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo elemento ...** . Seleccione **clase** y escriba `AttractionInformation` para el **nombre** y haga clic en el **agregar** botón: 

[![](table-views-images/data01-vs.png "Seleccione la clase y escriba AttractionInformation para el nombre")](table-views-images/data01-vs.png#lightbox)

-----

Editar la `AttractionInformation.cs` de archivos y darle un aspecto similar al siguiente:

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

Esta clase proporciona las propiedades para almacenar la información sobre una determinada **atracción**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A continuación, haga doble clic en el nombre del proyecto en el **solución Pad** nuevo y seleccione **agregar** > **nuevo archivo...** . Escriba `CityInformation` para el **nombre** y haga clic en el **New** botón: 

[![](table-views-images/data02.png "Escriba CityInformation para el nombre")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A continuación, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones** nuevo y seleccione **agregar** > **nuevo elemento...** . Escriba `CityInformation` para el **nombre** y haga clic en el **agregar** botón: 

[![](table-views-images/data02-vs.png "Escriba CityInformation para el nombre")](table-views-images/data02-vs.png#lightbox)

-----

Editar la `CityInformation.cs` de archivos y darle un aspecto similar al siguiente:

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

Esta clase contiene toda la información sobre un destino **City**, una colección de **atractivos** de dicha ciudad y proporciona dos métodos auxiliares (`AddAttraction`) para que sea más fácil agregar atractivos para el Ciudad.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>El origen de datos de la vista de tabla

Cada vista de tabla requiere un origen de datos (`UITableViewDataSource`) proporcionar los datos de la tabla y generar las filas necesarias como requerido por la vista de tabla.

Para el ejemplo anterior, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones**, seleccione **agregar** > **nuevo archivo...**  y llámelo `AttractionTableDatasource` y haga clic en el **New** botón para crear. A continuación, edite la `AttractionTableDatasource.cs` de archivos y darle un aspecto similar al siguiente:

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

¡Eche un vistazo a algunas secciones de la clase en detalle.

En primer lugar, hemos definido una constante para contener el identificador único de la celda de prototipo (es decir, el mismo identificador asignado en el Diseñador de la interfaz anterior), agrega un acceso directo hacia el controlador de vista de tabla y crea el almacenamiento de los datos:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

A continuación, guardar el controlador de vista de tabla, a continuación, crear y rellenar el origen de datos (con los modelos de datos definidos anteriormente) cuando se crea la clase:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Por ejemplo, el `PopulateCities` método simplemente crea objetos del modelo de datos en memoria sin embargo, estos podrían leer fácilmente desde un servicio web o de base de datos en una aplicación real:

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

El `NumberOfSections` método devuelve el número de secciones de la tabla:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Para **sin formato** con el estilo de vistas de tabla, siempre devuelven 1.

El `RowsInSection` método devuelve el número de filas de la sección actual:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Una vez más, para **sin formato** vistas de tabla, devolver el número total de elementos del origen de datos.

El `TitleForHeader` método devuelve el título determinados sección:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Para una **sin formato** tabla vista escriba, deje en blanco el título (`""`).

Por último, cuando se solicita mediante la vista de tabla, crear y rellenar una celda de prototipo utilizando el `GetCell` método: 

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

Para obtener más información sobre cómo trabajar con un `UITableViewDatasource`, vea de Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) documentación.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>El delegado de la vista de tabla

Cada vista de tabla requiere un delegado (`UITableViewDelegate`) para responder a interacción del usuario ni otros eventos del sistema en la tabla.

Para el ejemplo anterior, haga doble clic en el nombre del proyecto en el **el Explorador de soluciones**, seleccione **agregar** > **nuevo archivo...**  y llámelo `AttractionTableDelegate` y haga clic en el **New** botón para crear. A continuación, edite la `AttractionTableDelegate.cs` de archivos y darle un aspecto similar al siguiente:

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

¡Eche un vistazo a varias secciones de esta clase en detalles.

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

A continuación, cuando se selecciona una fila (el usuario hace clic en la superficie táctil de remoto de Apple) desee marcar la **atracción** representado por la fila seleccionada como favorito:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

A continuación, cuando el usuario resalta una fila (mediante y recibe el foco mediante la superficie de Touch remoto de Apple) que queremos presentar los detalles de la **atracción** representado por esa fila en la sección detalles de nuestro controlador de vista de división:

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

El `CanFocusRow` método se llama para cada fila que se va a obtener el foco en la vista de tabla. Devolver `true` si la fila puede obtener el foco, lo contrario devuelve `false`. En el caso de este ejemplo, hemos creado un personalizado `AttractionHighlighted` evento que se generará en cada fila, ya que recibe el foco.

Para obtener más información sobre cómo trabajar con un `UITableViewDelegate`, vea de Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) documentación.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>La celda de la vista de tabla

Para cada celda de prototipo que se agregan a la vista de tabla en el Diseñador de interfaz, también se crea una instancia personalizada de la celda de la vista de tabla (`UITableViewCell`) para que pueda rellenar la nueva celda (fila) después de crearla.

Para la aplicación de ejemplo, haga doble clic en el `AttractionTableCell.cs` archivo para abrirlo y editarlo y darle un aspecto similar al siguiente:

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

Para obtener más información sobre cómo trabajar con un `UITableViewCell`, vea de Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) documentación.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>El controlador de vista de tabla

Un controlador de vista de tabla (`UITableViewController`) administra una vista de tabla que se ha agregado a un guión gráfico a través del Diseñador de interfaz.

Para la aplicación de ejemplo, haga doble clic en el `AttractionTableViewController.cs` archivo para abrirlo y editarlo y darle un aspecto similar al siguiente:

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

Echemos un vistazo más de cerca a esta clase. En primer lugar, hemos creado accesos directos para facilitar el acceso a la vista de tabla `DataSource` y `TableDelegate`. Vamos a usarlas versiones posteriores para la comunicación entre la vista de tabla en el lado izquierdo de la vista en dos paneles y la vista de detalles de la derecha.

Por último, cuando se carga la vista de tabla en la memoria, es crear instancias de la `AttractionTableDatasource` y `AttractionTableDelegate` (tanto creada anteriormente) y adjuntarlos a la vista de tabla.

Para obtener más información sobre cómo trabajar con un `UITableViewController`, vea de Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) documentación.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Extracción de todos los elementos

Como se indicó al principio de este documento, vistas de la tabla se muestran generalmente en un lado de un [vista dividida](~/ios/tvos/user-interface/split-views.md) como navegación, con los detalles del elemento seleccionado aparece en el lado opuesto. Por ejemplo: 

[![](table-views-images/intro01.png "Aplicación de ejemplo de ejecución")](table-views-images/intro01.png#lightbox)

Puesto que se trata de un patrón estándar en tvOS, echemos un vistazo a los pasos finales para reunir todos los elementos y ha los lados izquierdos y derecho de la vista dividida interactúan entre sí.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>La vista de detalle

Para obtener el ejemplo de la aplicación de viajes presentada anteriormente, una clase personalizada (`AttractionViewController`) se define para el controlador de vista estándar presenta en el lado derecho de la vista de la división como la vista de detalle:

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

Por último, se agregaron las acciones personalizadas (eventos) a las tres `UIButton` instancias creadas en el Diseñador de la interfaz, que permite al usuario marcar una atracción como un _favoritos_, obtener _direcciones_ a un atracción y _reservar un vuelo_ para una ciudad determinada.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>El controlador de vista de navegación

Dado que el controlador de vista de tabla está anidado en un controlador de vista de navegación en el lado izquierdo de la vista de división, el controlador de vista de navegación se asignó una clase personalizada (`MasterNavigationController`) en el Diseñador de la interfaz y se define como sigue:

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

De nuevo, esta clase define solo algunos métodos abreviados para que resulten más fáciles de comunicarse a través de los dos lados de la división View Controller:

* `SplitView` : Es un vínculo al controlador de vista de división (`MainSpiltViewController`) que pertenece el controlador de vista de navegación.
* `TableController` -Obtiene el controlador de vista de tabla (`AttractionTableViewController`) que se presenta como la vista de la parte superior en el controlador de vista de navegación.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>El controlador de vista de división

Dado que el controlador de vista de división es la base de nuestra aplicación, creamos una clase personalizada (`MasterSplitViewController`) para él en el Diseñador de la interfaz y se define como sigue:

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

En primer lugar, creamos accesos directos a la **detalles** lateral de la vista de división (`AttractionViewController`) y a la **Master** lado (`MasterNavigationController`). De nuevo, resulta más fácil para la comunicación entre los dos lados más adelante.

A continuación, cuando la vista dividida se cargan en memoria, conectar el controlador de vista de división en ambos lados de la vista de división y responder al usuario resaltado una atracción en la vista de tabla (`AttractionHighlighted`), mostrando la atracción nueva en la **detalles**  lateral de la vista en dos paneles.

Vea la [tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/) aplicación de ejemplo para una implementación completa de vistas de tabla dentro de una vista en dos paneles.

## <a name="table-views-in-detail"></a>Vistas de tabla en detalle

Puesto que tvOS se basa en iOS, controladores de la vista de tabla y vistas de tabla están diseñados y se comportan de forma similar. Para obtener más información sobre cómo trabajar con la vista de tabla en una aplicación de Xamarin, vea nuestra iOS [trabajar con tablas y las celdas](~/ios/user-interface/controls/tables/index.md) documentación.

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con vistas de la tabla dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo. Y se presenta un ejemplo del uso de una vista de tabla dentro de una vista dividida, que es el uso típico de una vista de tabla en una aplicación tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
