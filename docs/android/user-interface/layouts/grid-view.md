---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 82c82de912fd253d45e6343e2dd1c50e389c6371
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) es un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que muestra elementos en una cuadrícula desplazable bidimensional. Los elementos de la cuadrícula se insertarán automáticamente en el diseño mediante un [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).

En este tutorial, creará una cuadrícula de imágenes en miniatura. Cuando se selecciona un elemento, un mensaje de notificación del sistema mostrará la posición de la imagen.

Inicie un nuevo proyecto denominado **HelloGridView**.

Encontrar algunas fotos que le gustaría utilizar, o [descargar estas imágenes de ejemplo](http://developer.android.com/shareables/sample_images.zip). Agregar los archivos de imagen al proyecto **recursos/Drawable** directory. En el **propiedades** ventana, establezca la acción de compilación para cada una de ellas **AndroidResource**.

Abra la **Resources/Layout/Main.axml** archivo e inserte lo siguiente:

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

Esto [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) acabe con toda la pantalla. Los atributos son bastante fácil de entender. Para obtener más información sobre atributos válidos, consulte el [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) referencia.

Abra `HelloGridView.cs` e inserte el siguiente código para el [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

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

Después de la **Main.axml** diseño está establecido para la vista de contenido, el [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) se captura desde el diseño con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). El [ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/) propiedad, a continuación, se usa para establecer un adaptador personalizado (`ImageAdapter`) como origen para todos los elementos que se mostrarán en la cuadrícula. El `ImageAdapter` se crea en el paso siguiente.

Para hacer algo cuando se hace clic en un elemento de la cuadrícula, un delegado anónimo está suscrito a la [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) eventos.
Muestra un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) que muestra la posición de índice (basado en cero) del elemento seleccionado (en un escenario real, la posición se podría usar para obtener la imagen con tamaño completa otra tarea). Tenga en cuenta que se pueden usar las clases de agente de escucha de estilo de Java en lugar de los eventos de .NET.

Crear una nueva clase denominada `ImageAdapter` que subclasifique [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

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

En primer lugar, se implementa algunos métodos necesarios se hereda de [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/). El constructor y el [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) propiedad son autoexplicativos. Normalmente, [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/) debe devolver el objeto real en la posición especificada en el adaptador, pero se omite en este ejemplo. Del mismo modo, [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/) debe devolver el identificador de fila del elemento, pero no es necesario aquí.

El primer método es necesario es [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/).
Este método crea un nuevo [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) para cada imagen agregada a la `ImageAdapter`. Cuando se llama a esto, un [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) se pasa en, que normalmente es un objeto reciclando (al menos una vez se ha llamado una vez), por lo que hay una comprobación para ver si el objeto es null. Si se *es* es null, un [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) se crea y se configura con propiedades que desee para la presentación de imagen:

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) establece el alto y ancho de la vista&mdash;Esto garantiza que, con independencia del tamaño de la puede dibujar, cada imagen se cambia el tamaño y recorta para ajustarse de estas dimensiones, según corresponda.

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) declara que se deben recortar imágenes hacia el centro (si es necesario).

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) define el relleno para todos los lados. (Tenga en cuenta que, si las imágenes tienen relaciones de aspecto diferentes, a continuación, menos relleno provocará para recortar más de la imagen si no coincide con las dimensiones indicadas en la ImageView.)

Si el [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) pasado a [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) es *no* es null, a continuación, en el equipo local [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) se inicializa con el reciclado [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) objeto.

Al final de la [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) método, el `position` entero que se pasa en el método se utiliza para seleccionar una imagen de la `thumbIds` matriz, que se establece como el recurso de imagen para la [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

Lo único que queda es definir el `thumbIds` matriz de recursos con estas características.

Ejecute la aplicación. El diseño de cuadrícula debe tener un aspecto similar al siguiente:

[![Captura de pantalla de ejemplo de GridView mostrando 15 imágenes](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Experimente con los comportamientos de la [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) y [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) elementos mediante el ajuste de sus propiedades. Por ejemplo, en lugar de usar [ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) pruebe a usar [ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/).


## <a name="references"></a>Referencias

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).

*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
