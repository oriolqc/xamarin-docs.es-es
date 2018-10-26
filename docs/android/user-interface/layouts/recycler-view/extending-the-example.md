---
title: Ampliando el ejemplo de RecyclerView
description: Agregar controladores de eventos de elemento y haga clic en la aplicación de ejemplo de RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: eca0f58a470228ce8e6331defe88c1ef727cef57
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105186"
---
# <a name="extending-the-recyclerview-example"></a>Ampliando el ejemplo de RecyclerView


La aplicación básica que se describe en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) realmente no hace gran cosa &ndash; simplemente se desplaza y muestra una lista fija de elementos de la fotografía para facilitar la exploración. En las aplicaciones del mundo real, los usuarios esperan poder interactuar con la aplicación punteando en los elementos de la pantalla. Además, el origen de datos subyacente puede cambiar (o se puede cambiar la aplicación) y el contenido de la pantalla debe ser coherente con estos cambios. En las secciones siguientes, obtendrá información sobre cómo controlar eventos click de elemento y actualizar `RecyclerView` cuando los cambios de origen de datos subyacente.


### <a name="handling-item-click-events"></a>Control de elemento y haga clic en eventos

Si un usuario toca un elemento en el `RecyclerView`, se genera un evento de elemento y haga clic para notificar a la aplicación respecto a qué elemento se tocó. No se genera este evento `RecyclerView` &ndash; en su lugar, la vista de artículo (que se incluye en el titular de la vista) detecta un toque y notifica estos dispositivos táctiles como los eventos click.

Para ilustrar cómo controlar eventos de clic de elemento, los siguientes pasos explican cómo se modifica la aplicación básica de visualización de fotografías a qué fotografía tenía se ha tocado por el usuario del informe. Cuando se produce un evento de elemento y haga clic en la aplicación de ejemplo, realiza la siguiente secuencia:

1.  La fotografía `CardView` detecta el evento click del elemento y notifica al adaptador.

2.  El adaptador reenvía el evento (que contiene información de posición de elemento) al controlador de clic de elemento de la actividad.

3.  Controlador de clic de elemento de la actividad responde al evento click del elemento.

En primer lugar, llama un miembro de controlador de evento `ItemClick` se agrega a la `PhotoAlbumAdapter` definición de clase:

```csharp
public event EventHandler<int> ItemClick;
```

A continuación, se agrega un método de controlador de eventos de elemento y haga clic en a `MainActivity`.
Este controlador muestra brevemente una notificación del sistema que indica qué elemento de la fotografía se tocó:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

A continuación, se necesita una línea de código para registrar el `OnItemClick` controlador con `PhotoAlbumAdapter`. Es un buen lugar para hacerlo inmediatamente después de `PhotoAlbumAdapter` se crea: 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

En este ejemplo básico, el registro del controlador tiene lugar en la actividad principal `OnCreate` método, pero una aplicación de producción puede registrar el controlador en `OnResume` y anular su registro en `OnPause` &ndash; consulte [ciclo de vida de actividad ](~/android/app-fundamentals/activity-lifecycle/index.md) para obtener más información.

`PhotoAlbumAdapter` Ahora se llamará `OnItemClick` cuando recibe un evento de elemento y haga clic en. El siguiente paso es crear un controlador en el adaptador que genera este `ItemClick` eventos. El método siguiente, `OnClick`, se agregará inmediatamente tras el adaptador `ItemCount` método:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Esto `OnClick` método es el adaptador *escucha* para eventos de clic de elemento de vistas del elemento. Antes de que se puede registrar este agente de escucha con una vista de elemento (a través del titular de la vista de la vista de elemento), el `PhotoViewHolder` constructor debe modificarse para que acepte este método como un argumento adicional y registrar `OnClick` con la vista artículo `Click` eventos.
Aquí es modificado `PhotoViewHolder` constructor:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

El `itemView` parámetro contiene una referencia a la `CardView` que se tocó por el usuario. Tenga en cuenta que la clase base del titular de la vista conoce la posición del elemento de diseño (`CardView`) que representa (a través de la `LayoutPosition` propiedad), y esta posición se pasa al adaptador `OnClick` método cuando se produce un evento de elemento y haga clic en. El adaptador `OnCreateViewHolder` se modifica el método para pasar el adaptador `OnClick` método al constructor del titular vista:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Ahora al compilar y ejecutar la aplicación de visualización de fotografías de ejemplo, pulsando una foto en la pantalla provocará una notificación del sistema que aparezcan que informa de qué fotografía se tocó:

[![Se pulsa la notificación del sistema de ejemplo que aparece cuando una tarjeta de foto](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Este ejemplo muestra solo un enfoque para implementar controladores de eventos con `RecyclerView`. Otro enfoque que podría ser usada aquí es colocar los eventos en el titular de la vista y tiene el adaptador Suscríbase a estos eventos. Si la aplicación de la foto de ejemplo proporciona una funcionalidad de edición de fotos, eventos independientes sería necesarios para la `ImageView` y `TextView` dentro de cada `CardView`: toca la `TextView` iniciaría una `EditView` cuadro de diálogo que permite al usuario editar el título y finales en los `ImageView` iniciaría una herramienta de Retocar fotográfica que permite al usuario recortar o girar la fotografía. Según las necesidades de su aplicación, debe diseñar el mejor método para controlar y responder a eventos de toque.

Para demostrar cómo `RecyclerView` puede actualizarse cuando los cambios del conjunto de datos, la aplicación de visualización de fotografías de ejemplo pueden modificarse para intercambiarla con la primera foto y tome una foto en el origen de datos de forma aleatoria. En primer lugar, un **elegir aleatorio** botón se agrega a la aplicación de ejemplo fotográfica **Main.axml** diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

A continuación, se agrega código al final de la actividad principal `OnCreate` método para localizar el `Random Pick` situado en el diseño y adjuntar un controlador a él:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

Este controlador llama el álbum de fotos `RandomSwap` método cuando el **elegir aleatorio** se pulsa el botón. El `RandomSwap` método aleatoriamente intercambia una foto con la primera foto en el origen de datos y, después, devuelve el índice de la foto intercambian de forma aleatoria. Al compilar y ejecutar la aplicación de ejemplo con este código, pulse el **elegir aleatorio** botón no produce un cambio de presentación porque el `RecyclerView` no es consciente del cambio en el origen de datos.

Para mantener `RecyclerView` actualizado después de que el origen de datos de los cambios, el **elegir aleatorio** haga clic en controlador debe modificarse para llamar a la del adaptador `NotifyItemChanged` método para cada elemento de la colección que ha cambiado (en este caso, dos elementos tienen puede cambiar: la primera foto y la foto se intercambiaron). Esto hace que `RecyclerView` actualice su presentación para que sea coherente con el nuevo estado del origen de datos:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

Ahora, cuando el **elegir aleatorio** se pulsa el botón, `RecyclerView` actualiza la presentación para mostrar que una foto más abajo en la colección se ha intercambiado por la primera foto en la colección:

[![Primera captura de pantalla antes del intercambio, la segunda captura de pantalla después del intercambio](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Por supuesto, `NotifyDataSetChanged` que se ha llamado en lugar de hacer las dos llamadas a `NotifyItemChanged`, pero si lo hace por lo que obligaría `RecyclerView` para actualizar toda la colección, aunque solo dos elementos de la colección ha cambiado. Una llamada a `NotifyItemChanged` es significativamente más eficaz que llamar a `NotifyDataSetChanged`.


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funcionalidad y los elementos de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
