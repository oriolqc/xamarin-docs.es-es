---
title: Personalizar ViewCell
description: Xamarin.Forms ViewCell es una celda que se puede agregar a un ListView o TableView, que contiene una vista definida por el desarrollador. En este artículo se muestra cómo crear a un representador personalizado para ViewCell que se hospeda dentro de un control ListView de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 3cb4d7f152e0f9540275f12f0ade568cd0552784
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935581"
---
# <a name="customizing-a-viewcell"></a>Personalizar ViewCell

_Xamarin.Forms ViewCell es una celda que se puede agregar a un ListView o TableView, que contiene una vista definida por el desarrollador. En este artículo se muestra cómo crear a un representador personalizado para ViewCell que se hospeda dentro de un control ListView de Xamarin.Forms. Esto detiene los cálculos de diseño de Xamarin.Forms que se llama varias veces durante el desplazamiento de ListView._

Cada celda de Xamarin.Forms tiene un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. Cuando un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) se representa mediante una aplicación de Xamarin.Forms, en iOS el `ViewCellRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `UITableViewCell` control. En la plataforma Android, el `ViewCellRenderer` nativo crea una instancia de clase `View` control. En la plataforma Universal de Windows (UWP), el `ViewCellRenderer` nativo crea una instancia de clase `DataTemplate`. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y el representador, consulte [clases Base del representador y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama ilustra la relación entre el [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) y los controles nativos correspondientes que lo implementan:

![](viewcell-images/viewcell-classes.png "Relación entre el Control ViewCell y los controles nativos de implementación")

El proceso de representación puede aprovecharse para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Cell) una celda personalizada de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Cell) la celda personalizada de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para la celda en cada plataforma.

Cada elemento ahora se explicará a su vez, para implementar un `NativeCell` representador que aprovecha las ventajas de un diseño específico de la plataforma para cada celda que se hospeda dentro de un Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) control. Esto detiene los cálculos de diseño de Xamarin.Forms desde que se llama varias veces durante `ListView` desplazamiento.

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>Creación de la celda personalizada

Se puede crear un control de celda personalizado mediante la creación de subclases del [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) clase, como se muestra en el ejemplo de código siguiente:

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```
La `NativeCell` clase se crea en el proyecto de biblioteca estándar de .NET y define la API para la celda personalizada. La celda personalizada expone `Name`, `Category`, y `ImageFilename` propiedades que se pueden mostrar mediante enlace de datos. Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>Consumo de la celda personalizada

El `NativeCell` celda personalizada puede hacer referencia en Xaml en el proyecto de biblioteca estándar de .NET al declarar un espacio de nombres para su ubicación y utilizando el prefijo de espacio de nombres en el elemento de celda personalizado. El siguiente ejemplo de código muestra cómo el `NativeCell` celda personalizada puede utilizarse en una página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <StackLayout>
              <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
              <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                  <ListView.ItemTemplate>
                      <DataTemplate>
                          <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                      </DataTemplate>
                  </ListView.ItemTemplate>
              </ListView>
          </StackLayout>
      </ContentPage.Content>
</ContentPage>
```

El `local` prefijo de espacio de nombres puede tener cualquier nombre. Sin embargo, el `clr-namespace` y `assembly` valores deben coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se utiliza para hacer referencia a la celda personalizada.

El siguiente ejemplo de código muestra cómo el `NativeCell` celda personalizada puede utilizarse en una página de C#:

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
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

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Un objeto Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) control se usa para mostrar una lista de los datos que se rellenan a través de la [ `ItemSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemsSource/) propiedad. El [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché intenta minimizar la `ListView` velocidad de consumo de memoria y en ejecución mediante el reciclado de celdas de la lista. Para obtener más información, consulte [estrategia de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

Cada fila de la lista contiene tres elementos de datos, un nombre, una categoría y un nombre de archivo de imagen. El diseño de cada fila de la lista está definido por el `DataTemplate` que hace referencia a través de la [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemTemplate/) propiedad enlazable. El `DataTemplate` define que cada fila de datos en la lista será un `NativeCell` que muestra su `Name`, `Category`, y `ImageFilename` propiedades mediante enlace de datos. Para obtener más información sobre la `ListView` control, vea [ListView](~/xamarin-forms/user-interface/listview/index.md).

Ahora se puede agregar a un representador personalizado a cada proyecto de aplicación para personalizar el diseño específico de la plataforma para cada celda.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `ViewCellRenderer` clase que representa la celda personalizada.
1. Invalide el método específico de la plataforma que representa la celda personalizada y escribir una lógica para personalizarla.
1. Agregar un `ExportRenderer` a la clase de representador personalizado para especificar que se utilizará para representar la celda personalizada de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no está registrado un representador personalizado, se usará el representador predeterminado de la clase base del control. Sin embargo, los representadores personalizados son necesarios en cada proyecto de la plataforma al representar un [ViewCell](xref:Xamarin.Forms.ViewCell) elemento.

El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](viewcell-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado NativeCell")

El `NativeCell` celda personalizada se representa mediante las clases de representador específica de la plataforma, que se derivan de la `ViewCellRenderer` clase para cada plataforma. Esto da como resultado de cada `NativeCell` celda personalizada que se representa con diseño específico de la plataforma, como se muestra en las capturas de pantalla siguiente:

![](viewcell-images/screenshots.png "NativeCell en cada plataforma")

La `ViewCellRenderer` clase expone métodos específicos de la plataforma para representar la celda personalizada. Se trata de la `GetCell` método en la plataforma iOS, el `GetCellCore` método en la plataforma Android y el `GetTemplate` método en UWP.

Cada clase de presentador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo de la celda de Xamarin.Forms que se procesa y el nombre de tipo del representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes describen la implementación de cada clase de representador personalizado específico de la plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

El ejemplo de código siguiente muestra al representador personalizado para la plataforma de iOS:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

El `GetCell` método se llama para crear cada celda que se mostrará. Cada celda es una `NativeiOSCell` instancia, que define el diseño de la celda y sus datos. La operación de la `GetCell` método depende de si el [ `ListView` ](xref:Xamarin.Forms.ListView) estrategia de almacenamiento:

- Cuando el [ `ListView` ](xref:Xamarin.Forms.ListView) estrategia de almacenamiento en caché es [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), el `GetCell` se invocará el método para cada celda. Un `NativeiOSCell` se crea para cada instancia `NativeCell` instancia que se muestra inicialmente en la pantalla. Cuando el usuario se desplaza a través de la `ListView`, `NativeiOSCell` serán volver a usar las instancias. Para obtener más información acerca de la celda iOS volver a usar, vea [reutilizar celdas](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Este código de representador personalizado llevará a cabo alguna celda volver a usar incluso cuando el [ `ListView` ](xref:Xamarin.Forms.ListView) se establece para conservar las celdas.

  Los datos mostrados por cada `NativeiOSCell` instancia, si recién creado o volver a utilizarse, se actualizará con los datos de cada `NativeCell` instancia por el `UpdateCell` método.

  > [!NOTE]
  > El `OnNativeCellPropertyChanged` nunca será método se invoca cuando el [ `ListView` ](xref:Xamarin.Forms.ListView) estrategia de almacenamiento en caché se establece para conservar las celdas.

- Cuando el [ `ListView` ](xref:Xamarin.Forms.ListView) estrategia de almacenamiento en caché es [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), el `GetCell` se invocará el método para cada celda que se muestra inicialmente en la pantalla. Un `NativeiOSCell` se crea para cada instancia `NativeCell` instancia que se muestra inicialmente en la pantalla. Los datos mostrados por cada `NativeiOSCell` instancia se actualizará con los datos de la `NativeCell` instancia por el `UpdateCell` método. Sin embargo, el `GetCell` no se invoca el método como el usuario se desplaza por la `ListView`. En su lugar, el `NativeiOSCell` serán volver a usar las instancias. `PropertyChanged` eventos, se generará en el `NativeCell` cuando cambian sus datos, la instancia y el `OnNativeCellPropertyChanged` controlador de eventos actualizará los datos en cada reutilizarse `NativeiOSCell` instancia.

El siguiente ejemplo de código muestra la `OnNativeCellPropertyChanged` método que se invoca cuando un `PropertyChanged` provoca el evento:

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Volver a utiliza este método actualiza los datos mostrados por `NativeiOSCell` instancias. Se realiza una comprobación para la propiedad que se ha modificado, como el método puede llamarse varias veces.

La `NativeiOSCell` clase define el diseño de cada celda y se muestra en el ejemplo de código siguiente:

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Esta clase define los controles utilizados para representar el contenido de la celda y su diseño. La clase implementa la [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) interfaz, que es necesaria cuando la [ `ListView` ](xref:Xamarin.Forms.ListView) usa el [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché. Esta interfaz especifica que la clase debe implementar la [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) propiedad, que debe devolver los datos de celda personalizada para las celdas reciclados.

El `NativeiOSCell` constructor inicializa la apariencia de la `HeadingLabel`, `SubheadingLabel`, y `CellImageView` propiedades. Estas propiedades se utilizan para mostrar los datos almacenados en el `NativeCell` instancia, con el `UpdateCell` método que se llama para establecer el valor de cada propiedad. Además, cuando el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) usa el [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) almacenamiento en caché de estrategia, los datos mostrados por la `HeadingLabel`, `SubheadingLabel`, y `CellImageView` las propiedades pueden ser actualiza el `OnNativeCellPropertyChanged` método en el representador personalizado.

Diseño de la celda se realiza mediante el `LayoutSubviews` invalidar, que establece las coordenadas de `HeadingLabel`, `SubheadingLabel`, y `CellImageView` dentro de la celda.

### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

El `GetCellCore` método se llama para crear cada celda que se mostrará. Cada celda es una `NativeAndroidCell` instancia, que define el diseño de la celda y sus datos. La operación de la `GetCellCore` método depende de si el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estrategia de almacenamiento:

- Cuando el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estrategia de almacenamiento en caché es [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), el `GetCellCore` se invocará el método para cada celda. Un `NativeAndroidCell` se crearán para cada `NativeCell` instancia que se muestra inicialmente en la pantalla. Cuando el usuario se desplaza a través de la `ListView`, `NativeAndroidCell` serán volver a usar las instancias. Para obtener más información acerca de la celda Android volver a usar, vea [reutilización de vista fila](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Tenga en cuenta que este código de representador personalizado llevará a cabo alguna celda volver a usar incluso cuando el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se establece para conservar las celdas.

  Los datos mostrados por cada `NativeAndroidCell` instancia, si recién creado o volver a utilizarse, se actualizará con los datos de cada `NativeCell` instancia por el `UpdateCell` método.

  > [!NOTE]
  > Tenga en cuenta que mientras el `OnNativeCellPropertyChanged` será el método se invoca cuando el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) es establece para conservar las celdas, no se actualizará la `NativeAndroidCell` los valores de propiedad.

- Cuando el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estrategia de almacenamiento en caché es [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), el `GetCellCore` se invocará el método para cada celda que se muestra inicialmente en la pantalla. Un `NativeAndroidCell` se crea para cada instancia `NativeCell` instancia que se muestra inicialmente en la pantalla. Los datos mostrados por cada `NativeAndroidCell` instancia se actualizará con los datos de la `NativeCell` instancia por el `UpdateCell` método. Sin embargo, el `GetCellCore` no se invoca el método como el usuario se desplaza por la `ListView`. En su lugar, el `NativeAndroidCell` serán volver a usar las instancias.  `PropertyChanged` eventos, se generará en el `NativeCell` cuando cambian sus datos, la instancia y el `OnNativeCellPropertyChanged` controlador de eventos actualizará los datos en cada reutilizarse `NativeAndroidCell` instancia.

El siguiente ejemplo de código muestra la `OnNativeCellPropertyChanged` método que se invoca cuando un `PropertyChanged` provoca el evento:

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Volver a utiliza este método actualiza los datos mostrados por `NativeAndroidCell` instancias. Se realiza una comprobación para la propiedad que se ha modificado, como el método puede llamarse varias veces.

La `NativeAndroidCell` clase define el diseño de cada celda y se muestra en el ejemplo de código siguiente:

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

Esta clase define los controles utilizados para representar el contenido de la celda y su diseño. La clase implementa la [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) interfaz, que es necesaria cuando la [ `ListView` ](xref:Xamarin.Forms.ListView) usa el [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché. Esta interfaz especifica que la clase debe implementar la [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) propiedad, que debe devolver los datos de celda personalizada para las celdas reciclados.

El `NativeAndroidCell` constructor aumenta la `NativeAndroidCell` diseño e inicializa el `HeadingTextView`, `SubheadingTextView`, y `ImageView` propiedades a los controles en el diseño aumentado. Estas propiedades se utilizan para mostrar los datos almacenados en el `NativeCell` instancia, con el `UpdateCell` método que se llama para establecer el valor de cada propiedad. Además, cuando el [ `ListView` ](xref:Xamarin.Forms.ListView) usa el [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) almacenamiento en caché de estrategia, los datos mostrados por la `HeadingTextView`, `SubheadingTextView`, y `ImageView` las propiedades pueden ser actualiza el `OnNativeCellPropertyChanged` método en el representador personalizado.

El ejemplo de código siguiente muestra la definición de diseño para el `NativeAndroidCell.axml` archivo de diseño:

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
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
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

Este diseño especifica que dos `TextView` controles y un `ImageView` control se usan para mostrar el contenido de la celda. Los dos `TextView` controles están orientados verticalmente dentro de un `LinearLayout` control, con todos los controles que sean independientes dentro de un `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creando al representador personalizado en UWP

En el ejemplo de código siguiente se muestra al representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

El `GetTemplate` método se llama para devolver la celda que se va a representar para cada fila de datos en la lista. Crea un `DataTemplate` para cada `NativeCell` instancia que se mostrará en la pantalla, con el `DataTemplate` definir la apariencia y el contenido de la celda.

El `DataTemplate` se almacena en el diccionario de recursos de nivel de aplicación y se muestra en el ejemplo de código siguiente:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
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

El `DataTemplate` especifica los controles utilizados para mostrar el contenido de la celda y su diseño y la apariencia. Dos `TextBlock` controles y un `Image` control se usan para mostrar el contenido de celda mediante enlace de datos. Además, una instancia de la `ConcatImageExtensionConverter` se utiliza para concatenar la `.jpg` extensión para cada nombre de archivo de imagen de archivo. Esto garantiza que el `Image` control puede cargar y presentar la imagen cuando resulta `Source` propiedad está establecida.

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear un representador personalizado para un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) que se hospeda dentro de un Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) control. Esto detiene los cálculos de diseño de Xamarin.Forms desde que se llama varias veces durante `ListView` desplazamiento.


## <a name="related-links"></a>Vínculos relacionados

- [Rendimiento de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
