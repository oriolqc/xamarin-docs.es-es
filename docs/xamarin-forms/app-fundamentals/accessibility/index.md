---
title: Accesibilidad
description: Crear una aplicación accesible garantiza que la aplicación sea utilizable por personas que enfocan la interfaz de usuario con una variedad de necesidades y experiencias.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: e4fb151b9664df7236d2c22ed54db09bf7bc65b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility"></a>Accesibilidad

_Crear una aplicación accesible garantiza que la aplicación sea utilizable por personas que enfocan la interfaz de usuario con una variedad de necesidades y experiencias._

Hacer que una aplicación de Xamarin.Forms accesible significa pensar sobre el diseño y de muchos elementos de interfaz de usuario. Para obtener instrucciones sobre cuestiones a considerar, vea el [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md). Ya se pueden solucionar muchos problemas de accesibilidad como fuentes grandes y la configuración de color y el contraste adecuada si Xamarin.Forms APIs.

El [accesibilidad Android](~/android/app-fundamentals/accessibility.md) y [iOS accesibilidad](~/ios/app-fundamentals/accessibility.md) guías contienen detalles de las API nativas expuestas por Xamarin y el [Guía de accesibilidad UWP en MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explica el método nativo en esa plataforma. Estas API se utilizan para implementar completamente aplicaciones accesibles en cada plataforma.

Xamarin.Forms no tiene actualmente *integrados* admite para todas la API disponibles en cada una de las plataformas subyacentes de accesibilidad. Sin embargo, admiten valores de configuración de accesibilidad en elementos de la interfaz de usuario para admitir las herramientas de asistencia de lector y navegación de pantalla, que es uno de los elementos más importantes de la creación de aplicaciones accesibles. Para obtener más información, consulte [valores de configuración de accesibilidad en elementos de la interfaz de usuario](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md).

Otra API de accesibilidad (como [PostNotification en iOS](~/ios/app-fundamentals/accessibility.md)) puede ser más adecuados para un [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) o [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) implementación. No se tratan en esta guía.

## <a name="testing-accessibility"></a>Probar la accesibilidad

Xamarin.Forms aplicaciones normalmente tienen como destino varias plataformas, lo que significa que probar las características de accesibilidad según la plataforma. Siga estos vínculos para obtener información sobre cómo comprobar la accesibilidad en cada plataforma:

- [**iOS pruebas**](~/ios/app-fundamentals/accessibility.md)
- [**Pruebas de Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)


## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Establecimiento de valores de accesibilidad en elementos de la interfaz de usuario](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md)
