---
title: Crear extensiones de marcado XAML
description: Definir sus propias extensiones de marcado XAML personalizados
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: d4ae3b42c5c926749310da6e36b6f4e9754d398c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="creating-xaml-markup-extensions"></a>Crear extensiones de marcado XAML

En el nivel de programación, una extensión de marcado XAML es una clase que implementa el [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) o [ `IMarkupExtension<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension%3CT%3E/) interfaz. Puede explorar el código fuente de las extensiones de marcado estándar se describe a continuación, en la [ **MarkupExtensions** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del repositorio de Xamarin.Forms GitHub. 

También es posible definir sus propias extensiones de marcado XAML personalizados derivando de `IMarkupExtension` o `IMarkupExtension<T>`. Use el formulario genérico si la extensión de marcado Obtiene un valor de un tipo determinado. Este es el caso de algunas de las extensiones de marcado de Xamarin.Forms:

- `TypeExtension` se deriva de `IMarkupExtension<Type>`
- `ArrayExtension` se deriva de `IMarkupExtension<Array>`
- `DynamicResourceExtension` se deriva de `IMarkupExtension<DynamicResource>`
- `BindingExtension` se deriva de `IMarkupExtension<BindingBase>`
- `ConstraintExpression` se deriva de `IMarkupExtension<Constraint>`

Los dos `IMarkupExtension` interfaces definen un solo método denominado `ProvideValue`: 

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

Puesto que `IMarkupExtension<T>` deriva de `IMarkupExtension` e incluye el `new` palabra clave en `ProvideValue`, contiene ambos `ProvideValue` métodos.

Muy a menudo, las extensiones de marcado XAML definen propiedades que contribuyen al valor devuelto. (La excepción obvia es `NullExtension`, en el que `ProvideValue` simplemente devuelve `null`.) El `ProvideValue` método tiene un único argumento de tipo `IServiceProvider` que se explicará más adelante en este artículo.

## <a name="a-markup-extension-for-specifying-color"></a>Una extensión de marcado para especificar Color

La extensión de marcado XAML siguiente permite construir una `Color` valor mediante componentes de matiz, saturación y luminosidad. Define cuatro propiedades de los cuatro componentes del color, incluido un componente alfa que se inicializa a 1. La clase se deriva de `IMarkupExtension<Color>` para indicar un `Color` valor devuelto:

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

Dado que `IMarkupExtension<T>` deriva de `IMarkupExtension`, la clase debe contener dos `ProvideValue` métodos, que devuelve `Color` y otro que devuelve `object`, pero el segundo método simplemente puede llamar al método primero.

El **HSL Color demostración** página muestra una variedad de maneras que `HslColorExtension` pueden aparecer en un archivo XAML para especificar el color de un `BoxView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    
    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

Tenga en cuenta que, cuando `HslColorExtension` es una etiqueta XML, las cuatro propiedades se establecen como atributos, pero cuando aparece entre llaves, las cuatro propiedades están separadas por comas sin comillas. Los valores predeterminados de `H`, `S`, y `L` son 0 y el valor predeterminado de `A` es 1, por lo que dichas propiedades se pueden omitir si desea establecer valores predeterminados. El último ejemplo muestra un ejemplo donde la luminosidad es 0, lo que normalmente se produce en negro, pero el canal alfa es 0,5, por lo que es semitransparente y aparece atenuado en el fondo blanco de la página:

[![Demostración de Color HSL](creating-images/hslcolordemo-small.png "HSL Color demostración")](creating-images/hslcolordemo-large.png#lightbox "HSL Color demostración")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Una extensión de marcado para tener acceso a los mapas de bits

El argumento `ProvideValue` es un objeto que implementa el [ `IServiceProvider` ](https://developer.xamarin.com/api/type/System.IServiceProvider/) interfaz, que se define en .NET `System` espacio de nombres. Esta interfaz tiene un miembro, un método denominado `GetService` con un `Type` argumento. 

El `ImageResourceExtension` clase se muestra a continuación muestra un uso posible de `IServiceProvider` y `GetService` para obtener un `IXmlLineInfoProvider` objeto que puede proporcionar información de línea y el carácter que indica que se detectó un error determinado. En este caso, se produce una excepción cuando el `Source` no se estableció la propiedad:

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;

        return ImageSource.FromResource(assemblyName + "." + Source);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` es útil cuando un archivo XAML que se necesita tener acceso a un archivo de imagen almacenado como un recurso incrustado en el proyecto de biblioteca de clases Portable. Usa el `Source` propiedad que se va a llamar el método estático `ImageSource.FromResource` método. Este método requiere un nombre de recurso completo, que está formada por el nombre del ensamblado, el nombre de carpeta y el nombre de archivo separados por puntos. El `ImageResourceExtension` no necesita el nombre de ensamblado se parte porque obtiene el nombre del ensamblado utilizando la reflexión y antepone a la `Source` propiedad. A pesar de todo, `ImageSource.FromResource` se debe invocar desde el ensamblado que contiene el mapa de bits, lo que significa que esta extensión de recursos XAML no puede formar parte de una biblioteca externa a menos que las imágenes están también en esa biblioteca. (Consulte la [ **imágenes incrustadas** ](~/xamarin-forms/user-interface/images.md#embedded_images) artículo para obtener más información sobre cómo acceder a los mapas de bits que se almacenan como recursos incrustados.) 

Aunque `ImageResourceExtension` requiere el `Source` propiedad se establece, el `Source` propiedad se indica en un atributo como la propiedad de contenido de la clase. Esto significa que el `Source=` se puede omitir la parte de la expresión entre llaves. En el **demostración de recursos de imagen** página, la `Image` elementos capturar dos imágenes con el nombre de la carpeta y el nombre de archivo separados por puntos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        
        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

Este es el programa que se ejecuta en las tres plataformas:

[![Demostración de recursos de imagen](creating-images/imageresourcedemo-small.png "demostración de recursos de imagen")](creating-images/imageresourcedemo-large.png#lightbox "demostración de recursos de imagen")

## <a name="service-providers"></a>Proveedores de servicios

Mediante el uso de la `IServiceProvider` argumento pasado a `ProvideValue`, las extensiones de marcado XAML pueden obtener acceso a información útil sobre el archivo XAML en el que está usándola. Pero para usar el `IServiceProvider` argumento correctamente, necesita saber qué tipo de servicios están disponibles en contextos determinados. La mejor manera para obtener una descripción de esta característica es estudiar el código fuente de las extensiones de marcado XAML existentes en el [ **MarkupExtensions** carpeta](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) en el repositorio de Xamarin.Forms en GitHub. Tenga en cuenta que algunos tipos de servicios son internos para Xamarin.Forms.

En algunas extensiones de marcado XAML, este servicio puede ser útil:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

El `IProvideValueTarget` interfaz define dos propiedades, `TargetObject` y `TargetProperty`. Cuando se obtiene esta información en el `ImageResourceExtension` (clase), `TargetObject` es el `Image` y `TargetProperty` es un `BindableProperty` de objeto para el `Source` propiedad de `Image`. Se trata de la propiedad en el que se ha establecido la extensión de marcado XAML.

El `GetService` llamada con un argumento de `typeof(IProvideValueTarget)` realmente devuelve un objeto de tipo `SimpleValueTargetProvider`, que se define en el `Xamarin.Forms.Xaml.Internals` espacio de nombres. Si convierte el valor devuelto de `GetService` a ese tipo, también puede acceder a un `ParentObjects` propiedad, que es una matriz que contiene el `Image` elemento, el `Grid` primario y el `ImageResourceDemoPage` primario de la `Grid`.

## <a name="conclusion"></a>Conclusión

Las extensiones de marcado XAML desempeñan un papel fundamental en XAML mediante la extensión de la capacidad de establecer los atributos de una variedad de orígenes. Además, si las extensiones de marcado XAML existentes no proporcionan exactamente lo que necesita, también puede escribir su propio. 


## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capítulo de extensiones de marcado XAML de libreta de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
