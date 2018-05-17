---
title: Diseños con pestañas
description: Información general de los diseños con pestañas en Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/08/2017
ms.openlocfilehash: 53ed5f91583d43839e96388194aea8c0d6ac5315
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="tabbed-layouts"></a>Diseños con pestañas


## <a name="overview"></a>Información general

Las pestañas son un patrón de interfaz de usuario populares en las aplicaciones móviles debido a su simplicidad y facilidad de uso. Proporcionan una manera coherente y fácil para navegar entre distintas pantallas en una aplicación. El sistema Android tiene varias API para las interfaces con pestañas: 

-   **ActionBar** &ndash; Esto forma parte de un nuevo conjunto de API que se introdujo en Android 3.0 (API nivel 11) con el fin de proporcionar una coherencia navegación y la interfaz de cambiar de vista. Ha sido trasladada atrás para Android 2.2 (API nivel 8) con el [biblioteca de compatibilidad de Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica las páginas de actual, anterior y siguiente de un `ViewPager`. `ViewPager` está disponible solo mediante [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obtener más información acerca de `PagerTabStrip`, consulte [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra de herramientas** &ndash; `Toolbar` es un componente de la barra de acción más reciente y más flexibles que reemplaza `ActionBar`. `Toolbar` está disponible en el círculo de Android 5.0 o posterior, y también está disponible para las versiones anteriores de Android a través de la [biblioteca de compatibilidad de Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete NuGet. 
    `Toolbar` ¿está el componente de la barra de acción recomendada para usar en aplicaciones de Android.
    Para obtener más información, consulte [barra de herramientas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Vínculos relacionados

- [Las pestañas diseño material -](https://material.io/guidelines/components/tabs.html)- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Paquete de compatibilidad con Android biblioteca v7 AppCompat NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
