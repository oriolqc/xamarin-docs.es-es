---
title: Plataforma de Apple (iOS y Mac)
description: 'Este documento describen varios temas relacionados con el desarrollo de Xamarin.iOS y Xamarin.Mac: código de uso compartido, la API unificada, enlace bibliotecas Objective-C, las referencias nativas, tipos nativos y mucho más.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b40758fa562e57415cd3c0818763ef0a7ce5dcca
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781538"
---
# <a name="apple-platform-ios-and-mac"></a>Plataforma de Apple (iOS y Mac)

## <a name="code-sharing"></a>Uso compartido de código

Para los elementos del código que no tienen ningún elemento de interfaz de usuario de la mejor manera de compartir código entre iOS y Mac sigue siendo el uso de [bibliotecas de clases Portable](~/cross-platform/app-fundamentals/pcl.md).

Para el código que tiene que realizar algún trabajo de la interfaz de usuario y, en el que desea compartir, debe usar [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) que le permiten colocar código para compartir en un solo proyecto y hacer que se compila con Mac y iOS cuando se hace referencia.

##  <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

La API unificada para iOS y proyectos de Mac utiliza los mismos espacios de nombres para marcos de trabajo para que puede utilizarse el mismo archivo de código en ambas plataformas para sin problemas para compartir el código. También permite compilaciones de bits de 32 y 64. La API unificada ha sido el valor predeterminado de la plantilla desde 2015 temprano y se recomienda para todos los proyectos nuevos - *sólo* proyectos API unificada pueden enviarse a la tienda de aplicaciones.

### <a name="classic-apis"></a>API clásicas

> [!NOTE]
> **Degradación de perfil clásico:** tal y como se han agregado nuevas plataformas en Xamarin.iOS empezamos a gradualmente dejar de utilizar características del perfil clásico (monotouch.dll). Por ejemplo, la opción de no NRC (que nueva-ref-count) se ha quitado. NRC siempre se ha habilitado para unificado todas las aplicaciones (es decir, no NRC nunca fue una opción) y no tiene problemas conocidos. Las versiones futuras quitará la opción de usar Boehm como el recolector de elementos no utilizados. También era una opción nunca disponible para las aplicaciones unificadas. La eliminación completa de la compatibilidad clásico está programada para otoño 2016 con la versión de Xamarin.iOS 10.0.

La Xamarin.iOS original (no unificado) y Xamarin.Mac APIs realizada para compartir el código más difícil porque no tenían marcos nativos cualquiera `MonoTouch.` o `MonoMac.` prefijos de espacio de nombres.  Se proporcionan algunos espacios de nombres vacío que permite a los desarrolladores para compartir código, agregue `using` las instrucciones que hacen referencia a espacios de nombres MonoMac y MonoTouch en el mismo archivo, pero esto fue un poco feo. La API clásica debe continuar sólo para su uso en aplicaciones heredadas que se distribuyen internamente (actualización a la API unificada se recomienda).


### <a name="updating-from-classic-to-the-unified-api"></a>Actualización de clásico a la API unificada

Se ofrecen instrucciones detalladas para actualizar cualquier aplicación desde la clásica a la API unificada.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Enlace de bibliotecas de Objective-C](binding/index.md)

Xamarin le permite poner de bibliotecas nativas en sus aplicaciones con enlaces. Esta sección se explica:

- cómo funcionan los enlaces,
- cómo crear manualmente un proyecto de enlace que permite incorporar código Objective-C a Xamarin, y
- cómo usar nuestro **Sharpie objetivo** herramienta para ayudar a automatizar el proceso.

## <a name="native-referencesnative-referencesmd"></a>[Referencias nativas](native-references.md)

##  <a name="macios-native-typesnativetypesmd"></a>[Tipos nativos Mac/iOS](nativetypes.md)

Para admitir 32 y 64 bits código transparente de C# y F #, estamos incorporando a nuevos tipos de datos.   Obtenga información acerca de ellos aquí.

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Creación de las aplicaciones de 32 y 64 bits](32-and-64/index.md)

Lo que necesita saber para admitir las aplicaciones de 32 y 64 bits.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Trabajo con tipos nativos en aplicaciones multiplataforma](native-types-cross-platform.md)

Este artículo incluye el uso de la nueva iOS tipos nativos de API unificada (`nint`, `nuint`, `nfloat`) en una aplicación multiplataforma donde el código se comparte con dispositivos de iOS no, como Android o sistemas operativos de Windows Phone.
Proporciona una visión general de cuándo se deben utilizar los tipos nativos y proporciona varias soluciones posibles a los casos donde se debe utilizar el nuevo tipo con código multiplataforma.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Selector de pila HttpClient e implementación de SSL/TLS](http-stack.md)

El nuevo Selector de pila HttpClient controla qué implementación HttpClient va a utilizar en la aplicación Xamarin.iOS, Xamarin.tvOS y Xamarin.Mac. Ahora puede volver a una implementación que usa de iOS, de tvOS o transportes nativo de OS x (`NSUrlSession` o `CFNetwork` según el sistema operativo).

SSL (capa de sockets seguros) y su sucesor, TLS (seguridad de capa de transporte), proporcionan compatibilidad para HTTP y otras conexiones de red a través de `System.Net.Security.SslStream`. La nueva opción de compilación de implementación de SSL/TLS se alterna entre la pila TLS de Mono y uno con la tecnología de pila TLS de Apple presente en iOS y Mac.
