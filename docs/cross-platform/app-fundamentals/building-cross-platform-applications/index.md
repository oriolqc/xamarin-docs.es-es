---
title: Compilar entre aplicaciones de la plataforma
description: "En un resumen de más de seis partes, esta sección describe cómo crear aplicaciones con la plataforma de desarrollo de Xamarin: de comprender el funcionamiento de Xamarin a diseñar aplicaciones móviles y, a continuación, probar e implementar las tiendas de aplicaciones distintos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 53c32003cd1a77a3aa5feb0ab26cedeab27789dc
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="sharing-code-options"></a>Opciones de código compartido

Hay dos opciones para compartir código entre aplicaciones móviles multiplataforma: proyectos de recurso compartido y bibliotecas de clases Portable. Estas opciones son [se tratan aquí](~/cross-platform/app-fundamentals/code-sharing.md); para obtener más información en [bibliotecas de clases Portable](~/cross-platform/app-fundamentals/pcl.md) y [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) también está disponible.

<a name="Sections" />

## <a name="building-cross-platform-mobile-apps"></a>Building Cross aplicaciones móviles de plataforma

 [Información general](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1: descripción de la plataforma Mobile Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2: arquitectura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3: configurar una solución de plataforma cruzada de Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4: trabajar con varias plataformas](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5: prácticas de uso compartido estrategias de código](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6: Pruebas y aprobaciones de App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />


## <a name="case-studies"></a>Casos prácticos

Los principios que se describen en este documento se colocan en práctica en la aplicación de ejemplo *Tasky*, así como [compilado previamente aplicaciones](https://xamarin.com/prebuilt) como [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />


### <a name="tasky"></a>Tasky

Tasky es una aplicación de lista de tareas pendientes simple para iOS, Android y Windows Phone.
Muestra los conceptos básicos de creación de aplicaciones multiplataforma con Xamarin y utiliza una base de datos local de SQLite.

 [![lista tasky](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky lista](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Leer la [Tasky caso práctico](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).


## <a name="summary"></a>Resumen

En esta sección se presenta las herramientas de desarrollo de aplicaciones de Xamarin y se describe cómo crear aplicaciones que tienen como destino varias plataformas móviles.

Abarca una arquitectura en capas ese código de estructuras para volver a usar en varias plataformas y se describen los patrones de software diferentes que pueden usarse en esa arquitectura.

Se proporcionan ejemplos de funciones de aplicación comunes (por ejemplo, las operaciones de archivo y red) y cómo puede crearse en una forma de multiplataforma.

Por último, brevemente describen pruebas y proporciona referencias a un caso práctico que coloca estos principios en acción.



## <a name="related-links"></a>Vínculos relacionados

- [Opciones de código compartido](~/cross-platform/app-fundamentals/code-sharing.md)
- [Caso práctico: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Aplicación de ejemplo tasky (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Desarrollo de aplicaciones móviles de Xamarin: Multiplataforma C# y los fundamentos de Xamarin.Forms](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/))
- [Desarrollo móvil con C# por Greg grilletes (o ' Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [Professional desarrollo móvil multiplataforma en C# de Scott Olson, John Hunter, Ben Horgen, Kenny concedida (Wrox)](http://www.wiley.com/WileyCDA/WileyTitle/productCd-1118157702.html)
