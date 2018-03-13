---
title: Otros comportamientos adjuntados
description: "Otros comportamientos adjuntados son las clases estáticas con una o varias propiedades adjuntas. Este artículo demuestra cómo crear y utilizar los comportamientos adjuntos."
ms.topic: article
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 84e60e8ce698e3d87db3e1bdc61613325ad831c8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="attached-behaviors"></a>Otros comportamientos adjuntados

_Otros comportamientos adjuntados son las clases estáticas con una o varias propiedades adjuntas. Este artículo demuestra cómo crear y utilizar los comportamientos adjuntos._

## <a name="overview"></a>Información general

Una propiedad adjunta es un tipo especial de propiedad enlazable. Están definidos en una clase pero están asociadas a otros objetos, y son reconocibles en XAML como atributos que contienen una clase y un nombre de propiedad separados por puntos.

Puede definir una propiedad adjunta un `propertyChanged` delegado que se ejecutará cuando cambia el valor de la propiedad, como cuando la propiedad se establece en un control. Cuando el `propertyChanged` ejecuta el delegado, ha pasado una referencia a los parámetros que contienen los valores antiguos y nuevos para la propiedad y el control en el que se va a adjuntar. Este delegado se puede utilizar para agregar funcionalidad nueva al control que se adjunta a la propiedad mediante la manipulación de la referencia que se pasa, como se indica a continuación:

1. El `propertyChanged` delegado convierte la referencia de control, que se recibe como un [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), con el tipo de control que es el comportamiento diseñado para mejorar.
1. El `propertyChanged` delegado modifica las propiedades del control, llama a métodos del control o controladores de eventos de registros para eventos expuestos por el control, para implementar la funcionalidad básica de comportamiento.

Un problema con otros comportamientos adjuntados radica en que se definen en un `static` (clase), con `static` propiedades y métodos. Esto dificulta la crear comportamientos adjuntos que tienen estado. Además, los comportamientos de Xamarin.Forms reemplazaron comportamientos adjuntos como el método preferido para la construcción de comportamiento. Para obtener más información acerca de los comportamientos de Xamarin.Forms, consulte [comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) y [reutilizable comportamientos](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Crear un comportamiento asociado

La aplicación de ejemplo muestra un `NumericValidationBehavior`, que resalta el valor especificado por el usuario en un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control en rojo, si no es un `double`. El comportamiento se muestra en el ejemplo de código siguiente:

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

El `NumericValidationBehavior` clase contiene una propiedad adjunta con el nombre `AttachBehavior` con un `static` captador y establecedor, que controla la adición o eliminación del comportamiento del control al que se adjuntará. Esto adjunta los registros de propiedad del `OnAttachBehaviorChanged` método que se ejecutará cuando cambia el valor de la propiedad. Este método registra o anular registra un controlador de eventos para el [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) eventos, en función del valor de la `AttachBehavior` propiedad adjunta. Proporciona la funcionalidad básica del comportamiento la `OnEntryTextChanged` método, que analiza el valor especificado en el [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) por el usuario y establece la `TextColor` propiedad a color rojo si el valor no es un `double`.

## <a name="consuming-an-attached-behavior"></a>Consumir un comportamiento asociado

El `NumericValidationBehavior` puede utilizarse la clase agregando el `AttachBehavior` propiedad adjunta un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

El equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

En tiempo de ejecución, el comportamiento responderá a la interacción con el control, según la implementación de comportamiento. Las capturas de pantalla siguientes muestran el comportamiento adjunto responde a las entradas no válidas:

[![](attached-images/screenshots-sml.png "Ejemplo de aplicación con un comportamiento adjunto")](attached-images/screenshots.png#lightbox "aplicación con un comportamiento adjunto de ejemplo")

> [!NOTE]
> Otros comportamientos adjuntados están escritos para un tipo de control (o una superclase que puede aplicar a muchos controles), y solo se debe agregar a un control compatible. Si intenta adjuntar un comportamiento a un control incompatible dará como resultado un comportamiento desconocido y depende de la implementación de comportamiento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Quitar un comportamiento adjunto de un Control

El `NumericValidationBehavior` clase puede quitarse de un control estableciendo la `AttachBehavior` propiedad adjunta `false`, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

El equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

En tiempo de ejecución, el `OnAttachBehaviorChanged` será el método se ejecuta cuando el valor de la `AttachBehavior` se establece la propiedad adjunta en `false`. El `OnAttachBehaviorChanged` método, a continuación, anule registrará el controlador de eventos para el [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) eventos, asegurándose de que el comportamiento no se ejecuta cuando el usuario interactúa con el control.

## <a name="summary"></a>Resumen

Este artículo muestra cómo crear y utilizar los comportamientos adjuntos. Adjunta los comportamientos son `static` clases con una o varias propiedades adjuntas.


## <a name="related-links"></a>Vínculos relacionados

- [Otros comportamientos adjuntados (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
