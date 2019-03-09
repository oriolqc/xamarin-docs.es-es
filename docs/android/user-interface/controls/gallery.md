---
title: Galería
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f9b73428531deeacc7bdea271cdc0c2872038e99
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666964"
---
# <a name="gallery"></a>Galería

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) es un widget de diseño que se usa para mostrar elementos en una lista de desplazamiento horizontal y coloca la selección actual en el centro de la vista.

> [!IMPORTANT]
> Este widget ha quedado obsoleta en Android 4.1 (nivel de API 16). 

En este tutorial, creará una galería de fotos y, a continuación, mostrar un mensaje de notificación cada vez que se selecciona un elemento de galería.

Después de la `Main.axml` diseño está establecido para la vista de contenido, el `Gallery` se captura desde el diseño con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
El [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
propiedad, a continuación, se usa para establecer un adaptador personalizado ( `ImageAdapter`) como origen para todos los elementos que se mostrará en el dallery. El `ImageAdapter` se crea en el paso siguiente.

Para hacer algo cuando se hace clic en un elemento en la galería, un delegado anónimo está suscrita a la [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
. Muestra una [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
que muestra la posición de índice (basado en cero) de los elementos seleccionados (en un escenario real, la posición podría usarse para obtener la imagen en tamaño completo de alguna otra tarea).

En primer lugar, hay algunas variables de miembro, incluida una matriz de identificadores que hacen referencia a las imágenes guardadas en el directorio de recursos drawable (**recursos/drawable**).

A continuación es el constructor de clase, donde el [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
para un `ImageAdapter` instancia se define y se guardan en un campo local.
A continuación, implementa algunos métodos necesarios se hereda de [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).
El constructor y el [`Count`](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)
propiedad son autoexplicativos. Normalmente, [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)
debe devolver el objeto real en la posición especificada en el adaptador, pero se omite en este ejemplo. Del mismo modo, [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)
debe devolver el identificador de fila del elemento, pero no es necesario aquí.

El método realiza el trabajo para aplicar una imagen a un [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
que se incrustarán en el [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
En este método, el miembro [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
se utiliza para crear un nuevo [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).
El [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
se prepara mediante la aplicación de una imagen de la matriz local de recursos drawable, establecer el [`Gallery.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)
alto y ancho de la imagen, establecer la escala para ajustarse a la [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
dimensiones y, a continuación, por último, establecer el fondo para utilizar el atributo pueden diseñar adquirido en el constructor.

Consulte [ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/) para otra opciones de escalado de imagen.

## <a name="walkthrough"></a>Tutorial

Inicie un nuevo proyecto denominado *HelloGallery*.

[![Captura de pantalla del nuevo proyecto de Android en el cuadro de diálogo nueva solución](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Buscar algunas fotos que le gustaría utilizar, o [descargar estas imágenes de ejemplo](https://developer.android.com/shareables/sample_images.zip).
Agregue los archivos de imagen al proyecto **recursos/Drawable** directory. En el **propiedades** ventana, establezca la acción de compilación para cada uno al **AndroidResource**.

Abra **Resources/Layout/Main.axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Abra `MainActivity.cs` e inserte el código siguiente para el [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Crear una nueva clase denominada `ImageAdapter` que cree subclases [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

    public override Java.Lang.Object GetItem (int position)
    {
          return null;
    }

    public override long GetItemId (int position)
    {
          return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

Ejecute la aplicación. Debe parecerse a la siguiente captura de pantalla:

![Captura de pantalla de HelloGallery mostrar imágenes de ejemplo](gallery-images/hellogallery3.png)



## <a name="references"></a>Referencias

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).


