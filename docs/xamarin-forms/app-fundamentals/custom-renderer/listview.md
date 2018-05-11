---
title: Personalizar un control ListView
description: Un control Xamarin.Forms ListView es una vista que muestra una colección de datos como una lista vertical. Este artículo demuestra cómo crear a un representador personalizado que encapsula los controles de lista específica de la plataforma y las distribuciones de celda nativo, lo que permite más control sobre el rendimiento del control de lista nativo.
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0d1afc2c14b19bbd03244affed494405776a3c99
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="customizing-a-listview"></a>Personalizar un control ListView

_Un control Xamarin.Forms ListView es una vista que muestra una colección de datos como una lista vertical. Este artículo demuestra cómo crear a un representador personalizado que encapsula los controles de lista específica de la plataforma y las distribuciones de celda nativo, lo que permite más control sobre el rendimiento del control de lista nativo._

Cada vista Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Cuando un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se representa mediante una aplicación de Xamarin.Forms, en iOS la `ListViewRenderer` se crea la instancia de clase, que a su vez crea una instancia nativo `UITableView` control. En la plataforma Android, el `ListViewRenderer` clase crea instancias nativo `ListView` control. En la plataforma Universal de Windows (UWP), el `ListViewRenderer` clase crea instancias nativo `ListView` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y representador, consulte [clases del representador Base y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

En el diagrama siguiente ilustra la relación entre el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) control y los controles nativos correspondientes que lo implementan:

![](listview-images/listview-classes.png "Relación entre el Control ListView y los controles nativos de implementación")

El proceso de representación puede tomar ventaja de implementar personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_ListView_Control) un control personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Control) el control personalizado de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el control en cada plataforma.

Cada elemento ahora se tratarán a su vez, para implementar un `NativeListView` representador que aprovecha las ventajas de los controles de lista específica de la plataforma y los diseños de celda nativo. Este escenario es útil al trasladar una aplicación nativa existente que contiene la lista y el código de celda que se puede volver a usar. Además, permite la personalización detallada de las características de control de lista que puede afectar al rendimiento, como la virtualización de datos.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Crear el Control ListView personalizado

Un personalizado [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) control se puede crear subclases el `ListView` de la clase, como se muestra en el ejemplo de código siguiente:

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

El `NativeListView` se crea en el proyecto de biblioteca estándar de .NET y define la API para el control personalizado. Este control expone un `Items` propiedad que se usa para rellenar el `ListView` con datos y que puede estar enlazado a datos de visualización. También expone un `ItemSelected` evento que se desencadena cuando se selecciona un elemento en un control de lista nativo específico de la plataforma. Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumir el Control personalizado

El `NativeListView` control personalizado puede hacer referencia en Xaml en el proyecto de biblioteca estándar de .NET declarando un espacio de nombres para su ubicación y utilizando el prefijo de espacio de nombres en el control. El siguiente ejemplo de código muestra cómo el `NativeListView` control personalizado se pueden usar en una página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
        <Grid>
          <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*" />
          </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
          <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

El `local` prefijo de espacio de nombres puede tener cualquier nombre. Sin embargo, el `clr-namespace` y `assembly` valores deben coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se utiliza para hacer referencia al control personalizado.

El siguiente ejemplo de código muestra cómo el `NativeListView` control personalizado se pueden usar en una página de C#:

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

El `NativeListView` control personalizado utiliza representadores personalizados específico de la plataforma para mostrar una lista de datos, que se rellenan a través de la `Items` propiedad. Cada fila de la lista contiene tres elementos de datos, un nombre, una categoría y un nombre de archivo de imagen. El diseño de cada fila de la lista se define mediante el representador personalizado específico de la plataforma.

> [!NOTE]
> Dado que la `NativeListView` control personalizado se representará mediante controles de lista específica de la plataforma que incluyen la capacidad de desplazamiento, el control personalizado no debe estar hospedado en controles de diseño desplazable como el [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/).

Ahora puede agregarse un representador personalizado a cada proyecto de aplicación para crear controles de lista específica de la plataforma y las distribuciones de celda nativo.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `ListViewRenderer` clase que representa el control personalizado.
1. Invalidar el `OnElementChanged` método que representa la lógica personalizada de control y de escritura para personalizarlo. Este método se llama cuando el correspondiente Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se crea.
1. Agregar un `ExportRenderer` atributo a la clase de representador personalizado para especificar que se utilizará para representar el control personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no se ha registrado un representador personalizado, se usará el representador predeterminado para la clase base de la celda.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](listview-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado NativeListView")

El `NativeListView` control personalizado se representa por clases del representador de específico de la plataforma, que se derivan de la `ListViewRenderer` clase para cada plataforma. Esto da como resultado de cada `NativeListView` control personalizado que se va a representar con controles de lista específica de la plataforma y las distribuciones de celda nativo, como se muestra en las siguientes capturas de pantalla:

![](listview-images/screenshots.png "NativeListView en cada plataforma")

El `ListViewRenderer` clase expone el `OnElementChanged` método, que se llama cuando se crea el control personalizado de Xamarin.Forms para representar el control nativo correspondiente. Este método toma una `ElementChangedEventArgs` parámetro, que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento Xamarin.Forms que el representador *era* adjunta y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo, el `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `NativeListView` instancia.

Una versión reemplazada de la `OnElementChanged` método en cada clase de representador específico de la plataforma, es el lugar donde realice la personalización de control nativo. Una referencia con tipo para el control nativo que se va a usar en la plataforma puede obtenerse a través del `Control` propiedad. Además, una referencia al control Xamarin.Forms que se esté representando puede obtenerse a través de la `Element` propiedad.

Se debe tener cuidado al suscribirse a los controladores de eventos en el `OnElementChanged` método, como se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

El control nativo solo debe configurarse y controladores de eventos suscribirse a cuando se adjunta el representador personalizado a un nuevo elemento de Xamarin.Forms. Del mismo modo, los controladores de eventos que se han suscrito debe cancelar su suscripción solo cuando se asocia el elemento el representador de cambios. Este enfoque le ayudará a crear a un representador personalizado que no sufren pérdidas de memoria.

Una versión reemplazada de la `OnElementPropertyChanged` método en cada clase de representador específico de la plataforma, es el lugar para responder a los cambios de propiedad enlazable en el control personalizado de Xamarin.Forms. Siempre se debe realizar una comprobación de la propiedad que se ha modificado, como esta invalidación puede llamarse muchas veces.

Cada clase de representador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado Xamarin.Forms que se procesa y el nombre del tipo de representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes tratan la implementación de cada clase de representador personalizado específico de la plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado de iOS

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma de iOS:

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

El `UITableView` control se configura mediante la creación de una instancia de la `NativeiOSListViewSource` de la clase, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Esta clase proporciona datos para el `UITableView` control invalidando el `RowsInSection` y `GetCell` métodos desde el `UITableViewSource` clase y exponer mediante una `Items` propiedad que contiene la lista de datos que se mostrará. La clase también proporciona un `RowSelected` invalidación del método que invoca el `ItemSelected` eventos proporcionados por el `NativeListView` control personalizado. Para obtener más información acerca del método de invalidaciones, vea [UITableViewSource subclases](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). El `GetCell` método devuelve un `UITableCellView` que se rellena con datos para cada fila de la lista y se muestra en el ejemplo de código siguiente:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

Este método crea un `NativeiOSListViewCell` instancia para cada fila de datos que se mostrará en la pantalla. El `NativeiOSCell` instancia define el diseño de cada celda y los datos de la celda. Cuando una celda desaparece de la pantalla debido al desplazamiento, la celda estará disponible para su reutilización. Esto evita desperdiciar memoria asegurándose de que sólo hay `NativeiOSCell` instancias para determinar si los datos que se muestran en la pantalla, en lugar de todos los datos en la lista. Para obtener más información sobre la reutilización de celda, vea [celda reutilizar](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). El `GetCell` método también lee el `ImageFilename` propiedad de cada fila de datos, siempre existe y lee la imagen y almacenarla como un `UIImage` instancia antes de actualizar el `NativeiOSListViewCell` instancia con los datos (nombre, categoría e imagen) para la fila.

La `NativeiOSListViewCell` clase define el diseño para cada celda y se muestra en el ejemplo de código siguiente:

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Esta clase define los controles utilizados para representar el contenido de la celda y su diseño. El `NativeiOSListViewCell` constructor crea instancias de `UILabel` y `UIImageView` controla e inicializa su apariencia. Estos controles se utilizan para mostrar datos de cada fila, con el `UpdateCell` método empleado para establecer estos datos en el `UILabel` y `UIImageView` instancias. La ubicación de estas instancias se establece mediante el invalidado `LayoutSubviews` método especificando sus coordenadas dentro de la celda.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Responde a un cambio de propiedad en el Control personalizado

Si el `NativeListView.Items` propiedad cambia debido a los elementos que se agregan a o se quita de la lista, el representador personalizado debe responder mostrando los cambios. Esto puede realizarse al invalidar la `OnElementPropertyChanged` método, que se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

El método crea una nueva instancia de la `NativeiOSListViewSource` clase que proporciona datos para el `UITableView` de control, siempre que el enlazables `NativeListView.Items` propiedad ha cambiado.

### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

Nativo `ListView` control se configura siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Esta configuración implica la creación de una instancia de la `NativeAndroidListViewAdapter` clase que proporciona datos a nativo `ListView` controlar y registrar un controlador de eventos para procesar el `ItemClick` eventos. A su vez, este controlador invocarán el `ItemSelected` eventos proporcionados por el `NativeListView` control personalizado. El `ItemClick` evento está cancelado la suscripción a si se asocia el elemento de Xamarin.Forms el representador de cambios.

El `NativeAndroidListViewAdapter` se deriva de la `BaseAdapter` clase y expone un `Items` propiedad que contiene la lista de datos que se mostrará, así como invalidar el `Count`, `GetView`, `GetItemId`, y `this[int]` métodos. Para obtener más información acerca de estos reemplazos de método, consulte [implementar un ListAdapter](~/android/user-interface/layouts/list-view/populating.md). El `GetView` método devuelve una vista para cada fila, rellenado con datos y se muestra en el ejemplo de código siguiente:

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

El `GetView` método se llama para devolver la celda que se representará, como un `View`, para cada fila de datos en la lista. Crea un `View` instancia para cada fila de datos que se mostrará en la pantalla, con el aspecto de la `View` instancia que se define en un archivo de diseño. Cuando una celda desaparece de la pantalla debido al desplazamiento, la celda estará disponible para su reutilización. Esto evita desperdiciar memoria asegurándose de que sólo hay `View` instancias para determinar si los datos que se muestran en la pantalla, en lugar de todos los datos en la lista. Para obtener más información sobre la reutilización de vista, vea [fila vista volver a usar](~/android/user-interface/layouts/list-view/populating.md).

El `GetView` método también rellena la `View` instancia con datos, incluida la lectura de los datos de imagen desde el nombre de archivo especificado en el `ImageFilename` propiedad.

El diseño de cada dispayed de celda por nativo `ListView` se define en el `NativeAndroidListViewCell.axml` archivo de diseño, que se aumenta en el `LayoutInflater.Inflate` método. En el ejemplo de código siguiente se muestra la definición de diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

Este diseño especifica que dos `TextView` controles y `ImageView` control se usan para mostrar el contenido de la celda. Los dos `TextView` controles se orientan verticalmente dentro de un `LinearLayout` control, con todos los controles sean independientes dentro de un `RelativeLayout`.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Responde a un cambio de propiedad en el Control personalizado

Si el `NativeListView.Items` propiedad cambia debido a los elementos que se agregan a o se quita de la lista, el representador personalizado debe responder mostrando los cambios. Esto puede realizarse al invalidar la `OnElementPropertyChanged` método, que se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

El método crea una nueva instancia de la `NativeAndroidListViewAdapter` clase que proporciona datos a nativo `ListView` de control, siempre que el enlazables `NativeListView.Items` propiedad ha cambiado.

### <a name="creating-the-custom-renderer-on-uwp"></a>Crear al representador personalizado en UWP

En el ejemplo de código siguiente se muestra al representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeUWPListViewRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

Nativo `ListView` control se configura siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Esta configuración implica la configuración de modo nativo `ListView` control responderá a los elementos que se selecciona, rellenar los datos mostrados por el control, definir la apariencia y el contenido de cada celda y registrar un controlador de eventos para procesar el `SelectionChanged` eventos. A su vez, este controlador invocarán el `ItemSelected` eventos proporcionados por el `NativeListView` control personalizado. El `SelectionChanged` evento está cancelado la suscripción a si se asocia el elemento de Xamarin.Forms el representador de cambios.

La apariencia y el contenido de cada nativo `ListView` celda se definen mediante un `DataTemplate` denominado `ListViewItemTemplate`. Esto `DataTemplate` se almacena en el diccionario de recursos de nivel de aplicación y se muestra en el ejemplo de código siguiente:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

El `DataTemplate` especifica los controles utilizados para mostrar el contenido de la celda y su diseño y la apariencia. Dos `TextBlock` controles y `Image` control se usan para mostrar el contenido de la celda a través del enlace de datos. Además, una instancia de la `ConcatImageExtensionConverter` se utiliza para concatenar la `.jpg` extensión para cada nombre de archivo de imagen de archivo. Esto garantiza que la `Image` control puede cargar y presenta la imagen cuando es `Source` se establece la propiedad.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Responde a un cambio de propiedad en el Control personalizado

Si el `NativeListView.Items` propiedad cambia debido a los elementos que se agregan a o se quita de la lista, el representador personalizado debe responder mostrando los cambios. Esto puede realizarse al invalidar la `OnElementPropertyChanged` método, que se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

El método vuelve a rellena nativo `ListView` control con los datos modificados, siempre que el enlazables `NativeListView.Items` propiedad ha cambiado.

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear a un representador personalizado que encapsula los controles de lista específica de la plataforma y las distribuciones de celda nativo, lo que permite más control sobre el rendimiento del control de lista nativo.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererListView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/listview/)
