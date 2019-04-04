---
title: Diseños con pestañas
description: Información general de los diseños con pestañas de Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
ms.openlocfilehash: 32d1ce4e440a962e02fda052375171bea7676053
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670167"
---
# <a name="tabbed-layouts"></a>Diseños con pestañas


## <a name="overview"></a>Información general

Las fichas son un patrón de interfaz de usuario populares en aplicaciones móviles debido a su simplicidad y la facilidad de uso. Proporcionan una manera coherente y fácil de navegar entre varias pantallas en una aplicación. Android tiene varias API para las interfaces con pestañas: 

-   **ActionBar** &ndash; Esto forma parte de un nuevo conjunto de API que se introdujo en Android 3.0 (API nivel 11) con el objetivo de ofrecer una coherente interfaz cambiar de vista y navegación. Se ha aplazado volver a Android 2.2 (nivel de API 8) con el [biblioteca de compatibilidad de Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica las páginas de actual, anterior y siguiente de un `ViewPager`. `ViewPager` está disponible solo mediante [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obtener más información acerca de `PagerTabStrip`, consulte [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra de herramientas** &ndash; `Toolbar` es un componente de barra de acción más reciente y más flexible que reemplaza `ActionBar`. `Toolbar` está disponible en Android Lollipop 5.0 o posterior, y también está disponible para versiones anteriores de Android a través de la [biblioteca de compatibilidad de Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete NuGet. 
    `Toolbar` actualmente es el componente de barra de acción recomendada para usar en aplicaciones para Android.
    Para obtener más información, consulte [barra de herramientas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Vínculos relacionados

- [Pestañas de material Design -](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Paquete de NuGet de compatibilidad con Android biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat V7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
