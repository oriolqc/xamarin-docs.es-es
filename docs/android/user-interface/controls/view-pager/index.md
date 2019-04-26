---
title: ViewPager
description: ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo hacia izquierda y derecha para ir avanzando por las páginas de datos. Esta guía explica cómo implementar la navegación con ViewPager, gestural con y sin fragmentos. También se describe cómo agregar indicadores de página mediante PagerTitleStrip y PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: bb9795eb1e77a48b01556c553ae19613d6ab6de6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267631"
---
# <a name="viewpager"></a>ViewPager

_ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo hacia izquierda y derecha para ir avanzando por las páginas de datos. Esta guía explica cómo implementar la navegación con ViewPager, gestural con y sin fragmentos. También se describe cómo agregar indicadores de página mediante PagerTitleStrip y PagerTabStrip._

 
## <a name="overview"></a>Información general

Un escenario común en el desarrollo de aplicaciones es la necesidad de proporcionar a los usuarios con la navegación gestural entre las vistas del mismo nivel. En este enfoque, el usuario pase el dedo izquierda o derecha para las páginas de acceso de contenido (por ejemplo, en un Asistente para instalación o una presentación). Puede crear estas vistas de deslizar rápidamente mediante el `ViewPager` widget, disponible en [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). El `ViewPager` es un widget de diseño que se compone de varias vistas secundarias donde cada vista secundaria constituye una página en el diseño: 

[![Aplicación de capturas de pantalla de TreePager con el ejemplo de pasar el dedo horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

Por lo general, `ViewPager` se usa junto con [fragmentos](https://developer.xamarin.com/guides/android/platform_features/fragments/); sin embargo, existen algunas situaciones donde desea usar `ViewPager` sin la complejidad agregada de `Fragment`s.

`ViewPager` usa un patrón de adaptador que proporcionarla con las vistas para mostrar. El adaptador usado aquí es conceptualmente similar a la utilizada por [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; suministrar una implementación de `PagerAdapter` para generar las páginas que el `ViewPager` muestra al usuario. Las páginas se muestran por `ViewPager` puede ser `View`s o `Fragment`s. Cuando `View`s se muestran, del adaptador subclases Android `PagerAdapter` clase base. Si `Fragment`s se muestran, del adaptador subclases Android `FragmentPagerAdapter`. También incluye la biblioteca de compatibilidad con Android `FragmentPagerAdapter` (una subclase de `PagerAdapter`) para ayudar con los detalles de la conexión `Fragment`s a los datos. 

Esta guía muestra ambos enfoques: 

-   En [Viewpager con vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md), un [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicación se desarrolla para demostrar cómo usar `ViewPager` para mostrar las vistas de un catálogo de árbol (una galería de imágenes de árboles de hoja caduca y duraderas). 
    `PagerTabStrip`  y `PagerTitleStrip` se usan para mostrar los títulos que ayudan con la navegación de páginas.

-   En [Viewpager con fragmentos](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), un poco más compleja [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicación se desarrolla para demostrar cómo usar `ViewPager` con `Fragment`s para compilar una aplicación que presenta problemas de matemáticas como tarjetas Flash y responde a la entrada del usuario. 


## <a name="requirements"></a>Requisitos

Para usar `ViewPager` en el proyecto de aplicación, debe instalar la [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) paquete. Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: Incluir NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

 
## <a name="architecture"></a>Arquitectura

Tres componentes se usan para implementar la navegación gestural con `ViewPager`:

-   ViewPager
-   Adaptador
-   Indicador de buscapersonas

Cada uno de estos componentes se resume a continuación.



### <a name="viewpager"></a>ViewPager

`ViewPager` es un administrador de diseño que muestra una colección de `View`s a la vez. Su trabajo consiste en detectar el gesto de pasar el dedo del usuario y vaya a la vista anterior o siguiente según corresponda. Por ejemplo, la captura de pantalla siguiente muestra un `ViewPager` realizar la transición de una imagen a la siguiente en respuesta a un gesto de usuario: 

[![Mostrar una transición entre las vistas de aplicación de primer plano de TreePager](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>Adaptador

`ViewPager` extrae sus datos de un *adaptador*. Es crear se ocupa el adaptador el `View`s mostrado por el `ViewPager`, lo que les brinda según sea necesario. El diagrama siguiente ilustra este concepto &ndash; el adaptador crea y rellena `View`s y les proporciona el `ViewPager`. Como el `ViewPager` detecta los gestos de pasar el dedo del usuario, solicita el adaptador para proporcionar adecuado `View` para mostrar: 

[![Diagrama que ilustra cómo en que el adaptador conecta las imágenes y los nombres para el ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

En este ejemplo concreto, cada `View` se construye a partir de una imagen de árbol y un nombre de árbol antes de pasarlo a la `ViewPager`. 



### <a name="pager-indicator"></a>Indicador de buscapersonas

`ViewPager` puede utilizarse para mostrar un gran conjunto de datos (por ejemplo, una galería de imágenes puede contener cientos de imágenes). Para ayudar al usuario navegar por conjuntos de datos grandes, `ViewPager` suele ir acompañado un *indicador buscapersonas* que muestra una cadena. La cadena podría ser el título de la imagen, un título o simplemente la posición de la vista actual del conjunto de datos. 

Hay dos vistas que pueden generar esta información de navegación para usted: `PagerTabStrip` y `PagerTitleStrip.` cada uno muestra una cadena en la parte superior de un `ViewPager`, y cada uno de ellos extrae sus datos de la `ViewPager`del adaptador, por lo que TI siempre permanece sincronizada con el que se muestra actualmente `View`. La diferencia entre ellos es que `PagerTabStrip` incluye un indicador visual para la cadena "actual" mientras `PagerTitleStrip` sucede no (como se muestra en estas capturas de pantalla): 

[![Capturas de pantalla de la aplicación TreePager con PagerTitleStrip y PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Esta guía se muestra cómo immplement `ViewPager`, adaptador y los componentes de aplicación de indicador y los integran a para admitir la navegación gestural. 



## <a name="related-links"></a>Vínculos relacionados

- [TreePager (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
