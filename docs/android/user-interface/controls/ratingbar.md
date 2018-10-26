---
title: RatingBar
description: Cómo agregar un widget RatingBar a una actividad de Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 97d2a126be70e210d2e8f4ebf4d7a25ff8777a02
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131858"
---
# <a name="ratingbar"></a>RatingBar

Un RatingBar es un widget de interfaz de usuario que muestra una clasificación de uno a cinco estrellas. El usuario puede seleccionar una clasificación punteando en una estrella en esta sección, creará un widget que permite al usuario proporcionar una clasificación, con el [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) widget.

![Ejemplo de un RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>Creación de un RatingBar

1. Abra el **Resource/layout/Main.axml** y agréguele el [`RatingBar`](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)
   elemento (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   El `android:numStars` atributo define cuántas estrellas para mostrar de la barra de clasificación. El `android:stepSize` atributo define la granularidad de cada estrella (por ejemplo, un valor de `0.5` permitirían clasificación por estrellas de la mitad).

2. Para hacer algo cuando se ha establecido un nuevo objeto rating, agregue el código siguiente al final de la [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
   método:

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Esta forma se capturan el [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) widget del diseño con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/) y, a continuación, establece un método de evento, después, define la acción que se realizará cuando el usuario establece una clasificación. En este caso, un simple [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensaje muestra el nuevo objeto rating.

3.  Ejecute la aplicación.

