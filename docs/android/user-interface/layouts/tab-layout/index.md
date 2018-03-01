---
title: "Diseños con pestañas"
description: "Información general de los diseños con pestañas en Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/23/2017
ms.openlocfilehash: f8680cde2e5536495f33d571adea9980020a72fa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="tabbed-layouts"></a>Diseños con pestañas

<a name="Overview" />

## <a name="overview"></a>Información general

Las pestañas son un patrón de interfaz de usuario populares en las aplicaciones móviles debido a su simplicidad y facilidad de uso. Proporcionan una manera coherente y fácil para navegar entre distintas pantallas en una aplicación. El sistema Android tiene varias API para las interfaces con pestañas: 

-   **TabHost** &ndash; se trata de la API original para crear interfaces de usuario con pestañas. Un `TabHost` widget se agrega a un diseño y actúa como contenedor para las vistas con pestañas. Esta API ya está en desuso y no se recomienda su uso. 

-   **ActionBar** &ndash; Esto forma parte de un nuevo conjunto de API que se introdujo en Android 3.0 (API nivel 11) con el fin de proporcionar una coherencia navegación y la interfaz de cambiar de vista. Ha sido trasladada atrás para Android 2.2 (API nivel 8) con el [biblioteca de compatibilidad de Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica las páginas de actual, anterior y siguiente de un `ViewPager`. `ViewPager` está disponible solo mediante [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obtener más información acerca de `PagerTabStrip`, consulte [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra de herramientas** &ndash; `Toolbar` es un componente de la barra de acción más reciente y más flexibles que reemplaza `ActionBar`. `Toolbar` está disponible en el círculo de Android 5.0 o posterior, y también está disponible para las versiones anteriores de Android a través de la [biblioteca de compatibilidad de Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete NuGet. 
    `Toolbar` ¿está el componente de la barra de acción recomendada para usar en aplicaciones de Android.
    Para obtener más información, consulte [barra de herramientas](~/android/user-interface/controls/tool-bar/index.md). 


Estas API son visualmente muy diferentes y no son compatibles entre sí. Pantalla de la siguiente imagen muestra `TabHost` y `ActionBar` en paralelo: 

![Capturas de pantalla de TabHost a la izquierda y ActionBar a la derecha](images/image01.png)

Estas API incompatibles existe debido a cambios importantes desde Android 3.0 (API nivel 11). Uno de estos cambios de la interfaz de usuario era la [acción barra modelo de diseño](http://www.androidpatterns.com/uap_pattern/action-bar), un modelo diseñado para facilitar el acceso a la funcionalidad de navegación y la clave en una aplicación. El `ActionBar` API se introdujo para admitir este patrón. 

El `ActionBar` API es más sencilla y sin duda proporciona una mejor experiencia de usuario. Se ha aplazado volver a Android 2.2 y es la opción preferida para las aplicaciones de Xamarin.Android. 

El `TabHost` API es compatible en todas las versiones de Android, pero requiere más esfuerzo de usar y no es coherente con el actual [Android directrices de interfaz de usuario](http://developer.android.com/design/index.html). Los desarrolladores de uso de esta API en absoluto y deben favorecer la ActionBar más recientes para sus aplicaciones Xamarin.Android. 


<a name="Introducing_ActionBarSherlock" />

## <a name="actionbarsherlock"></a>ActionBarSherlock

Antes de la API ActionBar utilizados para Android 2.2, los desarrolladores que deseaban la apariencia y funcionamiento más reciente de la API ActionBar, pero pudieron usar una biblioteca de terceros, [ActionBarSherlock](http://actionbarsherlock.com). ActionBarSherlock es que una extensión de la biblioteca de compatibilidad con Android diseñado para atrás el patrón de diseño de la barra de acción para Android 2.x. Cuando se ejecuta en Android 3.0 o posterior, ActionBarSherlock usarán automáticamente nativo `ActionBar` API proporcionada por Android. Las versiones anteriores de Android que va a usar una implementación personalizada que imite la apariencia y funcionamiento de las más recientes `ActionBar` API. El [ActionBarSherlock componente](https://www.nuget.org/packages/xamstore-XamarinActionBarSherlock/) resulta muy sencillo agregar ActionBarSherlock a una aplicación Xamarin.Android. 



## <a name="related-links"></a>Vínculos relacionados

- [Información general de TabHost](tab-host.md)
- [Tutorial de TabHost](~/android/user-interface/layouts/tab-layout/creating-a-tabbed-ui.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Paquete de compatibilidad con Android biblioteca v7 AppCompat NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
