---
title: ViewPager con vistas
description: ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo hacia izquierda y derecha para ir avanzando por las páginas de datos. Esta guía explica cómo implementar una interfaz de usuario swipeable con ViewPager y PagerTabStrip, utilizar vistas como las páginas de datos (una guía posteriores abarca cómo usar fragmentos de las páginas).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a8b7fa53d3384821d028e4a88ba22071a17e5bd9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61311610"
---
# <a name="viewpager-with-views"></a>ViewPager con vistas

_ViewPager es un administrador de diseño que le permite implementar la navegación gestural. Navegación gestural permite al usuario deslice el dedo hacia izquierda y derecha para ir avanzando por las páginas de datos. Esta guía explica cómo implementar una interfaz de usuario swipeable con ViewPager y PagerTabStrip, utilizar vistas como las páginas de datos (una guía posteriores abarca cómo usar fragmentos de las páginas)._

 
## <a name="overview"></a>Información general

Esta guía es un tutorial que proporciona una demostración paso a paso sobre cómo usar `ViewPager` para implementar una galería de imágenes de árboles de hoja caduca y duraderas. En esta aplicación, el usuario pase el dedo left y right a través de un catálogo de"árbol" para ver las imágenes de árbol. En la parte superior de cada página del catálogo, el nombre del árbol que aparece en un`PagerTabStrip`, y se muestra una imagen del árbol en un `ImageView`. Se usa un adaptador para la interfaz de la `ViewPager` al modelo de datos subyacente. Esta aplicación implementa un adaptador que se deriva de `PagerAdapter`. 

Aunque `ViewPager`-aplicaciones a menudo se implementan con `Fragment`s, hay algunos casos de uso relativamente sencillos donde la complejidad adicional de `Fragment`s no es necesario. Por ejemplo, la aplicación de galería de imágenes básica ilustrada en este tutorial no requiere el uso de `Fragment`s. Dado que el contenido es estático y deslizamientos única de usuario entre diferentes imágenes, la implementación se puede mantener más sencilla mediante el uso de los diseños y vistas de Android estándar. 



## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android denominado **TreePager** (consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener más información acerca de cómo crear nuevos proyectos de Android). A continuación, inicie el Administrador de paquetes de NuGet. (Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: Incluir NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Buscar e instalar **biblioteca de compatibilidad de Android v4**: 

[![Captura de pantalla de soporte técnico v4 Nuget seleccionado en el Administrador de paquetes de NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Esta acción también instalará cualquier reaquired de paquetes adicionales por **biblioteca de compatibilidad de Android v4**.



## <a name="add-an-example-data-source"></a>Agregar un origen de datos de ejemplo

En este ejemplo, el origen de datos del catálogo de árbol (representado por la `TreeCatalog` clase) proporciona la `ViewPager` con el contenido del elemento. 
`TreeCatalog` contiene una colección de imágenes de árbol y los títulos de árbol que el adaptador utilizará para la creación de listas para usar `View`s. El `TreeCatalog` constructor no requiere ningún argumento:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

La colección de imágenes en `TreeCatalog` se organiza de modo que cada imagen puede tener acceso a un indizador. Por ejemplo, la siguiente línea de código recupera el identificador de recurso de imagen para la tercera imagen de la colección: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Dado que los detalles de implementación `TreeCatalog` no son relevantes para familiarizarse con `ViewPager`, el `TreeCatalog` código no aparece aquí. El código fuente para `TreeCatalog` está disponible en [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Descargue este archivo de origen (o copie y pegue el código en un nuevo **TreeCatalog.cs** archivo) y agréguelo al proyecto. Además, descargue y descomprima el [archivos de imagen](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) en su **recursos/drawable** carpeta e incluirlos en el proyecto. 



## <a name="create-a-viewpager-layout"></a>Crear un diseño ViewPager

Abra **Resources/layout/Main.axml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>

```csharp
This XML defines a `ViewPager` that occupies the entire screen. Note that
you must use the fully-qualified name **android.support.v4.view.ViewPager**
because `ViewPager` is packaged in a support library. `ViewPager` is
available only from 
[Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);
it is not available in the Android SDK. 


## Set up ViewPager

Edit **MainActivity.cs** and add the following `using` statement:

```csharp
using Android.Support.V4.View;
```

Reemplace el método `OnCreate` con el código siguiente:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

Este código hace lo siguiente:

1.  Establece la vista de la **Main.axml** recursos de diseño.

2.  Recupera una referencia a la `ViewPager` del diseño.

3.  Crea una instancia de un nuevo `TreeCatalog` como origen de datos.

Al compilar y ejecutar este código, debería ver una pantalla similar a la captura de pantalla siguiente: 

[![Captura de pantalla de la aplicación muestra un ViewPager vacía](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

En este momento, el `ViewPager` está vacía porque falta un adaptador para obtener acceso al contenido en **TreeCatalog**. En la sección siguiente, un **PagerAdapter** se crea para conectar el `ViewPager` a la **TreeCatalog**. 


## <a name="create-the-adapter"></a>Crear el adaptador

`ViewPager` usa un objeto de controlador de adaptador que se encuentra entre el `ViewPager` y el origen de datos (vea la ilustración de [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter)). Para tener acceso a estos datos, `ViewPager` requiere que proporcione un adaptador personalizado derivado de `PagerAdapter`. Este adaptador rellena cada `ViewPager` página con el contenido del origen de datos. Dado que este origen de datos es específico de la aplicación, el adaptador personalizado es el código que entienda cómo tener acceso a los datos. Como los deslizamientos de usuario a través de páginas de la `ViewPager`, el adaptador extrae información del origen de datos y los carga en las páginas de la `ViewPager` para mostrar. 

Cuando se implementa un `PagerAdapter`, debe reemplazar lo siguiente:

-   **InstantiateItem** &ndash; crea la página (`View`) para una posición especificada y lo agrega a la `ViewPager`de colección de vistas. 

-   **DestroyItem** &ndash; quita una página de una posición especificada.

-   **Recuento de** &ndash; propiedad de solo lectura que devuelve el número de vistas (páginas) disponibles. 

-   **IsViewFromObject** &ndash; determina si una página está asociada a un objeto de clave específico. (Este objeto se crea mediante el `InstantiateItem` método.) En este ejemplo, el objeto de clave es la `TreeCatalog` objeto de datos.

Agregue un nuevo archivo denominado **TreePagerAdapter.cs** y reemplace su contenido con el código siguiente: 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

Este código crea el código auxiliar del esenciales `PagerAdapter` implementación. En las secciones siguientes, cada uno de estos métodos se reemplaza con el código de trabajo. 



### <a name="implement-the-constructor"></a>Implemente el Constructor

Cuando crea una instancia de la aplicación la `TreePagerAdapter`, proporciona un contexto (la `MainActivity`) y con instancias `TreeCatalog`. Agregue las siguientes variables de miembro y el constructor a la parte superior de la `TreePagerAdapter` clase **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

El propósito de este constructor es almacenar el contexto y `TreeCatalog` de instancia que el `TreePagerAdapter` va a usar. 



### <a name="implement-count"></a>Recuento de implementación

El `Count` implementación es relativamente sencilla: devuelve el número de árboles en el catálogo de árbol. Reemplaza `Count` por el código siguiente:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

El `NumTrees` propiedad de `TreeCatalog` devuelve el número de árboles (número de páginas) en el conjunto de datos.



### <a name="implement-instantiateitem"></a>Implementar InstantiateItem

El `InstantiateItem` método crea la página de una posición determinada. También debe agregar la vista recién creada para el `ViewPager`de ver la colección. Para hacer esto posible, el `ViewPager` pasa a sí mismo como el parámetro de contenedor. 

Reemplace el método `InstantiateItem` con el código siguiente:

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

Este código hace lo siguiente:

1.  Crea una instancia de un nuevo `ImageView` para mostrar la imagen de árbol en la posición especificada. La aplicación `MainActivity` es el contexto que se pasará a la `ImageView` constructor.

2.  Establece el `ImageView` recurso para el `TreeCatalog` Id. de recurso en la posición especificada de la imagen.

3.  Convierte el contenedor pasado `View` a un `ViewPager` referencia.
    Tenga en cuenta que debe usar `JavaCast<ViewPager>()` para realizar correctamente esta conversión (Esto es necesario para que Android realiza una conversión de tipos de comprobación en tiempo de ejecución).

4.  Agrega la instancia `ImageView` a la `ViewPager` y devuelve el `ImageView` al llamador.

Cuando el `ViewPager` muestra la imagen en `position`, muestra esto `ImageView`. Inicialmente, `InstantiateItem` se llama dos veces para rellenar las dos primeras páginas con las vistas. Cuando el usuario se desplaza, se llama de nuevo para mantener las vistas justo detrás y por delante del elemento que se muestra actualmente. 



### <a name="implement-destroyitem"></a>Implementar DestroyItem

El `DestroyItem` método quita una página de la posición especificada. En las aplicaciones donde puede cambiar la vista en cualquier posición dada, `ViewPager` debe tener alguna manera de quitar una vista obsoleta en esa posición antes de reemplazarla con una nueva vista. En el `TreeCatalog` ejemplo, la vista en cada posición no cambia, por lo que se quita una vista por `DestroyItem` será simplemente volver a agregarla cuando `InstantiateItem` se llama para esa posición. (Para mejorar la eficacia, uno podría implementar un grupo para reciclar `View`s que se vuelve a mostrar en la misma posición.) 

Reemplace el método `DestroyItem` con el código siguiente: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Este código hace lo siguiente:

1.  Convierte el contenedor pasado `View` en un `ViewPager` referencia.

2.  Convierte el objeto pasado de Java (`view`) en un C# `View` (`view as View`);

3.  Quita la vista de la `ViewPager`. 



### <a name="implement-isviewfromobject"></a>Implementar IsViewFromObject

Como diapositivas de usuario izquierda y derecha a través de las páginas de contenido, `ViewPager` llamadas `IsViewFromObject` para comprobar que el elemento secundario `View` en la posición especificada está asociada con el objeto del adaptador para esa misma posición (por lo tanto, se denomina el objeto del adaptador una *clave del objeto*). Para aplicaciones relativamente simples, la asociación es uno de identidad &ndash; clave del objeto del adaptador en esa instancia es la vista que se devolvió previamente a la `ViewPager` a través de `InstantiateItem`. Sin embargo para otras aplicaciones, la clave del objeto puede ser otra instancia de clase específica del adaptador que está asociada (pero no igual que) el elemento secundario que ver `ViewPager` muestra en esa posición. Sólo el adaptador sabe si están o no la vista pasada y la clave del objeto asociados. 

`IsViewFromObject` se debe implementar para `PagerAdapter` funcione correctamente. Si `IsViewFromObject` devuelve `false` para una determinada posición, `ViewPager` no mostrará la vista en esa posición. En el `TreePager` aplicación, el objeto clave devuelta por `InstantiateItem` *es* la página `View` de un árbol, por lo que el código solo tiene que comprobar para la identidad (es decir, la clave del objeto y la vista son del mismo). Reemplaza `IsViewFromObject` por el código siguiente: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>Agregar el adaptador a la ViewPager

Ahora que la `TreePagerAdapter` está implementado, es el momento de agregarlo a la `ViewPager`. En **MainActivity.cs**, agregue la siguiente línea de código al final de la `OnCreate` método:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Este código crea instancias el `TreePagerAdapter`, pasando el `MainActivity` como el contexto (`this`). Las instancias `TreeCatalog` se pasa en el segundo argumento del constructor. El `ViewPager`del `Adapter` propiedad está establecida para la instancia `TreePagerAdapter` objeto; este se conecta el `TreePagerAdapter` en el `ViewPager`. 

La implementación básica está completa ahora &ndash; compilar y ejecutar la aplicación. Debería ver la primera imagen del catálogo de árbol aparecen en la pantalla, tal como se muestra a la izquierda en la captura de pantalla siguiente. Deslice el dedo hacia izquierdo para ver más vistas de árbol, a continuación, derecha pasar el dedo para mover de nuevo a través del catálogo de árbol: 

[![Aplicación de capturas de pantalla de TreePager desplazándose a través de las imágenes de árbol](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Agregar un indicador de buscapersonas

Este mínimo `ViewPager` implementación muestra las imágenes del catálogo de árbol, pero no proporciona ninguna indicación de dónde esté el usuario en el catálogo. El siguiente paso es agregar un `PagerTabStrip`. El `PagerTabStrip` informa al usuario como a qué página se muestra y proporciona el contexto de navegación mostrando una sugerencia de las páginas anteriores y siguientes. `PagerTabStrip` está pensado para usarse como un indicador de la página actual de un `ViewPager`; se desplaza y se actualiza como los deslizamientos de usuario a través de cada página. 

Abra **Resources/layout/Main.axml** y agregue un `PagerTabStrip` al diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` y `PagerTabStrip` están diseñados para funcionar conjuntamente. Cuando se declara un `PagerTabStrip` dentro de un `ViewPager` diseño, el `ViewPager` buscará automáticamente el `PagerTabStrip` y conectarlo al adaptador. Al compilar y ejecutar la aplicación, debería ver el vacío `PagerTabStrip` aparece en la parte superior de cada pantalla: 

[![Captura de pantalla de primer plano de un PagerTabStrip vacía](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>Mostrar un título

Para agregar un título para cada pestaña de página, implemente el `GetPageTitleFormatted` método en el `PagerAdapter`-clase derivada. `ViewPager` las llamadas `GetPageTitleFormatted` (si se implementa) para obtener la cadena de título que describe la página en la posición especificada. Agregue el método siguiente a la `TreePagerAdapter` clase **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Este código recupera la cadena de título del árbol de la página especificada (posición) en el catálogo de árbol, lo convierte en un Java `String`y lo devuelve a la `ViewPager`. Al ejecutar la aplicación con este nuevo método, cada página muestra el título de árbol en el `PagerTabStrip`. Debería ver el nombre de árbol en la parte superior de la pantalla sin subrayado: 

[![Capturas de pantalla de las páginas con pestañas de PagerTabStrip rellenado de texto](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Puede pasar el dedo hacia delante y hacia atrás para ver la imagen de cada árbol subtítulos opcionales en el catálogo. 



### <a name="pagertitlestrip-variation"></a>Variación PagerTitleStrip

`PagerTitleStrip` es muy similar a `PagerTabStrip` , salvo que `PagerTabStrip` agrega un carácter de subrayado para la pestaña seleccionada actualmente. Puede reemplazar `PagerTabStrip` con `PagerTitleStrip` en el diseño y la ejecución de la aplicación de nuevo para ver su aspecto con anterior `PagerTitleStrip`: 

[![PagerTitleStrip con un subrayado quitado el texto](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Tenga en cuenta que el subrayado se quita cuando se convierte en `PagerTitleStrip`. 


 
## <a name="summary"></a>Resumen

Este tutorial proporciona un ejemplo paso a paso de cómo crear un basic `ViewPager`-según la aplicación sin usar `Fragment`s. Presentaba un origen de datos de ejemplo que contiene las imágenes y cadenas de título, un `ViewPager` diseño para mostrar las imágenes y un `PagerAdapter` subclase que conecta el `ViewPager` al origen de datos. Para ayudar al usuario navegar por el conjunto de datos, se incluyen instrucciones que explican cómo agregar un `PagerTabStrip` o `PagerTitleStrip` para mostrar el título de la imagen en la parte superior de cada página. 


## <a name="related-links"></a>Vínculos relacionados

- [TreePager (ejemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
