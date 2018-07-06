---
title: Solución de problemas de enlace
description: Esta guía describe qué hacer si tiene dificultades para una biblioteca de Objective-C de enlace. En particular, comenta los enlaces que faltan, las excepciones del argumento cuando se pasa null a un enlace y los errores de generación de informes.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: aaceada84b151856506ede66907274e2457c23d4
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854810"
---
# <a name="binding-troubleshooting"></a>Solución de problemas de enlace

Algunas sugerencias para solucionar problemas de enlaces para macOS (antes conocido como OS X) las API de Xamarin.Mac.

## <a name="missing-bindings"></a>Enlaces que faltan

Mientras que Xamarin.Mac cubre gran parte de las API de Apple, en ocasiones, es posible que deba llamar a algunas API de Apple que no tiene un enlace aún. En otros casos, deberá llamar a C, Objective-C de terceros que TI fuera del ámbito de los enlaces de Xamarin.Mac.

Si está trabajando con una API de Apple, es el primer paso para que Xamarin sepa que está experimentando una sección de la API que no tenemos cobertura para todavía. [Archivar un error](#reporting-bugs) teniendo en cuenta la API que faltan. Informes de clientes se usa para dar prioridad a las API que trabajamos en siguiente. Además, si tiene una licencia Business o Enterprise y esta falta de un enlace está bloqueando el progreso, también siga las instrucciones de [soporte](http://xamarin.com/support) para presentar una incidencia. No podemos prometer un enlace, pero en algunos casos podemos obtener un trabajo en torno a.

Una vez que notifique a Xamarin (si procede) de su enlace faltando, el paso siguiente es considerar enlace usted mismo. Tenemos una guía completa [aquí](~/cross-platform/macios/binding/overview.md) y alguna documentación no oficial [aquí](http://brendanzagaeski.appspot.com/xamarin/0002.html) de ajuste de los enlaces de Objective-C a mano. Si se llama a una API de C, puede utilizar el mecanismo de P/Invoke de C#, la documentación está [aquí](http://www.mono-project.com/docs/advanced/pinvoke/).

Si decide trabajar en el enlace usted mismo, tenga en cuenta que los errores en el enlace pueden generar todo tipo de bloqueos interesantes en el tiempo de ejecución nativo. En concreto, asegúrese de que la firma en C# coincida con la nativa en el número de argumentos y el tamaño de cada argumento. Si no lo hace pueden dañar la memoria o la pila y podría bloquearse inmediatamente o en algún momento arbitrario en el futuro o dañar los datos.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Excepciones del argumento cuando se pasa null a un enlace

Aunque Xamarin trabaja para ofrecer alta calidad y bien probados enlaces para las API de Apple, en ocasiones, errores y errores slip en. Problema con mucha más comunes que pueden surgir es una API producir `ArgumentNullException` cuando se pasa en null cuando la API subyacente acepta `nil`. Los archivos de encabezado nativo define la API a menudo no proporcionan suficiente información en el que las API que acepte nil y que se bloqueará si se le pasa.

Si experimenta un caso donde pasando `null` produce una `ArgumentNullException` pero piensa debe trabajar, siga estos pasos:

1. Compruebe la documentación de Apple y ejemplos para ver si puede encontrar la prueba que acepta `nil`. Si está familiarizado con Objective-C, puede escribir un programa de prueba pequeño para verificarlo.
2. [Archivar un error](#reporting-bugs).
3. ¿Puede solucionar el error? Si no puede llamar a la API con `null`, una simple comprobación de null en torno a las llamadas puede ser una solución alternativa fácil.
4. Sin embargo, algunas API necesitan pasar null para desactivar o deshabilitar algunas características. En estos casos, puede solucionar el problema llevando el Explorador de ensamblado (vea [buscar el miembro de C# para un determinado selector](~/mac/app-fundamentals/mac-apis.md#finding_selector)), copiar el enlace y quitar la comprobación de null. Asegúrese de archivar un error (paso 2) si hacerlo, como el enlace copiado no recibirá actualizaciones y revisiones que se realice en Xamarin.Mac y debe considerarse una solución a corto plazo.

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>Informar de errores

Sus comentarios son importantes para nosotros. Si encuentra algún problema con Xamarin.Mac:

- Compruebe los [foros de Xamarin.Mac](https://forums.xamarin.com/categories/mac).
- Busque en el [repositorio de problemas](https://github.com/xamarin/xamarin-macios/issues). 
- Antes de la migración a GitHub, los problemas de Xamarin se recopilaban en [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Busque allí para ver si están los mismos problemas.
- Si no encuentra un problema, registre uno nuevo en el [repositorio de problemas de GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Los problemas de GitHub son públicos. No es posible ocultar comentarios ni datos adjuntos. 

De la siguiente información, incluya toda la que pueda:

- Un ejemplo sencillo que reproduzca el problema. Siempre que sea posible, esta información es **muy útil**. 
- El seguimiento de la pila completo del bloqueo.
- El código de C# del bloqueo. 

## <a name="related-links"></a>Vínculos relacionados

- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Compilar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
