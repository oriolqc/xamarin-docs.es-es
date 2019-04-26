---
title: Mediante el Diseñador de Xamarin.Android
description: En este artículo es un tutorial del Diseñador de Xamarin.Android. Muestra cómo crear una interfaz de usuario para una aplicación de explorador pequeño color; Esta interfaz de usuario se crea completamente en el diseñador.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1174fe5cb417d4977fd6519086e6c4942e74c10b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61208141"
---
# <a name="using-the-xamarinandroid-designer"></a>Mediante el Diseñador de Xamarin.Android

_En este artículo es un tutorial del Diseñador de Xamarin.Android. Muestra cómo crear una interfaz de usuario para una aplicación de explorador pequeño color; Esta interfaz de usuario se crea completamente en el diseñador._


## <a name="overview"></a>Información general

Interfaces de usuario de Android se pueden crear mediante declaración utilizando archivos XML o mediante programación al escribir código. El Diseñador de Xamarin.Android permite a los desarrolladores crear y modificar los diseños declarativos visualmente, sin necesidad de editar manualmente archivos XML. El diseñador también proporciona comentarios en tiempo real que permite al desarrollador a evaluar los cambios de la interfaz de usuario sin tener que volver a implementar la aplicación en un dispositivo o a un emulador. Estas características de diseñador pueden acelerar enormemente el desarrollo de la IU de Android.
En este artículo se muestra cómo usar el Diseñador de Xamarin.Android para crear visualmente una interfaz de usuario.

## <a name="walkthrough"></a>Tutorial

El objetivo de este tutorial es usar el Diseñador de Android para crear una interfaz de usuario para una aplicación de explorador del color de ejemplo. La aplicación de explorador del color presenta una lista de colores, sus nombres y sus valores RGB. Obtendrá información sobre cómo agregar widgets a la **superficie de diseño** , así como también diseñar visualmente estos widgets. Después de eso, obtendrá información sobre cómo modificar widgets en forma interactiva el **superficie de diseño** o mediante el diseñador **propiedades** panel. Por último, verá cómo el diseño se ve cuando la aplicación se ejecuta en un dispositivo o emulador.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Crear un nuevo proyecto

El primer paso es crear un nuevo proyecto de Xamarin.Android. Inicie Visual Studio, haga clic en **nuevo proyecto...** y elija el **Visual C\# > Android > aplicación de Android (Xamarin)** plantilla.
Nombre de la nueva aplicación **DesignerWalkthrough** y haga clic en **Aceptar**.

[![Aplicación para Android en blanco](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

En el **nueva aplicación Android** cuadro de diálogo, elija **aplicación vacía** y haga clic en **Aceptar**:

[![Seleccione la plantilla de aplicación para Android en blanco](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>Agregaremos un diseño

El siguiente paso es crear un **LinearLayout** que contendrá el usuario elementos de la interfaz. Haga clic en **y diseño de los recursos** en el **el Explorador de soluciones** y seleccione **Agregar > nuevo elemento...** . En el **Agregar nuevo elemento** cuadro de diálogo, seleccione **diseño de Android**. Nombre del archivo **list_item** y haga clic en **agregar**:

[![Nuevo diseño](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

El nuevo **list_item** diseño se muestra en el diseñador. Tenga en cuenta que se muestran dos paneles &ndash; el *superficie de diseño* para el **list_item** está visible en el panel izquierdo, mientras que su origen XML se muestra en el panel derecho. Puede intercambiar las posiciones de los **superficie de diseño** y **origen** paneles, haga clic en el **intercambio paneles** icono se encuentra entre los dos paneles:

[![Vista de diseñador](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

Desde el **vista** menú, haga clic en **Other Windows > esquema del documento** para abrir el **esquema del documento**. El **esquema del documento** muestra que el diseño contiene actualmente un único **LinearLayout** widget:

[![Esquema del documento](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

El siguiente paso es crear la interfaz de usuario para la aplicación de explorador color dentro de este `LinearLayout`.

### <a name="creating-the-list-item-user-interface"></a>Creación de la interfaz de usuario de elemento de lista

Si el **cuadro de herramientas** no está visible el panel, haga clic en el **cuadro de herramientas** ficha a la izquierda. En el **cuadro de herramientas**, desplácese hacia abajo hasta la **imágenes & Media** sección y desplácese más hacia abajo hasta que encuentre un `ImageView`:

[![Busque ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

Como alternativa, puede escribir *ImageView* en la barra de búsqueda para localizar el `ImageView`:

[![Búsqueda de ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Arrastre este `ImageView` a la superficie de diseño (Esto `ImageView` se usará para mostrar una muestra de color en la aplicación de explorador de color):

[![ImageView en lienzo](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

A continuación, arrastre un `LinearLayout (Vertical)` widget desde el **cuadro de herramientas** en el diseñador. Tenga en cuenta que un contorno azul indica los límites del agregado `LinearLayout`. El **esquema del documento** muestra que es un elemento secundario de `LinearLayout`, ubicado en `imageView1 (ImageView)`:

[![Contorno azul](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Cuando se selecciona el `ImageView` en el diseñador, se mueve el contorno azul rodear el `ImageView`. Además, la selección se desplaza a `imageView1 (ImageView)` en el **esquema del documento**:

[![Seleccione ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

A continuación, arrastre un `Text (Large)` widget desde el **cuadro de herramientas** en recién agregado `LinearLayout`. Tenga en cuenta que el diseñador usa verde resalta para indicar donde se insertará el nuevo widget:

[![Información destacada de verde](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

A continuación, agregue un `Text (Small)` widget siguiente el `Text (Large)` widget:

[![Agregar un widget de texto pequeño](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

En este momento, la superficie del diseñador debería ser similar a la captura de pantalla siguiente:

[![Diseño del diseñador](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Si los dos `textView` widgets no son inside `linearLayout1`, arrástrelos hasta `linearLayout1` en el **esquema del documento** y colóquelas para que aparezcan como se muestra en la captura de pantalla anterior (con sangría aplicada bajo `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Organizar la interfaz de usuario

El paso siguiente consiste en modificar la interfaz de usuario para mostrar el `ImageView` a la izquierda, con los dos `TextView` apilan widgets a la derecha de la `ImageView`.

1.  Seleccione el control `ImageView`.

2.  En el **ventana propiedades**, escriba *ancho* en la búsqueda y localice **ancho de diseño**.

3.  Cambiar el **ancho de diseño** si se establece en `wrap_content`:

![Conjunto de encapsulado de contenido](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Otra manera de cambiar el `Width` configuración es hacer clic en el triángulo en el lado derecho del widget para cambiar su configuración de ancho para `wrap_content`:

![Arrastrar para establecer el ancho](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Vuelva a hacer clic en el triángulo devuelve el `Width` si se establece en `match_parent`. A continuación, vaya a la **esquema del documento** panel y seleccione la raíz `LinearLayout`:

[![Seleccione raíz LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Con la raíz `LinearLayout` seleccionado, vuelva a la **propiedades** panel, escriba *orientación* en la búsqueda y localice el **orientación** configuración. Cambio **orientación** a `horizontal`:

![Seleccione la orientación horizontal](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

En este momento, la superficie del diseñador debería parecerse a la siguiente captura de pantalla.
Tenga en cuenta que el `TextView` widgets se han movido a la derecha de la `ImageView`:

[![Diseño del diseñador](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modificar el espaciado

El paso siguiente es modificar la configuración de relleno y margen en la interfaz de usuario para proporcionar más espacio entre los widgets. Seleccione el `ImageView` en la superficie de diseño. En el **propiedades** panel, escriba `min` en el cuadro de búsqueda. Escriba `70dp` para **alto mínimo** y `50dp` para **ancho mínimo**:

[![Establezca el alto y ancho](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

En el **propiedades** panel, escriba `padding` en la búsqueda y escriba `10dp` para **relleno**. Estos `minHeight`, `minWidth` y `padding` configuración agrega relleno alrededor de todos los lados de la `ImageView` y alargarlo verticalmente. Tenga en cuenta que el diseño XML cambia a medida que escribe estos valores:

[![Establecer el relleno](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

La parte inferior, izquierda, derecha y configuración del relleno de arriba se puede establecer independientemente si escribe valores en el **relleno inferior**, **relleno izquierda**, **relleno derecho**y  **Relleno superior** campos, respectivamente.
Por ejemplo, establecer el **relleno izquierda** campo `5dp` y **relleno inferior**, **relleno derecho**, y **relleno superior** campos para `10dp`:

[![Configuración de relleno personalizada](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

A continuación, ajustar la posición de la `LinearLayout` widget que contiene los dos `TextView` widgets. En el **esquema del documento**, seleccione `linearLayout1`. En el **propiedades** ventana, escriba `margin` en el cuadro de búsqueda. Establecer **diseño margen inferior**, **diseño margen izquierda**, y **parte superior del margen de diseño** a `5dp`. Establecer **diseño margen derecho** a `0dp`:

[![Establecer los márgenes](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Quitar la imagen predeterminada

Dado que el `ImageView` se utiliza para mostrar colores (en lugar de imágenes), el siguiente paso es quitar el origen de la imagen predeterminada agregado por la plantilla.

1.  Seleccione el `ImageView` en el **superficie del diseñador**.

2.  En **propiedades**, escriba *src* en el cuadro de búsqueda.

3.  Haga clic en el cuadrado pequeño a la derecha de la **Src** propiedad configuración y seleccione **restablecer**:

[![Desactive la configuración de src ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Esto quita `android:src="@android:drawable/ic_menu_gallery"` desde el origen de XML para que `ImageView`.

### <a name="adding-a-listview-container"></a>Adición de un contenedor de ListView

Ahora que la **list_item** se define el diseño, el siguiente paso es agregar un `ListView` al diseño principal. Esto `ListView` contendrá una lista de **list_item**. 

En el **el Explorador de soluciones**, abra **Resources/layout/activity_main.axml**. En el **cuadro de herramientas**, busque el `ListView` widget y arrástrelo hasta el **superficie de diseño**. El `ListView` en el diseñador se quedan en blanco, excepto las líneas azules que describen su borde cuando está seleccionado. Puede ver el **esquema del documento** para comprobar que la **ListView** se agregó correctamente:

[![ListView nuevo](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

De forma predeterminada, el `ListView` tiene un `Id` valor `@+id/listView1`.
Mientras `listView1` sigue seleccionado en el **esquema del documento**, abra el **propiedades** panel, haga clic en **organizar por**y seleccione **categoría**.
Abra **Main**, busque el **Id** propiedad y cambie su valor a `@+id/myListView`:

[![Cambie el nombre de identificador a myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

En este momento, la interfaz de usuario está lista para su uso.

### <a name="running-the-application"></a>Ejecutar la aplicación

Abra **MainActivity.cs** y reemplace su código con lo siguiente:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

Este código usa un personalizado `ListView` adaptador para cargar la información de color y mostrar estos datos en la interfaz de usuario que acaba de crear. Para mantener este breve ejemplo, la información de color está codificada en una lista, pero el adaptador podría modificarse para extraer información de color de un origen de datos o calcular sobre la marcha. Para obtener más información acerca de `ListView` adaptadores, vea [ListView](~/android/user-interface/layouts/list-view/index.md).

Compile y ejecute la aplicación. Captura de pantalla siguiente es un ejemplo de cómo la aplicación aparece cuando se ejecuta en un dispositivo:

[![Captura de pantalla final](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Crear un nuevo proyecto

El primer paso es crear un nuevo proyecto de Xamarin.Android.

Inicie Visual Studio para Mac y haga clic en **nuevo proyecto...** . Elija la **aplicación Android** plantilla y haga clic en **siguiente**:

[![Aplicación para Android en blanco](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Nombre de la nueva aplicación **DesignerWalkthrough**. En **plataformas de destino**, seleccione **más reciente y lo mejor** y haga clic en **siguiente**:

[![Nombre de la aplicación](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

En la siguiente pantalla del cuadro de diálogo, haga clic en **crear**.

### <a name="adding-a-layout"></a>Agregaremos un diseño

El siguiente paso es crear un **LinearLayout** que contendrá el usuario elementos de la interfaz.

En Visual Studio para Mac, haga clic en **y diseño de los recursos** en el **solución** rellenar y seleccione **Agregar > nuevo archivo...** . En el **nuevo archivo** cuadro de diálogo, seleccione **Android > diseño**. Nombre del archivo **list_item** y haga clic en **New**:

[![Nuevo diseño](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Después de agrega este archivo, el nuevo **list_item** diseño se muestra en el **superficie de diseño** (si ve el mensaje, *Este proyecto contiene recursos que no se compilaron correctamente, representación puede verse afectada*, haga clic en **compilar > compilar todo** para compilar el proyecto):

[![Vista de diseñador](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Haga clic en el **origen** ficha en la parte inferior del diseñador para ver el origen XML para este diseño. Al hacer clic en el **esquema del documento** ficha a la derecha, muestra que el diseño contiene actualmente un único **LinearLayout** widget:

[![Diseñador XML](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

El siguiente paso es crear la interfaz de usuario para la aplicación de explorador del color.

### <a name="creating-the-list-item-user-interface"></a>Creación de la interfaz de usuario de elemento de lista

Haga clic en el **diseñador** ficha en la parte inferior de la pantalla para volver a la **superficie del Diseñador de**. En el **cuadro de herramientas** panel a la derecha, desplácese hacia abajo hasta la **imágenes & Media** sección y busque `ImageView`:

[![Busque ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

Como alternativa, puede escribir *ImageView* en la barra de búsqueda para localizar el `ImageView`:

[![Búsqueda de ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Arrastre este `ImageView` hasta la **superficie de diseño** (Esto `ImageView` se usará para mostrar una muestra de color en la aplicación de explorador de color):

[![ImageView en lienzo](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

A continuación, arrastre un `LinearLayout (Vertical)` widget desde el **cuadro de herramientas** en el **superficie de diseño**. Tenga en cuenta que un contorno azul indica los límites del agregado `LinearLayout`. El **esquema del documento** muestra que es un elemento secundario de `LinearLayout`, que se encuentra debajo de `imageView1 (ImageView)`:

[![Contorno azul](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Cuando se selecciona el `ImageView` en el diseñador, se mueve el contorno azul rodear el `ImageView`. Además, la selección se desplaza a `imageView1 (ImageView)` en el **esquema del documento**:

[![Seleccione ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

A continuación, arrastre un `Text (Large)` widget desde el **cuadro de herramientas** en recién agregado `LinearLayout`. Tenga en cuenta que mientras arrastra el mouse en el **superficie de diseño**, resalta donde se insertará el nuevo widget.
El `Text (Large)` widget debe encontrarse dentro de `linearLayout1` tal como se muestra aquí:

[![Agregar un widget de texto grande](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

A continuación, agregue un `Text (Small)` widget siguiente el `Text (Large)` widget. En este momento, el **superficie de diseño** debe ser similar a la captura de pantalla siguiente:

[![Agregar un widget de texto pequeño](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Si los dos `textView` widgets no son inside `linearLayout1`, arrástrelos hasta `linearLayout1` en el **esquema del documento** y colóquelas de modo que aparezcan como se muestra en la captura de pantalla anterior (con una sangría debajo `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Organizar la interfaz de usuario

El paso siguiente consiste en modificar la interfaz de usuario para mostrar el `ImageView` a la izquierda, con los dos `TextView` apilan widgets a la derecha de la `ImageView`.

1.  Con el `ImageView` seleccionado, haga clic en el **propiedades** ficha.

2.  Justo debajo del **propiedades** , haga clic **diseño**.

3.  Desplácese hacia abajo hasta **grupo de vista** y cambie el `Width` si se establece en `wrap_content`:

[![Conjunto de encapsulado de contenido](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Otra manera de cambiar el `Width` configuración es hacer clic en el triángulo en el lado derecho del widget para cambiar su configuración de ancho para `wrap_content`:

[![Arrastrar para establecer el ancho](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Vuelva a hacer clic en el triángulo devuelve el `Width` si se establece en `match_parent`. A continuación, vaya a la **esquema del documento** panel y seleccione la raíz `LinearLayout`:

[![Seleccione raíz LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Con la raíz `LinearLayout` seleccionado, vuelva a la **propiedades** ficha y haga clic en **Widget**. Cambiar el `Orientation` si se establece en `horizontal` tal como se muestra a continuación. En este momento, el **superficie de diseño** debe ser similar a la captura de pantalla siguiente. Tenga en cuenta que el `TextView` widgets se han movido a la derecha de la `ImageView`:

[![Seleccione la orientación horizontal](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>Modificar el espaciado

El paso siguiente es modificar la configuración de relleno y margen de la interfaz de usuario para proporcionar más espacio entre los widgets. Seleccione el `ImageView` y haga clic en el **diseño** pestaña **propiedades**. Cambiar el `Min Width` a `50dp`, el `Min Height` a `70dp`y el `Padding` a `10dp`.
Esto aplica relleno alrededor de todos los lados de la `ImageView` y lo alarga verticalmente:

[![Establecer el relleno](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

La parte superior, derecha, inferior y configuración de margen izquierdo se pueden establecer independientemente si escribe valores en el `Top`, `Right`, `Bottom`, y `Left` relleno de campos, respectivamente. Por ejemplo, establecer el `Left` para el valor de relleno `5dp` y `Top`, `Right`, y `Bottom` a los valores de relleno `10dp`. Tenga en cuenta que el `Padding` configuración cambia a una lista separada por comas de los valores siguientes:

[![Configuración de relleno personalizada](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

A continuación, ajustar la posición de la `LinearLayout` widget que contiene los dos `TextView` widgets. En el **esquema del documento**, seleccione `linearLayout1`. En el **propiedades** panel, seleccione el **diseño** ficha. Desplácese hacia abajo hasta la **grupo de vista** sección y establezca el `Left`, `Top`, `Right`, y `Bottom` márgenes a `5dp`, `5dp`, `0dp`, y `5dp` respectivamente:

[![Establecer los márgenes](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Quitar la imagen predeterminada

Dado que el `ImageView` se utiliza para mostrar colores (en lugar de imágenes), el siguiente paso es quitar el origen de la imagen predeterminada agregado por la plantilla.

1.  Seleccione el control `ImageView`.

2.  Haga clic en el **Widget** pestaña **propiedades**.

3.  Desactive el `Src` establecer para que esté en blanco:

[![Desactive la configuración de src ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Esto quita `android:src="@android:drawable/ic_menu_gallery"` desde el origen de XML para que `ImageView`.

### <a name="adding-a-listview-container"></a>Adición de un contenedor de ListView

Ahora que la **list_item** se define el diseño, el siguiente paso es agregar un `ListView` al diseño principal. Esto `ListView` contendrá una lista de **list_item**. 

En el **el Explorador de soluciones**, abra **Resources/layout/Main.axml**.
Haga clic en el `Button` widget (si existe) y elimínelo. En el **cuadro de herramientas**, busque el `ListView` widget y arrástrelo hasta el **superficie de diseño**.
El `ListView` en el diseñador se quedan en blanco, excepto las líneas azules que describen su borde cuando está seleccionado. Puede ver el **esquema del documento** para comprobar que la **ListView** se agregó correctamente:

[![ListView nuevo](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

De forma predeterminada, el `ListView` tiene un `Id` valor `@+id/listView1`.
Mientras `listView1` sigue seleccionado en el **esquema del documento**, abra el **propiedades** panel, haga clic en **organizar por**y seleccione **categoría**.
Abra **Main**, busque el **Id** propiedad y cambie su valor a `@+id/myListView`:

[![Cambie el nombre de identificador a myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

En este momento, la interfaz de usuario está lista para su uso.

### <a name="running-the-application"></a>Ejecutar la aplicación

Abra **MainActivity.cs** y reemplace su código con lo siguiente:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}
```

Este código usa un personalizado `ListView` adaptador para cargar la información de color y mostrar estos datos en la interfaz de usuario que acaba de crear. Para mantener este breve ejemplo, la información de color está codificada en una lista, pero el adaptador podría modificarse para extraer información de color de un origen de datos o calcular sobre la marcha. Para obtener más información acerca de `ListView` adaptadores, vea [ListView](~/android/user-interface/layouts/list-view/index.md).

Compile y ejecute la aplicación. Captura de pantalla siguiente es un ejemplo de cómo la aplicación aparece cuando se ejecuta en un dispositivo:

[![Captura de pantalla final](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>Resumen

En este artículo ha avanzado a través del proceso de uso del Diseñador de Xamarin.Android en Visual Studio para crear una interfaz de usuario para una aplicación básica.
Se ha demostrado cómo crear la interfaz de un único elemento en una lista y muestra cómo agregar widgets y colóquelas visualmente.
También se explica cómo asignar recursos y, a continuación, establecer varias propiedades en los widgets.
