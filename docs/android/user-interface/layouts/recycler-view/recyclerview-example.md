---
title: Un ejemplo de RecyclerView básico
description: Una aplicación de ejemplo que muestra cómo usar RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/30/2018
ms.openlocfilehash: d48796b3c62fc342bd86f2d58e74c5f1710174bb
ms.sourcegitcommit: 0a1c392829454468dbe92f81d975e124a22b7014
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2018
ms.locfileid: "39360843"
---
# <a name="a-basic-recyclerview-example"></a>Un ejemplo de RecyclerView básico

Para entender cómo `RecyclerView` funciona en una aplicación típica, este tema se analizan los [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicación de ejemplo, un ejemplo de código simple que usa `RecyclerView` para mostrar una extensa colección de fotos: 

[![Dos capturas de pantalla de una aplicación de RecyclerView que usa CardViews para mostrar fotografías](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** usa [CardView](~/android/user-interface/controls/card-view.md) para implementar cada elemento de fotografía en el `RecyclerView` diseño. Debido `RecyclerView`de ventajas de rendimiento, esta aplicación de ejemplo es capaz de desplazarse rápidamente a través de una colección de fotografías grande sin problemas y sin lugar a retrasos evidentes.


### <a name="an-example-data-source"></a>Un origen de datos de ejemplo

En esta aplicación de ejemplo, un origen de datos de "álbum de fotos" (representado por la `PhotoAlbum` clase) proporciona `RecyclerView` con el contenido del elemento.
`PhotoAlbum` es una colección de fotos con subtítulos; Cuando crea una instancia, obtener una colección de listas para usar de 32 fotos:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Cada instancia de la foto en `PhotoAlbum` expone propiedades que permiten leer su identificador de recurso de imagen, `PhotoID`y su cadena de título, `Caption`. La colección de fotos se organiza separando cada foto puede tener acceso a un indizador. Por ejemplo, las siguientes líneas de código tener acceso a los Id. de recurso de imagen y el título de la foto décima en la colección:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` También proporciona un `RandomSwap` método que se puede llamar para intercambiar la primera foto en la colección con una foto elegido aleatoriamente en otra parte de la colección:

```csharp
mPhotoAlbum.RandomSwap ();
```

Dado que los detalles de implementación `PhotoAlbum` no son relevantes para familiarizarse con `RecyclerView`, el `PhotoAlbum` código fuente no se muestra aquí. El código fuente para `PhotoAlbum` está disponible en [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) en el [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicación de ejemplo.


### <a name="layout-and-initialization"></a>Diseño e inicialización

El archivo de diseño, **Main.axml**, consta de una única `RecyclerView` dentro de un `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Tenga en cuenta que debe usar el nombre completo **android.support.v7.widget.RecyclerView** porque `RecyclerView` se empaqueta en una biblioteca de soporte técnico. El `OnCreate` método `MainActivity` inicializa este diseño, crea instancias del adaptador y prepara el origen de datos subyacente:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

Este código hace lo siguiente:

1. Crea una instancia de la `PhotoAlbum` origen de datos.

2. El origen de datos de álbum de fotos que se pasa al constructor del adaptador, `PhotoAlbumAdapter` (que se define más adelante en esta guía). 
   Tenga en cuenta que se considera una práctica recomendada para pasar el origen de datos como un parámetro al constructor del adaptador. 

3. Obtiene el `RecyclerView` del diseño.

4. Se conecta el adaptador en el `RecyclerView` instancia mediante una llamada a la `RecyclerView` `SetAdapter` método como se indicó anteriormente.

### <a name="layout-manager"></a>Administrador de diseño

Cada elemento de la `RecyclerView` está formado por un `CardView` que contiene una imagen de foto y el título de foto (detalles se tratan en el [titular de la vista](#view-holder) sección más adelante). Predefinido `LinearLayoutManager` se utiliza para diseñar cada `CardView` en una disposición de desplazamiento vertical:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Este código reside en la actividad principal `OnCreate` método. El constructor para el Administrador de diseño requiere una *contexto*, por lo que `MainActivity` se pasa mediante `this` tal como se muestra anteriormente.

En lugar de usar el el predefind `LinearLayoutManager`, puede conectar un administrador de diseño personalizado que muestra dos `CardView` elementos side-by-side, implementar un efecto de animación pasar para recorrer en iteración la colección de fotos. Más adelante en esta guía, verá un ejemplo de cómo modificar el diseño mediante el intercambio en un administrador de diseño diferente.

<a name="view-holder" />

### <a name="view-holder"></a>Titular de la vista

La clase de titular de la vista se denomina `PhotoViewHolder`. Cada `PhotoViewHolder` instancia contiene referencias a la `ImageView` y `TextView` de un elemento de fila asociados, que está colocado en un `CardView` como muestra el diagrama aquí:

[![Diagrama de CardView que contiene un ImageView y TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` se deriva de `RecyclerView.ViewHolder` y contiene las propiedades para almacenar referencias a la `ImageView` y `TextView` se muestra en el diseño anterior.
`PhotoViewHolder` consta de dos propiedades y un constructor:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```
En este ejemplo de código, el `PhotoViewHolder` constructor se pasa una referencia a la vista de elemento primario (el `CardView`) que `PhotoViewHolder` ajusta. Tenga en cuenta que, siempre reenviar al elemento primario elemento view al constructor base. El `PhotoViewHolder` llamadas de constructor `FindViewById` en la vista de elemento primario para cada uno de sus referencias de vista secundarios, buscar `ImageView` y `TextView`, almacene los resultados en el `Image` y `Caption` propiedades, respectivamente. El adaptador recupera más adelante ver referencias de estas propiedades cuando actualiza esta `CardView`de vistas secundarias con nuevos datos.

Para obtener más información acerca de `RecyclerView.ViewHolder`, consulte el [referencia de clase RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Adaptador

El adaptador carga cada `RecyclerView` fila con datos de una fotografía determinada. Para una fotografía determinada en la posición de fila *P*, por ejemplo, el adaptador busca los datos asociados en la posición *P* dentro del origen de datos y copias de estos datos a la fila de elementos en la posición *P* en el `RecyclerView` colección. El adaptador utiliza el titular de la vista para buscar las referencias para el `ImageView` y `TextView` en esa posición, por lo que no tiene que llamar repetidamente a `FindViewById` para las vistas cuando el usuario se desplaza por la colección de fotografía y vuelve a utilizar las vistas.

En **RecyclerViewer**, se deriva una clase de adaptador `RecyclerView.Adapter` crear `PhotoAlbumAdapter`:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

El `mPhotoAlbum` miembro contiene el origen de datos (el álbum de fotos) que se pasó al constructor; el constructor copia el álbum de fotos en esta variable de miembro. Los siguientes requeridos `RecyclerView.Adapter` se implementan los métodos:

-   **`OnCreateViewHolder`** &ndash; Crea una instancia el titular de archivo y la vista de diseño de elemento.

-   **`OnBindViewHolder`** &ndash; Carga los datos en la posición especificada en las vistas cuyas referencias se almacenan en el titular de la vista especificada.

-   **`ItemCount`** &ndash; Devuelve el número de elementos del origen de datos.

El Administrador de diseño llama a estos métodos mientras coloca elementos dentro de la `RecyclerView`. Se examina la implementación de estos métodos en las secciones siguientes.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

El Administrador de diseño llama a `OnCreateViewHolder` cuando el `RecyclerView` necesita un nuevo titular de la vista para representar un elemento. `OnCreateViewHolder` aumenta la vista de elemento de archivo de la vista Diseño y el ajusta la vista en un nuevo `PhotoViewHolder` instancia. El `PhotoViewHolder` constructor busca y almacena las referencias a vistas secundarias en el diseño, como se describió anteriormente en [titular de la vista](#view-holder).

Cada elemento de la fila se representa mediante un `CardView` que contiene un `ImageView` (de la foto) y un `TextView` (para el título). Este diseño se encuentra en el archivo **PhotoCardView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

Este diseño representa un elemento de fila única en el `RecyclerView`. El `OnBindViewHolder` método (descrito a continuación) copia los datos del origen de datos en el `ImageView` y `TextView` de este diseño.
`OnCreateViewHolder` aumenta este diseño para una ubicación de la foto determinada en el `RecyclerView` y crea una instancia de un nuevo `PhotoViewHolder` instancia (que busca y se almacena en caché las referencias a la `ImageView` y `TextView` vistas secundarias en el asociado `CardView` diseño):

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

La instancia del titular de la vista resultante, `vh`, se devuelve al llamador (el Administrador de diseño).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Cuándo está listo para mostrar una vista concreta en el Administrador de diseño la `RecyclerView`del área visible de la pantalla, lo llama el adaptador `OnBindViewHolder` método para rellenar el elemento situado en la posición de la fila especificada con el contenido del origen de datos. `OnBindViewHolder` Obtiene la información de la fotografía para la posición de fila especificado (recurso de imagen de la fotografía y la cadena de título de la foto) y copiará estos datos a las vistas asociadas. Las vistas se encuentran a través de las referencias almacenadas en el objeto de titular de la vista (que se pasa a través de la `holder` parámetro):

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

El objeto de titular de la vista pasado debe convertirse en el tipo de titular de la vista derivada (en este caso, `PhotoViewHolder`) antes de utilizarse.
El adaptador carga el recurso de imagen en la vista que hace referencia el titular de la vista `Image` propiedad y se copia el texto del título en la vista que hace referencia el titular de la vista `Caption` propiedad. Esto *enlaza* la vista asociada con sus datos.

Tenga en cuenta que `OnBindViewHolder` es el código que se ocupa directamente de la estructura de los datos. En este caso, `OnBindViewHolder` entiende cómo asignar el `RecyclerView` posición a su elemento de datos asociados en el origen de datos de elemento. La asignación es sencilla en este caso porque la posición se puede usar como un índice de matriz en el álbum de fotos; Sin embargo, los orígenes de datos más complejos pueden requerir código adicional para establecer este tipo de asignación.


#### <a name="itemcount"></a>ItemCount

El `ItemCount` método devuelve el número de elementos en la recopilación de datos. En la aplicación de Visor de fotos de ejemplo, el número de elementos es el número de fotografías del álbum de fotografías:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Para obtener más información acerca de `RecyclerView.Adapter`, consulte el [referencia de clase RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Colocación de todos los elementos

Resultante `RecyclerView` implementación para la aplicación de la foto de ejemplo consta de `MainActivity` código que crea el origen de datos, Administrador de diseño y el adaptador. `MainActivity` crea el `mRecyclerView` instancia, crea una instancia de origen de datos y el adaptador y se conecta el Administrador de diseño y el adaptador:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder` Busca y almacena en caché las referencias de vista:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter` implementa los reemplazos de método requiere tres:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

Cuando este código se compila y ejecuta, crea la foto básica ver aplicación tal como se muestra en las capturas de pantalla siguiente:

[![Dos capturas de pantalla de visualización de la aplicación con tarjetas de fotos que se desplaza verticalmente fotografías](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Si no se va a dibujar sombras (tal y como se muestra en la captura de pantalla anterior), edite **Properties/Androidmanifest.XML** y agregue el valor de atributo siguiente a la `<application>` elemento:

```xml
android:hardwareAccelerated="true"
```

Esta aplicación básica solo admite la exploración de álbum de fotos. No responde para eventos de toque de elemento, ni maneja los cambios en los datos subyacentes. Esta funcionalidad se agrega en [ampliando el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).




### <a name="changing-the-layoutmanager"></a>Cambiar el LayoutManager

Debido `RecyclerView`de flexibilidad, es fácil modificar la aplicación para usar un administrador de diseño diferentes. En el ejemplo siguiente, se modifica para mostrar el álbum de fotos con un diseño de cuadrícula que se desplaza horizontalmente en lugar de con un diseño vertical lineal. Para ello, se modifica la creación de instancias del Administrador de diseño para usar el `GridLayoutManager` como sigue:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Este cambio de código reemplaza la vertical `LinearLayoutManager` con un `GridLayoutManager` que presenta una cuadrícula formada por dos filas Desplácese en dirección horizontal. Cuando se compila y se vuelva a ejecutar la aplicación, verá que las fotografías se muestran en una cuadrícula y que el desplazamiento horizontal en lugar de vertical:

[![Captura de pantalla de ejemplo de aplicación con fotografías de desplazamiento horizontal en una cuadrícula](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Cambiando únicamente una línea de código es es posible modificar la aplicación de visualización de fotografías para usar un diseño diferente con un comportamiento diferente.
Tenga en cuenta que el código del adaptador ni el diseño XML tenía que modificarse para cambiar el estilo de diseño. 

En el tema siguiente, [ampliando el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), esta aplicación de ejemplo básico se ha ampliado para controlar eventos click de elemento y actualizar `RecyclerView` cuando los cambios de origen de datos subyacente.



## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funcionalidad y los elementos de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Ampliando el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
