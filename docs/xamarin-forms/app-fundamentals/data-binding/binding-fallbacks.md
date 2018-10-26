---
title: Reservas de enlace de Xamarin.Forms
description: En este artículo se explica cómo fortalecer los enlaces mediante la definición de los valores de reserva que se usará si se produce un error de enlace.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: fa375720730630065609e328b343e16578c6f1df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131994"
---
# <a name="xamarinforms-binding-fallbacks"></a>Reservas de enlace de Xamarin.Forms

En ocasiones dan error enlaces de datos, porque no se puede resolver el origen de enlace o porque el enlace se realiza correctamente pero devuelve un `null` valor. Si bien estos escenarios se pueden controlar con los convertidores de tipos de valor, u otro código adicional, los enlaces de datos se pueden realizar más sólidos mediante la definición de valores de reserva para su uso si se produce un error en el proceso de enlace. Esto puede realizarse mediante la definición de la [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) y [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propiedades en una expresión de enlace. Dado que estas propiedades residen en el [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) (clase), puede usarse con los enlaces, enlaces compilados y con el `Binding` extensión de marcado.

> [!NOTE]
> El uso de la [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) y [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propiedades en una expresión de enlace es opcional.

## <a name="defining-a-fallback-value"></a>Definir un valor de reserva

El [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) propiedad permite que se puede definir un valor reserva que se usará cuando el enlace *origen* no se puede resolver. Un escenario común para establecer esta propiedad es cuando se enlaza a las propiedades de origen que podrían no existir en todos los objetos en una colección enlazada tipos heterogéneos.

El **MonkeyDetail** ilustra la página Configuración del [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) propiedad:

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

El enlace en el [ `Label` ](xref:Xamarin.Forms.Label) define un [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) valor que se establecerá en el destino si no se puede resolver el origen de enlace. Por lo tanto, el valor definido por el `FallbackValue` propiedad se mostrará si el `Population` propiedad no existe en el objeto enlazado. Tenga en cuenta que aquí la `FallbackValue` valor de propiedad está delimitado por caracteres de comillas simples (apóstrofo).

En lugar de definir [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) valores de propiedad en línea, se recomienda que para definirlas como recursos en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). La ventaja de este enfoque es que estos valores se definen una vez en una sola ubicación y son localizables más fácilmente. A continuación, se pueden recuperar los recursos mediante el `StaticResource` extensión de marcado:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> No es posible establecer el `FallbackValue` propiedad con una expresión de enlace.

Este es el programa que se ejecutan en las tres plataformas:

![Enlace FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue enlace")

Cuando el `FallbackValue` propiedad no está establecida en una expresión de enlace y la ruta de acceso de enlace o parte de la ruta de acceso no se resuelve, [ `BindableProperty.DefaultValue` ](xref:Xamarin.Forms.BindableProperty.DefaultValue) se establece en el destino. Sin embargo, cuando el `FallbackValue` propiedad está establecida y la ruta de acceso de enlace o parte de la ruta de acceso no se resuelve, el valor de la `FallbackValue` propiedad value se establece en el destino. Por lo tanto, en el **MonkeyDetail** página el [ `Label` ](xref:Xamarin.Forms.Label) muestra "Tamaño de la población desconocido", porque le falta el elemento enlazado un `Population` propiedad.

> [!IMPORTANT]
> No se ejecuta un convertidor de valores definido en una expresión de enlace cuando la [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) propiedad está establecida.

## <a name="defining-a-null-replacement-value"></a>Definir un valor de reemplazo de null

El [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propiedad permite definir un valor de reemplazo que se usará cuando el enlace *origen* se ha resuelto, pero el valor es `null`. Un escenario común para establecer esta propiedad es cuando se enlaza a las propiedades de origen que podrían ser `null` en una colección enlazada.

El **monos** ilustra la página Configuración del [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) propiedad:

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Los enlaces en el [ `Image` ](xref:Xamarin.Forms.Image) y [ `Label` ](xref:Xamarin.Forms.Label) definen [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) valores que se aplicará si la ruta de acceso de enlace devuelve `null`. Por lo tanto, los valores definidos por el `TargetNullValue` se mostrarán las propiedades para los objetos de la colección donde el `ImageUrl` y `Location` propiedades no están definidas. Tenga en cuenta que aquí la `TargetNullValue` los valores de propiedad están delimitados por caracteres de comillas simples (apóstrofo).

En lugar de definir [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) valores de propiedad en línea, se recomienda que para definirlas como recursos en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). La ventaja de este enfoque es que estos valores se definen una vez en una sola ubicación y son localizables más fácilmente. A continuación, se pueden recuperar los recursos mediante el `StaticResource` extensión de marcado:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> No es posible establecer el `TargetNullValue` propiedad con una expresión de enlace.

Este es el programa que se ejecutan en las tres plataformas:

[![Enlace TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "TargetNullValue enlace")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue enlace")

Cuando el `TargetNullValue` propiedad no está establecida en una expresión de enlace, un valor de origen de `null` se convertirán si se define un convertidor de valores, si un `StringFormat` está definido, y, a continuación, se establece el resultado en el destino. Sin embargo, cuando el `TargetNullValue` propiedad se establece un valor de origen de `null` se convertirán si se ha definido un convertidor de valores, y si sigue siendo `null` después de la conversión, el valor de la `TargetNullValue` propiedad está establecida en el destino.

> [!IMPORTANT]
> No se aplicará el formato de cadena en una expresión de enlace cuando la `TargetNullValue` se establece la propiedad.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
