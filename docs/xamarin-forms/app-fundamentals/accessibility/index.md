---
title: Accesibilidad de Xamarin.Forms
description: Creación de una aplicación accesible garantiza que la aplicación sea utilizable por las personas que enfocar la interfaz de usuario con una variedad de necesidades y experiencias.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: ac0ffbdce6b0c55e8ad9d774d80e3d9b8bf84089
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116451"
---
# <a name="xamarinforms-accessibility"></a>Accesibilidad de Xamarin.Forms

_Creación de una aplicación accesible garantiza que la aplicación sea utilizable por las personas que enfocar la interfaz de usuario con una variedad de necesidades y experiencias._

Hacer que una aplicación de Xamarin.Forms que sean accesibles, significa que pensar en el diseño y de muchos elementos de interfaz de usuario. Para obtener instrucciones sobre cuestiones a considerar, vea el [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md). Ya se pueden solucionar muchos problemas de accesibilidad como fuentes grandes y la configuración de color y contraste adecuada si Xamarin.Forms APIs.

El [accesibilidad Android](~/android/app-fundamentals/accessibility.md) y [iOS accesibilidad](~/ios/app-fundamentals/accessibility.md) guías contienen información detallada de las API nativas expuestas por Xamarin y el [Guía de accesibilidad UWP en MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explica el enfoque nativo en esa plataforma. Estas API se usan para implementar completamente aplicaciones accesibles en cada plataforma.

Xamarin.Forms no tiene actualmente *integrada* admite para todas las API disponibles en cada una de las plataformas subyacentes de accesibilidad. Sin embargo, admite establecer las propiedades de automatización en los elementos de interfaz de usuario para admitir las herramientas de Ayuda de lector y navegación de pantalla, que es una de las partes más importantes de la creación de aplicaciones accesibles. Para obtener más información, consulte [propiedades de automatización](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Las aplicaciones de Xamarin.Forms también pueden tener el orden de tabulación de controles especificado. Para obtener más información, consulte [navegación mediante teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Otra API de accesibilidad (como [PostNotification en iOS](~/ios/app-fundamentals/accessibility.md)) puede ser más adecuada para un [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) o [Custom Renderer](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) implementación. No se tratan en esta guía.

## <a name="testing-accessibility"></a>Probar la accesibilidad

Las aplicaciones de Xamarin.Forms normalmente múltiples plataformas de destino, lo que significa que las pruebas de las características de accesibilidad según la plataforma. Siga estos vínculos para obtener información sobre cómo probar la accesibilidad en cada plataforma:

- [**iOS pruebas**](~/ios/app-fundamentals/accessibility.md)
- [**Las pruebas de Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Propiedades de automatización](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Accesibilidad del teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)
