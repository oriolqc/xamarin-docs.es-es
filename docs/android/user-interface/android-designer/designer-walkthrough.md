---
title: Mediante el Diseñador de Android
description: Este tema es un tutorial del diseñador Xamarin.Android. Muestra cómo crear una interfaz de usuario para una aplicación de explorador de color pequeñas; Esta interfaz de usuario se crea completamente en el diseñador.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/10/2018
ms.openlocfilehash: 8d1dc410d5336d9c2505a18720cc7f734e838c39
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798637"
---
# <a name="using-the-android-designer"></a>Mediante el Diseñador de Android

_Este tema es un tutorial del diseñador Xamarin.Android. Muestra cómo crear una interfaz de usuario para una aplicación de explorador de color pequeñas; Esta interfaz de usuario se crea completamente en el diseñador._


## <a name="overview"></a>Información general

Interfaces de usuario de Android pueden crearse mediante declaración utilizando archivos XML o mediante programación, escribir código. El Diseñador de Xamarin.Android permite a los desarrolladores crear y modificar los diseños declarativos visualmente, sin tener que tratar con la tarea de editar manualmente los archivos XML. El diseñador también proporciona comentarios en tiempo real, lo que permite al desarrollador de evaluar los cambios de la interfaz de usuario sin tener que volver a implementar la aplicación para un dispositivo o un emulador. Esto puede acelerar el desarrollo de la interfaz de usuario de Android tremendamente. En este artículo, le presentaremos un tutorial que muestra cómo utilizar el Diseñador de Xamarin.Android para crear visualmente una interfaz de usuario.


## <a name="walkthrough"></a>Tutorial

El objetivo de este tutorial consiste en usar el Diseñador de Android para crear una interfaz de usuario para una aplicación de explorador de color de ejemplo que presenta una lista de colores, sus nombres y sus valores RGB. Echemos un vistazo aprenderán agregar widgets a la superficie de diseño, así como diseñar visualmente estos widgets. Después de eso, explicaremos cómo modificar los widgets de forma interactiva en la superficie de diseño o mediante el diseñador **controlador de propiedad**. Por último, veremos el aspecto de nuestro diseño cuando se ejecuta la aplicación.

Comencemos.


### <a name="creating-a-new-project"></a>Crear un proyecto nuevo

El primer paso es crear un nuevo proyecto de Xamarin.Android.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Inicie Visual Studio y haga clic en **nuevo proyecto...**  , a continuación, elija la **Visual C\# > Android > aplicación de Android (Xamarin)** plantilla:

[![Aplicación vacía Android](designer-walkthrough-images/vs/01-android-app-sml.w157.png)](designer-walkthrough-images/vs/01-android-app.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Inicie Visual Studio para Mac y haga clic en **nueva solución...** . Elija la **aplicación Android** plantilla y haga clic en **siguiente**:

[![Aplicación vacía Android](designer-walkthrough-images/xs/01-android-app-sml.png)](designer-walkthrough-images/xs/01-android-app.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Nombre de la nueva aplicación **DesignerWalkthrough** y haga clic en **Aceptar**.

[![Aplicación de nombre](designer-walkthrough-images/vs/02-name-app-sml.png)](designer-walkthrough-images/vs/02-name-app.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Nombre de la nueva aplicación **DesignerWalkthrough**. En **plataformas de destino**, seleccione **más reciente y máximo** y haga clic en **siguiente**:

[![Aplicación de nombre](designer-walkthrough-images/xs/02-designer-walkthrough-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough.png#lightbox)

En la siguiente pantalla del cuadro de diálogo, haga clic en **crear**.

-----



### <a name="adding-a-layout"></a>Agregar un diseño

Vamos a crear un **LinearLayout** que se usará para almacenar elementos de la interfaz de nuestro usuario.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En Visual Studio, haga clic en **recursos/diseño** en el **el Explorador de soluciones** y seleccione **Agregar > nuevo elemento...** . En el **Agregar nuevo elemento** cuadro de diálogo, seleccione **diseño Android**. Asignar nombre al archivo **ListItem.axml** y haga clic en **agregar**:

[![Nuevo diseño](designer-walkthrough-images/vs/03-new-layout-sml.w157.png)](designer-walkthrough-images/vs/03-new-layout.w157.png#lightbox)

El nuevo **ListItem** diseño se muestra en el diseñador:

[![Vista de diseñador](designer-walkthrough-images/vs/04-designer-view-sml.png)](designer-walkthrough-images/vs/04-designer-view.png#lightbox)

Haga clic en el **origen** ficha en la parte inferior del diseñador para ver el origen XML para este diseño:

[![Diseñador XML](designer-walkthrough-images/vs/05-designer-xml-sml.png)](designer-walkthrough-images/vs/05-designer-xml.png#lightbox)

Desde el **vista** menú, haga clic en **otras ventanas > esquema del documento** para abrir el **esquema del documento**. El **esquema del documento** muestra que el diseño contiene actualmente un único **LinearLayout** widget:

[![Esquema del documento](designer-walkthrough-images/vs/06-document-outline-sml.png)](designer-walkthrough-images/vs/06-document-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En Visual Studio para Mac, haga clic en **recursos/diseño** en el **solución** rellenar y seleccione **Agregar > nuevo archivo...** . En el **nuevo archivo** cuadro de diálogo, seleccione **Android > diseño**. Asignar nombre al archivo **ListItem** y haga clic en **New**:

[![Nuevo diseño](designer-walkthrough-images/xs/03-new-layout-sml.png)](designer-walkthrough-images/xs/03-new-layout.png#lightbox)

El nuevo **ListItem** diseño se muestra en el diseñador:

[![Vista de diseñador](designer-walkthrough-images/xs/04-designer-view-sml.png)](designer-walkthrough-images/xs/04-designer-view.png#lightbox)

Haga clic en el **origen** ficha en la parte inferior del diseñador para ver el origen XML para este diseño. Al hacer clic en el **esquema del documento** ficha a la derecha, muestra que el diseño contiene actualmente un único **LinearLayout** widget:

[![Diseñador XML](designer-walkthrough-images/xs/05-designer-xml-sml.png)](designer-walkthrough-images/xs/05-designer-xml.png#lightbox)

-----



### <a name="creating-the-list-item-user-interface"></a>Crear la interfaz de usuario del elemento de lista

A continuación, vamos a crear la interfaz de usuario para la aplicación de explorador de color.
Haga clic en el **diseñador** ficha para volver a la superficie de diseño.
En el **cuadro de herramientas**, desplácese hacia abajo hasta la **imágenes & Media** y la sección de leer detenidamente cada elemento hasta que encuentre un *ImageView*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Busque ImageView](designer-walkthrough-images/vs/07-locate-imageview-sml.png)](designer-walkthrough-images/vs/07-locate-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Busque ImageView](designer-walkthrough-images/xs/06-locate-imageview-sml.png)](designer-walkthrough-images/xs/06-locate-imageview.png#lightbox)

-----

Como alternativa, puede escribir *ImageView* en la barra de búsqueda para localizar el `ImageView` widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Búsqueda de ImageView](designer-walkthrough-images/vs/08-imageview-search-sml.png)](designer-walkthrough-images/vs/08-imageview-search.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Búsqueda de ImageView](designer-walkthrough-images/xs/07-imageview-search-sml.png)](designer-walkthrough-images/xs/07-imageview-search.png#lightbox)

-----

Arrastre este `ImageView` a la superficie de diseño:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![ImageView en el lienzo](designer-walkthrough-images/vs/09-imageview-on-canvas-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![ImageView en el lienzo](designer-walkthrough-images/xs/08-imageview-on-canvas-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas.png#lightbox)

-----

Esto `ImageView` se usará para mostrar un muestrario de colores en nuestra aplicación de explorador de color.

A continuación, arrastre un `LinearLayout (Vertical)` widget desde el **cuadro de herramientas** en el diseñador. Tenga en cuenta que un contorno azul indica los límites de agregado `LinearLayout`y el **esquema del documento** muestra que se encuentra directamente debajo de `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Contorno azul](designer-walkthrough-images/vs/10-blue-outline-sml.png)](designer-walkthrough-images/vs/10-blue-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Contorno azul](designer-walkthrough-images/xs/10-blue-outline-sml.png)](designer-walkthrough-images/xs/10-blue-outline.png#lightbox)

-----

Cuando se selecciona el `ImageView` en el diseñador, se mueve el contorno azul rodear el `ImageView`; en el **esquema del documento**, la selección se mueve a `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Seleccione ImageView](designer-walkthrough-images/vs/11-select-imageview-sml.png)](designer-walkthrough-images/vs/11-select-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Seleccione ImageView](designer-walkthrough-images/xs/11-select-imageview-sml.png)](designer-walkthrough-images/xs/11-select-imageview.png#lightbox)

-----

A continuación, arrastre un `Text (Large)` widget desde el **cuadro de herramientas** en recién agregado `LinearLayout`. Observe que el diseñador utiliza verde resalta para indicar dónde se insertará el widget de nuevo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Se resalta en verde](designer-walkthrough-images/vs/12-green-highlight-sml.png)](designer-walkthrough-images/vs/12-green-highlight.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Se resalta en verde](designer-walkthrough-images/xs/12-green-highlight-sml.png)](designer-walkthrough-images/xs/12-green-highlight.png#lightbox)

-----

A continuación, agregue un `Text (Small)` widget siguiente el `Text (Large)` widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Agregar un widget de texto pequeño](designer-walkthrough-images/vs/13-add-small-text-sml.png)](designer-walkthrough-images/vs/13-add-small-text.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Agregar un widget de texto pequeño](designer-walkthrough-images/xs/13-add-small-text-sml.png)](designer-walkthrough-images/xs/13-add-small-text.png#lightbox)

-----

En este momento, el diseñador debe ser similar a la captura de pantalla siguiente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diseño del diseñador](designer-walkthrough-images/vs/14-raw-layout-sml.png)](designer-walkthrough-images/vs/14-raw-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diseño del diseñador](designer-walkthrough-images/xs/14-raw-layout-sml.png)](designer-walkthrough-images/xs/14-raw-layout.png#lightbox)

-----

Si los dos `textView` widgets no están dentro `linearLayout1`, arrástrelos hasta `linearLayout1` en el **esquema del documento** y colóquelas para que aparezcan como se muestra en la captura de pantalla anterior (con sangría aplicada bajo `linearLayout1`).



### <a name="arranging-the-user-interface"></a>Organizar la interfaz de usuario

Vamos a modificar la interfaz de usuario para mostrar el `ImageView` a la izquierda, con los dos `TextView` widgets apilan a la derecha de la `ImageView`.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Seleccione el control `ImageView`.

2.  En el **ventana propiedades**, haga clic en el **por categorías** ordenar icono y desplácese hacia abajo hasta la **diseño - Vista ViewGroup** sección.

3.  Cambiar el `layout_width` si se establece en `wrap_content`:

![Establecer ajuste contenido](designer-walkthrough-images/vs/15-wrap-content.png "conjunto encapsulado contenido")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Con el `ImageView` seleccionado, haga clic en el **propiedades** ficha.

2.  Justo debajo del **propiedades** , haga clic en **diseño**.

3.  Desplácese hacia abajo hasta **vista ViewGroup** y cambie el `Width` si se establece en `wrap_content`:

[![Conjunto de encapsulado de contenido](designer-walkthrough-images/xs/15-wrap-content-sml.png)](designer-walkthrough-images/xs/15-wrap-content.png#lightbox)

-----

Otra manera de cambiar el `Width` configuración es hacer clic en el triángulo en el lado derecho del widget para cambiar su configuración de ancho para `wrap_content`:

[![Arrastrar para establecer ancho](designer-walkthrough-images/xs/16-width-arrow-sml.png)](designer-walkthrough-images/xs/16-width-arrow.png#lightbox)

Haga clic de nuevo en el triángulo que se devuelve el `Width` si se establece en `match_parent`.

A continuación, cambie a la **esquema del documento** y seleccione la raíz `LinearLayout`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Seleccione raíz LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Seleccione raíz LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout.png#lightbox)

-----
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Con la raíz `LinearLayout` seleccionado, volver a la **propiedades** ventana, haga clic en el **alfabético** ordenar icono y desplácese hasta que encuentre `orientation`. Cambiar el `orientation` si se establece en `horizontal`:

![Seleccione la orientación horizontal](designer-walkthrough-images/vs/17-horizontal-orientation.png "Seleccionar orientación horizontal")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Con la raíz `LinearLayout` seleccionado, volver a la **propiedades** ficha y haga clic en **Widget**. Cambiar el `Orientation` si se establece en `horizontal`:

[![Seleccione la orientación horizontal](designer-walkthrough-images/xs/18-horizontal-orientation-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation.png#lightbox)

-----

En este momento, el diseñador debe ser similar a la captura de pantalla siguiente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diseño del diseñador](designer-walkthrough-images/vs/18-designer-layout-sml.png)](designer-walkthrough-images/vs/18-designer-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diseño del diseñador](designer-walkthrough-images/xs/19-designer-layout-sml.png)](designer-walkthrough-images/xs/19-designer-layout.png#lightbox)

-----


### <a name="modifying-the-spacing"></a>Modificar el espaciado

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A continuación, se modificará la configuración de relleno y margen de la interfaz de usuario para proporcionar más espacio entre los widgets. Seleccione el `ImageView`, haga clic en el **por categorías** el icono de búsqueda en el **propiedades** ventana y desplácese hacia abajo hasta la **diseño** sección. Cambiar el `Min Height` a `70dp`, el `Min Width` a `50dp`y el `padding` a `10dp`. Esto aplica relleno en todos los lados de la `ImageView` y se alarga verticalmente:

[![Establecer el relleno](designer-walkthrough-images/vs/19-padding-widths-sml.png)](designer-walkthrough-images/vs/19-padding-widths.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A continuación, se modificará la configuración de relleno y margen de la interfaz de usuario para proporcionar más espacio entre los widgets. Seleccione el `ImageView` y haga clic en el **diseño** en la ficha **propiedades**. Cambiar el `Padding` a `10dp`, el `Min Width` a `50dp`y el `Min Height` a `70dp`. Esto aplica relleno en todos los lados de la `ImageView` y se alarga verticalmente:

[![Establecer el relleno](designer-walkthrough-images/xs/20-padding-widths-sml.png)](designer-walkthrough-images/xs/20-padding-widths.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

La parte inferior, izquierda, derecha y superior relleno configuración se puede establecer de forma independiente mediante la especificación de valores en el `paddingBottom`, `paddingLeft`, `paddingRight`, y `paddingTop` campos, respectivamente.
Por ejemplo, establecer el `paddingLeft` campo `5dp` y `paddingBottom`, `paddingRight`, y `paddingTop` campos a `10dp`:

[![Configuración de relleno personalizada](designer-walkthrough-images/vs/20-custom-padding-sml.png)](designer-walkthrough-images/vs/20-custom-padding.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

La parte superior, derecha, inferior y configuración de relleno del lado izquierdo pueden establecerse de forma independiente mediante la especificación de valores en el `Top`, `Right`, `Bottom`, y `Left` rellenar los campos, respectivamente. Por ejemplo, establecer el `Left` para el valor de relleno `5dp` y `Top`, `Right`, y `Bottom` a los valores de relleno `10dp`. Tenga en cuenta que el `Padding` configuración cambia a una lista separada por comas de los valores siguientes:

[![Configuración de relleno personalizada](designer-walkthrough-images/xs/21-custom-padding-sml.png)](designer-walkthrough-images/xs/21-custom-padding.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A continuación, ajustar la posición de la `LinearLayout` widget que contiene las dos `TextView` widgets. En el **esquema del documento**, seleccione `linearLayout1`. En el **propiedades** ventana, desplácese hasta la **diseño - Vista ViewGroup** sección. Establecer `layout_marginBottom`, `layout_marginLeft`, `layout_marginRight`, y `layout_marginTop` a `5dp`, `5dp`, `0dp`, y `5dp` respectivamente:

[![Establecer los márgenes](designer-walkthrough-images/vs/21-margins-sml.png)](designer-walkthrough-images/vs/21-margins.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A continuación, ajustar la posición de la `LinearLayout` widget que contiene las dos `TextView` widgets. En el **esquema del documento**, seleccione `linearLayout1`. En el **propiedades** panel, seleccione la **diseño** ficha. Desplácese hacia abajo hasta la **vista ViewGroup** sección y establezca la `Left`, `Top`, `Right`, y `Bottom` márgenes a `5dp`, `5dp`, `0dp`, y `5dp` respectivamente:

[![Establecer los márgenes](designer-walkthrough-images/xs/22-margins-sml.png)](designer-walkthrough-images/xs/22-margins.png#lightbox)

-----



### <a name="removing-the-default-image"></a>Quitar la imagen predeterminada

Como vamos a usar el `ImageView` para mostrar colores (en lugar de imágenes), vamos a quitar el origen de la imagen predeterminada agregado por la plantilla.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Seleccione el control `ImageView`.

2.  En **propiedades**, busque la `src` campo.

3.  Desactive el `src` configuración para que esté en blanco:

![Borrar el valor de src ImageView](designer-walkthrough-images/vs/22-clear-img-src.png "borrar el valor de src ImageView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Seleccione el control `ImageView`.

2.  Haga clic en el **Widget** en la ficha **propiedades**.

3.  Desactive el `Src` configuración para que esté en blanco:

[![Borrar el valor de src ImageView](designer-walkthrough-images/xs/23-clear-src-sml.png)](designer-walkthrough-images/xs/23-clear-src.png#lightbox)

-----


### <a name="adding-a-listview-container"></a>Agregar un contenedor de ListView

Ahora que el **ListItem** se define el diseño, se agregará un `ListView` para el diseño principal. Esto `ListView` contendrá una lista de **ListItems**.
En el **cuadro de herramientas**, busque la `ListView` widget y arrástrelo hasta la superficie de diseño. El `ListView` en el diseñador se quedan en blanco excepto líneas azules que indican su borde cuando se selecciona. Puede ver el **esquema del documento** para comprobar que la **ListView** se agregó correctamente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![ListView nueva](designer-walkthrough-images/vs/23-new-listview.png "nueva ListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![ListView nueva](designer-walkthrough-images/xs/24-new-listview-sml.png)](designer-walkthrough-images/xs/24-new-listview.png#lightbox)

-----

De forma predeterminada, el `ListView` tiene un `Id` valo `@+id/listView1`.
Abra la **Widget** en la ficha **propiedades** y cambie el `Id` a `@+id/myListView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cambiar el nombre de Id. de myListView](designer-walkthrough-images/vs/24-change-id.png "Id. de cambio de nombre a myListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Cambiar el nombre de Id. de myListView](designer-walkthrough-images/xs/25-change-id-sml.png)](designer-walkthrough-images/xs/25-change-id.png#lightbox)

-----

En este punto, la interfaz de usuario está lista para su uso.



### <a name="running-the-application"></a>Ejecutar la aplicación


Abra **MainActivity.cs** y reemplace el código con lo siguiente:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity (Label = "DesignerWalkthrough", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem> ();
        ListView listView;

        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);

            SetContentView (Resource.Layout.Main);
            listView = FindViewById<ListView> (Resource.Id.myListView);

            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.DarkRed,
                                               ColorName = "Dark Red", Code = "8B0000" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.SlateBlue,
                                               ColorName = "Slate Blue", Code = "6A5ACD" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.ForestGreen,
                                               ColorName = "Forest Green", Code = "228B22" });

            listView.Adapter = new ColorAdapter (this, colorItems);
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
        public override View GetView (int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.ListItem, null);
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

Este código usa un personalizado `ListView` adaptador para cargar la información de color y mostrar estos datos en la interfaz de usuario que hemos acabamos de crear. Para mantener este ejemplo breve, la información de color está codificado de forma rígida en una lista, pero se podría modificar el adaptador para extraer información de color de un origen de datos o calcular sobre la marcha. Para obtener más información acerca de `ListView` adaptadores, vea [ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md).

Compile y ejecute la aplicación. Captura de pantalla siguiente es un ejemplo de cómo la aplicación aparece cuando se ejecuta en un dispositivo:

[![Captura de pantalla final](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)



## <a name="summary"></a>Resumen

En este artículo, hemos visto cómo utilizar el Diseñador de Xamarin.Android en Visual Studio para Mac para crear una interfaz de usuario. A continuación, se muestra cómo crear la interfaz para un elemento único en una lista.
A lo largo de la forma, explicamos cómo agregar widgets y prepararlos visualmente, así como cómo asignar recursos y para establecer diversas propiedades de los widgets. En conclusión, se muestra cómo la interfaz que hemos creado en el diseñador se ejecuta en una aplicación de ejemplo.
