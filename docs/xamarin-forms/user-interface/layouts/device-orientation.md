---
title: Orientación del dispositivo
description: Este artículo se explica cómo las aplicaciones de Xamarin.Forms de diseño que un aspecto excelente en orientación vertical y horizontal.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: 9245a17423d97887d2032856b10427685b25c29b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244356"
---
# <a name="device-orientation"></a>Orientación del dispositivo

Es importante tener en cuenta cómo se utilizará la aplicación y cómo se puede incorporar orientación horizontal para mejorar la experiencia del usuario. Diseños individuales pueden diseñarse para dar cabida a varias orientaciones y mejor usa el espacio disponible. En el nivel de aplicación, rotación puede habilitar o deshabilitar.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Controlar la orientación

Cuando el uso de Xamarin.Forms, es el método admitido para controlar la orientación del dispositivo que se usará la configuración para cada proyecto individual.

### <a name="ios"></a>iOS

En iOS, la orientación del dispositivo está configurado para las aplicaciones que usan el **Info.plist** archivo. Este archivo incluirá la configuración de orientación para iPhone y iPod, así como opciones para iPad si la aplicación incluye como un destino. Las siguientes son instrucciones específicas para el IDE. Utilice las opciones del IDE en la parte superior de este documento para seleccionar qué instrucciones que le gustaría ver:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En Visual Studio, abra el proyecto de iOS y abra **Info.plist**. El archivo se abrirá en un panel de configuración, a partir de la ficha de información de implementación de iPhone:

![iPhone información de implementación en Visual Studio](device-orientation-images/orientation-vs-iphone.png)

Para configurar la orientación de iPad, seleccione la **iPad información de implementación** ficha situada en la esquina superior izquierda del panel, a continuación, seleccione desde las orientaciones disponibles:

![Orientaciones de dispositivos compatibles en Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En Visual Studio para Mac, abra el proyecto de iOS y abra **Info.plist**. En el **aplicación** ficha, secciones estarán disponibles para establecer la orientación:

![iPhone información de implementación en Visual Studio para Mac](device-orientation-images/orientation-xam-ui.png)

Si prefiere editar los valores mediante una interfaz de editor de valores de clave, seleccione la **origen**> ficha en la parte inferior de la pantalla:

![Admite dispositivos orientaciones en Visual Studio para Mac](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

Para controlar la orientación en Android, abra **MainActivity.cs** y establezca la orientación mediante el atributo al decorar la `MainActivity` clase:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android admite varias opciones para especificar la orientación:

- **Horizontal** &ndash; fuerza la orientación de la aplicación a horizontal, independientemente de los datos de sensor.
- **Vertical** &ndash; fuerza la orientación de la aplicación a vertical, independientemente de los datos de sensor.
- **Usuario** &ndash; hace que la aplicación se mostrará con orientación preferido del usuario.
- **Detrás de** &ndash; provoca la orientación de la aplicación a la misma que la orientación de la [actividad](https://developer.xamarin.com/api/type/Android.App.Activity/) detrás de él.
- **Sensor de** &ndash; provoca la orientación de la aplicación quedará determinado por el sensor, incluso si el usuario ha deshabilitado la rotación automática.
- **SensorLandscape** &ndash; hace que la aplicación utilizar la orientación horizontal durante el uso de datos de sensor para cambiar la dirección de la pantalla está sufriendo (de modo que la pantalla no está considerada como boca abajo).
- **SensorPortrait** &ndash; hace que la aplicación utilizar la orientación vertical durante el uso de datos de sensor para cambiar la dirección de la pantalla está sufriendo (de modo que la pantalla no está considerada como boca abajo).
- **ReverseLandscape** &ndash; hace que la aplicación utilizar la orientación horizontal, orientada hacia la dirección opuesta de habitual, de forma que aparezca "boca abajo".
- **ReversePortrait** &ndash; hace que la aplicación utilizar la orientación vertical, orientada hacia la dirección opuesta de habitual, de forma que aparezca "boca abajo".
- **FullSensor** &ndash; hace que la aplicación confiar en los datos de sensor para seleccionar la orientación correcta (fuera de los 4 posibles).
- **FullUser** &ndash; hace que la aplicación usar las preferencias del usuario orientación. Si está habilitada la rotación automática, pueden utilizar todas las 4 orientaciones.
- **UserLandscape** &ndash; _\[no admite\]_ hace que la aplicación utilizar la orientación horizontal, a menos que el usuario tiene la rotación automática está habilitada, en cuyo caso utilizará el sensor para determinar la orientación. Esta opción interrumpirá la compilación.
- **UserPortrait** &ndash; _\[no admite\]_ hace que la aplicación utilizar la orientación vertical, a menos que el usuario tiene la rotación automática está habilitada, en cuyo caso utilizará el sensor para determinar la orientación. Esta opción interrumpirá la compilación.
- **Bloqueado** &ndash; _\[no admite\]_ hace que la aplicación usar la orientación de la pantalla, lo que resulta en el inicio, sin responder a los cambios en el dispositivo de la física orientación. Esta opción interrumpirá la compilación.

Tenga en cuenta que las API nativas de Android proporcionan mucho control sobre cómo se administra la orientación, incluidas las opciones que contradicen explícitamente al usuario expresan las preferencias.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En la plataforma Universal de Windows (UWP), orientaciones admitidas se establecen en los **Package.appxmanifest** archivo. Abriendo el manifiesto revelará un panel de configuración donde se pueden seleccionar las orientaciones compatibles.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Reaccionar a los cambios de orientación

Xamarin.Forms no ofrece los eventos nativos para notificar a la aplicación de cambios de orientación de código compartido. Sin embargo, el `SizeChanged` eventos de la `Page` se activa cuando el ancho o el alto de la `Page` cambios. Cuando el ancho de la `Page` es mayor que el alto, el dispositivo está en modo horizontal. Para obtener más información, consulte [mostrar una imagen basada en la orientación de la pantalla](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/).

> [!NOTE]
> Hay un paquete de NuGet existente, disponible para recibir notificaciones de cambios de orientación en código compartido. Consulte la [repositorio de GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) para obtener más información.

Como alternativa, es posible reemplazar la [ `OnSizeAllocated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnSizeAllocated(System.Double,System.Double)/) método en un `Page`, insertar cualquier diseño cambiar lógica allí. El `OnSizeAllocated` se llama al método cada vez que un `Page` se asigna un nuevo tamaño, lo que sucede siempre se gira el dispositivo. Tenga en cuenta que la implementación base de `OnSizeAllocated` realiza funciones de diseño importantes, por lo que es importante llamar a la implementación base en la invalidación:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Error al realizar ese paso se producirá en una página no funciona.

Tenga en cuenta que la `OnSizeAllocated` puede llamar al método muchas veces cuando se gira un dispositivo. Cambiar el diseño cada vez que desperdicia recursos y puede provocar parpadeos. Considere el uso de una variable de instancia dentro de la página para realizar el seguimiento de si la orientación es en horizontal o vertical y solo volver a dibujar cuando se produce un cambio:

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

Una vez que se ha detectado un cambio de orientación del dispositivo, puede que desee agregar o quitar vistas adicionales de la interfaz de usuario para reaccionar ante los cambios en el espacio disponible. Por ejemplo, considere la posibilidad de la calculadora integrada en cada plataforma en vertical:

![](device-orientation-images/calculator-portrait.png "Aplicación Calculadora en vertical")

y horizontal:

![](device-orientation-images/calculator-landscape.png "Aplicación Calculadora en horizontal")

Tenga en cuenta que las aplicaciones aprovechar el espacio disponible mediante la adición de más funcionalidad en horizontal.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Diseño dinámico

Es posible a las interfaces de diseño mediante los diseños integrados para que hagan el cambio correctamente cuando se gira el dispositivo. Al diseñar interfaces que seguirán siendo atractiva al responder a los cambios de orientación tenga en cuenta las siguientes reglas generales:

- **Preste atención a proporciones** &ndash; cambios en la orientación pueden causar problemas cuando se realizan determinadas suposiciones con respecto a los índices. Por ejemplo, una vista que tendría una gran cantidad de espacio en el 1/3 del espacio vertical de una pantalla en vertical no encajar en 1/3 del espacio vertical en horizontal.
- **Debe tener cuidado con valores absolutos** &ndash; valores absolutos (píxeles) que tengan sentido en vertical no pueden tener sentido en horizontal. Cuando es necesario efectuar valores absolutos, utilice los diseños anidados para aislar su impacto. Por ejemplo, sería razonable utilizar valores absolutos en un `TableView` `ItemTemplate` cuando la plantilla de elemento tiene un valor de ancho uniforme garantizada.

Las reglas anteriores también se aplican al implementar interfaces por varios tamaños de pantalla y son generalmente considera los procedimientos recomendados. El resto de esta guía explica los ejemplos específicos de los diseños de capacidad de respuesta con cada uno de los diseños principales de Xamarin.Forms.

> [!NOTE]
> Para evitar confusiones, en las secciones siguientes muestran cómo implementar diseños responde con un solo tipo de `Layout` a la vez. En la práctica, a menudo resulta más sencillo mezclar `Layout`s para lograr un diseño deseado mediante la más sencilla o más intuitiva `Layout` para cada componente.

### <a name="stacklayout"></a>StackLayout

Tenga en cuenta la siguiente aplicación, que se muestra en vertical:

![](device-orientation-images/photo-stack-portrait.png "Aplicación de fotografía en vertical")

y horizontal:

![](device-orientation-images/photo-stack-landscape.png "Aplicación de fotografía en horizontal")

Que se consigue con el código XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Algunos C# se usa para cambiar la orientación de `outerStack` en función de la orientación del dispositivo:

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

Tenga en cuenta lo siguiente:

- `outerStack` se ajusta para presentar los controles y la imagen como una pila horizontal o vertical según la orientación, para aprovechar mejor el espacio disponible.


### <a name="absolutelayout"></a>AbsoluteLayout

Tenga en cuenta la siguiente aplicación, que se muestra en vertical:

![](device-orientation-images/photo-abs-portrait.png "Aplicación de fotografía en vertical")

y horizontal:

![](device-orientation-images/photo-abs-landscape.png "Aplicación de fotografía en horizontal")

Que se consigue con el código XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Tenga en cuenta lo siguiente:

- Debido al modo en que se ha diseñado la página, no es necesario para el código de procedimiento para introducir la capacidad de respuesta.
- El `ScrollView` se usa para permitir que la etiqueta esté visible incluso cuando el alto de la pantalla es menor que la suma de los altos fijos de los botones y la imagen.


### <a name="relativelayout"></a>RelativeLayout

Tenga en cuenta la siguiente aplicación, que se muestra en vertical:

![](device-orientation-images/photo-rel-portrait.png "Aplicación de fotografía en vertical")

y horizontal:

![](device-orientation-images/photo-rel-landscape.png "Aplicación de fotografía en horizontal")

Que se consigue con el código XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

Tenga en cuenta lo siguiente:

- Debido al modo en que se ha diseñado la página, no es necesario para el código de procedimiento para introducir la capacidad de respuesta.
- El `ScrollView` se usa para permitir que la etiqueta esté visible incluso cuando el alto de la pantalla es menor que la suma de los altos fijos de los botones y la imagen.

### <a name="grid"></a>Cuadrícula

Tenga en cuenta la siguiente aplicación, que se muestra en vertical:

![](device-orientation-images/photo-grid-portrait.png "Aplicación de fotografía en vertical")

y horizontal:

![](device-orientation-images/photo-grid-landscape.png "Aplicación de fotografía en horizontal")

Que se consigue con el código XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Junto con el código siguiente procedimientos para controlar los cambios de rotación:

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

Tenga en cuenta lo siguiente:

- Debido al modo en que se ha diseñado la página, hay un método para cambiar la posición de la cuadrícula de los controles.


## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Ejemplo de BusinessTumble (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [Diseño dinámico (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [Mostrar una imagen basada en la orientación de pantalla](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/)
