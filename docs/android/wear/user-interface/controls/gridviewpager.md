---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 12e7e31cda9818a3cb2e2efc331a0be5d0c334e5
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740853"
---
# <a name="gridviewpager"></a>GridViewPager

El [FilterUserControl](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) ejemplo muestra cómo implementar el patrón de navegación selector 2D para Android Wear.

![Captura de pantalla de ejemplo de FilterUserControl en una pantalla cuadrada](gridviewpager-images/gridviewpager.png)

En primer lugar agregue el [soporte de Xamarin Android Wear](https://www.nuget.org/packages/Xamarin.Android.Wear/) paquete NuGet al proyecto.

El diseño XML este aspecto:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Crear un [`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(o una subclase, como [`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
para proporcionar vistas para mostrar que el usuario navega.

El [adaptador de ejemplo](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) se muestra cómo implementar los métodos necesarios, incluidas las invalidaciones para `RowCount`, `GetColumnCount`, `GetBackground`, y `GetFragment`

Conectar el adaptador tal como se muestra:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Vínculos relacionados

- [Documento de selector 2D de Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [Android.support.wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (sample)](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)
