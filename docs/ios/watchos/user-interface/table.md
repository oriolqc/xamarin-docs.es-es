---
title: los controles de tabla en Xamarin de watchOS
description: Este documento describe cómo usar los controles de la tabla de watchOS en Xamarin. Describe la adición de una tabla, agregando un controlador de fila, crear y rellenar las filas, responder a derivaciones y mucho más.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: cd5e7299874bbfb1b652315a549b9d067d58e9a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109398"
---
# <a name="watchos-table-controls-in-xamarin"></a>los controles de tabla en Xamarin de watchOS

WatchOS `WKInterfaceTable` control es mucho más sencillo que su equivalente en iOS, pero se realiza una función similar. Crea una lista desplazable de filas que puede tener diseños personalizados, y que responden a eventos de toque.

![](table-images/table-list-sml.png "Lista de supervisión de tabla") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Agregar una tabla

Arrastre el **tabla** control en una escena. De forma predeterminada será similar a este (un diseño de filas sin especificar solo se muestran):

[![](table-images/add-table-sml.png "Agregar una tabla")](table-images/add-table.png#lightbox)

Asigne un nombre de la tabla la **propiedades** del panel **nombre** cuadro, por lo que puede hacer referencia a en el código.

## <a name="add-a-row-controller"></a>Agregar un controlador de fila

La tabla incluye automáticamente una sola fila, representada por un controlador de la fila que contiene un **grupo** control de forma predeterminada.

Para establecer el **clase** para el controlador de fila, seleccione la fila en la **esquema del documento** y escriba un nombre de clase en el **propiedades** panel:

[![](table-images/add-row-controller-sml.png "Escriba un nombre de clase en el panel de propiedades")](table-images/add-row-controller.png#lightbox)

Una vez que se establece la clase de controlador de la fila, el IDE creará correspondiente C# archivo del proyecto. Arrastre controles (por ejemplo, etiquetas) a la fila y asígneles los nombres por lo que puede hacer referencia a código.




## <a name="create-and-populate-rows"></a>Crear y rellenar las filas

`SetNumberOfRows` crea las clases de controlador de la fila para cada fila, mediante el `Identifier` para seleccionar el correcto. Si se le asignó el controlador de fila personalizado `Identifier`, cambiar **predeterminado** en el fragmento de código siguiente para el identificador que utilizó. El `RowController` *para cada fila* se crea cuando `SetNumberOfRows` se llama y la tabla mostrada.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> Filas de tabla no están virtualizadas como en iOS. Intente limitar el número de filas (Apple recomienda inferior a 20).

Una vez que se han creado las filas, deberá rellenar cada celda (como `GetCell` lo haría en iOS). Este fragmento de código desde el [WatchTables ejemplo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) actualiza la etiqueta en cada fila

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Uso de `SetNumberOfRows` y, a continuación, un bucle que utiliza `GetRowController` hace que toda la tabla para enviarse a la inspección. En las vistas siguientes de la tabla, si necesita agregar o quitar filas específicas use `InsertRowsAt` y `RemoveRowsAt` para mejorar el rendimiento.


## <a name="respond-to-taps"></a>Responder a las pulsaciones

Puede responder a la selección de filas de dos maneras diferentes:

- implementar el `DidSelectRow` método en el controlador de interfaz, o
- Cree un segue en el guión gráfico e implemente `GetContextForSegue` si desea que la selección de fila para abrir otra escena.

### <a name="didselectrow"></a>DidSelectRow

Para controlar mediante programación la selección de filas, implementar el `DidSelectRow` método. Para abrir una nueva escena, use `PushController` y pase el identificador de la escena y el contexto de datos para usar:

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

Arrastre un segue en el guión gráfico de la fila de la tabla a otra escena (mantenga presionada la **Control** mientras arrastra).
Asegúrese de seleccionar el objeto segue y asígnele un identificador el **propiedades** pad (como `secondLevel` en el ejemplo siguiente).

En el controlador de interfaz, implemente el `GetContextForSegue` método y devolver el contexto de datos que se debería proporcionar a la escena que se presenta por el objeto segue.

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

De forma predeterminada el control de tabla tiene un tipo de fila única que puede diseñar. Para agregar más filas "plantillas" usan el **filas** cuadro el **propiedades** pad para crear más controladores de fila:

![](table-images/prototype-rows1.png "Establecer el número de filas de prototipo")

Establecer el **filas** propiedad **3** creará los marcadores de posición de fila adicional para que pueda arrastrar controles en. Para cada fila, establezca el **clase** nombre en el **propiedades** panel para asegurarse de que se crea la clase de controlador de fila.

![](table-images/prototype-rows2.png "Las filas de prototipo en el diseñador")

Para rellenar una tabla con tipos de fila diferente, utilice el `SetRowTypes` método para especificar el tipo de controlador de la fila que se usará para cada fila de la tabla. Utilice los identificadores de fila para especificar qué controlador de fila se debe usar para cada fila.

El número de elementos de esta matriz debe coincidir con el número de filas que se espera que sea en la tabla:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Al rellenar una tabla con varios controladores de fila, deberá realizar un seguimiento de qué tipo de espera como rellenar la interfaz de usuario:

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

watchOS 3 introdujo una nueva característica para las tablas: la capacidad de desplazarse por las páginas de detalles relacionados con cada fila, sin tener que volver a la tabla y elija otra fila. Deslice el dedo hacia arriba y hacia abajo, o mediante la corona Digital se pueden desplazar las pantallas de detalles.

![](table-images/table-scroll-sml.png "Ejemplo de paginación de detalle vertical") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Esta característica solo está disponible mediante la edición en el guión gráfico de Interface Builder de Xcode.

Para habilitar esta característica, seleccione el `WKInterfaceTable` en la superficie de diseño y marca el **Vertical detalle paginación** opción:

![](table-images/vertical-detail-paging-sml.png "Seleccionar la opción de paginación de detalle Vertical")

Como [explica por Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) la navegación de tabla debe usar los objetos Segue para que funcione la característica de paginación. Volver a escribir cualquier código existente que usa `PushController` usar los objetos Segue en su lugar.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Apéndice: Ejemplo de código de controlador de fila

El IDE creará dos archivos de código automáticamente cuando se crea un controlador de fila en el diseñador. El código de estos archivos generados se muestra a continuación como referencia.

La primera se denominará para la clase, por ejemplo **RowController.cs**, similar al siguiente:

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

El otro **. designer.cs** archivo es una definición de clase parcial que contiene las salidas y acciones que se crean en la superficie del diseñador, como en este ejemplo con una `WKInterfaceLabel` control:

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

Las salidas y acciones que se declara aquí pueden hacer referencia en código, sin embargo, el **. designer.cs** archivo no debe editarse directamente.



## <a name="related-links"></a>Vínculos relacionados

- [WatchTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de la tabla de Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
