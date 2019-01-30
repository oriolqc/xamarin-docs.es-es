---
title: Controles personalizados en el Diseñador de Xamarin para iOS
description: El Diseñador de Xamarin para iOS es compatible con los controles de representación personalizado creado en el proyecto o hace referencia desde orígenes externos, como el Store del componente de Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c409fcc018379401c1ab40573495da12a8220c5a
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233671"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Controles personalizados en el Diseñador de Xamarin para iOS

_El Diseñador de Xamarin para iOS es compatible con los controles de representación personalizado creado en el proyecto o hace referencia desde orígenes externos, como el Store del componente de Xamarin._

El Diseñador de Xamarin para iOS es una herramienta eficaz para visualizar la interfaz de usuario de la aplicación y proporciona compatibilidad para la mayoría de las vistas de iOS y controladores de vista de edición de WYSIWYG. La aplicación también puede contener controles personalizados que extienden las integradas en iOS. Si estos controles personalizados se escriben con algunas instrucciones en mente, también puede representar mediante iOS Designer, que proporciona una experiencia de edición incluso más completa. Este documento da un vistazo a esas instrucciones.

## <a name="requirements"></a>Requisitos

En la superficie de diseño se representará un control que cumple todos los requisitos siguientes:

1.  Es una subclase directa o indirecta de [UIView](xref:UIKit.UIView) o [UIViewController](xref:UIKit.UIViewController). Otros [NSObject](xref:Foundation.NSObject) subclases aparecerá como un icono en la superficie de diseño.
2.  Tiene un [RegisterAttribute](xref:Foundation.RegisterAttribute) exponerlo a Objective-C.
3.  Tiene [el constructor de IntPtr necesario](~/ios/internals/api-design/index.md).
4.  O bien implementa el [IComponent](xref:System.ComponentModel.IComponent) interfaz o tiene un [DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute) establecida en True.

Controles definidos en el código que cumplan los requisitos anteriores aparecerán en el diseñador cuando se compila su proyecto de contenedor para el simulador. De forma predeterminada, todos los controles personalizados aparecerán en el **componentes personalizados** sección de la **cuadro de herramientas**. Sin embargo, el [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute) se puede aplicar a la clase del control personalizado para especificar una sección diferente.

El diseñador no admite cargar bibliotecas de Objective-C de terceros.

## <a name="custom-properties"></a>Propiedades personalizadas

Una propiedad declarada por un control personalizado aparecerá en el panel de propiedades si se cumplen las condiciones siguientes:

1.  La propiedad tiene un captador público y un establecedor.
1.  La propiedad tiene un [ExportAttribute](xref:Foundation.ExportAttribute) , así como un [BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) establecida en True.
1.  El tipo de propiedad es un tipo numérico, tipo de enumeración, string, bool, [SizeF](xref:System.Drawing.SizeF), [UIColor](xref:UIKit.UIColor), o [UIImage](xref:UIKit.UIImage). Esta lista de tipos compatibles se puede expandir en el futuro.


La propiedad también puede estar representada con un [atributo DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute) para especificar la etiqueta que se muestra para él en el panel de propiedades.

## <a name="initialization"></a>Inicialización

Para `UIViewController` subclases, debe usar el [ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad) método para el código que depende de las vistas que creó en el diseñador.

Para `UIView` y otros `NSObject` subclases, el [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib) método es el lugar recomendado para realizar la inicialización del control personalizado una vez que se carga desde el archivo de diseño. Esto es porque no se establecerán todas las propiedades personalizadas que se establece en el panel de la propiedad cuando se ejecuta el constructor del control, pero se establecerán antes `AwakeFromNib` se denomina:


```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

Si el control está diseñado también para crearse directamente desde el código, desea crear un método que tiene el código de inicialización común, similar al siguiente:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>AwakeFromNib e inicialización de propiedad

Debe tener cuidado sobre cuándo y dónde debe inicializar las propiedades pueden diseñables en un componente personalizado como para no sobrescribir los valores que se han establecido en el Diseñador de iOS. Por ejemplo, realice el siguiente código:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {
    
    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

El `CustomView` componente expone un `Counter` propiedad que se puede establecer por el desarrollador en el Diseñador de iOS. Sin embargo, con independencia de qué valor se establece en el diseñador, el valor de la `Counter` propiedad siempre será cero (0). El motivo es el siguiente:

-  Una instancia de la `CustomControl` se aumenta desde el archivo de guión gráfico.
-  Se establecen las propiedades modificadas en el Diseñador de iOS (por ejemplo, establecer el valor de la `Counter` a dos (2), por ejemplo).
-  El `AwakeFromNib` método se ejecuta y se realiza una llamada al componente `Initialize` método.
-  Dentro de `Initialize` el valor de la `Counter` se está restableciendo la propiedad en cero (0).


Para corregir la situación anterior, inicializar el `Counter` propiedad en otros lugares (como en el constructor del componente) o no se invalidan los `AwakeFromNib` método y llamar a `Initialize` si ninguna inicialización adicional fuera de lo que requiere que el componente actualmente se controla por sus constructores.

## <a name="design-mode"></a>Modo de diseño

En la superficie de diseño, debe cumplir un control personalizado algunas restricciones:

-  Recursos del lote de aplicación no están disponibles en modo de diseño. Las imágenes están disponibles cuando se cargan a través de [UIImage métodos](xref:UIKit.UIImage) .
-  Las operaciones asincrónicas, como solicitudes web, no deben realizarse en modo de diseño. La superficie de diseño no es compatible con la animación o cualquier otra actualización asincrónica a la interfaz de usuario del control.


Puede implementar un control personalizado [IComponent](xref:System.ComponentModel.IComponent) y usar el [DesignMode](xref:System.ComponentModel.ISite.DesignMode) propiedad para comprobar si está en la superficie de diseño. En este ejemplo, la etiqueta mostrará "Modo de diseño" en la superficie de diseño y el "Runtime" en tiempo de ejecución:

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null &amp;&amp; Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

Debe comprobar siempre el `Site` propiedad `null` antes de intentar tener acceso a cualquiera de sus miembros. Si `Site` es `null`, es seguro asumir el control no se está ejecutando en el diseñador.
En el modo de diseño, `Site` se establecerá una vez se ha ejecutado el constructor del control y antes de `AwakeFromNib` se llama.

## <a name="debugging"></a>Depuración

Un control que cumple los requisitos anteriores se muestra en el cuadro de herramientas y se representan en la superficie.
Si no se representa un control, busque errores en el control o uno de sus dependencias.

La superficie de diseño a menudo puede detectar las excepciones producidas por controles individuales sin dejar de procesar otros controles. El control error se reemplaza por un marcador de posición de rojo, y puede ver el seguimiento de excepciones, haga clic en el icono de exclamación:

 ![](ios-designable-controls-overview-images/exception-box.png "Un control defectuoso como marcador de posición rojo y los detalles de excepción")

Si los símbolos de depuración están disponibles para el control, tendrá el seguimiento de los nombres de archivo y números de línea. Haga doble clic en una línea en el seguimiento de pila se desplazará hasta esa línea en el código fuente.

Si el diseñador no puede aislar el control defectuoso, aparecerá un mensaje de advertencia en la parte superior de la superficie de diseño:

 ![](ios-designable-controls-overview-images/info-bar.png "Un mensaje de advertencia en la parte superior de la superficie de diseño")

Representación completa se reanudará cuando se ha corregido el control defectuoso o se quita de la superficie de diseño.

## <a name="summary"></a>Resumen

Este artículo presenta la creación y aplicación de controles personalizados en el Diseñador de iOS. En primer lugar describen los requisitos que deben cumplir los controles para representarse en la superficie de diseño y exponer propiedades personalizadas en el panel de propiedades. A continuación, examinamos el código subyacente: inicialización del control y la propiedad DesignMode. Por último describe lo que sucede cuando se produzcan excepciones y cómo resolver este problema.