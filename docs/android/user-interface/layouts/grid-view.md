---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 63164d90419f3a49d9eb52a52d02e05fbee43dbf
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667625"
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) es un [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
Muestra elementos en una cuadrícula desplazable bidimensional. Los elementos de cuadrícula se insertan automáticamente en el diseño mediante un [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).

En este tutorial, creará una cuadrícula de imágenes en miniatura. Cuando se selecciona un elemento, un mensaje de notificación mostrará la posición de la imagen.

Inicie un nuevo proyecto denominado **HelloGridView**.

Buscar algunas fotos que le gustaría utilizar, o [descargar estas imágenes de ejemplo](https://developer.android.com/shareables/sample_images.zip). Agregue los archivos de imagen al proyecto **recursos/Drawable** directory. En el **propiedades** ventana, establezca la acción de compilación para cada uno al **AndroidResource**.

Abra el **Resources/Layout/Main.axml** archivo e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

Esto [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) rellenará toda la pantalla. Los atributos son bastante fácil de entender. Para obtener más información sobre atributos válidos, vea el [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) referencia.

Abra `HelloGridView.cs` e inserte el código siguiente para el [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Después de la **Main.axml** diseño está establecido para la vista de contenido, el [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) se captura desde el diseño con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). El [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
propiedad, a continuación, se usa para establecer un adaptador personalizado (`ImageAdapter`) como origen para todos los elementos que se mostrará en la cuadrícula. El `ImageAdapter` se crea en el paso siguiente.

Para hacer algo cuando se hace clic en un elemento de la cuadrícula, un delegado anónimo está suscrita a la [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) eventos.
Muestra un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) que muestra la posición de índice (basado en cero) del elemento seleccionado (en un escenario real, la posición podría usarse para obtener la imagen en tamaño completo de alguna otra tarea). Tenga en cuenta que las clases de agente de escucha de estilo Java pueden usarse en lugar de los eventos. NET.

Crear una nueva clase denominada `ImageAdapter` que cree subclases [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

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
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

En primer lugar, implementa algunos métodos necesarios se hereda de [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/). El constructor y el [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) propiedad son autoexplicativos. Normalmente, [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/)
debe devolver el objeto real en la posición especificada en el adaptador, pero se omite en este ejemplo. Del mismo modo, [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/)
debe devolver el identificador de fila del elemento, pero no es necesario aquí.

Es el primer método es necesario [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/).
Este método crea un nuevo [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
para cada imagen que se agrega a la `ImageAdapter`. Cuando esto se llama a un [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
se pasa en, que normalmente es un objeto reciclando (al menos una vez que se ha llamado una vez), por lo que hay una comprobación para ver si el objeto es null. Si lo *es* es null, un [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
se crea y se configura con las propiedades deseadas para la presentación de imagen:

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) establece el alto y ancho de la vista&mdash;Esto garantiza que, independientemente del tamaño de la drawable, cada imagen cambia de tamaño y recorta para ajustarse a estas dimensiones, según corresponda.

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) declara que se deben recortar imágenes hacia el centro (si es necesario).

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) define el relleno para todos los lados. (Tenga en cuenta que, si las imágenes tienen relaciones de aspecto diferentes, a continuación, menos relleno provocará para recortar más de la imagen si no coincide con las dimensiones indicadas en la ImageView.)

Si el [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) pasado a [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) es *no* es null, a continuación, en el equipo local [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
se inicializa con el reciclado [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) objeto.

Al final de la [`GetView()`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)
método, el `position` entero que se pasa al método se utiliza para seleccionar una imagen desde el `thumbIds` matriz, que se establece como el recurso de imagen para el [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

Lo único que queda es definir el `thumbIds` matriz de recursos drawable.

Ejecute la aplicación. El diseño de cuadrícula debe ser algo parecido a esto:

[![Captura de pantalla de ejemplo de GridView mostrando 15 imágenes](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Experimente con los comportamientos de la [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) y [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
elementos ajustando sus propiedades. Por ejemplo, en lugar de usar [ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) intente usar [ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/).


## <a name="references"></a>Referencias

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).

*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
