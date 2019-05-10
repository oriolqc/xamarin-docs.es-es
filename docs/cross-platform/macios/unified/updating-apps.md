---
title: Actualizar aplicaciones existentes a Unified API
description: Este documento incluye vínculos a diversas guías que describen cómo actualizar aplicaciones de Xamarin a Unified API. Describe las aplicaciones de Xamarin.iOS, las aplicaciones de Xamarin.Mac. Aplicaciones de Xamarin.Forms, los tipos nativos en aplicaciones multiplataforma y proyectos de enlace.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 62b8e82a1191f3213453e9a213ea615e476662d5
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978226"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Actualizar aplicaciones existentes a Unified API

> [!IMPORTANT]
> La API clásica de Xamarin, que precede a la API unificada, está desusada.
> - La última versión de Xamarin.iOS para admitir la API clásica (monotouch.dll) era 9.10 de Xamarin.iOS.
> - Xamarin.Mac siga siendo compatible con la API clásica, pero ya no se actualizará. Puesto que está en desuso, los desarrolladores deben mover sus aplicaciones a Unified API.

## <a name="how-to-update-your-apps"></a>Cómo actualizar las aplicaciones

Hay tres pasos para actualizar sus aplicaciones:

1. Corrija las advertencias del compilador en el código existente, especialmente las relacionadas con las API en desuso.

2. Utilice la herramienta de migración integradas en Visual Studio para Mac para actualizar los archivos de proyecto y los espacios de nombres.

3. Corrección de los demás errores del compilador relacionados con el nuevo [64 tipos](~/cross-platform/macios/nativetypes.md) y [otras API](~/cross-platform/macios/unified/overview.md#deprecated-typos) que han cambiado. Desproteger [estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) para obtener más información sobre las actualizaciones manuales que podrían ser necesarios.

Hay directrices específicas para cada producto que le ayudarán a actualizar sus aplicaciones a la API unificada y compatibilidad con 64 bits:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Aplicaciones de Xamarin.iOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Aplicaciones de Xamarin.iOS existentes se pueden actualizar a Unified API mediante la herramienta de migración automatizada integrada en Visual Studio para Mac. Algunas de las revisiones adicionales, a continuación, pueden ser necesarios, como se explica en [estas instrucciones](~/cross-platform/macios/unified/updating-ios-apps.md) y [sugerencias](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Aplicaciones de Xamarin.Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Las aplicaciones existentes de Xamarin.Mac pueden actualizarse a Unified API mediante la herramienta de migración automatizada integrada en Visual Studio para Mac. Algunas de las revisiones adicionales, a continuación, pueden ser necesarios, como se explica en [estas instrucciones](~/cross-platform/macios/unified/updating-mac-apps.md) y [sugerencias](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Aplicaciones de Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Siga estas instrucciones para actualizar una solución existente de Xamarin.Forms con un proyecto de iOS para usar la API unificada. Compatibilidad con la API unificada solo está disponible en Xamarin.Forms 1.3 y versiones posteriores, por lo que [las instrucciones](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) también se explica cómo actualizar la aplicación de Xamarin.Forms en la versión 1.3. Estos [sugerencias](~/cross-platform/macios/unified/updating-tips.md) puede ayudar a actualizar cualquier código nativo de iOS en los representadores personalizados o los servicios de dependencia.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/nativetypes.md)

En este artículo se describe el uso de iOS nuevos tipos nativos de API unificada (nint, nuint, nfloat) en una aplicación multiplataforma donde se comparte código con los dispositivos que no son iOS como Android o sistemas operativos de Windows Phone. Proporciona información detallada sobre cuándo se deben utilizar los tipos nativos y proporciona varias soluciones posibles para los casos donde se debe usar el nuevo tipo con el código entre plataformas.

## <a name="update-bindings-to-the-unified-api"></a>Actualizar los enlaces a Unified API

Los clientes que han creado los enlaces a las bibliotecas de Objective-C deberá actualizar el proyecto de enlace para reflejar los cambios en la API subyacente (donde algunos tipos ahora será 64 bits).
Siga estas instrucciones para [actualizar un proyecto de enlace existente para admitir la API unificada](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Actualización de aplicaciones de Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Actualización de aplicaciones de Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Actualizando enlaces](~/cross-platform/macios/unified/update-binding.md)
- [Actualización de sugerencias](~/cross-platform/macios/unified/updating-tips.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
