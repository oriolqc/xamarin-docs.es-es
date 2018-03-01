---
title: Trabajar con indicadores de progreso
description: "Este artículo tratan diseñar y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b5d3a03324e73b06bd3defe7e6610163c3d1b26d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-progress-indicators"></a>Trabajar con indicadores de progreso

_Este artículo tratan diseñar y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS._


Puede haber ocasiones cuando se necesita la aplicación de Xamarin.tvOS para cargar contenido nuevo o realizar una operación de procesamiento largo. En estos momentos, se debe presentar un indicador de actividad o barra de progreso para que el usuario sepa que la aplicación se está ejecutando y para ofrecerles alguna señal sobre la duración de la tarea que se va a ejecutar.

[ ![](progress-indicators-images/intro01.png "Indicadores de progreso de ejemplo")](progress-indicators-images/intro01.png)

<a name="About-Activity-Indicators" />

## <a name="about-activity-indicators"></a>Acerca de los indicadores de actividad

Un indicador de actividad presenta como un icono de engranaje de giro visualmente y se utiliza para representar una tarea de una longitud indeterminada. El indicador se presenta cuando la tarea se inicia y desaparece cuando se completa la tarea.

Apple tiene las siguientes sugerencias para trabajar con indicadores de actividad:

- **Siempre que sea posible, utilice progreso barras en su lugar** : porque un indicador de actividad no proporciona al usuario comentarios sobre el tiempo que se tarda el proceso que se va a ejecutar, utilice siempre una barra de progreso si se conoce la longitud (por ejemplo, el número de bytes para descargar en un archivo).
- **Mantener el indicador animado** -los usuarios para relacionar un indicador de actividad estacionarios a una aplicación detenida por lo que siempre debe tener el indicador animado mientras se está mostrando.
- **Describir la tarea que se está procesando** -mostrar simplemente el indicador de actividad por sí solo no es suficiente, el usuario debe ser informado sobre el proceso que están esperando. Incluir una etiqueta significativa (normalmente, una frase única y completa) que define claramente la tarea.

<a name="Summary" />

## <a name="about-progress-bars"></a>Acerca de las barras de progreso

Una barra de progreso se presenta como una línea que se rellena con color para indicar el estado y la longitud de una tarea que consumen muchos recursos. Barras de progreso siempre debe utilizarse cuando la longitud de las tareas se conoce o se puede calcular.

Apple tiene las siguientes sugerencias para trabajar con barras de progreso:

- **Notificar el progreso con precisión** -barras de progreso siempre debe ser una representación precisa del tiempo necesario para completar una tarea. No falsificar nunca el tiempo para que la aplicación aparezca ocupado.
- **Uso de las duraciones de Well-Defined** -barra de progreso no sólo debe mostrar que tiene una tarea larga coloca, pero proporcionan al usuario y la indicación de la cantidad de la tarea se completa y una estimación del tiempo restante.

<a name="Progress-Indicators-and-Storyboards" />

## <a name="progress-indicators-and-storyboards"></a>Guiones gráficos e indicadores de progreso

Es la manera más fácil trabajar con el indicador de progreso en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. En el **solución Pad**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **indicador de actividad de** desde el **cuadro de herramientas** y colóquela en la vista: 

    [ ![](progress-indicators-images/activity01.png "Un indicador de actividad")](progress-indicators-images/activity01.png)
1. En el **ficha Widget** de la **panel de propiedades**, puede ajustar varias propiedades del indicador de actividad como su **estilo** y **comportamiento**: 

    [ ![](progress-indicators-images/activity02.png "La pestaña de Widget ")](progress-indicators-images/activity02.png)
1. Arrastre un **curso** desde el **cuadro de herramientas** y colóquela en la vista: 

    [ ![](progress-indicators-images/activity03.png "Una vista del progreso")](progress-indicators-images/activity03.png)
1. En el **ficha Widget** de la **Property Explorer**, puede ajustar varias propiedades de la vista de progreso como su **estilo** y **progreso**(porcentaje completado): 

    [ ![](progress-indicators-images/activity04.png "La pestaña de Widget")](progress-indicators-images/activity04.png)
1. Por último, asignar **nombres** a los controles de modo que pueda responder a ellos en código C#. Por ejemplo: 

    [ ![](progress-indicators-images/activity05.png "Asigne un nombre")](progress-indicators-images/activity05.png)
1. Guarde los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **indicador de actividad de** desde el **cuadro de herramientas** y colóquela en la vista: 

    [ ![](progress-indicators-images/activity01-vs.png "Un indicador de actividad")](progress-indicators-images/activity01-vs.png)
1. En el **ficha Widget** de la **el Explorador de propiedades**, puede ajustar varias propiedades del indicador de actividad como su **estilo** y **comportamiento**: 

    [ ![](progress-indicators-images/activity02-vs.png "La pestaña de Widget")](progress-indicators-images/activity02-vs.png)
1. Arrastre un **curso** desde el **cuadro de herramientas** y colóquela en la vista: 

    [ ![](progress-indicators-images/activity03-vs.png "Una vista del progreso")](progress-indicators-images/activity03-vs.png)
1. En el **ficha Widget** de la **Property Explorer**, puede ajustar varias propiedades de la vista de progreso como su **estilo** y **progreso**(porcentaje completado): 

    [ ![](progress-indicators-images/activity04-vs.png "La pestaña de Widget")](progress-indicators-images/activity04-vs.png)
1. Por último, asignar **nombres** a los controles de modo que pueda responder a ellos en código C#. Por ejemplo: 

    [ ![](progress-indicators-images/activity05-vs.png "Asigne un nombre")](progress-indicators-images/activity05-vs.png)
1. Guarde los cambios.

-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Activity-Indicators" />

## <a name="working-with-activity-indicators"></a>Trabajar con indicadores de actividad

Como se mencionó anteriormente, se debería mostrar indicadores de actividad cuando la aplicación ejecuta un proceso largo, pero no conoce la longitud exacta de tiempo que requerirá la tarea.

En cualquier momento puede ver si el indicador de actividad se ejecuta la animación de giro activando el `IsAnimating` propiedad. Si el `HidesWhenStopped` propiedad es `true`, el indicador de actividad se ocultarán automáticamente cuando se detiene la animación.

Puede usar el código siguiente para iniciar la animación: 

```csharp
ActivityIndicator.StartAnimating();
```

Y, a continuación detendrá la animación:

```csharp
ActivityIndicator.StopAnimating();
```

<a name="Working-with-Progress-Bars" />

## <a name="working-with-progress-bars"></a>Trabajar con barras de progreso

Una vez más, una barra de progreso debe utilizarse siempre que la aplicación ejecuta una tarea larga de una duración de conocidos. 

El `Progress` propiedad se utiliza para establecer la cantidad de la tarea que se ha completado de 0% a 100% (de 0,0 a 1,0). Use la `ProgressTintColor` propiedad para establecer el color de la barra de cantidad completada y el `TrackTintColor` propiedad para establecer el color de fondo (cantidad incompleta).

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
