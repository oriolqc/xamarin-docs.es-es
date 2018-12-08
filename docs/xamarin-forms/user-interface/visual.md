---
title: Xamarin.Forms Visual
description: Este artículo presentan Xamarin.Forms Visual, que representa las vistas de forma idéntica, o idéntica en gran medida, en iOS y Android.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2018
ms.openlocfilehash: df2351e35817a6468fed236752eea8e5ad11024f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061891"
---
# <a name="xamarinforms-visual"></a>Xamarin.Forms Visual

![Vista previa](~/media/shared/preview.png)

_Este artículo presentan Xamarin.Forms Visual, que representa las vistas de forma idéntica, o idéntica en gran medida, en iOS y Android._

Muchos programadores que desean crear aplicaciones de Xamarin.Forms que tienen un aspecto idénticos o idéntica en gran medida en iOS y Android. Xamarin.Forms 4.0-pre1 incluye un mecanismo para incluir los representadores adicionales que implementan una apariencia visual, con las aplicaciones que participan en la apariencia a través de un `Visual` propiedad:

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>    
```

Los representadores que implementan la apariencia visual, a continuación, se usan para las vistas de representador, en lugar de los representadores de forma predeterminada. En tiempo de selección del representador, el `Visual` propiedad de la vista es inspeccionar y se incluye en el proceso de selección del representador. Además, si la `Visual` propiedad cambia en tiempo de ejecución, se vuelve a crear el representador especificado junto con los elementos secundarios.

> [!IMPORTANT]
> El `Visual` propiedad está definida en el `VisualElement` (clase), con vistas al heredar el `Visual` valor de propiedad de sus elementos primarios. Por consiguiente, establecer el `Visual` propiedad en un `ContentPage` garantiza que todas las vistas admitidas en la página usará ese aspecto visual. Además, el `Visual` propiedad se puede invalidar en una vista.

Xamarin.Forms 4.0-pre1 incluye una apariencia visual experimental basada en diseño de materiales, con los representadores que se conoce como los representadores de material. Los representadores de material actualmente se incluyen para las siguientes vistas en iOS y Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)

Funcionalmente, los representadores de material no son distintos a los representadores de forma predeterminada. Sin embargo, que son actualmente experimentales y solo se puede usar agregando la siguiente línea de código para su `AppDelegate` clase en iOS, así como su `MainActivity` clase en Android, antes de llamar a `Forms.Init`:

```csharp
Forms.SetFlags("Visual_Experimental");
```

Además, en iOS, el proyecto de plataforma debe tener la [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents/) instalado el paquete de NuGet. En Android, Visual sólo funciona con la API de 29, debe usar v28 de las bibliotecas de soporte técnico de su proyecto de la plataforma y establecer su tema para heredar de un tema de los componentes de Material o continuar heredar de un tema AppCompat al agregar algunos nuevos atributos de tema al tema. Para obtener más información, consulte [Introducción a los componentes de Material para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Las capturas de pantalla siguientes muestran una interfaz de usuario que incluye las cuatro vistas de para qué material representadores existen, pero va a representar utilizando a los representadores de forma predeterminada:

[![Default representadores](visual-images/default-renderers.png "las vistas con los representadores de forma predeterminada")](visual-images/default-renderers-large.png#lightbox)

Las capturas de pantalla siguientes muestran la misma interfaz de usuario que se va a representar utilizando a los representadores de materiales:

[![Los representadores de material](visual-images/material-renderers.png "las vistas con los representadores de material")](visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Con los representadores de material los controles representados permanecen controles nativos y, por lo tanto, seguirá habiendo diferencias de la interfaz de usuario entre plataformas para áreas como fuentes, colores, sombras y elevación.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
