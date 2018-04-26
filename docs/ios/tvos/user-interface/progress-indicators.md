---
title: Trabajar con indicadores de progreso
description: Este artículo tratan diseñar y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/25/2018
ms.openlocfilehash: d512dfddb3a6c81767f937272a4ffb1ab1a35372
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="working-with-progress-indicators"></a>Trabajar con indicadores de progreso

_Este artículo tratan diseñar y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS._

Puede haber ocasiones cuando se necesita la aplicación de Xamarin.tvOS para cargar contenido nuevo o realizar una operación de procesamiento largo. En estos momentos, se debe presentar un indicador de actividad o una barra de progreso para que el usuario sepa que la aplicación se está ejecutando y para ofrecerles alguna señal sobre la duración de la tarea que se va a ejecutar.

![Indicadores de progreso de ejemplo](progress-indicators-images/intro01.png "indicadores de progreso de ejemplo")

## <a name="about-activity-indicators"></a>Acerca de los indicadores de actividad

Un indicador de actividad se presenta como un icono de engranaje de giro y se utiliza para representar una tarea de una longitud indeterminada. El indicador se presenta cuando la tarea se inicia y desaparece cuando se completa la tarea.

Apple tiene las siguientes sugerencias para trabajar con indicadores de actividad:

- **Siempre que sea posible, utilice en su lugar las barras de progreso** : porque un proporciona del indicador de actividad le llevará al usuario no largos de comentarios en cuanto a cómo el proceso que se va a ejecutar, utilice siempre una barra de progreso si se conoce la longitud (por ejemplo, el número de bytes para descargar en un archivo).
- **Mantener el indicador animado** -los usuarios para relacionar un indicador de actividad estacionarios a una aplicación detenida, por lo que siempre debe animar el indicador mientras se está mostrando.
- **Describir la tarea que se está procesando** -mostrar simplemente el indicador de actividad por sí solo no es suficiente; el usuario debe ser informado sobre el proceso en el que están a la espera. Incluir una etiqueta significativa (normalmente, una frase única y completa) que define claramente la tarea.

## <a name="about-progress-bars"></a>Acerca de las barras de progreso

Una barra de progreso se presenta como una línea que se rellena con color para indicar el estado y la longitud de una tarea que consumen muchos recursos. Barras de progreso siempre deben utilizarse cuando la longitud de las tareas se conoce o se puede calcular.

Apple tiene las siguientes sugerencias para trabajar con barras de progreso:

- **Informar con exactitud de progreso** -barras de progreso siempre deben presentar una representación precisa del tiempo necesario para completar una tarea. No falsificar nunca el tiempo para que la aplicación aparezca ocupado.
- **Uso de las duraciones bien definidas** -colocar barras no sólo deben mostrar que tiene una tarea larga de progreso, pero proporcionan al usuario y la indicación de la cantidad de la tarea se completa y una estimación del tiempo restante.

## <a name="progress-indicators-and-storyboards"></a>Guiones gráficos e indicadores de progreso

Es la manera más fácil trabajar con un indicador de progreso en una aplicación Xamarin.tvOS agregarlo a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. En el **solución Pad**, haga doble clic en el **Main.storyboard** archivo y abrirlo y editarlo.

2. Arrastre un **indicador de actividad de** desde el **cuadro de herramientas** y colóquela en la vista: 

    ![Un indicador de actividad](progress-indicators-images/activity01.png "un indicador de actividad")

3. En el **Widget** pestaña de la **panel de propiedades**, puede ajustar varias propiedades del indicador de actividad como su **estilo**, **comportamiento**, y **nombre**: 

    ![La pestaña de Widget de un indicador de actividad](progress-indicators-images/activity02.png "pestaña el Widget de un indicador de actividad")
    
    El **nombre** determina el nombre de la propiedad que representa el indicador de actividad en código C#.

4. Arrastre un **curso** desde el **cuadro de herramientas** y colóquela en la vista: 

    ![Una vista del progreso](progress-indicators-images/activity03.png "una vista del progreso")

5. En el **Widget** pestaña de la **Property Explorer**, puede ajustar varias propiedades de la vista de progreso como su **estilo**, **progreso**(porcentaje completado), y **nombre**: 

    ![La pestaña de Widget para obtener una vista del progreso](progress-indicators-images/activity04.png "pestaña el Widget para obtener una vista del progreso")
    
    El **nombre** determina el nombre de la propiedad que representa la vista del progreso en código C#.

6. Guarde los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. En el **el Explorador de soluciones**, haga doble clic en el **Main.storyboard** archivo y abrirlo y editarlo.

2. Arrastre un **indicador de actividad de** desde el **cuadro de herramientas** y colóquela en la vista: 

    ![Un indicador de actividad](progress-indicators-images/activity01-vs.png
    "un indicador de actividad")

3. En el **Widget** pestaña de la **el Explorador de propiedades**, puede ajustar varias propiedades del indicador de actividad como su **estilo**, **comportamiento**, y **nombre**: 

    ![La pestaña de Widget de un indicador de actividad](progress-indicators-images/activity02-vs.png "pestaña el Widget de un indicador de actividad")

    El **nombre** determina el nombre de la propiedad que representa el indicador de actividad en código C#.

4. Arrastre un **curso** desde el **cuadro de herramientas** y colóquela en la vista: 

   ![Una vista del progreso](progress-indicators-images/activity03-vs.png "una vista del progreso")

5. En el **Widget** pestaña de la **Property Explorer**, puede ajustar varias propiedades de la vista de progreso como su **estilo**, **progreso**(porcentaje completado), y **nombre**: 

    ![La pestaña de Widget para obtener una vista del progreso](progress-indicators-images/activity04-vs.png "pestaña el Widget para obtener una vista del progreso")
    
    El **nombre** determina el nombre de la propiedad que representa la vista del progreso en código C#.

6. Guarde los cambios.

-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Trabajar con indicadores de actividad

Como se mencionó anteriormente, se deberían mostrar indicadores de actividad cuando la aplicación está ejecutando un proceso largo de longitud indeterminada.

En cualquier momento, puede ver si un indicador de actividad es animar comprobando su `IsAnimating` propiedad. Si el `HidesWhenStopped` propiedad es `true`, el indicador de actividad se ocultarán automáticamente cuando se detiene la animación.

Puede usar el código siguiente para iniciar la animación: 

```csharp
ActivityIndicator.StartAnimating();
```

Y, a continuación detendrá la animación:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Estos fragmentos de código supone que el indicador de actividad **nombre** se estableció en **ActivityIndicator** en el **Widget** pestaña del Diseñador de iOS.

## <a name="working-with-progress-bars"></a>Trabajar con barras de progreso

Una vez más, una barra de progreso debe utilizarse siempre que la aplicación ejecuta una tarea larga de una duración determinada. 

El `Progress` propiedad se utiliza para establecer la cantidad de la tarea que se ha completado de 0% a 100% (de 0,0 a 1,0). Use la `ProgressTintColor` propiedad para establecer el color de la barra de cantidad completada y el `TrackTintColor` propiedad para establecer el color de fondo (cantidad incompleta).

## <a name="summary"></a>Resumen

Diseñar y trabajar con indicadores de progreso dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
