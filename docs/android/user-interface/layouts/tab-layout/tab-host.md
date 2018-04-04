---
title: Pestaña diseño con TabHost
description: En este artículo le proporcionará una introducción de alto nivel de la la TabHost, una API más antiguos que se utilizan para crear los diseños con pestañas en una aplicación Xamarin.Android.
ms.prod: xamarin
ms.assetid: 77B890A4-27A6-41DF-81BA-22C6116A8FB2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: ae5b9020e08575bcd453703f3df14f63b288d2f5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="tab-layout-with-tabhost"></a>Pestaña diseño con TabHost

_En este artículo le proporcionará una introducción de alto nivel de la la TabHost, una API más antiguos que se utilizan para crear los diseños con pestañas en una aplicación Xamarin.Android._


## <a name="overview"></a>Información general

> [!NOTE]
> `TabHost` es una API antigua que ha quedado en desuso por Google. Los desarrolladores pueden crear aplicaciones con pestañas con el [ActionBar](~/android/user-interface/controls/action-bar.md). La `ActionBar` está disponible en todas las versiones de Android. Que se introdujo en Android 3.0 (API nivel 11) y volver procede para Android 2.2 (API nivel 8) y Android 2.3 (API nivel 10) en el [V7 AppCompat biblioteca](http://developer.android.com/tools/support-library/features.html#v7-appcompat), que está disponible para Xamarin.Android a través de la [Xamarin Biblioteca de compatibilidad de Android - V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) paquete.

El `TabHost` es la API anterior, original para crear un usuario con pestañas interfacesIt es ideal para las aplicaciones de Xamarin.Android que debe ser compatible con Android 2.2 y 2.3 Android y no se puede usar **ActionBarSherlock**.
Los siguientes cinco componentes se tienen en cuenta con la `TabHost` API:

-  **TabActivity** &ndash; se trata de una vista especializada que actúa como un contenedor para un `TabHost` (descritos a continuación).

-  **TabHost** &ndash; se trata de un contenedor para la interfaz de usuario con pestañas. Hospeda dos elementos secundarios: una lista de etiquetas de pestaña y un `FrameLayout` que muestra el contenido de las pestañas.

-  **TabWidget** &ndash; este control muestra una lista de etiquetas de pestaña, uno para cada pestaña en el `TabHost` . Cada pestaña en un `TabHost` se representan mediante un `TabHost.TabSpec` objeto. Este objeto contiene los metadatos de cada pestaña. Cuando el usuario selecciona una ficha, el `TabHost` responde a la selección en la pestaña adecuada.

-  **FrameLayout** &ndash; debe tener una interfaz de usuario con pestañas un `FrameLayout` contenidos dentro de un `TabHost`. Se usa para mostrar el contenido de la pestaña.

-  **Las actividades o vistas** &ndash; cuando se selecciona una ficha, muestra una actividad o una vista en la `FrameLayout`.

El diagrama siguiente muestra cómo todos estos componentes se relacionan entre sí:

![Diagrama que ilustra el diseño del marco en TabWidget en TabHost](tab-host-images/image03.png)

El contenido de las pestañas puede ser actividades o vistas. Vistas son relativamente ligeras y simples, pero pueden dar lugar a una gran cantidad de código relacionados con co-habitating en la actividad. El resultado será una separación deficiente de los aspectos y una clase inflan que es difícil de mantener. En cambio, actividades requieren recursos del sistema, pero permitir que un enfoque más modular con la lógica de cada pestaña encapsulada en su propia clase distintos.


## <a name="summary"></a>Resumen

En este artículo se explica los componentes de alto niveles de la versión más antigua `TabHost` API para Android y cómo se relacionan entre sí.



## <a name="related-links"></a>Vínculos relacionados

- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabHost.TabSpec](https://developer.xamarin.com/api/type/Android.Widget.TabHost+TabSpec/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Paquete de compatibilidad con Android biblioteca v7 AppCompat NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
