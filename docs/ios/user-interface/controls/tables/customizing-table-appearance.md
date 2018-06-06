---
title: Personalizar la apariencia de una tabla en Xamarin.iOS
description: Este documento describe cómo personalizar la apariencia de una tabla en Xamarin.iOS. Se trata los estilos de celda, Accesorios, separadores de celda y diseños de celda personalizado.
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 03383c69afb6afa9282d44751475d74fdcd92d4a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789959"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>Personalizar la apariencia de una tabla en Xamarin.iOS

La manera más sencilla de cambiar la apariencia de una tabla es usar un estilo de celda diferentes. Puede cambiar el estilo de celda se utiliza cuando se crea cada celda de la `UITableViewSource`del `GetCell` método.

## <a name="cell-styles"></a>Estilos de celda

Existen cuatro estilos integrados:

-  **Predeterminado** : admite una `UIImageView`.
-  **Subtítulo** : admite una `UIImageView` y el subtítulo.
-  **Valor1** : derecha subtítulo alineado, es compatible con un `UIImageView`.
-  **Value2** : título es alineado a la derecha y subtítulo está alineado a la izquierda (pero no hay ninguna imagen).


Estas capturas de pantalla muestran cómo aparece cada estilo:

 [![](customizing-table-appearance-images/image7.png "Estas capturas de pantalla muestran cómo aparece cada estilo")](customizing-table-appearance-images/image7.png#lightbox)

El ejemplo **CellDefaultTable** contiene el código para generar estas pantallas. El estilo de celda se establece el `UITableViewCell` constructor, similar al siguiente:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

[Admite propiedades](http://developer.xamarin.com/api/type/UIKit.UITableViewCell/) de la celda, a continuación, se puede establecer estilo:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Accesorios

Las celdas pueden tener los siguientes accesorios que se agrega a la derecha de la vista:

-   **Marca de verificación** : puede utilizarse para indicar la selección múltiple en una tabla.
-   **DetailButton** : responde a touch independientemente del resto de la celda, lo que le permite realizar una función diferente a tocar la celda en Sí (como la apertura de una ventana emergente o una ventana que no es parte de un `UINavigationController` pila).
-   **DisclosureIndicator** : se usa normalmente para indicar que tocar la celda se abrirá otra vista.
-   **DetailDisclosureButton** : una combinación de la `DetailButton` y `DisclosureIndicator`.


Se trata de su apariencia:

 [![](customizing-table-appearance-images/image8.png "Accesorios de ejemplo")](customizing-table-appearance-images/image8.png#lightbox)

Para mostrar uno de estos accesorios puede establecer el `Accessory` propiedad en el `GetCell` método:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

Cuando el `DetailButton` o `DetailDisclosureButton` se muestran, también debe invalidar el `AccessoryButtonTapped` para realizar alguna acción cuando que se toca.

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

El ejemplo **CellAccessoryTable** muestra un ejemplo del uso de accesorios.

## <a name="cell-separators"></a>Separadores de celda

Los separadores de celda son celdas de la tabla utilizadas para separar la tabla. Las propiedades se establecen en la tabla.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

También es posible agregar un efecto de desenfoque o dinamismo para el separador:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

El separador también puede tener un recuadro:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>La creación de diseños de celda personalizado

Para cambiar el estilo visual de una tabla que se deben proporcionar celdas personalizadas para que se muestre. La celda personalizada puede tener distintos diseños de colores y el control.

El ejemplo CellCustomTable se implementa un `UITableViewCell` subclase que define un diseño personalizado de `UILabel`s y un `UIImage` con diferentes fuentes y colores. Las celdas resultantes tiene el siguiente aspecto:

 [![](customizing-table-appearance-images/image9.png "Diseños de celda personalizado")](customizing-table-appearance-images/image9.png#lightbox)

La clase de celda personalizada consiste en solo tres métodos:

-   **Constructor** – crea los controles de interfaz de usuario y establece las propiedades de estilo personalizado (p. ej. tipo de fuente, tamaño y colores).
-   **UpdateCell** : un método para `UITableView.GetCell` se utiliza para establecer las propiedades de la celda.
-   **LayoutSubviews** : establecer la ubicación de los controles de interfaz de usuario. En el ejemplo, cada celda tiene el mismo diseño, pero una celda más compleja (especialmente en aquellos con diferentes tamaños) que tenga posiciones de diseño diferentes en función del contenido que se va a mostrar.


El ejemplo de código en **CellCustomTable > CustomVegeCell.cs** sigue:

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

El `GetCell` método de la `UITableViewSource` debe modificarse para crear la celda personalizada:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```



## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
