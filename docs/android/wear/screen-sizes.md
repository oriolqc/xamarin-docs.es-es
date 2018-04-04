---
title: Trabajar con tamaños de pantalla
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 12272b6db10249fb3750edd853ebb01a99586427
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-screen-sizes"></a>Trabajar con tamaños de pantalla

Los dispositivos Android de desgaste pueden tener un rectangular o una presentación de redondeo, que también puede ser diferentes tamaños.

![Capturas de pantalla de desgaste rectangular y redondear muestra](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Identifica el tipo de pantalla

La biblioteca de compatibilidad de desgaste proporciona algunos controles que ayudan a detectan y adaptan a las formas de pantalla diferentes, como `WatchViewStub` y `BoxInsetLayout`.

Tenga en cuenta que algunos de los otros admiten controles de la biblioteca (como `GridViewPager`) *automáticamente* detectar de forma de pantalla por sí mismos y no debe agregarse como elementos secundarios de los controles describen a continuación.

### <a name="watchviewstub"></a>WatchViewStub

Consulte la [WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/) ejemplo para ver cómo detectar el tipo de pantalla y mostrar un diseño diferente para cada tipo.

El archivo de diseño principal contiene un `android.support.wearable.view.WatchViewStub` que hace referencia a diseños diferentes para las pantallas rectangulares y redondear mediante la `app:rectLayout` y `app:roundLayout` atributos:

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

La solución contiene diseños diferentes para cada estilo que se seleccionará en tiempo de ejecución:

![Archivos que se muestran en los recursos y diseño](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

En lugar de crear diseños diferentes para cada tipo de pantalla, también puede crear una vista única que se adapta a las pantallas rectangulares o redonda.

Esto [ejemplo Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) muestra cómo utilizar el `BoxInsetLayout` para utilizar el mismo diseño en pantallas rectangulares y redondear.


## <a name="wear-ui-designer"></a>Desgaste del Diseñador de la interfaz de usuario

El Diseñador de Android en Xamarin es compatible con pantallas rectangulares y redondear:

![Seleccione la pantalla de Android desgaste cuadrado en el Diseñador de Android de Xamarin](screen-sizes-images/design-screen-type.png)

La superficie de diseño en estilo rectangular se muestra aquí:

![Superficie de diseño en estilo rectangular](screen-sizes-images/design-rect.png) 

La superficie de diseño en estilo round se muestra aquí:

![Superficie de diseño en estilo round](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Desgaste del simulador

El **administrador del emulador de Google** contiene las definiciones de dispositivo para ambos tipos de pantalla. Puede crear rectangulares y redondear emuladores para probar la aplicación.

![Usan las definiciones de dispositivo que se muestra en el administrador del emulador de Google](screen-sizes-images/emulator-devices.png)

Para una pantalla rectangular, el emulador se representará similar al siguiente:

![Emulador de representación de una pantalla rectangular](screen-sizes-images/recipe-2.png) 

Se representarán como esta para una pantalla round:

![Emulador de representación de una pantalla de redondeo](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Vídeo

[Aplicaciones de pantalla completa para Android desgaste](https://www.youtube.com/watch?v=naf_WbtFAlY) de [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

