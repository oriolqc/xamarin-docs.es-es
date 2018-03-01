---
title: Accesibilidad en macOS
description: "Esta guía describe características para crear una aplicación accesible basada en Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 0117364f02302add1f8788de1a79e4c4210fd07b
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="accessibility-on-macos"></a>Accesibilidad en macOS

Esta página describe cómo usar la API de accesibilidad de macOS para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Hacer referencia a la [accesibilidad Android](~/android/app-fundamentals/accessibility.md) y [iOS accesibilidad](~/ios/app-fundamentals/accessibility.md) páginas para otra API de la plataforma.

Para entender cómo la accesibilidad API funciona macOS (anteriormente denominados OS X), primer examen el [modelo de accesibilidad de OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Que describe los elementos de interfaz de usuario

AppKit utiliza el `NSAccessibility` protocolo que se va a exponer las API que ayudan a hacer accesible la interfaz de usuario. Esto incluye un comportamiento predeterminado que intenta establecer valores significativos para las propiedades de accesibilidad, como la configuración de un botón `AccessibilityLabel`. La etiqueta es normalmente una sola palabra o frase corta que describe el control o la vista.

### <a name="storyboard-files"></a>Archivos de guión gráfico

Xamarin.Mac utiliza el generador de interfaz de Xcode para editar archivos de guión gráfico.
Información de accesibilidad que se puede editar en el **inspector de identidad** cuando se selecciona un control en la superficie de diseño (como se muestra en la captura de pantalla siguiente):

[![Adición de accesibilidad en el generador de interfaz de Xcode](accessibility-images/xcode.png "agregar accesibilidad en el generador de interfaz de Xcode")](accessibility-images/xcode-large.png)

### <a name="code"></a>Código

Xamarin.Mac no expone actualmente como `AccessibilityLabel` establecedor.  Agregue el siguiente método auxiliar para establecer la etiqueta de accesibilidad:

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

Este método, a continuación, se puede utilizar en el código tal como se muestra:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

El `AccessibilityHelp` propiedad sirve para obtener una explicación de lo que hace el control o la vista y solo se debe agregar la etiqueta no puede proporcionar información suficiente. El texto de ayuda debe aún ser tan cortas como sea posible, por ejemplo "elimina el documento".

Algunos elementos de la interfaz de usuario no son relevantes para el acceso accesible (por ejemplo, una etiqueta junto a una entrada que tiene su propia etiqueta de accesibilidad y ayuda).
En estos casos, establecer `AccessibilityElement = false` para que estos controles o las vistas se omitirán los lectores de pantalla u otras herramientas de accesibilidad.

Apple proporciona [las directrices de accesibilidad](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) que explica las prácticas recomendadas para el texto de ayuda y las etiquetas de accesibilidad.

## <a name="custom-controls"></a>Controles personalizados

Consulte de Apple [directrices para controles personalizados accesibles](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) para obtener más información sobre los pasos adicionales necesarios.

## <a name="testing-accessibility"></a>Probar la accesibilidad

macOS proporciona un **accesibilidad Inspector** que ayuda a probar la funcionalidad de accesibilidad. El inspector se incluye en Xcode.

La primera vez que se inicia, el **accesibilidad Inspector** requerirá permiso para controlar el equipo a través de accesibilidad:

![Inspector de accesibilidad solicitando permiso para ejecutar](accessibility-images/accessibility-inspector-1.png "solicitando permiso para ejecutar el Inspector de accesibilidad")

Desbloquear la pantalla de configuración (si es necesario, en la parte inferior izquierda) y marca el **accesibilidad Inspector**:

![Pantalla de configuración para habilitar la accesibilidad Inspector](accessibility-images/accessibility-inspector-2.png "pantalla de configuración para habilitar el Inspector de accesibilidad")

Una vez habilitada, el inspector aparece como una ventana flotante que puede desplazar por la pantalla. La captura de pantalla siguiente muestra el inspector que se ejecutan junto a una aplicación de ejemplo de Mac. Mientras el cursor se mueve a través de la ventana, el inspector muestra todas las propiedades accesibles de cada control:

[![Ejemplo de la ejecución del Inspector de accesibilidad](accessibility-images/accessibility-example.png "ejecución del Inspector de ejemplo de accesibilidad")](accessibility-images/accessibility-example-large.png)

Para obtener más información, lea la [probar la accesibilidad de la Guía de OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Accesibilidad de Mac](https://www.apple.com/accessibility/mac/)
