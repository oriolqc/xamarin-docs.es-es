---
title: Controles personalizados en el Diseñador de Xamarin para iOS
description: El Diseñador de Xamarin para iOS admite controles personalizados de representación creado en el proyecto o se hace referencia desde orígenes externos como el almacén de componentes de Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 113fab2fd0d1a055d566606885cefbafe3185529
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30782189"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Controles personalizados en el Diseñador de Xamarin para iOS

_El Diseñador de Xamarin para iOS admite controles personalizados de representación creado en el proyecto o se hace referencia desde orígenes externos como el almacén de componentes de Xamarin._

El Diseñador de Xamarin para iOS es una herramienta eficaz para visualizar la interfaz de usuario de la aplicación y proporciona compatibilidad para la mayoría de las vistas de iOS y controladores de la vista de edición WYSIWYG. La aplicación también puede contener controles personalizados que amplían las integradas en iOS. Si estos controles personalizados se escriben con algunas instrucciones en mente, también se representan mediante el diseñador, proporcionar una experiencia de edición aún más sofisticada de iOS. Este documento echa un vistazo a esas directrices.

## <a name="requirements"></a>Requisitos

Un control que cumple los siguientes requisitos se representará en la superficie de diseño:

1.  Es una subclase directa o indirecta de [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) o [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIView/Controller). Otros [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) subclases aparecerá como un icono en la superficie de diseño.
2.  Tiene una [RegisterAttribute](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) va a exponer a objetivo-C.
3.  Tiene [el constructor de IntPtr necesario](~/ios/internals/api-design/index.md).
4.  O bien implementa la [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) interfaz o tiene un [DesignTimeVisibleAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DesignTimeVisibleAttribute/) establecida en True.

Controles definidos en el código que cumplen los requisitos anteriores aparecerán en el diseñador cuando se compila el proyecto que lo contiene para el simulador. De forma predeterminada, todos los controles personalizados aparecerán en el **componentes personalizados** sección de la **cuadro de herramientas**. Sin embargo, el [CategoryAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.CategoryAttribute/) se puede aplicar a la clase del control personalizado para especificar una sección diferente.

El diseñador no admite cargar bibliotecas de terceros Objective-C.

## <a name="custom-properties"></a>Propiedades personalizadas

Una propiedad declarada por un control personalizado aparecen en el panel de propiedades si se cumplen las condiciones siguientes:

1.  La propiedad tiene un captador público y un establecedor.
1.  La propiedad tiene un [ExportAttribute](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) , así como un [BrowsableAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.BrowsableAttribute/) establecida en True.
1.  El tipo de propiedad es un tipo numérico, tipo de enumeración, string, bool, [SizeF](https://developer.xamarin.com/api/type/System.Drawing.SizeF/), [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/), o [UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/). Se puede expandir a esta lista de tipos admitidos en el futuro.


La propiedad también se puede decorar con un [DisplayNameAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DisplayNameAttribute/) para especificar la etiqueta que se muestra para él en el panel de propiedades.

## <a name="initialization"></a>Inicialización

Para `UIViewController` subclases, debe usar el [ViewDidLoad](https://developer.xamarin.com/api/member/UIKit.UIViewController.ViewDidLoad/) método para el código que depende de que creó en el Diseñador de vistas.

Para `UIView` y otros `NSObject` subclases, la [AwakeFromNib](https://developer.xamarin.com/api/member/Foundation.NSObject.AwakeFromNib/) método es el lugar recomendado para realizar la inicialización del control personalizado una vez que se carga desde el archivo de diseño. Esto es porque no se establecerán cualquier propiedad personalizada que se establece en el panel de propiedades cuando se ejecuta el constructor del control, pero se establecerá antes `AwakeFromNib` se llama:


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

Si el control también está diseñado para crearse directamente desde el código, puede que desee crear un método que tiene el código de inicialización comunes, como el siguiente:

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

Debe tener cuidado sobre cuándo y dónde debe inicializar las propiedades pueden diseñables en un componente personalizado como para no sobrescribir los valores que se han establecido en el Diseñador de iOS. Como ejemplo, considere el siguiente código:

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

El `CustomView` componente expone un `Counter` propiedad que se pueden establecer por el desarrollador en el Diseñador de iOS. Sin embargo, con independencia de qué valor se establece en el diseñador, el valor de la `Counter` propiedad siempre será cero (0). El motivo es el siguiente:

-  Una instancia de la `CustomControl` exagerar desde el archivo del guión gráfico.
-  Se establecen las propiedades que se modifica en el Diseñador de iOS (por ejemplo, establecer el valor de la `Counter` a dos (2), por ejemplo).
-  El `AwakeFromNib` método se ejecuta y se realiza una llamada al componente `Initialize` método.
-  Dentro de `Initialize` el valor de la `Counter` propiedad que se va a se restablece en cero (0).


Para corregir la situación anterior, inicializar el `Counter` propiedad en otros lugares (como se muestra en el constructor del componente) o no se invalidan los `AwakeFromNib` método y llamar a `Initialize` si ninguna inicialización continúa fuera de lo que requiere que el componente actualmente se controla por sus constructores.

## <a name="design-mode"></a>Modo de diseño

En la superficie de diseño, debe cumplir un control personalizado a algunas restricciones:

-  Recursos del paquete de aplicación no están disponibles en modo de diseño. Las imágenes están disponibles cuando carga a través de [UIImage métodos](https://developer.xamarin.com/api/type/UIKit.UIImage/%2fM) .
-  Operaciones asincrónicas, tales como las solicitudes web, no deben realizarse en modo de diseño. La superficie de diseño no admite la animación o cualquier otra actualización asincrónica a la interfaz de usuario del control.


Puede implementar un control personalizado [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) y use la [DesignMode](https://developer.xamarin.com/api/property/System.ComponentModel.ISite.DesignMode/) propiedad que se va a comprobar si está en la superficie de diseño. En este ejemplo, la etiqueta mostrará "Modo de diseño" en la superficie de diseño y el "Tiempo de ejecución" en tiempo de ejecución:

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

Siempre debe comprobar la `Site` propiedad `null` antes de intentar tener acceso a cualquiera de sus miembros. Si `Site` es `null`, es seguro asumir el control no se está ejecutando en el diseñador.
En el modo de diseño, `Site` se establecerá después de haberse ejecutado el constructor del control y antes de `AwakeFromNib` se llama.

## <a name="debugging"></a>Depuración

Un control que cumple los requisitos anteriores se muestran en el cuadro de herramientas y se representan en la superficie.
Si no se procesa un control, busque errores en el control o uno de sus dependencias.

La superficie de diseño con frecuencia puede detectar las excepciones producidas por controles individuales mientras continúa representar otros controles. El control error se reemplaza por un marcador de posición rojo y podrá ver el seguimiento de excepción, haga clic en el icono de exclamación:

 ![](ios-designable-controls-overview-images/exception-box.png "Un control defectuoso como marcador de posición rojo y los detalles de excepción")

Si los símbolos de depuración están disponibles para el control, el seguimiento tendrán nombres de archivo y números de línea. Haga doble clic en una línea en el seguimiento de pila saltará a la línea en el código fuente.

Si el diseñador no puede aislar el control de error, aparecerá un mensaje de advertencia en la parte superior de la superficie de diseño:

 ![](ios-designable-controls-overview-images/info-bar.png "Un mensaje de advertencia en la parte superior de la superficie de diseño")

Representación completa se reanudará cuando el control error está fija o se quita de la superficie de diseño.

## <a name="summary"></a>Resumen

En este artículo se introdujo la creación y aplicación de controles personalizados en el Diseñador de iOS. En primer lugar describen los requisitos que los controles deben cumplir para que se representan en la superficie de diseño y exponer propiedades personalizadas en el panel de propiedades. A continuación, examinando el código subyacente - inicialización del control y la propiedad DesignMode. Por último describe lo que sucede cuando se producen excepciones y cómo resolver este problema.