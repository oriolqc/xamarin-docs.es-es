---
title: Creación de objetos de la interfaz de usuario en Xamarin.iOS
description: Este documento proporciona información general de cómo crear una interfaz de usuario en Xamarin.iOS. Describe el Diseñador de iOS, Xcode Interface Builder, C#y los guiones gráficos.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a80bf876e239e1788a371a1f09d36d73247d4611
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865012"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Creación de objetos de la interfaz de usuario en Xamarin.iOS

Grupos de Apple relacionados con partes de funcionalidad en "marcos", que equivalen a los espacios de nombres de Xamarin.iOS. `UIKit` es el espacio de nombres que contiene todos los controles de interfaz de usuario para iOS.

Cada vez que el código debe hacer referencia a un control de interfaz de usuario, como una etiqueta o un botón, no olvide incluir la siguiente instrucción using:

```csharp
using UIKit;
```

Todos los controles que se describen en este capítulo se encuentran en el espacio de nombres UIKit, y cada nombre de clase del control de usuario tiene el `UI` prefijo.

Puede editar los controles de interfaz de usuario y los diseños de tres maneras:

-  **[Diseñador de iOS de Xamarin](~/ios/user-interface/designer/index.md)**  : Diseñador de diseño integrado de Xamarin de uso para diseñar pantallas. Haga doble clic en el guión gráfico o los archivos XIB para editar con el diseñador integrado.
-  **Interface Builder de Xcode** : arrastre controles a los diseños de pantalla con Interface Builder. Abra el guión gráfico o un archivo XIB de Xcode haciendo clic con el archivo en el **panel de solución** y eligiendo **abrir con > Interface Builder de Xcode**.
-  **Uso de C#**  – controles también pueden ser mediante programación construidos con código y agregados a la jerarquía de vistas.

Se pueden agregar nuevos archivos de guión gráfico y XIB con el botón secundario en un proyecto de iOS y eligiendo **Agregar > nuevo archivo...** .

Cualquier método que utilice, las propiedades del control y los eventos todavía se pueden manipular con C# en la lógica de aplicación.

## <a name="using-xamarin-ios-designer"></a>Utilizar Xamarin iOS Designer

Para empezar a crear la interfaz de usuario en el Diseñador de iOS, haga doble clic en un archivo de guión gráfico. Se puede arrastrar controles a la superficie de diseño desde el **cuadro de herramientas** tal como se muestra a continuación:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Panel de cuadro de herramientas")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Panel de cuadro de herramientas - Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Cuando se selecciona un control en la superficie de diseño la **panel de propiedades** mostrarán los atributos para ese control. El **Widget > identidad > nombre** campo, que se rellena en la captura de pantalla siguiente, se usa como el *toma* nombre. Se trata de cómo hacer referencia el control en C#:

 [![](creating-ui-objects-images/image3b.png "Panel del Widget de propiedades")](creating-ui-objects-images/image3b.png#lightbox)

Para obtener un análisis más profundo de mediante el Diseñador de iOS, consulte el [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md) guía.

## <a name="using-xcode-interface-builder"></a>Uso de Interface Builder de Xcode

Si no está familiarizado con el uso de Interface Builder, consulte Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/) documentos.

Para abrir un guion gráfico en Xcode, haga doble clic para acceder al menú contextual para el archivo de guión gráfico y elija Abrir con el **Interface Builder de Xcode**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Menú contextual de guión gráfico: Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Menú contextual de guión gráfico: Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Se puede arrastrar controles a la superficie de diseño desde el **biblioteca de objetos** se muestra a continuación:

 [![](creating-ui-objects-images/image5a.png "Biblioteca de objetos de Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Al diseñar la interfaz de usuario con Interface Builder, debe crear un **toma** para cada control que desea hacer referencia en C#. Esto se realiza al activar el **Editor del asistente** mediante el centro de **Editor** botón en el botón de barra de herramientas de Xcode:

 [![](creating-ui-objects-images/image6a.png "Botón Editor del Asistente")](creating-ui-objects-images/image6a.png#lightbox)

Haga clic en un objeto de interfaz de usuario; a continuación, **Control arrastrar** en el archivo. h. Para **Control arrastrar**, mantenga presionada la tecla control, a continuación, haga clic y mantenga sobre el objeto de interfaz de usuario que va a crear para la toma de corriente (o acción). Mantenga presionada la tecla Control mientras arrastra al archivo de encabezado. Finalizar arrastrando a continuación el `@interface` definición. Debe aparecer una línea azul con un título toma insertar o una colección de salida, como se muestra en la captura de pantalla siguiente.

Al soltar el clic se le pedirá que proporcione un nombre para la toma de corriente, que se usará para crear un C# propiedad que se puede hacer referencia en el código:

 [![](creating-ui-objects-images/image8a.png "Creación de una salida")](creating-ui-objects-images/image8a.png#lightbox)

Para obtener más información sobre cómo Interface Builder de Xcode se integra con Visual Studio para Mac, consulte el [generación de código Xib](~/ios/internals/xib-code-generation.md#generated) documento.

## <a name="using-c"></a>Uso deC#

Si decide crear mediante programación un objeto de interfaz de usuario mediante C# (en una vista o controlador de vista, por ejemplo), siga estos pasos:

-  Declarar un campo de nivel de clase para el objeto de interfaz de usuario. Cree el propio control una vez, en `ViewDidLoad` por ejemplo. El objeto puede hacer referencia a lo largo de los métodos del ciclo de vida del controlador de vista (p ej.
`ViewWillAppear`).
-  Crear un `CGRect` que define el marco del control (sus coordenadas X e Y en la pantalla, así como su ancho y alto). Deberá asegurarse de que tiene un `using CoreGraphics` la directiva para esto.
-  Llame al constructor para crear y asignar el control.
-  Establecer las propiedades o los controladores de eventos.
-  Llame a `Add()` para agregar el control a la jerarquía de vistas.

Este es un ejemplo sencillo de creación de un `UILabel` en un controlador de vista mediante C#:

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>Uso de C# y guiones gráficos

Cuando se agregan controladores de vista a la superficie de diseño, dos correspondiente C# archivos se crean en el proyecto. En este ejemplo, `ControlsViewController.cs` y `ControlsViewController.designer.cs` se han creado automáticamente:

 [![](creating-ui-objects-images/image9b.png "ViewController de clase parcial")](creating-ui-objects-images/image9b.png#lightbox)

El `MainViewController.cs` está destinado a archivo *su código*. Aquí es donde el `View` métodos del ciclo de vida como `ViewDidLoad` y `ViewWillAppear` se implementan y donde puede agregar sus propias propiedades, campos y métodos.

El `ControlsViewController.designer.cs` es código generado que contiene una clase parcial. Al nombre un control en el diseño de superficie en Visual Studio para Mac, o crear una acción o salida en Xcode, una propiedad correspondiente o el método parcial, se agrega al archivo de diseñador (designer.cs). El código siguiente muestra un ejemplo de código generado para los dos botones y una vista de texto, donde uno de los botones también tiene un `TouchUpInside` eventos.

Estos elementos de la clase parcial que su código pueda hacer referencia a los controles y responder a las acciones que se declaran en la superficie de diseño:

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

El `designer.cs` archivo no debe editarse manualmente: el IDE (Visual Studio para Mac o Visual Studio) es responsable de mantenerlo sincronizado con el guión gráfico.

Cuando se agregan los objetos de la interfaz de usuario mediante programación a un `View` o `ViewController`, crear instancias y administrar las referencias de objeto por su cuenta y, por tanto, no se requiere ningún archivo de diseñador.



## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/monotouch/Controls/)
