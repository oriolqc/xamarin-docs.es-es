---
title: Accesibilidad en iOS
description: Este documento describe la accesibilidad en iOS, que tratan diversas propiedades y características que pueden usarse para hacer que su aplicación puede usar tantos usuarios como sea posible.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/18/2016
ms.openlocfilehash: aa3e15797ae1dac621ea8a78345044be1387ebaa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108904"
---
# <a name="accessibility-on-ios"></a>Accesibilidad en iOS

Esta página describe cómo usar las API de accesibilidad de iOS para crear aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Hacer referencia a la [accesibilidad Android](~/android/app-fundamentals/accessibility.md) y [accesibilidad OS X](~/mac/app-fundamentals/accessibility.md) páginas para otra API de plataforma.

## <a name="describing-ui-elements"></a>Que describe los elementos de interfaz de usuario

iOS proporciona el `AccessibilityLabel` y `AccessibilityHint` lector para que sean más accesibles los controles de pantalla de propiedades para los desarrolladores agregar texto descriptivo que sirve el VoiceOver. Los controles también se pueden etiquetar con uno o varios rasgos que proporcionan contexto adicional en los modos de acceso.

Algunos controles no es posible que deben ser accesibles (por ejemplo, una etiqueta en una entrada de texto o una imagen que es puramente decorativa) el `IsAccessibilityElement` se proporciona para deshabilitar la accesibilidad en esos casos.

**Diseñador de interfaz de usuario**

El **panel de propiedades** contiene una sección de accesibilidad que permite esta configuración se puede editar cuando se selecciona un control en el Diseñador de la interfaz de usuario de iOS:

![](accessibility-images/ios-designer-sml.png "Configuración de accesibilidad")

**C#**

También se pueden establecer estas propiedades directamente en el código:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>¿Qué es AccessibilityIdentifier?

El `AccessibilityIdentifier` se usa para establecer una clave única que puede usarse para hacer referencia a elementos de interfaz de usuario a través de la API de UIAutomation.

El valor de `AccessibilityIdentifier` nunca se habla ni se muestra al usuario.

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

El `UIAccessibility.PostNotification` método permite que los eventos que se elevará a usuario fuera de una interacción directa (por ejemplo, cuando interactúan con un control específico).

### <a name="announcement"></a>Anuncio

Un anuncio puede enviarse desde el código para informar al usuario que cambie algún estado (por ejemplo, ha completado una operación en segundo plano). Esto podría ir acompañada de una indicación visual de la interfaz de usuario:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

El `LayoutChanged` anuncio se usa cuando el diseño de pantalla:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>Localización y accesibilidad

Propiedades de accesibilidad, como la etiqueta y la sugerencia se pueden localizar simplemente como otro texto en la interfaz de usuario.

**MainStoryboard.strings**

Si la interfaz de usuario está colocada en un guión gráfico, puede proporcionar traducciones para las propiedades de accesibilidad en la misma manera que otras propiedades. En el ejemplo siguiente, un `UITextField` tiene un **identificador de localización** de `Pqa-aa-ury` y dos propiedades de accesibilidad que se establece en español:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Este archivo se colocarían en la **es.lproj** directorio de contenido en español.

**Localizable.Strings**

Como alternativa, las traducciones se pueden agregar a la **Localizable.strings** archivo en el directorio contenido localizado (p ej. **es.lproj** para español):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Estas traducciones se pueden usar en C# a través de la `LocalizedString` método:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Hacer referencia a la [iOS localización guía](~/ios/app-fundamentals/localization/index.md) para obtener más detalles sobre la localización de contenido.

<a name="testing" />

## <a name="testing-accessibility"></a>Probar la accesibilidad

VoiceOver está habilitada en el **configuración** aplicación navegando a **General > Accesibilidad > VoiceOver**:

![](accessibility-images/settings-sml.png "Establecer la velocidad de habla")

El **accesibilidad** pantalla también proporciona ajustes de zoom, el tamaño del texto, opciones de color y contraste, configuración de voz y otras opciones de configuración.

Siga estas [instrucciones VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) para probar la accesibilidad en dispositivos iOS.


## <a name="simulator-testing"></a>Pruebas del simulador

Cuando se prueba en el simulador, el **accesibilidad Inspector** está disponible para ayudar a comprobar las propiedades de accesibilidad y los eventos están configurados correctamente. Activar el inspector en el **configuración** aplicación navegando a **General > Accesibilidad > accesibilidad Inspector**:

![](accessibility-images/settings-inspector-sml.png "Habilitar el Inspector de accesibilidad")

Una vez habilitada, la ventana del inspector se mantiene a través de la pantalla de iOS en todo momento.
Este es un ejemplo de la salida cuando se selecciona una fila de la vista de tabla: tenga en cuenta la **etiqueta** contiene una frase que proporciona el contenido de la fila y que es "done" (es decir. está visible el "Tick"):

![](accessibility-images/tableview-a11y-sml.png "Usar el Inspector de accesibilidad")

Mientras el inspector está visible, utilice el icono "X" en la parte superior izquierda para temporalmente mostrar y ocultar la superposición y habilitar o deshabilitar la configuración de accesibilidad.



## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Accesibilidad (Apple) de iOS](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
