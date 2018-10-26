---
title: FilterUserControl
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 1cb71fa2c73b9ab151555559b22def4be1cf5c73
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112772"
---
# <a name="gridviewpager"></a>FilterUserControl

El [FilterUserControl](https://developer.xamarin.com/samples/GridViewPager/) ejemplo muestra cómo implementar el patrón de navegación selector 2D para Android Wear.

![Captura de pantalla de ejemplo de FilterUserControl en una pantalla cuadrada](gridviewpager-images/gridviewpager.png)

En primer lugar agregue el [soporte de Xamarin Android Wear](http://www.nuget.org/packages/Xamarin.Android.Wear/) paquete NuGet al proyecto.

El diseño XML este aspecto:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Crear un [`GridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(o una subclase, como [`FragmentGridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
para proporcionar vistas para mostrar que el usuario navega.

El [adaptador de ejemplo](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) se muestra cómo implementar los métodos necesarios, incluidas las invalidaciones para `RowCount`, `GetColumnCount`, `GetBackground`, y `GetFragment`

Conectar el adaptador tal como se muestra:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Vínculos relacionados

- [Documento de selector 2D de Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [Android.support.wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [FilterUserControl (ejemplo)](https://developer.xamarin.com/samples/GridViewPager/)
