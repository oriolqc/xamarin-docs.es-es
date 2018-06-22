---
title: FilterUserControl
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 3a0b1ec9359b1c6067c253b4d04126dbdd726cc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763441"
---
# <a name="gridviewpager"></a>FilterUserControl

El [FilterUserControl](https://developer.xamarin.com/samples/GridViewPager/) ejemplo muestra cómo implementar el modelo de navegación de selector 2D para desgaste Android.

![Captura de pantalla de ejemplo de FilterUserControl en una pantalla de cuadrado](gridviewpager-images/gridviewpager.png)

Agregue primero la [Xamarin Android desgaste admiten](http://www.nuget.org/packages/Xamarin.Android.Wear/) paquete NuGet para el proyecto.

El diseño no XML es similar al siguiente:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Crear un [ `GridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html) (o una subclase como [ `FragmentGridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html) para proporcionar vistas para mostrar como el usuario se desplaza.

El [adaptador de ejemplo](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) muestra cómo implementar los métodos necesarios, incluidas las invalidaciones para `RowCount`, `GetColumnCount`, `GetBackground`, y `GetFragment`

Conecte el adaptador tal como se muestra:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Vínculos relacionados

- [Documento de selector 2D de Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [Android.support.wearable documentos](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [FilterUserControl (ejemplo)](https://developer.xamarin.com/samples/GridViewPager/)
