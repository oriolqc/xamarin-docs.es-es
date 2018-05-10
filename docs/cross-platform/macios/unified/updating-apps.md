---
title: Actualizar aplicaciones existentes a la API unificada
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 588c01ef9f9ee014592c9d8dc72f2b8be20dfee3
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Actualizar aplicaciones existentes a la API unificada

> [!IMPORTANT]
> **Degradación de perfil clásico:** tal y como se han agregado nuevas plataformas en Xamarin.iOS empezamos a gradualmente dejar de utilizar características del perfil clásico (monotouch.dll). Por ejemplo, la opción de no NRC (que nueva-ref-count) se ha quitado. NRC siempre se ha habilitado para unificado todas las aplicaciones (es decir, no NRC nunca fue una opción) y no tiene problemas conocidos. Las versiones futuras quitará la opción de usar Boehm como el recolector de elementos no utilizados. También era una opción nunca disponible para las aplicaciones unificadas. La eliminación completa de la compatibilidad clásico está programada para el próximo otoño con el lanzamiento de Xamarin.iOS 10.0.




## <a name="how-to-update-your-apps"></a>Cómo actualizar las aplicaciones

Universidad de Xamarin tiene un vídeo disponible de forma gratuita en **actualizar a la API unificada de iOS**. Visite [Xamarin Universidad rayos conferencias](http://university.xamarin.com/lightninglectures) para observar!

[ ![](updating-apps-images/xamu-video-sml.png "Universidad de Xamarin")](http://university.xamarin.com/lightninglectures)

Hay tres pasos para actualizar sus aplicaciones:

1. Corrija las advertencias de compilador en el código existente, especialmente las relativas a las API en desuso.

2. Utilice la herramienta de migración integrada en Visual Studio para Mac para actualizar los archivos de proyecto y los espacios de nombres.

3. Corrección restantes errores de compilador relacionados con el nuevo [64 tipos](~/cross-platform/macios/nativetypes.md) y [otras API](~/cross-platform/macios/unified/index.md#deprecated-typos) que han cambiado. Extraer del repositorio [estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) para obtener más información sobre las actualizaciones manuales que podrían ser necesarios.

Hay guías específicas para cada producto que le ayudarán a actualizar las aplicaciones a la API unificada y la compatibilidad de 64 bits:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Aplicaciones de Xamarin.iOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Las aplicaciones existentes de Xamarin.iOS pueden actualizarse a la API unificada con la herramienta de migración automática integrada en Visual Studio para Mac. Algunas de las revisiones adicionales, a continuación, pueden requerir, como se explica en [estas instrucciones](~/cross-platform/macios/unified/updating-ios-apps.md) y [sugerencias](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Aplicaciones de Xamarin.Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Las aplicaciones existentes de Xamarin.Mac pueden actualizarse a la API unificada con la herramienta de migración automática integrada en Visual Studio para Mac. Algunas de las revisiones adicionales, a continuación, pueden requerir, como se explica en [estas instrucciones](~/cross-platform/macios/unified/updating-mac-apps.md) y [sugerencias](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Aplicaciones de Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Siga estas instrucciones para actualizar una solución de Xamarin.Forms existente con un proyecto de iOS para usar la API unificada. Compatibilidad con la API unificada solo está disponible en Xamarin.Forms 1.3 y versiones posteriores, por lo que [las instrucciones](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) también se explica cómo actualizar la aplicación de Xamarin.Forms para la versión 1.3. Estos [sugerencias](~/cross-platform/macios/unified/updating-tips.md) puede ayudar a actualizar cualquier código nativo de iOS representadores personalizados o servicios de dependencia.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/nativetypes.md)

Este artículo incluye el uso de la nueva iOS tipos nativos de API unificada (nint, nuint, nfloat) en una aplicación multiplataforma donde el código se comparte con dispositivos de iOS no, como Android o sistemas operativos de Windows Phone. Proporciona una visión general de cuándo se deben utilizar los tipos nativos y proporciona varias soluciones posibles a los casos donde se debe utilizar el nuevo tipo con código multiplataforma.

## <a name="update-bindings-to-the-unified-api"></a>Actualizar los enlaces a la API unificada

Los clientes que han creado los enlaces a bibliotecas de C objetivo deberán actualizar el proyecto de enlace para reflejar los cambios en la API subyacente (donde algunos tipos ahora será 64 bits).
Siga estas instrucciones para [actualizar un proyecto de enlace existente para admitir la API unificada](~/cross-platform/macios/unified/update-binding.md).




## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Actualizar aplicaciones de Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Actualizar aplicaciones de Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Actualizar enlaces](~/cross-platform/macios/unified/update-binding.md)
- [Actualización de sugerencias](~/cross-platform/macios/unified/updating-tips.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
