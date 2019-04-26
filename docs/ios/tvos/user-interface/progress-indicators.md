---
title: Trabajar con tvOS indicadores de progreso en Xamarin
description: Este documento describe cómo trabajar con indicadores de progreso en una aplicación de tvOS con Xamarin. Describe las barras de progreso y los indicadores de actividad.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/25/2018
ms.openlocfilehash: cbd2b2de237a5bb22d1dc0242569b96b12bca070
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61180731"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>Trabajar con tvOS indicadores de progreso en Xamarin

_Este artículo trata el diseño y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS._

Puede que en ocasiones cuando su aplicación Xamarin.tvOS necesita cargar contenido nuevo o realizar una operación de procesamiento de larga duración. Durante esos períodos de tiempo se debe presentar un indicador de actividad o una barra de progreso para informar al usuario que todavía se está ejecutando la aplicación y para darles indicaciones sobre la duración de la tarea que se va a ejecutar.

![Indicadores de progreso de ejemplo](progress-indicators-images/intro01.png "indicadores de progreso de ejemplo")

## <a name="about-activity-indicators"></a>Acerca de los indicadores de actividad

Un indicador de actividad se presenta como un icono de engranaje de giro y se utiliza para representar una tarea de una longitud indeterminada. El indicador se presenta cuando la tarea se inicia y desaparece cuando se completa la tarea.

Apple tiene las siguientes sugerencias para trabajar con indicadores de actividad:

- **Siempre que sea posible, utilice en su lugar las barras de progreso** : porque un proporciona del indicador de actividad adoptará el usuario sin comentarios sobre la longitud del proceso en ejecución, utilice siempre una barra de progreso si la longitud se conoce (por ejemplo, el número de bytes para descargar en un archivo).
- **Mantener el indicador animado** -los usuarios para relacionar un indicador de actividad estacionarios a una aplicación detenida, por lo que siempre debe animar el indicador mientras se está mostrando.
- **Describir la tarea que se está procesando** -mostrar simplemente el indicador de actividad por sí solo no es suficiente; el usuario necesita para mantenerse informado sobre el proceso en el que están esperando. Incluir una etiqueta significativa (normalmente una frase única y completa) que defina claramente la tarea.

## <a name="about-progress-bars"></a>Acerca de las barras de progreso

Una barra de progreso se presenta como una línea que se rellena con color para indicar el estado y la longitud de una tarea que requieren mucho tiempo. Barras de progreso siempre se deben usar cuando la longitud de las tareas se conoce o se puede calcular.

Apple tiene las siguientes sugerencias para trabajar con las barras de progreso:

- **Informar con exactitud el progreso** -barras de progreso siempre deben presentar una representación precisa del tiempo necesario para completar una tarea. No falsificar nunca el tiempo para que la aplicación aparezca ocupado.
- **Uso de las duraciones bien definidas** -colocar barras no sólo deben mostrar que está teniendo una tarea larga de progreso, pero proporcionan al usuario y la indicación de qué parte de la tarea se completa y una estimación del tiempo restante.

## <a name="progress-indicators-and-storyboards"></a>Guiones gráficos e indicadores de progreso

Es la manera más fácil trabajar con un indicador de progreso en una aplicación Xamarin.tvOS agregarlo a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
1. En el **panel de solución**, haga doble clic en el **Main.storyboard** archivo y abrirlo para su edición.

2. Arrastre un **indicador de actividad** desde el **cuadro de herramientas** y colóquela en la vista: 

    ![Un indicador de actividad](progress-indicators-images/activity01.png "un indicador de actividad")

3. En el **Widget** pestaña de la **panel de propiedades**, puede ajustar varias propiedades del indicador de actividad, como su **estilo**, **comportamiento**, y **nombre**: 

    ![La pestaña de Widget de un indicador de actividad](progress-indicators-images/activity02.png "pestaña el Widget de un indicador de actividad")
    
    El **nombre** determina el nombre de la propiedad que representa el indicador de actividad en C# código.

4. Arrastre un **curso** desde el **cuadro de herramientas** y colóquela en la vista: 

    ![Una vista del progreso](progress-indicators-images/activity03.png "una vista del progreso")

5. En el **Widget** pestaña de la **Property Explorer**, puede ajustar varias propiedades de la vista del progreso, como su **estilo**, **progreso**(porcentaje completado), y **nombre**: 

    ![La pestaña de Widget para obtener una vista del progreso](progress-indicators-images/activity04.png "pestaña el Widget para obtener una vista del progreso")
    
    El **nombre** determina el nombre de la propiedad que representa la vista del progreso en C# código.

6. Guarde los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. En el **el Explorador de soluciones**, haga doble clic en el **Main.storyboard** archivo y abrirlo para su edición.

2. Arrastre un **indicador de actividad** desde el **cuadro de herramientas** y colóquela en la vista: 

    ![Un indicador de actividad](progress-indicators-images/activity01-vs.png
    "un indicador de actividad")

3. En el **Widget** pestaña de la **Explorador de propiedades**, puede ajustar varias propiedades del indicador de actividad, como su **estilo**, **comportamiento**, y **nombre**: 

    ![La pestaña de Widget de un indicador de actividad](progress-indicators-images/activity02-vs.png "pestaña el Widget de un indicador de actividad")

    El **nombre** determina el nombre de la propiedad que representa el indicador de actividad en C# código.

4. Arrastre un **curso** desde el **cuadro de herramientas** y colóquela en la vista: 

   ![Una vista del progreso](progress-indicators-images/activity03-vs.png "una vista del progreso")

5. En el **Widget** pestaña de la **Property Explorer**, puede ajustar varias propiedades de la vista del progreso, como su **estilo**, **progreso**(porcentaje completado), y **nombre**: 

    ![La pestaña de Widget para obtener una vista del progreso](progress-indicators-images/activity04-vs.png "pestaña el Widget para obtener una vista del progreso")
    
    El **nombre** determina el nombre de la propiedad que representa la vista del progreso en C# código.

6. Guarde los cambios.

-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Trabajar con indicadores de actividad

Como se indicó anteriormente, se deben mostrar indicadores de actividad cuando la aplicación se está ejecutando un proceso largo de longitud indeterminada.

En cualquier momento, puede ver si se anima un indicador de actividad mediante la comprobación de su `IsAnimating` propiedad. Si el `HidesWhenStopped` propiedad es `true`, el indicador de actividad se ocultarán automáticamente cuando se detiene la animación.

Puede usar el código siguiente para iniciar la animación: 

```csharp
ActivityIndicator.StartAnimating();
```

Y lo siguiente detendrá la animación:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Estos fragmentos de código suponen que el indicador de actividad **nombre** se estableció en **ActivityIndicator** en el **Widget** ficha del Diseñador de iOS.

## <a name="working-with-progress-bars"></a>Trabajar con las barras de progreso

Nuevamente, una barra de progreso debe usarse siempre que la aplicación ejecuta una tarea de larga ejecución de una duración determinada. 

El `Progress` propiedad se usa para establecer la cantidad de la tarea que se ha completado de 0% a 100% (de 0,0 a 1,0). Use la `ProgressTintColor` propiedad para establecer el color de la barra de la cantidad completado y el `TrackTintColor` propiedad para establecer el color de fondo (cantidad sin completar).

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
