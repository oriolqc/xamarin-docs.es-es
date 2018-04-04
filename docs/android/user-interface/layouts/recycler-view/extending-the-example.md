---
title: Siguiendo con el ejemplo RecyclerView
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 83147261a2d5458272f7e2bc105154da4308f4b0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="extending-the-recyclerview-example"></a>Siguiendo con el ejemplo RecyclerView


La aplicación básica se describe en [un ejemplo básico se los RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) realmente no hace mucho &ndash; simplemente se desplaza y muestra una lista fija de elementos de la fotografía para facilitar la exploración. En las aplicaciones reales, los usuarios esperan poder interactuar con la aplicación, puntee en elementos en la pantalla. Además, el origen de datos subyacente puede cambiar (o cambiarse por la aplicación) y el contenido de la pantalla debe ser coherente con estos cambios. En las siguientes secciones, obtendrá información sobre cómo controlar eventos click de elemento y actualizar `RecyclerView` cuando cambios de origen de datos subyacente.


### <a name="handling-item-click-events"></a>Control de eventos de elemento y haga clic en

Cuando un usuario toca un elemento en el `RecyclerView`, se genera un evento de elemento y haga clic en para notificar a la aplicación en cuanto a que se toca el elemento. Este evento no se genera por `RecyclerView` &ndash; en su lugar, la vista de artículo (que se incluye en el titular de la vista) detecta retoques y notifica estos últimos retoques como click (eventos).

Para ilustrar cómo controlar eventos de clic de elemento, los pasos siguientes explica cómo se modifica la aplicación básica de visualización de fotografías a informe qué fotografía tenía ha usadas por el usuario. Cuando se produce un evento de elemento y haga clic en la aplicación de ejemplo, tiene lugar la siguiente secuencia:

1.  La fotografía `CardView` detecta el evento click de elemento y notifica al adaptador.

2.  El adaptador reenvía el evento (con información de posición de elemento) al controlador de clic de elemento de la actividad.

3.  Controlador de clic de elemento de la actividad responde al evento click del elemento.

En primer lugar, llama a un miembro de controlador de evento `ItemClick` se agrega a la `PhotoAlbumAdapter` definición de clase:

```csharp
public event EventHandler<int> ItemClick;
```

A continuación, se agrega un método de controlador de eventos de elemento y haga clic en a `MainActivity`.
Este controlador muestra brevemente una notificación del sistema que indica qué elemento de la fotografía se tocadas:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

A continuación, se necesita una línea de código para registrar el `OnItemClick` controlador con `PhotoAlbumAdapter`. Es un buen lugar para hacer esto inmediatamente después de `PhotoAlbumAdapter` se crea (en la actividad principal `OnCreate` (método)):

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

`PhotoAlbumAdapter` Ahora llamará a `OnItemClick` cuando recibe un evento de elemento y haga clic en. El paso siguiente consiste en crear un controlador en el adaptador que lo genera `ItemClick` eventos. El método siguiente, `OnClick`, se agrega inmediatamente después del adaptador `ItemCount` método:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Esto `OnClick` método es el adaptador *escucha* para eventos de clic de elemento de vistas del elemento. Antes de que se puede registrar este agente de escucha con una vista de elemento (a través de titular de la vista de la vista elemento), el `PhotoViewHolder` constructor debe modificarse para que acepte este método como un argumento adicional y registrar `OnClick` con la vista artículo `Click` eventos.
Aquí es modificados `PhotoViewHolder` constructor:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

El `itemView` parámetro contiene una referencia a la `CardView` que se usadas por el usuario. Tenga en cuenta que la clase base del titular de la vista conoce la posición del diseño del elemento (`CardView`) que representa (a través de la `LayoutPosition` propiedad), y esta posición se pasa al adaptador `OnClick` método cuando tiene lugar un evento de elemento y haga clic en. El adaptador `OnCreateViewHolder` método se modifica para pasar el adaptador `OnClick` método al constructor del titular vista:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Ahora al compilar y ejecutar la aplicación de ejemplo de visualización de fotografías, puntee una foto en la pantalla provocará una notificación del sistema parece que los informes se tocadas qué fotografía:

[![Notificación de ejemplo que aparece cuando una tarjeta de fotografía que se derivan](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Este ejemplo muestra solo un enfoque para implementar los controladores de eventos con `RecyclerView`. Otro enfoque que podría ser usada aquí es colocar los eventos en el titular de la vista y tener el adaptador para suscribirse a estos eventos. Si la aplicación de fotografía de ejemplo proporciona una capacidad de edición de fotografías, eventos independientes sería necesarios para la `ImageView` y `TextView` dentro de cada `CardView`: afecta a la `TextView` iniciaría una `EditView` cuadro de diálogo que permite al usuario editar el título y en los últimos retoques el `ImageView` iniciaría una herramienta de Retocar de fotos que permite al usuario recortar o girar la fotografía. Según las necesidades de la aplicación, debe diseñar el mejor método para controlar y responder a la entrada táctil eventos.

Para demostrar cómo `RecyclerView` pueden actualizarse cuando los cambios del conjunto de datos, la aplicación de visualización de fotografías de ejemplo se pueden modificar para elegir una foto en el origen de datos al azar e intercambiar con la primera foto. En primer lugar, un **elegir aleatorio** botón se agrega a la aplicación de fotografía de ejemplo **Main.axml** diseño:

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

A continuación, se agrega código al final de la actividad principal `OnCreate` método para buscar el `Random Pick` situado en el diseño y adjuntar un controlador a él:

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

Este controlador llama el álbum de fotos `RandomSwap` método cuando el **elegir aleatorio** botón que se derivan. El `RandomSwap` método aleatoriamente intercambia una foto con la primera foto en el origen de datos, a continuación, devuelve el índice de la foto intercambian de forma aleatoria. Cuando se compila y ejecuta la aplicación de ejemplo con este código, puntee en el **elegir aleatorio** botón no produce un cambio de visualización porque el `RecyclerView` no es consciente de los cambios al origen de datos.

Para mantener `RecyclerView` actualizado después de que el origen de datos cambia, la **elegir aleatorio** haga clic en controlador debe modificarse para llamar el adaptador `NotifyItemChanged` método para cada elemento de la colección que ha cambiado (en este caso, dos elementos tienen cambiar: la primera foto y la foto intercambió). Esto hace que `RecyclerView` actualice su presentación para que sea coherente con el nuevo estado del origen de datos:

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

Ahora, cuando el **elegir aleatorio** botón que se derivan, `RecyclerView` actualiza la presentación para mostrar que una foto más abajo en la colección se han intercambiado con la primera foto en la colección:

[![Captura de pantalla de primera antes de intercambio, captura de pantalla de segundo después del intercambio](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Por supuesto, `NotifyDataSetChanged` podría llamar en lugar de realizar las dos llamadas a `NotifyItemChanged`, pero al hacerlo por lo que exigiría `RecyclerView` para actualizar toda la colección, aunque solo dos elementos de la colección ha cambiado. Al llamar a `NotifyItemChanged` es mucho más eficaz que llamar a `NotifyDataSetChanged`.


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funcionalidad y los elementos de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
