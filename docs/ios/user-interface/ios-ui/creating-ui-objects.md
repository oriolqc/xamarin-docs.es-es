---
title: Crear objetos de la interfaz de usuario
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: e7a61dcf2cf2fabf575e30ef402121db3bea7912
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="creating-user-interface-objects"></a>Crear objetos de la interfaz de usuario

Grupos de Apple relacionadas con elementos de la funcionalidad en "marcos", que equivale a los espacios de nombres de Xamarin.iOS. `UIKit` es el espacio de nombres que contiene todos los controles de interfaz de usuario para iOS.

Cada vez que el código debe hacer referencia a un control de interfaz de usuario, como una etiqueta o un botón, no olvide incluir la siguiente instrucción using:

```csharp
using UIKit;
```


Todos los controles descritos en este capítulo se encuentran en el espacio de nombres UIKit, y cada nombre de clase del control de usuario tiene el `UI` prefijo.

Puede editar los controles de interfaz de usuario y las distribuciones de tres maneras:

-  **[Xamarin iOS diseñador](~/ios/user-interface/designer/index.md)**  : Diseñador de diseño integrado de Xamarin de uso para diseñar pantallas. Haga doble clic en el guión gráfico o archivos XIB para editar con el diseñador integrado.
-  **El generador de interfaz de Xcode** : arrastrar controles a los diseños de pantalla con el generador de interfaz. Abrir el guión gráfico o el archivo XIB en Xcode haciendo clic en el archivo en el **panel de la solución** y elija **abrir con > Xcode interfaz generador**.
-  **Con C#** : controles pueden ser mediante programación construidos con código y agrega a la jerarquía de vista.

Pueden agregarse nuevos archivos de guión gráfico y XIB, haga doble clic en un proyecto de iOS y elija **Agregar > nuevo archivo...** .

Cualquier método que utilice, controlar las propiedades y eventos todavía se pueden manipular con C# en la lógica de aplicación.

## <a name="using-xamarin-ios-designer"></a>Usar el Diseñador de iOS de Xamarin

Para empezar a crear la interfaz de usuario en el Diseñador de iOS, haga doble clic en un archivo del guión gráfico. Se pueden arrastrar controles a la superficie de diseño de la **cuadro de herramientas** tal como se muestra a continuación:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 [![](creating-ui-objects-images/image2b.png "Panel de cuadro de herramientas")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 [![](creating-ui-objects-images/image2b-vs.png "Panel de cuadro de herramientas - Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Cuando se selecciona un control en la superficie de diseño la **panel de propiedades** mostrará los atributos de ese control. El **Widget > identidad > nombre** campo, que se rellena en la captura de pantalla siguiente, se utiliza como el *toma* nombre. Se trata de cómo puede hacer referencia el control en C#:

 [![](creating-ui-objects-images/image3b.png "Panel de Widget de propiedades")](creating-ui-objects-images/image3b.png#lightbox)

Para obtener un análisis más profundo en mediante el Diseñador de iOS, consulte el [introducción en el Diseñador de iOS](~/ios/user-interface/designer/introduction.md) guía.

## <a name="using-xcode-interface-builder"></a>Mediante el generador de interfaz de Xcode

Si no está familiarizado con el uso de la interfaz de generador, consulte de Apple [interfaz generador](https://developer.apple.com/xcode/interface-builder/) documentos.

Para abrir un guión gráfico en Xcode, haga clic en para acceder al menú contextual para el archivo del guión gráfico y elija Abrir con el **Xcode interfaz generador**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 [![](creating-ui-objects-images/imagexcode.png "Menú contextual de guión gráfico: Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](creating-ui-objects-images/imagexcode-vs.png "Menú contextual de guión gráfico: Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Se pueden arrastrar controles a la superficie de diseño de la **biblioteca de objetos** se muestra a continuación:

 [![](creating-ui-objects-images/image5a.png "Biblioteca de objetos de Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Al diseñar la interfaz de usuario con el generador de interfaz debe crear un **toma** para cada control que desee hacer referencia en C#. Esto se realiza activando el **Asistente Editor** mediante el centro de **Editor** botón en el botón de barra de herramientas Xcode:

 [![](creating-ui-objects-images/image6a.png "Botón Ayudante para el Editor")](creating-ui-objects-images/image6a.png#lightbox)

Haga clic en un objeto de interfaz de usuario; a continuación, **Control arrastre** en el archivo .h. Para ** arrastre Control **, mantenga presionada la tecla control, a continuación, haga clic y mantenga sobre el objeto de interfaz de usuario que va a crear para la toma de corriente (o acción). Mantener presionada la tecla CTRL mientras arrastra en el archivo de encabezado. Finalizar arrastrando a continuación el `@interface` definición. Debe aparecer una línea azul con un título insertar toma de corriente o una colección de salida, como se muestra en la captura de pantalla siguiente.

Al soltar el clic se le pedirá que proporcione un nombre para la toma de corriente, que se usará para crear una propiedad de C# que se puede hacer referencia en el código:

 [![](creating-ui-objects-images/image8a.png "Crear una salida")](creating-ui-objects-images/image8a.png#lightbox)

Para obtener más información sobre cómo el generador de interfaz de Xcode se integra con Visual Studio para Mac, consulte el [generación de código de Xib](~/ios/internals/xib-code-generation.md#generated) documento.

##  <a name="using-c"></a>Uso de C#

Si decide crear mediante programación un objeto de interfaz de usuario con C# (en una vista o el controlador de vista, por ejemplo), siga estos pasos:

-  Declarar un campo de nivel de clase para el objeto de interfaz de usuario. Crear el propio control una vez, en `ViewDidLoad` por ejemplo. El objeto puede hacer referencia a lo largo de los métodos del ciclo de vida del controlador de vista (p. ej.
`ViewWillAppear`).
-  Crear un `CGRect` que define el marco del control (sus coordenadas X e Y en la pantalla, así como su ancho y alto). Debe asegurarse de que tiene un `using CoreGraphics` la directiva para esto.
-  Llame al constructor para crear y asignar el control.
-  Establecer las propiedades o los controladores de eventos.
-  Llame a `Add()` para agregar el control a la jerarquía de vista.

Este es un ejemplo simple de crear un `UILabel` en un controlador de vista con C#:

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

Cuando se agregan controladores de la vista a la superficie de diseño, se crean dos correspondientes archivos de C# en el proyecto. En este ejemplo, `ControlsViewController.cs` y `ControlsViewController.designer.cs` se han creado automáticamente:

 [![](creating-ui-objects-images/image9b.png "Clase parcial ViewController")](creating-ui-objects-images/image9b.png#lightbox)

El `MainViewController.cs` archivo está pensando para *el código*. Aquí es donde el `View` métodos del ciclo de vida como `ViewDidLoad` y `ViewWillAppear` se implementan y donde puede agregar sus propias propiedades, campos y métodos.

El `ControlsViewController.designer.cs` es código generado que contiene una clase parcial. Se agrega al nombre un control en el diseño expuesta en Visual Studio para Mac, o crear un enchufe o acción en Xcode, una propiedad correspondiente o el método parcial, en el archivo de diseñador (designer.cs). El código siguiente muestra un ejemplo de código generado para dos botones y una vista de texto, donde uno de los botones también tiene un `TouchUpInside` eventos.

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

El `designer.cs` archivo no debe editarse manualmente: IDE (Visual Studio para Mac o en Visual Studio) es responsable de mantenerlo sincronizado con el guión gráfico.

Cuando se agregan los objetos de la interfaz de usuario mediante programación a un `View` o `ViewController`, crear una instancia y administrar las referencias a objetos usted mismo y, por tanto, no se requiere ningún archivo de diseñador.



## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
