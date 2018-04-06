---
title: Xamarin para desarrolladores de Objective-C
description: Si es usted desarrollador de Objective-C, ya tiene la posibilidad de empezar a aprovechar sus habilidades y el código existente de Objective-C en la plataforma Xamarin, al tiempo que disfruta de los beneficios de reutilizar código de C#. Esta sección sirve como punto de entrada a Xamarin.iOS y aporta una gran cantidad de referencias sobre el uso del código de Objective-C existente desde C#.
ms.prod: xamarin
ms.assetid: 9F3C86A3-403E-4025-99CA-99FCA86DC828
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e29762fb258f7d796878c85bfe6f7aaa93207c5e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xamarin-for-objective-c-developers"></a>Xamarin para desarrolladores de Objective-C

_Si es usted desarrollador de Objective-C, comience a aprovechar sus habilidades y el código existente de Objective-C en la plataforma Xamarin, al tiempo que disfruta de los beneficios de reutilizar código de C#. Esta sección sirve como punto de entrada a Xamarin.iOS y aporta una gran cantidad de referencias sobre el uso del código de Objective-C existente desde C#._

Xamarin ofrece a los desarrolladores que tienen iOS como destino una ruta trasladar su código que no es de interfaz de usuario a C# independiente de la plataforma, de manera que se pueda utilizar en cualquier lugar en que C# esté disponible, como Android a través de Xamarin.Android y las diferentes versiones de Windows. Sin embargo, el mero hecho de usar C# con Xamarin no implica que pueda aprovechar sus habilidades y el código de Objective-C. De hecho, conocer Objective-C lo convierte en un mejor desarrollador de Xamarin.iOS porque Xamarin expone todas las API de plataforma nativas de iOS y OS X que conoce y que le encantan, como UIKit, Core Animation, Core Foundation y Core Graphics, por nombrar algunas. Al mismo tiempo, obtiene la eficacia del lenguaje C#, incluidas características como LINQ y Generics, así como completas bibliotecas de clase base de .NET para usar en sus aplicaciones nativas.

Además, Xamarin le permite aprovechar los recursos de Objective-C existentes mediante una tecnología que se conoce como "enlaces". Basta con crear una biblioteca estática en Objective C y exponerla a C# a través de un enlace, como se muestra en el diagrama siguiente:

 [![](images/01-bindings.png "Biblioteca estática en Objective-C expuesta a C# a través de un enlace")](images/01-bindings.png#lightbox)

Esto no tiene por qué limitarse al código que no es de interfaz de usuario. Los enlaces pueden exponer también el código de interfaz de usuario desarrollado en Objective-C.

## <a name="transitioning-from-objective-c"></a>Transición desde Objective-C

Encontrará una gran cantidad de información en nuestro sitio de documentación que le ayudará a facilitar la transición a Xamarin, donde se muestra cómo integrar código de C# con lo que ya conoce. Estos son algunos de los principales materiales por los que puede comenzar:

-   [Manual de C# para los desarrolladores Objective-C](primer.md): un breve manual para los desarrolladores de Objective-C que desean pasarse a Xamarin y el lenguaje C#. 
-   [Tutorial: enlace de una biblioteca de Objective-C](~/ios/platform/binding-objective-c/walkthrough.md): un tutorial detallado para volver a usar código existente de Objective-C en una aplicación de Xamarin.iOS. 


## <a name="binding-objective-c"></a>Enlace de Objective-C

Cuando haya adquirido unas nociones generales sobre las diferencias y similitudes entre C# y Objective-C y haya trabajando con el tutorial de enlace anterior, estará listo para la transición a la plataforma Xamarin. Si quiere profundizar en la materia, puede encontrar más información detallada sobre las tecnologías de enlace de Xamarin.iOS, incluida una exhaustiva referencia de enlace, en la sección [Enlace de Objective-C](~/ios/platform/binding-objective-c/index.md).

## <a name="cross-platform-development"></a>Desarrollo multiplataforma

Finalmente, después de pasarse a Xamarin.iOS, seguramente querrá consultar la guía multiplataforma de que disponemos, con estudios de caso de aplicaciones de referencia que hemos desarrollado y procedimientos recomendados para crear código multiplataforma reutilizable, contenidos en la [sección Building Cross Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) (Generación de aplicaciones multiplataforma).
