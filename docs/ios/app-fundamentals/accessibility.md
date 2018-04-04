---
title: Accesibilidad en iOS
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/18/2016
ms.openlocfilehash: af28d0866337c769d1d6102317fc186c49ec259e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility-on-ios"></a>Accesibilidad en iOS

Esta página describe cómo usar las API de accesibilidad de iOS para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Hacer referencia a la [accesibilidad Android](~/android/app-fundamentals/accessibility.md) y [accesibilidad de OS X](~/mac/app-fundamentals/accessibility.md) páginas para otra API de la plataforma.

## <a name="describing-ui-elements"></a>Que describe los elementos de interfaz de usuario

iOS proporciona el `AccessibilityLabel` y `AccessibilityHint` lector para que sea más accesible los controles de pantalla de propiedades para que los desarrolladores agregar un texto descriptivo que se puede usar por el VoiceOver. También se pueden etiquetar controles con uno o varios rasgos que proporcionan contexto adicional en los modos de acceso.

Algunos controles no es necesario que sea accesible (por ejemplo, una etiqueta en una entrada de texto o una imagen que es puramente decorativa): el `IsAccessibilityElement` se proporciona para deshabilitar la accesibilidad en esos casos.

**Diseñador de la interfaz de usuario**

El **panel de propiedades** contiene una sección de accesibilidad que permite que esta configuración se puede editar cuando se selecciona un control en el Diseñador de la interfaz de usuario de iOS:

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

El `AccessibilityIdentifier` se usa para establecer una clave única que puede usarse para hacer referencia a elementos de la interfaz de usuario a través de la API de UIAutomation.

El valor de `AccessibilityIdentifier` nunca se pronuncia ni se muestra al usuario.

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

El `UIAccessibility.PostNotification` método permite que los eventos que se genera para el usuario fuera de una interacción directa (por ejemplo, cuando interactúa con un control específico).

### <a name="announcement"></a>Anuncio

Se envía un anuncio desde el código para informar al usuario que ha cambiado algún estado (por ejemplo, ha completado una operación en segundo plano). Esto puede ir acompañado de una indicación visual de la interfaz de usuario:

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


## <a name="accessibility-and-localization"></a>Accesibilidad y la localización

Propiedades de accesibilidad, como la etiqueta y la sugerencia se pueden localizar simplemente como otro texto en la interfaz de usuario.

**MainStoryboard.strings**

Si la interfaz de usuario se encuentra en un guión gráfico, puede proporcionar traducciones para las propiedades de accesibilidad de la misma manera que otras propiedades. En el ejemplo siguiente, un `UITextField` tiene un **identificador de localización** de `Pqa-aa-ury` y dos propiedades de accesibilidad que se va a establecer en español:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Este archivo se colocaría en la **es.lproj** directorio contenido en español.

**Localizable.strings**

Como alternativa, las traducciones se pueden agregar a la **Localizable.strings** archivo en el directorio contenido localizado (p. ej. **es.lproj** para español):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Las traducciones que pueden usarse en C# mediante el `LocalizedString` método:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Hacer referencia a la [Guía de localización de iOS](~/ios/app-fundamentals/localization/index.md) para obtener más información sobre la localización de contenido.

<a name="testing" />

## <a name="testing-accessibility"></a>Probar la accesibilidad

VoiceOver está habilitada en el **configuración** aplicación, vaya a **General > Accesibilidad > VoiceOver**:

![](accessibility-images/settings-sml.png "Establecer la velocidad de habla")

El **accesibilidad** pantalla también proporciona opciones de zoom, el tamaño del texto, opciones de color y el contraste, configuración de voz y otras opciones de configuración.

Siga estas [instrucciones VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) para probar la accesibilidad en dispositivos iOS.


## <a name="simulator-testing"></a>Probar el simulador

Cuando se prueba en el simulador, la **accesibilidad Inspector** está disponible para ayudar a comprobar las propiedades de accesibilidad y los eventos están configurados correctamente. Activar el inspector en la **configuración** aplicación, vaya a **General > Accesibilidad > accesibilidad Inspector**:

![](accessibility-images/settings-inspector-sml.png "Habilite el Inspector de accesibilidad")

Una vez habilitada, la ventana del inspector mantiene el mouse sobre la pantalla de iOS en todo momento.
Este es un ejemplo de la salida cuando se selecciona una fila de la vista de tabla: tenga en cuenta el **etiqueta** contiene una frase que proporciona el contenido de la fila y que es "done" (es decir. la marca de verificación está visible):

![](accessibility-images/tableview-a11y-sml.png "Con el Inspector de accesibilidad")

Mientras el inspector está visible, utilice el icono "X" en la parte superior izquierda para temporalmente mostrar y ocultar la superposición y habilitar o deshabilitar la configuración de accesibilidad.



## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS accesibilidad (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
