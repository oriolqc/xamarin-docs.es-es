---
title: Plataforma de Apple (iOS y Mac)
description: 'Este documento describen los distintos temas relacionados con el desarrollo de Xamarin.iOS y Xamarin.Mac: código compartido, la API unificada, enlace bibliotecas Objective-C, las referencias nativas, tipos nativos y mucho más.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b40758fa562e57415cd3c0818763ef0a7ce5dcca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199772"
---
# <a name="apple-platform-ios-and-mac"></a>Plataforma de Apple (iOS y Mac)

## <a name="code-sharing"></a>Uso compartido de código

Para los elementos del código que no tengan ningún elemento de interfaz de usuario de la mejor manera de compartir código entre iOS y Mac sigue siendo el uso de [bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md).

Para el código que tiene que realizar algún trabajo de la interfaz de usuario y sin embargo, desea compartir, debe usar [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) que le permiten colocar código para compartir en un solo proyecto y que se compila con Mac y iOS cuando se hace referencia.

##  <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

La API unificada para proyectos de iOS y Mac usa los mismos espacios de nombres para marcos de trabajo para que se puede usar el mismo archivo de código en ambas plataformas, para compartir sin problemas de código. También permite que las compilaciones de bits de 32 y 64. Unified API ha sido el valor predeterminado de la plantilla desde principios de 2015 y se recomienda para todos los nuevos proyectos - *sólo* proyectos Unified API se pueden enviar a la aplicación de Store.

### <a name="classic-apis"></a>API clásicas

> [!NOTE]
> **Degradación de perfil clásico:** Cuando se agregan nuevas plataformas en Xamarin.iOS hemos empezado a dejar de utilizar gradualmente características desde el perfil de clásico (monotouch.dll). Por ejemplo, se ha quitado la opción de no NRC (que nueva-ref-count). NRC siempre se ha habilitado para unificada de todas las aplicaciones (es decir, no NRC nunca fue una opción) y no tiene ningún problema conocido. Las versiones futuras quitará la opción de usar Boehm como el recolector de elementos no utilizados. Esto también era una opción nunca disponible para aplicaciones unificadas. La eliminación completa de la compatibilidad clásico está programada para el otoño de 2016 con la versión de Xamarin.iOS 10.0.

El Xamarin.iOS (no unificada) original y Xamarin.Mac APIs hicieron uso compartido de código más difícil debido a que los marcos nativos tenían cualquiera `MonoTouch.` o `MonoMac.` prefijos de espacio de nombres.  Hemos proporcionado algunos espacios de nombres vacío que permite a los desarrolladores puedan compartir código mediante la adición de `using` las instrucciones que hacen referencia a los espacios de nombres MonoMac y MonoTouch en el mismo archivo, pero esto era un poco feo. La API clásica solo debe continuar para usarse en aplicaciones heredadas que se distribuyan internamente (actualización de la API unificada se recomienda).


### <a name="updating-from-classic-to-the-unified-api"></a>Actualización del modelo clásico a Unified API

Hay instrucciones detalladas para actualizar cualquier aplicación desde el modelo clásico a Unified API.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Enlace de bibliotecas de Objective-C](binding/index.md)

Xamarin le permite incorporar bibliotecas nativas a las aplicaciones con los enlaces. Esta sección se explica:

- cómo funcionan los enlaces,
- cómo crear manualmente un proyecto de enlace que le permite incorporar código Objective-C a Xamarin, y
- cómo usar nuestro **Sharpie objetivo** herramienta para ayudar a automatizar el proceso.

## <a name="native-referencesnative-referencesmd"></a>[Referencias nativas](native-references.md)

##  <a name="macios-native-typesnativetypesmd"></a>[Tipos nativos de iOS y Mac](nativetypes.md)

Para admitir 32 y 64 bits código transparente C# y F#, estamos presentando nuevos tipos de datos.   Obtenga información sobre ellas aquí.

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Creación de las aplicaciones de 32 y 64 bits](32-and-64/index.md)

Lo que necesita saber para admitir las aplicaciones de 32 y 64 bits.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Trabajo con tipos nativos en aplicaciones multiplataforma](native-types-cross-platform.md)

En este artículo se describe el uso de iOS nuevos tipos nativos de API unificada (`nint`, `nuint`, `nfloat`) en una aplicación multiplataforma que se comparte código con los dispositivos que no son iOS como Android o sistemas operativos de Windows Phone.
Proporciona información detallada sobre cuándo se deben utilizar los tipos nativos y proporciona varias soluciones posibles para los casos donde se debe usar el nuevo tipo con el código entre plataformas.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Selector de pila HttpClient e implementación de SSL/TLS](http-stack.md)

El nuevo Selector de pila HttpClient controla qué implementación de HttpClient para usar en la aplicación de Xamarin.iOS, Xamarin.tvOS y Xamarin.Mac. Ahora puede cambiar a una implementación que usa de iOS, del tvOS o transportes nativos OS x (`NSUrlSession` o `CFNetwork` según el sistema operativo).

SSL (capa de sockets seguros) y su sucesor, TLS (Transport Layer Security), proporcionan compatibilidad para HTTP y otras conexiones de red a través de `System.Net.Security.SslStream`. La nueva opción de compilación de implementación de SSL/TLS cambia entre la pila TLS de Mono y otro con la tecnología de pila TLS de Apple está presente en iOS y Mac.
