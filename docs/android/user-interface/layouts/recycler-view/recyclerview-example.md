---
title: Un ejemplo de RecyclerView básico
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d5be838dcb5530ece76c3701d8fce10403622e8d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770617"
---
# <a name="a-basic-recyclerview-example"></a>Un ejemplo de RecyclerView básico


Para entender cómo `RecyclerView` funciona en una aplicación típica, este tema se exploran el [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicación de ejemplo, un ejemplo de código sencillo que utiliza `RecyclerView` para mostrar una extensa colección de fotos: 

[![Dos capturas de pantalla de una aplicación de RecyclerView que usa CardViews para mostrar fotografías](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** utiliza [CardView](~/android/user-interface/controls/card-view.md) para implementar cada elemento de la fotografía en la `RecyclerView` diseño. Debido `RecyclerView`de ventajas de rendimiento, esta aplicación de ejemplo es capaz de desplazarse rápidamente a través de una colección de fotografías grande de forma sencilla y sin retrasos evidentes.


### <a name="an-example-data-source"></a>Un origen de datos de ejemplo

En esta aplicación de ejemplo, un origen de datos de "álbum de fotos" (representado por la `PhotoAlbum` clase) proporciona `RecyclerView` con el contenido del elemento.
`PhotoAlbum` es una colección de fotos con subtítulos; Cuando se crea una instancia, obtendrá una colección de 32 fotos listos para su uso:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Cada instancia de la foto en `PhotoAlbum` expone propiedades que permiten leer su identificador de recurso de imagen, `PhotoID`y su cadena de título, `Caption`. La colección de fotos está organizada de manera que un indizador que se puede tener acceso a cada fotografía. Por ejemplo, las siguientes líneas de código tener acceso a los Id. de recurso de imagen y el título de la foto décima en la colección:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` También proporciona un `RandomSwap` método que puede llamar para intercambiar la primera foto en la colección con una foto elegidos al azar en otra parte de la colección:

```csharp
mPhotoAlbum.RandomSwap ();
```

Dado que los detalles de implementación `PhotoAlbum` no son relevantes para familiarizarse con `RecyclerView`, el `PhotoAlbum` es que el código fuente no se mencionan aquí. El código fuente `PhotoAlbum` está disponible en [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) en el [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicación de ejemplo.


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

Tenga en cuenta que debe usar el nombre completo **android.support.v7.widget.RecyclerView** porque `RecyclerView` se empaqueta en una biblioteca de compatibilidad. El `OnCreate` método `MainActivity` inicializa este diseño, crea instancias del adaptador y prepara el origen de datos subyacente:

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
   Tenga en cuenta que se considera una práctica recomendada de pasar el origen de datos como un parámetro al constructor del adaptador. 

3. Obtiene el `RecyclerView` del diseño.

4. Se conecta el adaptador en el `RecyclerView` instancia mediante una llamada a la `RecyclerView` `SetAdapter` método como se indicó anteriormente.

### <a name="layout-manager"></a>Administrador de diseño

Cada elemento de la `RecyclerView` está formada por un `CardView` que contiene una imagen de la foto y un título de foto (detalles se tratan en la [titular de la vista](#view-holder) sección más adelante). Predefinido `LinearLayoutManager` se utiliza para mostrar cada uno de ellos `CardView` en una organización de desplazamiento vertical:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Este código reside en la actividad principal `OnCreate` método. El constructor para el Administrador de diseño requiere un *contexto*, por lo que la `MainActivity` se pasa mediante `this` tal como se muestra anteriormente.

En lugar de usar la la predefind `LinearLayoutManager`, puede conectar un administrador de diseño personalizado que muestra dos `CardView` elementos side-by-side, implementar un efecto de animación de pasar página cuando para recorrer en iteración la colección de fotografías. Más adelante en esta guía, verá un ejemplo de cómo modificar el diseño mediante el intercambio en un administrador de diseño diferente.

<a name="view-holder" />

### <a name="view-holder"></a>Titular de la vista

La clase de titular de la vista se denomina `PhotoViewHolder`. Cada `PhotoViewHolder` instancia contiene referencias a la `ImageView` y `TextView` de un elemento de la fila asociada, que se encuentra en un `CardView` como diagrama aquí:

[![Diagrama de CardView que contiene un ImageView y TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` se deriva de `RecyclerView.ViewHolder` y contiene propiedades para almacenar referencias a la `ImageView` y `TextView` se muestra en el diseño anterior.
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
En este ejemplo de código, el `PhotoViewHolder` constructor se pasa una referencia a la vista de elemento primario (el `CardView`) que `PhotoViewHolder` ajusta. Tenga en cuenta que, siempre reenviar al elemento primario vista artículo al constructor base. El `PhotoViewHolder` llamadas de constructor `FindViewById` en la vista de elemento primario para ubicar cada uno de sus referencias de vista secundario, `ImageView` y `TextView`, almacenando los resultados en la `Image` y `Caption` propiedades, respectivamente. El adaptador recupera más adelante ver referencias de estas propiedades cuando actualiza esta `CardView`de vistas secundarias con nuevos datos.

Para obtener más información acerca de `RecyclerView.ViewHolder`, consulte el [referencia de clase RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Adaptador

El adaptador carga cada uno `RecyclerView` fila con datos de una fotografía concreta. Para una fotografía determinada en la posición de fila *P*, por ejemplo, el adaptador busca los datos asociados en la posición *P* dentro del origen de datos y copias de estos datos a la fila de elemento en la posición *P* en el `RecyclerView` colección. El adaptador utiliza el titular de la vista para buscar las referencias para el `ImageView` y `TextView` en esa posición, por lo que no tiene que llamar repetidamente a `FindViewById` para las vistas cuando el usuario se desplaza a través de la colección de fotografía y vuelve a utilizar vistas.

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

El `mPhotoAlbum` miembro contiene el origen de datos (el álbum de fotos) que se haya pasado al constructor; el constructor copia el álbum de fotos a esta variable miembro. Los siguientes requeridos `RecyclerView.Adapter` se implementan métodos:

-   **`OnCreateViewHolder`** &ndash; Crea una instancia el titular de archivo y la vista de diseño de elemento.

-   **`OnBindViewHolder`** &ndash; Carga los datos en la posición especificada en las vistas cuyas referencias se almacenan en el titular de la vista especificada.

-   **`ItemCount`** &ndash; Devuelve el número de elementos del origen de datos.

El Administrador de diseño llama a estos métodos mientras es colocar elementos dentro de la `RecyclerView`. En las secciones siguientes, se examina la implementación de estos métodos.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Las llamadas del Administrador de diseño `OnCreateViewHolder` cuando el `RecyclerView` necesita un nuevo titular de la vista para representar un elemento. `OnCreateViewHolder` aumenta la vista de elemento de archivo de la vista Diseño y ajusta la vista en una nueva `PhotoViewHolder` instancia. El `PhotoViewHolder` constructor busca y almacena las referencias a vistas secundarias en el diseño, como se describió anteriormente en [titular de la vista](#view-holder).

Cada elemento de fila se representa mediante un `CardView` que contiene un `ImageView` (para la foto) y un `TextView` (para el título). Este diseño se encuentra en el archivo **PhotoCardView.axml**:

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

Este diseño representa un elemento de fila única en la `RecyclerView`. El `OnBindViewHolder` método (descrito a continuación) copia los datos del origen de datos en el `ImageView` y `TextView` de este diseño.
`OnCreateViewHolder` aumenta este diseño para una ubicación de la fotografía determinada en el `RecyclerView` y crea una instancia de un nuevo `PhotoViewHolder` instancia (que busca y se almacena en caché las referencias a la `ImageView` y `TextView` vistas secundarias en el asociado `CardView` diseño):

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

Cuando está listo para mostrar una vista concreta en el Administrador de diseño la `RecyclerView`del área visible de la pantalla, llama el adaptador `OnBindViewHolder` método para rellenar el elemento situado en la posición de la fila especificada con el contenido del origen de datos. `OnBindViewHolder` Obtiene la información de la fotografía para la posición de la fila especificada (recurso de imagen de la foto y la cadena de título de la foto) y copiará estos datos a las vistas asociadas. Las vistas se encuentran a través de referencias almacenadas en el objeto de contenedor de vista (que se pasa a través de la `holder` parámetro):

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

El objeto de marcador de pasa en la vista en primer lugar debe convertirse en el tipo de marcador de vista derivadas (en este caso, `PhotoViewHolder`) antes de usarlo.
El adaptador carga el recurso de imagen en la vista al que hace referencia el titular de la vista `Image` propiedad y se copia el texto del título en la vista al que hace referencia el titular de la vista `Caption` propiedad. Esto *enlaza* la vista asociada con sus datos.

Tenga en cuenta que `OnBindViewHolder` es el código que se ocupa directamente de la estructura de los datos. En este caso, `OnBindViewHolder` entiende cómo asignar el `RecyclerView` posición a su elemento de datos asociados en el origen de datos de elemento. La asignación es sencilla en este caso porque la posición se puede usar como un índice de matriz en el álbum de fotos; Sin embargo, los orígenes de datos más complejos pueden requerir código adicional para establecer este tipo de asignación.


#### <a name="itemcount"></a>ItemCount

El `ItemCount` método devuelve el número de elementos de la recopilación de datos. En la aplicación de Visor de fotos de ejemplo, el número de elementos es el número de fotos en el álbum de fotos:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Para obtener más información acerca de `RecyclerView.Adapter`, consulte el [referencia de clase RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Colocarlo todos juntos

Resultante `RecyclerView` implementación para la aplicación de fotografía de ejemplo consta de `MainActivity` código que crea el origen de datos, Administrador de diseño y el adaptador. `MainActivity` crea el `mRecyclerView` instancia, crea una instancia de origen de datos y el adaptador y se conecta en el Administrador de diseño y el adaptador:

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

Cuando se compila y ejecuta este código, se crea la foto básica ver aplicación tal como se muestra en las capturas de pantalla siguiente:

[![Dos capturas de pantalla de aplicación tarjetas de fotos de desplazamiento vertical de la visualización de fotografías](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Esta aplicación básica solo admite la exploración del álbum de fotografías. No ha respondido para eventos de elemento táctil, ni controlar los cambios en los datos subyacentes. Esta funcionalidad se agrega en [extiende el ejemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).


### <a name="changing-the-layoutmanager"></a>Cambiar el LayoutManager

Debido `RecyclerView`de flexibilidad, es fácil modificar la aplicación para utilizar un administrador de diseño diferente. En el ejemplo siguiente, se cambia para mostrar el álbum de fotos con un diseño de cuadrícula que se desplaza horizontalmente en lugar de con un diseño lineal vertical. Para ello, se modifica la creación de instancias del Administrador de diseño para utilizar el `GridLayoutManager` como se indica a continuación:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Este cambio de código reemplaza vertical `LinearLayoutManager` con un `GridLayoutManager` que presenta una cuadrícula formada por dos filas Desplácese en dirección horizontal. Cuando se compila y se vuelva a ejecutar la aplicación, verá que las fotografías se muestran en una cuadrícula y que el desplazamiento es horizontal en lugar de vertical:

[![Captura de pantalla de ejemplo de aplicación con las fotografías de desplazamiento horizontal en una cuadrícula](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Al cambiar una única línea de código, tiene es posible modificar la aplicación de visualización de fotografías para que use un diseño diferente con un comportamiento diferente.
Tenga en cuenta que el código del adaptador ni el diseño XML tenía que modificar para cambiar el estilo de diseño. 

En el tema siguiente, [extiende el ejemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), esta aplicación de ejemplo básico se ha ampliado para controlar los eventos de elemento y haga clic en y actualizar `RecyclerView` cuando cambios de origen de datos subyacente.



## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funcionalidad y los elementos de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Siguiendo con el ejemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
