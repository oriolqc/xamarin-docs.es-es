---
title: Usar datos en tiempo de diseño con el controlador de vista previa XAML
description: En este artículo se explica cómo usar datos en tiempo de diseño para mostrar los diseños con mucha actividad de datos en la vista previa de XAML sin ejecutar la aplicación.
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 0ff9f8b5ee6f9468650b6535745706bee8f96536
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58866363"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>Usar datos en tiempo de diseño con el controlador de vista previa XAML

_Algunos diseños son difíciles de visualizar sin datos. Utilice estos consejos para sacar el máximo partido de la vista previa de las páginas de datos con mucha actividad en la vista previa de XAML._

## <a name="design-time-data-basics"></a>Conceptos básicos de datos de tiempo de diseño

Datos en tiempo de diseño están datos falsos que se establece para facilitar los controles visualizar en la vista previa de XAML. Para empezar, agregue las siguientes líneas de código para el encabezado de la página XAML:

```csharp
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

Después de agregar los espacios de nombres, puede colocar `d:` delante de cualquier atributo o un control para mostrarlo en la vista previa de XAML. Elementos con `d:` no se muestran en tiempo de ejecución.

Por ejemplo, puede agregar texto a una etiqueta que normalmente tiene datos enlazados a él.

```csharp
<Label Text={Binding Name} d:Text="Name" />
```

[![Con texto en una etiqueta de datos en tiempo de diseño](xaml-previewer-images/designtimedata-label-sm.png "tiempo de diseño de datos con el texto de una etiqueta")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

 En este ejemplo, sin `d:Text`, el controlador de vista previa de XAML mostraría nada para la etiqueta. En su lugar, muestra "Name" en la etiqueta tendrá datos reales en tiempo de ejecución.

Puede usar `d:` con cualquier atributo para un control de Xamarin.Forms, como colores, tamaños de fuente y espaciado. Incluso puede agregarlo al propio control:

```csharp
<d:Button Text="Design Time Button" />
```

[![Datos con un control de botón en tiempo de diseño](xaml-previewer-images/designtimedata-controls-sm.png "datos con un control de botón en tiempo de diseño")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

En este ejemplo, el botón aparece sólo en tiempo de diseño. Use este método para colocar un marcador de posición en una [control personalizado no compatible con el controlador de vista previa de XAML](render-custom-controls.md).

## <a name="preview-images-at-design-time"></a>Imágenes de vista previa en tiempo de diseño

Puede establecer un origen de tiempo de diseño para las imágenes que se enlaza a la página o se carga dinámicamente en. En el proyecto Android, agregue la imagen que desea mostrar en la vista previa de XAML para el **recursos > Drawable** carpeta. En el proyecto de iOS, agregue la imagen a la **recursos** carpeta. A continuación, puede mostrar esa imagen en la vista previa de XAML en tiempo de diseño:

```csharp
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```
[![Datos con imágenes en tiempo de diseño](xaml-previewer-images/designtimedata-image-sm.png "con iamges datos en tiempo de diseño")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Datos de tiempo de diseño para ListView

ListView es una forma popular para mostrar datos en una aplicación móvil. Sin embargo, son difíciles de visualizar sin datos reales. Para utilizar datos en tiempo de diseño con ellos, tendrá que crear una matriz de tiempo de diseño que se usará como ItemsSource. El controlador de vista previa de XAML muestra lo que está en esa matriz en la ListView en tiempo de diseño.

```csharp
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![Diseño de datos de tiempo con un ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "datos con un ListView en tiempo de diseño")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

Este ejemplo muestra un ListView de tres TextCells en la vista previa de XAML. Puede cambiar `x:String` a un modelo de datos existente en el proyecto.

Consulte [app de Hanselman.Forms de James Montemagno](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L36-L57) para obtener un ejemplo más complejo.


## <a name="alternative-hardcode-a-static-viewmodel"></a>Alternativa: Codificar un ViewModel estático

Si no desea agregar datos en tiempo de diseño a controles individuales, puede configurar un almacén de datos ficticios para enlazar a la página. Hacer referencia a la de James Montemagno [entrada de blog sobre cómo agregar datos en tiempo de diseño](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver cómo enlazar a una clase ViewModel estático en XAML.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="requirements"></a>Requisitos

Datos en tiempo de diseño requieren una versión mínima de Xamarin.Forms 3.6.

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense muestra con líneas onduladas Mis datos en tiempo de diseño

Esto es un problema conocido y se corregirá en una próxima versión de Visual Studio. El proyecto aún se compilará sin errores.

### <a name="the-xaml-previewer-stopped-working"></a>El controlador de vista previa de XAML ha dejado de funcionar

Intente cerrar y volver a abrir el archivo XAML y limpiar y recompilar el proyecto.
