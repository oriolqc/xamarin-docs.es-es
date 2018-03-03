---
title: Establezca los valores de accesibilidad en elementos de la interfaz de usuario
description: "Xamarin.Forms permite que los valores de accesibilidad que se establecerán en elementos de la interfaz de usuario mediante el uso de las propiedades asociadas de la clase AutomationProperties, que a su vez valores del conjunto de accesibilidad nativo. Este artículo explica cómo utilizar la clase AutomationProperties, para que un lector de pantalla puede hablar acerca de los elementos en la página."
ms.topic: article
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 4aeeea7f946a121b12741d2da217daf531935849
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="setting-accessibility-values-on-user-interface-elements"></a>Establezca los valores de accesibilidad en elementos de la interfaz de usuario

_Xamarin.Forms permite que los valores de accesibilidad que se establecerán en elementos de la interfaz de usuario mediante el uso de las propiedades asociadas de la clase AutomationProperties, que a su vez valores del conjunto de accesibilidad nativo. Este artículo explica cómo utilizar la clase AutomationProperties, para que un lector de pantalla puede hablar acerca de los elementos en la página._

## <a name="overview"></a>Información general

Xamarin.Forms permite valores de accesibilidad que se establecerá en elementos de la interfaz de usuario a través de las propiedades adjuntas siguientes:

- `AutomationProperties.IsInAccessibleTree` : indica si el elemento está disponible para una aplicación accesible. Para obtener más información, consulte [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` : una breve descripción del elemento que actúa como un identificador para el elemento speakable. Para obtener más información, consulte [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` : una descripción más larga del elemento, que puede considerarse como texto de información sobre herramientas asociado al elemento. Para obtener más información, consulte [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` : permite que otro elemento definir la información de accesibilidad para el elemento actual. Para obtener más información, consulte [AutomationProperties.LabeledBy](#labeledby).

Estos adjunta valores de accesibilidad nativo del conjunto de propiedades para que un lector de pantalla puede hablar sobre el elemento. Para obtener más información acerca de las propiedades asociadas, consulte [adjunta propiedades](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> Mediante el `AutomationProperties` propiedades adjuntas pueden afectar a la ejecución de pruebas de interfaz de usuario en Android. El [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/), `AutomationProperties.Name` y `AutomationProperties.HelpText` ambas propiedades establecen nativo `ContentDescription` propiedad, con el `AutomationProperties.Name` y `AutomationProperties.HelpText` valores de propiedad tiene prioridad sobre la `AutomationId`valor (si ambos `AutomationProperties.Name` y `AutomationProperties.HelpText` están configurados, los valores se concatenarán). Esto significa que las pruebas buscando `AutomationId` se producirá un error si `AutomationProperties.Name` o `AutomationProperties.HelpText` también se establecen en el elemento. En este escenario, se debe modificar pruebas de interfaz de usuario para buscar el valor de `AutomationProperties.Name` o `AutomationProperties.HelpText`, o una concatenación de ambos.

Cada plataforma tiene un lector de pantalla diferentes para incluir comentarios en los valores de accesibilidad:

- iOS tiene VoiceOver. Para obtener más información, consulte [accesibilidad de prueba en un dispositivo con VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) en developer.apple.com.
- El sistema Android tiene TalkBack. Para obtener más información, consulte [accesibilidad de probar la aplicación](https://developer.android.com/training/accessibility/testing.html#talkback) en developer.android.com.
- Windows tiene Narrador. Para obtener más información, consulte [comprobar escenarios de aplicación principal mediante el uso de Narrador](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/).

Sin embargo, el comportamiento exacto de un lector de pantalla depende del software y de la configuración del usuario del mismo. Por ejemplo, la mayoría de los lectores de pantalla leen el texto asociado a un control cuando recibe el foco, permitiendo a los usuarios para orientarse a medida que se mueven entre los controles en la página. Algunos lectores de pantalla también leen la interfaz de usuario de la aplicación completa cuando aparece una página, lo que permite al usuario de todo el contenido de información disponibles de la página de recepción antes de intentar navegar por ésta.

Los lectores de pantalla también leen valores de distintos tipos de accesibilidad. En la aplicación de ejemplo:

- VoiceOver leerá el `Placeholder` valor de la `Entry`, seguido de las instrucciones para usar el control.
- TalkBack leerá el `Placeholder` valor de la `Entry`, seguido de la `AutomationProperties.HelpText` valor, seguido de las instrucciones para usar el control.
- El Narrador leerá el `AutomationProperties.LabeledBy` valor de la `Entry`, seguido de instrucciones sobre cómo utilizar el control.

Además, el Narrador dará prioridad a `AutomationProperties.Name`, `AutomationProperties.LabeledBy`y, a continuación, `AutomationProperties.HelpText`. En Android, puede combinar TalkBack el `AutomationProperties.Name` y `AutomationProperties.HelpText` valores. Por lo tanto, se recomienda que pruebas minuciosa accesibilidad se lleva a cabo en cada plataforma para garantizar una experiencia óptima.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

El `AutomationProperties.IsInAccessibleTree` propiedad adjunta es un `boolean` que determina si el elemento está accesible y, por lo que se ve, para los lectores de pantalla. Debe establecerse en `true` usar la accesibilidad otra propiedades adjuntas. Esto puede realizarse en XAML como se indica a continuación:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

Como alternativa, se puede establecer en C# como sigue:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Tenga en cuenta que la [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método también se puede utilizar para establecer el `AutomationProperties.IsInAccessibleTree` adjunta la propiedad: `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

La `AutomationProperties.Name` valor de propiedad adjunta debe ser una cadena de texto cortas, descriptivas que utiliza un lector de pantalla para anunciar un elemento. Esta propiedad debe establecerse para los elementos que tienen un significado que es importante para comprender el contenido o interactuar con la interfaz de usuario. Esto puede realizarse en XAML como se indica a continuación:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

Como alternativa, se puede establecer en C# como sigue:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Tenga en cuenta que la [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método también se puede utilizar para establecer el `AutomationProperties.Name` adjunta la propiedad: `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

El `AutomationProperties.HelpText` adjunto propiedad debe establecerse en texto que describe el elemento de la interfaz de usuario y puede ser considerarse texto de información sobre herramientas asociado al elemento. Esto puede realizarse en XAML como se indica a continuación:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

Como alternativa, se puede establecer en C# como sigue:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Tenga en cuenta que la [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método también se puede utilizar para establecer el `AutomationProperties.HelpText` adjunta la propiedad: `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

En algunas plataformas, para editar controles como un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), el `HelpText` propiedad a veces se omite y reemplazado con texto de marcador de posición. Por ejemplo, "Escriba aquí el nombre" es un buen candidato para la [ `Entry.Placeholder` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) propiedad que coloca el texto en el control antes de la entrada del usuario real.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

El `AutomationProperties.LabeledBy` propiedad adjunta permite que otro elemento definir la información de accesibilidad para el elemento actual. Por ejemplo, un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) junto a un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) puede utilizarse para describir lo que el `Entry` representa. Esto puede realizarse en XAML como se indica a continuación:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

Como alternativa, se puede establecer en C# como sigue:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Tenga en cuenta que la [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método también se puede utilizar para establecer el `AutomationProperties.IsInAccessibleTree` adjunta la propiedad: `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="summary"></a>Resumen

Este artículo explica cómo establecer valores de accesibilidad en usuario elementos de la interfaz en una aplicación de Xamarin.Forms mediante el uso de propiedades adjuntas desde la `AutomationProperties` clase. Estos adjunta valores de accesibilidad nativo del conjunto de propiedades para que un lector de pantalla puede hablar acerca de los elementos en la página.


## <a name="related-links"></a>Vínculos relacionados

- [Propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)
- [Accesibilidad (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
