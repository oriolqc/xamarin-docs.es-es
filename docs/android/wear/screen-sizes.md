---
title: Trabajar con tamaños de pantalla en el SO de Xamarin.Android y Wear
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 9fc22a3c08b60a8474b006f1c9225155b9705507
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113123"
---
# <a name="working-with-screen-sizes"></a>Trabajar con tamaños de pantalla

Los dispositivos Android Wear pueden tener un rectangular o una presentación de redondeo, que también puede ser diferentes tamaños.

![Muestra las capturas de pantalla de desgaste rectangular y redondeo](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Que identifica el tipo de pantalla

La biblioteca de compatibilidad de desgaste proporciona algunos controles que le ayudan a detectan y adaptan a las formas de pantalla diferentes, tales como `WatchViewStub` y `BoxInsetLayout`.

Tenga en cuenta que algunas de las otras admiten controles de la biblioteca (por ejemplo, `GridViewPager`) *automáticamente* detectar de forma de pantalla a sí mismos y no debe agregarse como elementos secundarios de los controles se describen a continuación.

### <a name="watchviewstub"></a>WatchViewStub

Consulte la [WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/) ejemplo para ver cómo detectar el tipo de pantalla y mostrar un diseño diferente para cada tipo.

El archivo de diseño principal contiene un `android.support.wearable.view.WatchViewStub` que hace referencia a diferentes diseños de pantallas rectangulares y redondear mediante el `app:rectLayout` y `app:roundLayout` atributos:

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

En lugar de crear diseños diferentes para cada tipo de pantalla, también puede crear una vista única que se adapta a las pantallas redondeadas o rectangulares.

Esto [ejemplo Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) se muestra cómo usar el `BoxInsetLayout` para usar el mismo diseño de pantallas rectangulares y round.


## <a name="wear-ui-designer"></a>Use el Diseñador de interfaz de usuario

El Diseñador de Android de Xamarin es compatible con pantallas rectangulares y redondeo:

![Selección de la pantalla de Android Wear cuadrado en el Diseñador de Android de Xamarin](screen-sizes-images/design-screen-type.png)

Aquí se muestra la superficie de diseño rectangular estilo:

![Superficie de diseño en estilo rectangular](screen-sizes-images/design-rect.png) 

La superficie de diseño en el estilo de redondeo se muestra aquí:

![Superficie de diseño en el estilo de redondeo](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Usar el simulador

El **Google Emulator Manager** contiene las definiciones de dispositivos para ambos tipos de pantalla. Puede crear emuladores rectangulares y round para probar la aplicación.

![Use las definiciones de dispositivos que se muestra en el Administrador de emuladores de Google](screen-sizes-images/emulator-devices.png)

El emulador se representará de forma similar al siguiente para una pantalla rectangular:

![Emulador de la representación de una pantalla rectangular](screen-sizes-images/recipe-2.png) 

Se representará de forma similar al siguiente para una pantalla redonda:

![Emulador de la representación de una pantalla redonda](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Vídeo

[Aplicaciones de pantalla completa para Android Wear](https://www.youtube.com/watch?v=naf_WbtFAlY) desde [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

