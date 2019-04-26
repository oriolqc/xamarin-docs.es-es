---
title: Creación de aplicaciones multiplataforma
description: 'En un resumen de más de seis partes, esta sección describe cómo crear aplicaciones con la plataforma de desarrollo de Xamarin: desde la comprensión de cómo funciona Xamarin para diseñar aplicaciones móviles y, a continuación, probar e implementar en diferentes tiendas de aplicaciones.'
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 683400e24844308769f0562552641216d45e7d11
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276396"
---
# <a name="building-cross-platform-applications"></a>Creación de aplicaciones multiplataforma

Hay dos opciones para compartir código entre aplicaciones móviles multiplataforma: Compartir proyectos de activos y bibliotecas de clases portables. Estas opciones son [que se tratan aquí](~/cross-platform/app-fundamentals/code-sharing.md); obtener más información sobre [bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md) y [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) también está disponible.

<a name="Sections" />

 [Información general](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1: descripción de la plataforma móvil en Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2: arquitectura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3: configurar una solución multiplataforma de Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4: trabajo con varias plataformas](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5: prácticas de uso compartido estrategias de código](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6: Pruebas y aprobaciones de App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>Casos prácticos

Los principios descritos en este documento se colocan en la práctica en la aplicación de ejemplo *Tasky*, así como [compilado previamente aplicaciones](https://xamarin.com/prebuilt) como [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky es una aplicación de lista de tareas pendientes sencilla para iOS, Android y Windows Phone.
Muestra los conceptos básicos de la creación de una aplicación multiplataforma con Xamarin y usa una base de datos SQLite local.

 [![tasky list](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [![tasky list](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Leer el [Tasky caso práctico](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).

## <a name="summary"></a>Resumen

En esta sección se presenta las herramientas de desarrollo de aplicaciones de Xamarin y se explica cómo crear aplicaciones que tienen como destino varias plataformas móviles.

Se trata de una arquitectura por capas ese código estructuras para volver a usar en varias plataformas y describe los patrones de software diferentes que pueden usarse en esa arquitectura.

Se proporcionan ejemplos de funciones de aplicación comunes (por ejemplo, las operaciones de archivo y red) y cómo pueden crearse de forma multiplataforma.

Por último, brevemente describen las pruebas y proporciona referencias a un caso práctico que coloca estos principios en acción.

## <a name="related-links"></a>Vínculos relacionados

- [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Caso práctico: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Aplicación de ejemplo tasky (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Desarrollo de aplicaciones móviles de Xamarin: Multiplataforma C# y Xamarin.Forms Fundamentals (Amazon)](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [Desarrollo móvil con C# por Greg encadenaron (o ' Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [Professional desarrollo móvil multiplataforma en C# por Scott Olson, John Hunter, Ben Horgen, Kenny concedida (Wrox)](http://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
