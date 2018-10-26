---
title: Accesibilidad en macOS
description: Este documento describe cómo trabajar con características de accesibilidad de macOS en una aplicación de Xamarin.Mac. Describe los elementos de interfaz de usuario donde se describe en los guiones gráficos y código, controles personalizados y la accesibilidad de pruebas.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fdda52309ffdb0d32cc42a4dff052cd9050b1e4f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116620"
---
# <a name="accessibility-on-macos"></a>Accesibilidad en macOS

Esta página describe cómo usar la API de accesibilidad de macOS para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Hacer referencia a la [accesibilidad Android](~/android/app-fundamentals/accessibility.md) y [iOS accesibilidad](~/ios/app-fundamentals/accessibility.md) páginas para otra API de plataforma.

Comprender cómo las API de accesibilidad de macOS (anteriormente denominados OS X), primero revise el [modelo de accesibilidad de OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Que describe los elementos de interfaz de usuario

AppKit usa el `NSAccessibility` protocolo para exponer las API que permiten que la interfaz de usuario sea accesible. Esto incluye un comportamiento predeterminado que se intenta establecer valores significativos para las propiedades de accesibilidad, como el establecimiento de un botón `AccessibilityLabel`. La etiqueta es normalmente una sola palabra o frase corta que describe el control o la vista.

### <a name="storyboard-files"></a>Archivos de guión gráfico

Xamarin.Mac usa Interface Builder de Xcode para editar archivos de guión gráfico.
Información de accesibilidad se puede editar en el **inspector de identidad** cuando se selecciona un control en la superficie de diseño (como se muestra en la siguiente captura de pantalla):

[![Adición de accesibilidad en Interface Builder de Xcode](accessibility-images/xcode.png "adición de accesibilidad en Interface Builder de Xcode")](accessibility-images/xcode-large.png#lightbox)

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

Este método puede utilizarse, a continuación, en el código tal como se muestra:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

El `AccessibilityHelp` propiedad sirve para obtener una explicación de lo que hace el control o la vista y solo se debe agregar la etiqueta no puede proporcionar información suficiente. El texto de ayuda debe aún ser tan cortas como sea posible, por ejemplo "elimina el documento".

Algunos elementos de la interfaz de usuario no son relevantes para el acceso sea accesible (por ejemplo, una etiqueta junto a una entrada que tiene su propia etiqueta de accesibilidad y ayuda).
En estos casos, establecer `AccessibilityElement = false` para que se omitirá estos controles o las vistas por los lectores de pantalla u otras herramientas de accesibilidad.

Apple proporciona [las directrices de accesibilidad](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) que explica los procedimientos recomendados para accesibilidad etiquetas y texto de ayuda.

## <a name="custom-controls"></a>Controles personalizados

Hacer referencia a Apple [directrices para controles personalizados accesible](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) para obtener más información sobre los pasos adicionales necesarios.

## <a name="testing-accessibility"></a>Probar la accesibilidad

macOS proporciona un **accesibilidad Inspector** que ayuda a probar la funcionalidad de accesibilidad. El inspector se incluye con Xcode.

La primera vez que se inicie, el **accesibilidad Inspector** requerirá permiso para controlar el equipo a través de accesibilidad:

![Solicitando permiso para ejecutar el Inspector de accesibilidad](accessibility-images/accessibility-inspector-1.png "solicitando permiso para ejecutar el Inspector de accesibilidad")

Desbloquear la pantalla de configuración (si es necesario, en la parte inferior izquierda) y marca el **accesibilidad Inspector**:

![Pantalla de configuración para habilitar el Inspector de accesibilidad](accessibility-images/accessibility-inspector-2.png "pantalla de configuración para habilitar el Inspector de accesibilidad")

Una vez habilitada, el inspector aparece como una ventana flotante que se puede mover la pantalla. La captura de pantalla siguiente muestra el inspector ejecutando junto a una aplicación de ejemplo de Mac. El cursor se mueve a través de la ventana, el inspector muestra todas las propiedades accesibles de cada control:

[![Ejemplo de ejecución del Inspector de accesibilidad](accessibility-images/accessibility-example.png "ejecución del Inspector de ejemplo de accesibilidad")](accessibility-images/accessibility-example-large.png#lightbox)

Para obtener más información, lea el [probar la accesibilidad de la Guía de OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Accesibilidad de Mac](https://www.apple.com/accessibility/mac/)
