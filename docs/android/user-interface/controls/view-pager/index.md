---
title: ViewPager
description: "ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo izquierdo y derecho con el paso a través de páginas de datos. Esta guía explica cómo implementar la navegación gestural con ViewPager, con y sin fragmentos. También describe cómo agregar indicadores de página usando PagerTitleStrip y PagerTabStrip."
ms.topic: article
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 80ba525b87d2008f290e32fde56265630bac729a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="viewpager"></a>ViewPager

_ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo izquierdo y derecho con el paso a través de páginas de datos. Esta guía explica cómo implementar la navegación gestural con ViewPager, con y sin fragmentos. También describe cómo agregar indicadores de página usando PagerTitleStrip y PagerTabStrip._

<a name="overview" />
 
## <a name="overview"></a>Información general

Un escenario común en el desarrollo de aplicaciones es la necesidad de proporcionar a los usuarios con navegación gestural entre las vistas del mismo nivel. En este enfoque, el usuario lectores de crédito izquierda o derecha para páginas de acceso de contenido (por ejemplo, en un Asistente para la instalación o una presentación con diapositivas). Puede crear estas vistas deslice el dedo mediante la `ViewPager` widget, disponible en [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). El `ViewPager` es un widget de diseño consta de varias vistas secundarias donde cada vista secundaria constituye una página en el diseño: 

[![Aplicación de capturas de pantalla de TreePager con ejemplo deslice el dedo horizontal](images/01-intro-sml.png)](images/01-intro.png)

Por lo general, `ViewPager` se usa junto con [fragmentos](https://developer.xamarin.com/guides/android/platform_features/fragments/); sin embargo, existen algunas situaciones en las que tal vez desee usar `ViewPager` sin la complejidad agregada de `Fragment`s.

`ViewPager` usa un patrón de adaptador para proporcionar a las vistas para mostrar. El adaptador usado aquí es conceptualmente similar a la utilizada por [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; proporcionar una implementación de `PagerAdapter` para generar las páginas que la `ViewPager` muestra al usuario. Las páginas se muestran por `ViewPager` puede ser `View`s o `Fragment`s. Cuando `View`s se muestran, del adaptador subclases Android `PagerAdapter` clase base. Si `Fragment`s se muestran, del adaptador subclases Android `FragmentPagerAdapter`. También incluye la biblioteca de compatibilidad con Android `FragmentPagerAdapter` (una subclase de `PagerAdapter`) para ayudar a los detalles de la conexión `Fragment`s a los datos. 

Esta guía muestra ambos enfoques: 

-   En [Viewpager con vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md), [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicación se ha desarrollado para demostrar cómo usar `ViewPager` para mostrar las vistas de un catálogo de árbol (una galería de imágenes de árboles de hoja caduca y permanentes). 
    `PagerTabStrip`  y `PagerTitleStrip` se usan para mostrar títulos que ayudan a navegar en páginas.

-   En [Viewpager con fragmentos](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), ligeramente más complejo [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/) aplicación se ha desarrollado para demostrar cómo usar `ViewPager` con `Fragment`s para compilar una aplicación que presenta problemas de matemáticas como tarjetas de Flash y responde a los proporcionados por el usuario. 

<a name="requirements" />

## <a name="requirements"></a>Requisitos

Usar `ViewPager` en el proyecto de aplicación, debe instalar la [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) paquete. Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: incluidos unos NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

<a name="architecture" />
 
## <a name="architecture"></a>Arquitectura

Tres componentes se usan para implementar el desplazamiento gestural con `ViewPager`:

-   ViewPager
-   Adaptador
-   Indicador de buscapersonas

Cada uno de estos componentes se resume a continuación.


<a name="viewpager" />

### <a name="viewpager"></a>ViewPager

`ViewPager` es un administrador de diseño que muestra una colección de `View`s a la vez. Su trabajo consiste en detectar gesto de deslizar rápidamente del usuario y vaya a la vista siguiente o anterior, según corresponda. Por ejemplo, la captura de pantalla siguiente muestra un `ViewPager` que realiza la transición de una imagen a la siguiente en respuesta a un gesto de usuario: 

[![Aplicación de la vista de cerca de TreePager mostrar una transición entre las vistas](images/02-transition-sml.png)](images/02-transition.png)


<a name="adapter" />

### <a name="adapter"></a>Adaptador

`ViewPager` extrae los datos de un *adaptador*. Trabajo del adaptador consiste en crear el `View`s mostrado por el `ViewPager`, enviarles según sea necesario. El diagrama siguiente ilustra este concepto &ndash; el adaptador crea y rellena `View`s y proporciona a los `ViewPager`. Como el `ViewPager` detecta movimientos de deslice el dedo del usuario, solicita el adaptador para proporcionar la correspondiente `View` para mostrar: 

[![Diagrama que ilustra la forma en que el adaptador conecta imágenes y nombres para el ViewPager](images/03-adapter-sml.png)](images/03-adapter.png)

En este ejemplo concreto, cada `View` se construye a partir de una imagen de árbol y un nombre de árbol antes de pasarlo a la `ViewPager`. 


<a name="indicator" />

### <a name="pager-indicator"></a>Indicador de buscapersonas

`ViewPager` puede utilizarse para mostrar un conjunto de datos grande (por ejemplo, una galería de imágenes puede contener cientos de imágenes). Para ayudar al usuario navegar por los conjuntos de datos grandes, `ViewPager` a menudo viene acompañado por un *indicador de buscapersonas* que muestra una cadena. La cadena puede ser el título de la imagen, un título o simplemente la posición de la vista actual del conjunto de datos. 

Hay dos vistas que pueden generar esta información de exploración para usted: `PagerTabStrip` y `PagerTitleStrip.` cada uno muestra una cadena en la parte superior de un `ViewPager`, y cada uno de ellos extrae los datos de la `ViewPager`del adaptador, por lo que TI siempre permanece sincronizado con el que se muestra actualmente `View`. La diferencia entre ellos es que `PagerTabStrip` incluye un indicador visual de la cadena "actual" al `PagerTitleStrip` sucede no (como se muestra en estas capturas de pantalla): 

[![Capturas de pantalla de la aplicación TreePager con PagerTitleStrip y PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png)

Esta guía se demuestra cómo immplement `ViewPager`, adaptador y los componentes de aplicación de indicador e integrarlos para admitir la navegación gestural. 



## <a name="related-links"></a>Vínculos relacionados

- [TreePager (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
