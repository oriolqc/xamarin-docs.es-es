---
title: Vistas nativo en XAML
description: "Pueden hacer referencia directamente a vistas nativo de la plataforma Universal de Windows, iOS y Android desde archivos XAML de Xamarin.Forms. Propiedades y los controladores de eventos se pueden establecer en vistas nativo y pueden interactuar con vistas de Xamarin.Forms. Este artículo demuestra cómo utilizar vistas nativo de archivos XAML de Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: f4345e107a32c3a583c246fe5dbe24590960c870
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="native-views-in-xaml"></a>Vistas nativo en XAML

_Pueden hacer referencia directamente a vistas nativo de la plataforma Universal de Windows, iOS y Android desde archivos XAML de Xamarin.Forms. Propiedades y los controladores de eventos se pueden establecer en vistas nativo y pueden interactuar con vistas de Xamarin.Forms. Este artículo demuestra cómo utilizar vistas nativo de archivos XAML de Xamarin.Forms._

En este artículo se trata los temas siguientes:

- [Consumir vistas nativo](#consuming) – el proceso para utilizar una vista nativa de XAML.
- [Utilización de enlaces nativo](#native_bindings) : enlace hacia y desde las propiedades de vistas nativo de datos.
- [Pasar argumentos a vistas nativo](#passing_arguments) : pasar argumentos a los constructores de la vista nativa y una llamada a vista nativa métodos de generador.
- [Para hacer referencia a vistas nativas de código](#native_view_code) : recuperar instancias de la vista nativa declarados en un archivo XAML, el archivo de código subyacente.
- [Creación de subclases de vistas nativo](#subclassing) : creación de subclases de vistas nativo para definir una API sencilla y compatible con XAML.  

<a name="overview" />

## <a name="overview"></a>Información general

Para incrustar una vista nativo en un archivo XAML de Xamarin.Forms:

1. Agregar un `xmlns` declaración de espacio de nombres en el archivo XAML para el espacio de nombres que contiene la vista nativa.
1. Cree una instancia de la vista nativa en el archivo XAML.

> [!NOTE]
> XAMLC debe estar desactivada para las páginas XAML que utilizan vistas nativo.

Para hacer referencia a una vista nativa desde un archivo de código subyacente, debe utilizar un proyecto de recurso compartido (SAP) y ajustar el código específico de la plataforma con directivas de compilación condicional. Para obtener más información, consulte [haciendo referencia a vistas nativo desde el código](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Consumir vistas nativo

En el ejemplo de código siguiente se muestra cómo consumir vistas nativo para cada plataforma para un Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Así como especificar el `clr-namespace` y `assembly` para un espacio de nombres de vista nativo, un `targetPlatform` también debe especificarse. Esta propiedad debe establecerse en uno de los valores de la [ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/) enumeración y normalmente se establecerá en `iOS`, `Android`, o `Windows`. En tiempo de ejecución, el analizador de XAML pasará por alto cualquier prefijo de espacio de nombres XML que tienen un `targetPlatform` que no coincide con la plataforma en la que se ejecuta la aplicación.

Cada declaración de espacio de nombres puede utilizarse para hacer referencia a cualquier clase o estructura de espacio de nombres especificado. Por ejemplo, el `ios` declaración de espacio de nombres puede utilizarse para hacer referencia a cualquier clase o estructura en la de iOS `UIKit` espacio de nombres. Propiedades de la vista nativa se pueden establecer mediante XAML, pero deben coincidir con los tipos de propiedad y el objeto. Por ejemplo, el `UILabel.TextColor` propiedad está establecida en `UIColor.Red` mediante la `x:Static` extensión de marcado y el `ios` espacio de nombres.

Propiedades enlazables y adjuntadas propiedades enlazables también pueden establecerse en vistas nativo utilizando el `Class.BindableProperty="value"` sintaxis. Cada vista nativo se ajusta en una específica de la plataforma `NativeViewWrapper` instancia, que se deriva de la [ `Xamarin.Forms.View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) clase. Al establecer una propiedad enlazable o propiedad enlazable adjunta en una vista nativo transfiere el valor de propiedad para el contenedor. Por ejemplo, puede especificarse un diseño horizontal centrado estableciendo `View.HorizontalOptions="Center"` en la vista nativa.

> [!NOTE]
> Tenga en cuenta que no se puede usar estilos con vistas nativo, como estilos solo pueden tener como destino propiedades que están respaldados por `BindableProperty` objetos.

Constructores de widget Android suele ser necesitan el Android `Context` objeto como argumento y esto pueden ponerse a disposición a través de una propiedad estática en la `MainActivity` clase. Por lo tanto, al crear un widget de Android en XAML, el `Context` objeto generalmente se debe pasar al constructor del widget mediante el `x:Arguments` atribuir a un `x:Static` extensión de marcado. Para obtener más información, consulte [pasar argumentos a vistas Native](#passing_arguments).

> [!NOTE]
> Tenga en cuenta que una vista nativa con nombres `x:Name` no es posible en un proyecto de biblioteca de clases portables (PCL) o un proyecto de Asset compartida (SAP). Si lo hace, se generará una variable del tipo nativo, lo que provocará un error de compilación. Sin embargo, pueden ajustar vistas nativo en `ContentView` instancias y se recuperan en el archivo de código subyacente, siempre que se está usando un SAP. Para obtener más información, consulte [que hace referencia a una vista nativas desde código](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Enlaces nativo

Enlace de datos se utiliza para sincronizar una interfaz de usuario con su origen de datos y simplifica la forma en que una aplicación de Xamarin.Forms muestra e interactúa con sus datos. Siempre que el objeto de origen implementa la `INotifyPropertyChanged` cambios en las interfaces, en el *origen* objeto se inserta automáticamente en el *destino* objeto por el marco de enlace y cambios en el *destino* objeto opcionalmente se puede insertar en el *origen* objeto.

Propiedades de vistas nativo también pueden utilizar el enlace de datos. En el ejemplo de código siguiente se muestra el enlace de datos mediante las propiedades de vistas nativo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

La página contiene una [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) cuyo [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) propiedad se enlaza a la `NativeSwitchPageViewModel.IsSwitchOn` propiedad. El [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de la página se establece en una nueva instancia de la `NativeSwitchPageViewModel` clase en el archivo de código subyacente, con la clase ViewModel que implementa el `INotifyPropertyChanged` interfaz.

La página también contiene un conmutador para cada plataforma. Cada conmutador nativo usa una [ `TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) enlace para actualizar el valor de la `NativeSwitchPageViewModel.IsSwitchOn` propiedad. Por lo tanto, cuando el conmutador está desactivado, el `Entry` está deshabilitado, y cuando el modificador está activado, el `Entry` está habilitado. Las capturas de pantalla siguientes muestran esta funcionalidad en cada plataforma:

![](xaml-images/native-switch-disabled.png "Conmutador deshabilitado")
![](xaml-images/native-switch-enabled.png "conmutador habilitado")

Automáticamente se admiten enlaces bidireccionales siempre que implemente la propiedad nativa `INotifyPropertyChanged`, admite la observación de clave y valor (KVO) en iOS, o es una `DependencyProperty` en UWP. Sin embargo, muchas vistas nativo no admiten la notificación de cambio de propiedad. Para estas vistas, puede especificar un [ `UpdateSourceEventName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.UpdateSourceEventName/) valor de propiedad como parte de la expresión de enlace. Esta propiedad debe establecerse en el nombre de un evento de la vista nativa que indica cuándo ha cambiado la propiedad de destino. A continuación, cuando cambia el valor del modificador nativo, el `Binding` clase recibirá una notificación de que el usuario ha cambiado el valor del modificador y el `NativeSwitchPageViewModel.IsSwitchOn` se actualiza el valor de propiedad.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Pasar argumentos a vistas nativo

Argumentos de constructor pueden pasarse a vistas nativo utilizando el `x:Arguments` atribuir a un `x:Static` extensión de marcado. Asimismo, los métodos de generador de vista nativo (`public static` métodos que devuelven objetos o valores del mismo tipo que la clase o estructura que define los métodos) se puede llamar mediante la especificación del método nombre con el `x:FactoryMethod` atributo y sus argumentos mediante el `x:Arguments` atributo.

En el ejemplo de código siguiente se muestra dos técnicas:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

El [ `UIFont.FromName` ](https://developer.xamarin.com/api/member/UIKit.UIFont.FromName/) método generador se usa para establecer el [ `UILabel.Font` ](https://developer.xamarin.com/api/property/UIKit.UILabel.Font/) propiedad a una nueva [ `UIFont` ](https://developer.xamarin.com/api/type/UIKit.UIFont/) en iOS. El `UIFont` nombre y el tamaño se especifican los argumentos de método que son elementos secundarios de la `x:Arguments` atributo.

El [ `Typeface.Create` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/p/System.String/Android.Graphics.TypefaceStyle/) método generador se usa para establecer el [ `TextView.Typeface` ](https://developer.xamarin.com/api/property/Android.Widget.TextView.Typeface/) propiedad a una nueva [ `Typeface` ](https://developer.xamarin.com/api/type/Android.Graphics.Typeface/) en Android. El `Typeface` nombre de familia y estilo se especifican mediante los argumentos del método que son elementos secundarios de la `x:Arguments` atributo.

El [ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) constructor se usa para establecer el [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) propiedad a una nueva `FontFamily` en la plataforma Universal de Windows (UWP). El `FontFamily` nombre se especifica mediante el argumento del método que es un elemento secundario de la `x:Arguments` atributo.

> [!NOTE]
> Argumentos deben coincidir con los tipos requeridos por el constructor o método generador.

Las capturas de pantalla siguientes muestran el resultado de especificar los argumentos de método y el constructor de fábrica para establecer la fuente en distintas vistas nativo:

![](xaml-images/passing-arguments.png "Establecer fuentes en vistas nativo")

Para obtener más información acerca de cómo pasar argumentos en XAML, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Para hacer referencia a vistas nativas de código

Aunque no es posible para el nombre de una vista nativo con el `x:Name` atributo, es posible recuperar una instancia de la vista nativa declarada en un archivo XAML de su archivo de código subyacente en un proyecto de acceso compartido, siempre que la vista nativo es un elemento secundario de un [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) que especifica un `x:Name` valor de atributo. A continuación, dentro de las directivas de compilación condicional en el archivo de código subyacente debe ser:

1. Recuperar el [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propiedad valor y convertirlo en una específica de la plataforma `NativeViewWrapper` tipo.
1. Recuperar el `NativeViewWrapper.NativeElement` propiedad y convertirlo en el tipo de vista nativo.

A continuación, se puede invocar la API nativa en la vista nativa para realizar las operaciones deseadas. Este enfoque también ofrece la ventaja que varias vistas XAML nativas para diferentes plataformas pueden ser elementos secundarios del mismo [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/). En el ejemplo de código siguiente se muestra esta técnica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
            <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

En el ejemplo anterior, las vistas nativo para cada plataforma son elementos secundarios de [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) controles, con el `x:Name` valor de atributo que se va a utilizar para recuperar el `ContentView` en el código subyacente:

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

El [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) acceso a la propiedad para recuperar la vista nativa ajustada como un específico de la plataforma `NativeViewWrapper` instancia. El `NativeViewWrapper.NativeElement` , a continuación, se obtiene acceso de propiedad para recuperar la vista nativa como tipo nativo. API de la vista nativo, a continuación, se invoca para llevar a cabo las operaciones deseadas.

IOS y Android botones nativos comparten la misma `OnButtonTap` controlador de eventos, porque cada botón nativo consume una `EventHandler` delegar en respuesta a un evento de entrada táctil. Sin embargo, la plataforma Universal de Windows (UWP) usa un independiente `RoutedEventHandler`, que a su vez los consume el `OnButtonTap` controlador de eventos en este ejemplo. Por lo tanto, cuando un botón nativo se hace clic, el `OnButtonTap` ejecuta el controlador de eventos, que escala y gira el control nativo dentro de la [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) denominado `contentViewTextParent`. Las capturas de pantalla siguientes muestran este que se producen en cada plataforma:

![](xaml-images/contentview.png "ContentView que contiene un Control nativo")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Creación de subclases de vistas nativo

Muchos iOS y Android vistas nativo no son adecuadas para crear instancias en XAML porque usan métodos, en lugar de propiedades, para configurar el control. La solución a este problema consiste en vistas nativo de subclase de contenedores que definen una API sencilla y compatible con XAML más que utilice propiedades para el control de la instalación, y eventos de independiente de la plataforma. Las vistas nativo ajustadas pueden, a continuación, se coloca en un proyecto de recurso compartido (SAP) y encierre entre las directivas de compilación condicional, o colocar en los proyectos específicos de la plataforma y hace referencia a partir de XAML en un proyecto de biblioteca de clases portables (PCL).

En el ejemplo de código siguiente se muestra una página de Xamarin.Forms que consume una subclase vistas nativo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

La página contiene un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) que muestra las frutas elegidas por el usuario de un control nativo. El `Label` se enlaza a la `SubclassedNativeControlsPageViewModel.SelectedFruit` propiedad. El [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de la página se establece en una nueva instancia de la `SubclassedNativeControlsPageViewModel` clase en el archivo de código subyacente, con la clase ViewModel que implementa el `INotifyPropertyChanged` interfaz.

La página también contiene una vista de selector nativo para cada plataforma. Cada vista nativo muestra la colección de frutas enlazando su `ItemSource` propiedad a la `SubclassedNativeControlsPageViewModel.Fruits` colección. Esto permite al usuario elegir una frutas, tal y como se muestra en las capturas de pantalla siguiente:

![](xaml-images/sub-classed.png "Subclase vistas nativo")

En iOS y Android los selectores nativo use métodos para configurar los controles. Por lo tanto, deben ser una subclase estos selectores para exponer propiedades para que sean fáciles de usar XAML. En la plataforma Universal de Windows (UWP), el `ComboBox` ya es compatible con XAML y, por lo que no requiere la creación de subclases.

### <a name="ios"></a>iOS

Las subclases de la implementación de iOS la [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) vista y expone propiedades y un evento que puede consumir fácilmente a partir de XAML:

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

El `MyUIPickerView` clase expone `ItemsSource` y `SelectedItem` propiedades y un `SelectedItemChanged` eventos. A [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) requiere una subyacente [ `UIPickerViewModel` ](https://developer.xamarin.com/api/type/UIKit.UIPickerViewModel/) data model, que se obtiene acceso a la `MyUIPickerView` propiedades y eventos. El `UIPickerViewModel` modelo de datos proporcionado por el `PickerModel` clase:

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

El `PickerModel` clase proporciona el almacenamiento subyacente para el `MyUIPickerView` (clase), a través de la `Items` propiedad. Cada vez que el elemento seleccionado en el `MyUIPickerView` cambios, el [ `Selected` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.Selected/) se ejecuta el método, que actualiza el índice seleccionado y se activa la `ItemChanged` eventos. Esto garantiza que la `SelectedItem` propiedad siempre devolverá el último elemento que se ha seleccionado por el usuario. Además, el `PickerModel` clase invalida los métodos que se usan para el programa de instalación el `MyUIPickerView` instancia.

### <a name="android"></a>Android

Las subclases de implementación Android el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) vista y expone propiedades y un evento que puede consumir fácilmente a partir de XAML:

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

El `MySpinner` clase expone `ItemsSource` y `SelectedObject` propiedades y un `ItemSelected` eventos. Los elementos mostrados por el `MySpinner` clase proporcionados por el [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.Adapter/) asociado a la vista y los elementos se rellenan en el `Adapter` cuando el `ItemsSource` primero se establece la propiedad. Cada vez que el elemento seleccionado en el `MySpinner` clase cambios, el `OnBindableSpinnerItemSelected` actualizaciones del controlador de eventos el `SelectedObject` propiedad.

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar vistas nativo de archivos XAML de Xamarin.Forms. Propiedades y los controladores de eventos se pueden establecer en vistas nativo y pueden interactuar con vistas de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [NativeSwitch (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeSwitch/)
- [Forms2Native (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Forms2Native/)
- [NativeViewInsideContentView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeViewInsideContentView/)
- [SubclassedNativeControls (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/SubclassedNativeControls/)
- [Formularios nativos](~/xamarin-forms/platform/native-forms.md)
- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
