---
title: Control de tabla
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 0b8d8d08db15959a47093f255a891605a089ea00
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="table-control"></a>Control de tabla

El watchOS `WKInterfaceTable` control es mucho más fácil que su equivalente de iOS, pero realiza una función similar. Crea una lista de desplazamiento de filas que pueden incluir diseños personalizados, y que responden a eventos de toque.

![](table-images/table-list-sml.png "Lista de supervisión de tabla") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Agregar una tabla

Arrastre el **tabla** control en una escena. De forma predeterminada será similar a este (que se muestran un diseño de fila no especificado):

[![](table-images/add-table-sml.png "Agregar una tabla")](table-images/add-table.png#lightbox)

Asigne un nombre de la tabla en la **propiedades** del panel de **nombre** cuadro, por lo que se puede hacer referencia en el código.

## <a name="add-a-row-controller"></a>Agregar un controlador de fila

La tabla incluye automáticamente una fila única, representada por un controlador de fila que contiene un **grupo** control de forma predeterminada.

Para establecer el **clase** para el controlador de fila, seleccione la fila en la **esquema del documento** y escriba un nombre de clase en el **propiedades** panel:

[![](table-images/add-row-controller-sml.png "Escriba un nombre de clase en el panel de propiedades")](table-images/add-row-controller.png#lightbox)

Una vez que se establece la clase de controlador de la fila, el IDE creará un archivo de C# correspondiente en el proyecto. Arrastrar controles (como etiquetas) a la fila y asígneles nombres, por lo que pueden hacer referencia en el código.




## <a name="create-and-populate-rows"></a>Crear y rellenar las filas

`SetNumberOfRows` crea las clases de controlador de la fila para cada fila, con el `Identifier` para seleccionar el correcto. Si se le asignó el controlador de fila personalizado `Identifier`, cambiar **predeterminado** en el fragmento de código siguiente para el identificador que utilizó. El `RowController` *para cada fila* se crea cuando `SetNumberOfRows` se llama y la tabla que aparece.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> **Tenga en cuenta**: filas de la tabla no están virtualizadas que están en iOS. Pruebe a limitar el número de filas (Apple recomienda inferior a 20).
Una vez que se han creado las filas, debe rellenar cada celda (como `GetCell` sería hacerlo en iOS). Este fragmento de código desde el [WatchTables ejemplo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) actualiza la etiqueta en cada fila

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> **Nota:** mediante `SetNumberOfRows` y, a continuación, un bucle que utiliza `GetRowController` hace que toda la tabla para enviarse a la inspección. En las vistas siguientes de la tabla, si necesita agregar o quitar filas específicas use `InsertRowsAt` y `RemoveRowsAt` para mejorar el rendimiento.


## <a name="respond-to-taps"></a>Responder a derivaciones

Puede responder a la selección de filas de dos maneras diferentes:

- implementar el `DidSelectRow` método en el controlador de interfaz, o
- Cree un segue en el guión gráfico e implemente `GetContextForSegue` si desea que la selección de fila para abrir otra escena.

### <a name="didselectrow"></a>DidSelectRow

Para controlar mediante programación la selección de filas, implementar la `DidSelectRow` método. Para abrir una nueva escena, use `PushController` y pase el identificador de la escena y el contexto de datos que se utiliza:

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

Arrastre un segue en el guión gráfico de la fila de la tabla a otra escena (mantenga presionada la **Control** clave mientras arrastra el puntero).
Asegúrese de seleccionar el segue y asígnele un identificador el **propiedades** pad (como `secondLevel` en el ejemplo siguiente).

En el controlador de interfaz, implemente el `GetContextForSegue` método y devuelven el contexto de datos que se debe proporcionar a la escena que presenta el segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Estos datos se pasan a la escena de guión gráfico de destino en su `Awake` método.

## <a name="multiple-row-types"></a>Varios tipos de fila

De forma predeterminada el control de tabla tiene un tipo de fila única que se puede diseñar. Para agregar más filas 'plantillas' utilizan el **filas** cuadro el **propiedades** pad para crear más controladores de fila:

![](table-images/prototype-rows1.png "Si se establece el número de filas de prototipo")

Establecer el **filas** propiedad **3** creará los marcadores de posición de fila adicional para que pueda arrastrar controles en. Para cada fila, establezca la **clase** nombre en el **propiedades** panel para asegurarse de que se crea la clase de controlador de fila.

![](table-images/prototype-rows2.png "Las filas de prototipo en el diseñador")

Para rellenar una tabla con tipos diferentes de la fila, utilice la `SetRowTypes` método para especificar el tipo de controlador de fila que se usará para cada fila de la tabla. Utilice los identificadores de fila para especificar qué controlador de fila debe utilizarse para cada fila.

El número de elementos de esta matriz debe coincidir con el número de filas que se espera que sea en la tabla:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Al rellenar una tabla con varios controladores de fila, debe realizar un seguimiento de qué tipo de espera como rellenar la interfaz de usuario:

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```


## <a name="vertical-detail-paging"></a>Paginación de detalle vertical

watchOS 3 incorpora una nueva característica para las tablas: la capacidad para desplazarse por las páginas de detalles relacionados con cada fila, sin tener que volver a la tabla y elija otra fila. Las pantallas de detalles se pueden desplazar Deslizar rápidamente hacia arriba y hacia abajo, o mediante la copa Digital.

![](table-images/table-scroll-sml.png "Ejemplo de paginación de detalle vertical") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> **Advertencia:** esta característica sólo está disponible mediante la edición del guión gráfico en el generador de interfaz de Xcode.

Para habilitar esta característica, seleccione la `WKInterfaceTable` en la superficie de diseño y marca el **paginación de detalle Vertical** opción:

![](table-images/vertical-detail-paging-sml.png "Al seleccionar la opción de paginación de detalle Vertical")

Como [explica por Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) debe usar la navegación de tabla segues para que funcione la característica de paginación. Volver a escribir cualquier código existente que utiliza `PushController` usar segues en su lugar.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Apéndice: Ejemplo de código de controlador de fila

El IDE crea dos archivos de código automáticamente cuando se crea un controlador de fila en el diseñador. El código de estos archivos generados se muestra a continuación como referencia.

La primera se denominará para la clase, por ejemplo **RowController.cs**, similar a la siguiente:

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

El otro **. designer.cs** archivo es una definición de clase parcial que contiene las salidas y las acciones que se crean en la superficie del diseñador, como en este ejemplo con una `WKInterfaceLabel` control:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

Las salidas y las acciones que se declara aquí, a continuación, pueden hacer referencia en código, sin embargo, el **. designer.cs** archivo no debe editarse directamente.



## <a name="related-links"></a>Vínculos relacionados

- [WatchTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de tabla de Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
