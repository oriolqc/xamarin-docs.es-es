---
title: Comportamientos asociados
description: Comportamientos asociados son las clases estáticas con una o varias de las propiedades adjuntas. En este artículo se muestra cómo crear y usar comportamientos asociados.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2c9bd9ad4e7572b9eae6f0073da8a2c8f1e7c9fc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995351"
---
# <a name="attached-behaviors"></a>Comportamientos asociados

_Comportamientos asociados son las clases estáticas con una o varias de las propiedades adjuntas. En este artículo se muestra cómo crear y consumir los comportamientos asociados._

## <a name="overview"></a>Información general

Una propiedad adjunta es un tipo especial de propiedad enlazable. Se define en una clase pero adjunta a otros objetos, y son reconocibles en XAML como atributos que contienen una clase y un nombre de propiedad separados por un punto.

Puede definir una propiedad adjunta un `propertyChanged` delegado que se ejecutará cuando el valor de la propiedad cambia, como cuando la propiedad se establece en un control. Cuando el `propertyChanged` ejecuta el delegado, ha pasado una referencia al control en el que se va a adjuntar y parámetros que contienen los valores antiguos y nuevos para la propiedad. Este delegado puede usarse para agregar la nueva funcionalidad al control que se adjunta a la propiedad mediante la manipulación de la referencia que se pasa, como se indica a continuación:

1. El `propertyChanged` delegado convierte la referencia de control, que se recibe como un [ `BindableObject` ](xref:Xamarin.Forms.BindableObject), con el tipo de control que el comportamiento está diseñado para mejorar.
1. El `propertyChanged` delegado modifica las propiedades del control, llama a métodos del control, o controladores de eventos de registros para los eventos expuestos por el control, para implementar la funcionalidad del comportamiento de núcleo.

Un problema con los comportamientos asociados es la que se definen en un `static` (clase), con `static` propiedades y métodos. Esto dificulta crear comportamientos asociados que tienen estado. Además, los comportamientos de Xamarin.Forms reemplazaron comportamientos asociados como el método preferido para la construcción de comportamiento. Para obtener más información acerca de los comportamientos de Xamarin.Forms, consulte [comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) y [comportamientos reutilizables](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Creación de un comportamiento asociado

La aplicación de ejemplo se muestra un `NumericValidationBehavior`, que resalta el valor especificado por el usuario en un [ `Entry` ](xref:Xamarin.Forms.Entry) controlar en rojo, si no es un `double`. El comportamiento se muestra en el ejemplo de código siguiente:

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

El `NumericValidationBehavior` clase contiene una propiedad adjunta mencionada `AttachBehavior` con un `static` captador y establecedor, que controla la adición o eliminación del comportamiento para el control al que se adjuntará. Esto adjunta la propiedad registra el `OnAttachBehaviorChanged` método que se ejecutará cuando cambia el valor de la propiedad. Este método registra o anular registra un controlador de eventos para el [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) eventos, según el valor de la `AttachBehavior` propiedad adjunta. Proporciona la funcionalidad básica del comportamiento de la `OnEntryTextChanged` método, que analiza el valor especificado en el [ `Entry` ](xref:Xamarin.Forms.Entry) por el usuario y establece el `TextColor` propiedad a rojo si el valor no es un `double`.

## <a name="consuming-an-attached-behavior"></a>Consumir un comportamiento asociado

El `NumericValidationBehavior` puede utilizarse la clase agregando el `AttachBehavior` propiedad adjunta un [ `Entry` ](xref:Xamarin.Forms.Entry) controlar, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

El equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

En tiempo de ejecución, el comportamiento responderá a la interacción con el control, según la implementación del comportamiento. Las capturas de pantalla siguientes muestran el comportamiento de adjunto responder a la entrada no válida:

[![](attached-images/screenshots-sml.png "Ejemplo de aplicación con el comportamiento asociado")](attached-images/screenshots.png#lightbox "aplicación con un comportamiento adjunto de ejemplo")

> [!NOTE]
> Se escriben los comportamientos asociados para un tipo de control (o superclase que puede aplicar a muchos controles), y solo se debe agregar a un control compatible. Al intentar adjuntar un comportamiento a un control incompatible dará como resultado un comportamiento desconocido y depende de la implementación de comportamiento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Quitar un comportamiento asociado de un Control

El `NumericValidationBehavior` clase se puede quitar de un control estableciendo la `AttachBehavior` propiedad adjunta `false`, tal y como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

El equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

En tiempo de ejecución, el `OnAttachBehaviorChanged` será el método se ejecuta cuando el valor de la `AttachBehavior` se establece la propiedad adjunta en `false`. El `OnAttachBehaviorChanged` método, a continuación, anular registrará el controlador de eventos para el [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) eventos, lo que garantiza que el comportamiento no se ejecuta cuando el usuario interactúa con el control.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo crear y consumir los comportamientos asociados. Los comportamientos asociados son clases `static` con una o varias propiedades asociadas.


## <a name="related-links"></a>Vínculos relacionados

- [Comportamientos asociados (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
