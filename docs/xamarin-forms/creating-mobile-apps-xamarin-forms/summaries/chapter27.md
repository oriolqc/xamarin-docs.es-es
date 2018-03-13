---
title: "Resumen de capítulo 27. Representadores personalizados"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 6d7c2b17e9596b7d2dd26aaf77cf13f7f8086cd5
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumen de capítulo 27. Representadores personalizados

Un elemento de Xamarin.Forms como `Button` se representa con un botón específico de la plataforma encapsulado en una clase denominada `ButtonRenderer`.  Este es el [versión de iOS de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), el [versión de Android de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)y el [versión de Windows en tiempo de ejecución de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ButtonRenderer.cs).

Este capítulo aborda cómo se pueden escribir sus propio representadores para crear vistas personalizadas que se asignan a objetos específicos de la plataforma.

## <a name="the-complete-class-hierarchy"></a>La jerarquía de clases completa

Hay siete ensamblados que contienen el código específico de la plataforma de Xamarin.Forms.
Puede ver el origen en GitHub con estos vínculos:

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muy pequeño)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.WinRT** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT) (mayor que los tres siguientes)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)
- [**Xamarin.Forms.Platform.WinRT.Tablet**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Tablet)
- [**Xamarin.Forms.Platform.WinRT.Phone**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Phone)

El [ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) ejemplo muestra una jerarquía de clases para los ensamblados que son válidos para la plataforma que se está ejecuta.

Observará una clase importante denominada `ViewRenderer`. Se trata de la clase que se deriva de cuando se crea a una representación específica de la plataforma. Existe en tres versiones diferentes, dado que está asociado con el sistema de la vista de la plataforma de destino:

El archivo iOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L26) tiene argumentos genéricos:

- `TView` la restricción [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` la restricción [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

El Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L14) tiene argumentos genéricos:

- `TView` la restricción [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` la restricción [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

El tiempo de ejecución de Windows [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ViewRenderer.cs#L12) diferente tiene denominado argumentos genéricos:

- `TElement` la restricción [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeElement` la restricción [`Windows.UI.Xaml.FrameworkElement`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.aspx)

Al escribir un representador, se puede derivar una clase de `View`y, a continuación, escribir varias `ViewRenderer` clases, uno para cada plataforma compatible. Cada implementación específica de la plataforma hará referencia a una clase nativa que se deriva el tipo que se especifica como el `TNativeView` o `TNativeElement` parámetro.

## <a name="hello-custom-renderers"></a>Hola, representadores personalizados.

El [ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) programa hace referencia a una vista personalizada denominada `HelloView` en su [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) clase.

El [ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) clase se incluye en el **HelloRenderers** del proyecto y se deriva simplemente de `View`.

El [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) clase en el **HelloRenderers.iOS** proyecto deriva de `ViewRenderer<HelloView, UILabel>`. En el `OnElementChanged` override, crea una nativas para iOS `UILabel` y llamadas `SetNativeControl`.

El [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) clase en el **HelloRenderers.Droid** proyecto deriva de `ViewRenderer<HelloView, TextView>`. En el `OnElementChanged` override, crea un Android `TextView` y llamadas `SetNativeControl`.

El [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) clase en el **HelloRenderers.UWP** y otros proyectos de Windows se deriva de `ViewRenderer<HelloView, TextBlock>`. En el `OnElementChanged` override, crea una ventana de `TextBlock` y llamadas `SetNativeControl`.

Todos los la `ViewRenderer` derivados contienen un `ExportRenderer` atributo en el nivel de ensamblado que asocia el `HelloView` clase con ese `HelloViewRenderer` clase. Se trata cómo Xamarin.Forms ubica a representadores en los proyectos de plataforma individuales:

[![Captura de pantalla triple de Hello vista](images/ch27fg02-small.png "representadores personalizados")](images/ch27fg02-large.png#lightbox "representadores personalizados")

## <a name="renderers-and-properties"></a>Representadores y propiedades

El siguiente conjunto de representadores implementa el dibujo de la elipse y se encuentra en los distintos proyectos de la [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solución.

El [ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) clase está en el **Xamarin.FormsBook.Platform** plataforma. La clase es similar a `BoxView` y define una propiedad de solo: `Color` de tipo `Color`.

El representador no puede transferir valores de propiedad establecidos en un `View` al objeto nativo invalidando el `OnElementPropertyChanged` método en el representador. Dentro de este método (y en la mayoría del representador), están disponibles dos propiedades:

- `Element`, el elemento de Xamarin.Forms
- `Control`, la vista nativo o el objeto de widget o control

Los tipos de estas propiedades dependen de los parámetros genéricos para `ViewRenderer`. En este ejemplo, `Element` es de tipo `EllipseView`.

El `OnElementPropertyChanged` invalidación, por tanto, puede transferir la `Color` valor de la `Element` a nativo `Control` objeto, probablemente tenga algún tipo de conversión. Los representadores de tres son:

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), que usa un [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) clase de la elipse.
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), que usa un [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) clase de la elipse.
- En tiempo de ejecución de Windows: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), que puede utilizar las ventanas nativas [ `Ellipse` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.ellipse.aspx) clase.

El [ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) clase muestra algunos de estos `EllipseView` objetos:

[![Captura de pantalla triple de demostración de elipse](images/ch27fg03-small.png "representadores personalizados de EllipseView")](images/ch27fg03-large.png#lightbox "EllipseView personalizado representadores")

El [ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) devoluciones de mensajes un `EllipseView` desactivado los lados de la pantalla.

## <a name="renderers-and-events"></a>Representadores y eventos

También es posible en los representadores indirectamente generar eventos. El [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) clase es similar a la normal Xamarin.Forms `Slider` pero permite especificar un número de pasos discretos entre el `Minimum` y `Maximum` valores.

Los representadores de tres son:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- En tiempo de ejecución de Windows: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

El representador no detecta cambios en el control nativo y, a continuación, llame a `SetValueFromRenderer`, que hace referencia a una propiedad enlazable definida en el `StepSlider`, un cambio en lo que hace que la `StepSlider` para desencadenar una `ValueChanged` eventos.

El [ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) ejemplo muestra el control deslizante nueva.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 27 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Ejemplos de capítulo 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
