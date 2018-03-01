---
title: "Imágenes"
description: "Las imágenes se pueden compartir entre plataformas con Xamarin.Forms, se pueden cargar específicamente para cada plataforma, o se pueden descargar para su presentación."
ms.topic: article
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: cddf3c58020b534abdc1541d967a9a7f5a4d891a
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="images"></a>Imágenes

_Las imágenes se pueden compartir entre plataformas con Xamarin.Forms, se pueden cargar específicamente para cada plataforma, o se pueden descargar para su presentación._

Las imágenes son una parte fundamental de navegación por la aplicación, la facilidad de uso y la personalización de marca. Aplicaciones de Xamarin.Forms necesitan poder compartir imágenes entre todas las plataformas, pero también puede mostrar diferentes imágenes en cada plataforma.

Imágenes específicas de la plataforma también son necesarias para iconos y pantallas de presentación; estas deben configurarse de forma según la plataforma.

Este documento describen los siguientes temas:

- [ **Imágenes locales** ](#Local_Images) -mostrar imágenes incluidos con la aplicación, incluida la resolución de resoluciones originales como iOS valores altos de PPP de Retina o Android, las versiones de una imagen.
- [ **Imágenes incrustadas** ](#Embedded_Images) -mostrar imágenes incrusta como un recurso de ensamblado.
- [ **Descargar imágenes** ](#Downloading_Images) : descargar y mostrar imágenes.
- [ **Iconos y las pantallas de presentación** ](#Icons_and_splashscreens) -iconos específicos de la plataforma y las imágenes de inicio.

## <a name="displaying-images"></a>Mostrar imágenes

Xamarin.Forms utiliza el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) vista para mostrar imágenes en una página. Tiene dos propiedades importantes:

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) : Un [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) instancia, archivo, Uri o recurso, que establece la imagen para mostrar.
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -Cómo cambiar el tamaño de la imagen dentro de los límites que se está mostrando en (ya sea para stretch, recortar o formato de pantalla ancha).

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) instancias pueden obtenerse utilizando los métodos estáticos para cada tipo de origen de la imagen:

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -Requiere un nombre de archivo o la ruta del archivo que se pueden resolver en cada plataforma.
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -Requiere un objeto Uri, p. ej.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -Requiere un identificador de recursos en un archivo de imagen incrustado en la aplicación o PCL, con un **acción: EmbeddedResource compilar**.
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -Requiere una secuencia que proporciona los datos de imagen.

El [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) propiedad determina cómo se ampliará la imagen para que quepa en el área de presentación:

- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/) -Ajusta la imagen para completamente y exactamente rellenar el área de presentación. Esto puede dar lugar a que la imagen está distorsionada.
- [`AspectFill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFill/) -Recorta la imagen de manera que ocupe el área de presentación mientras conserva el aspecto (ie. sin distorsión).
- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/) -Buzones la imagen (si es necesario) para que toda la imagen quepa en el área de presentación, con espacio en blanco que se agrega a la parte superior o inferior o lados dependiendo de si la imagen es ancho o alto.

Se pueden cargar imágenes desde un [archivo local](#Local_Images_in_Xaml), [recurso incrustado](#embedded_images), o [descargado](#Downloading_Images).

<a name="Local_Images" />

## <a name="local-images"></a>Imágenes locales

Archivos de imagen se pueden agregar a cada proyecto de aplicación y al que hace referencia desde el código de Xamarin.Forms compartido. Para usar una imagen única en todas las aplicaciones, *el mismo nombre de archivo debe usarse en cada plataforma*, y debe ser un nombre de recurso de Android válido (es decir. se permiten solo letras minúsculas, números, el carácter de subrayado y el período).

- **iOS** : el preferido de la manera de administrar y mantener imágenes puesto que iOS 9 consiste en usar **conjuntos de imágenes del catálogo de activos**, que debe contener todas las versiones de una imagen que son necesarias para admitir varios dispositivos y escalar factores para un aplicación. Para obtener más información, consulte [agregar imágenes a un conjunto de imágenes de catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -colocar imágenes en el **/puede dibujar recursos** directorio con **acción de compilación: AndroidResource**. También se pueden proporcionar versiones de alta y baja PPP de una imagen (en un nombre apropiado **recursos** subdirectorios como **pueden dibujar ldpi**, **pueden dibujar hdpi**y **puede dibujar xhdpi**).
- **Windows Phone** -colocar imágenes en el directorio raíz de la aplicación con **acción de compilación: contenido**.
- **Plataforma universal de Windows (UWP)** -colocar imágenes en el directorio raíz de la aplicación con **acción de compilación: contenido**.

> [!IMPORTANT]
> Antes de iOS 9, imágenes normalmente se colocaron en el **recursos** carpeta con **acción de compilación: BundleResource**. Sin embargo, este método para trabajar con imágenes en una aplicación de iOS está en desuso por Apple. Para obtener más información, consulte [tamaños de las imágenes y los nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Adhesión a estas reglas de nomenclatura de archivos y la colocación permite que el código XAML siguiente cargar y mostrar la imagen en todas las plataformas:

```xaml
<Image Source="waterfront.jpg" />
```

El código equivalente en C# es como sigue:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Las capturas de pantalla siguientes muestran el resultado de mostrar una imagen local en cada plataforma:

[ ![Elemento ImageSource local](images-images/local-sml.png "mostrar una imagen Local de aplicación de ejemplo")](images-images/local.png "mostrar una imagen Local de aplicación de ejemplo")

Para obtener mayor flexibilidad del `Device.RuntimePlatform` propiedad puede utilizarse para seleccionar un archivo de imagen distinto o la ruta de acceso de algunas o todas las plataformas, como se muestra en este ejemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar el mismo nombre de archivo de imagen en todas las plataformas, el nombre debe ser válido en todas las plataformas. Android drawables tiene restricciones de nomenclatura: se permiten solo letras minúsculas, números, caracteres de subrayado y período – y para la compatibilidad multiplataforma esto debe ir seguido en todas las demás plataformas demasiado. El nombre de archivo de ejemplo **waterfront.png** sigue las reglas, pero algunos ejemplos de nombres de archivo no válidos incluyen "agua front.png", "WaterFront.png", "agua-front.png" y "wåterfront.png".

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluciones nativas (Retina y valores altos de PPP)

IOS y Android plataformas incluyen compatibilidad con las soluciones de otra imagen, donde el sistema operativo elige la imagen apropiada en tiempo de ejecución en función de las capacidades del dispositivo. Xamarin.Forms utiliza las API de las plataformas nativas para cargar imágenes locales, por lo que admite automáticamente las soluciones alternativas si los archivos correctamente se denomina y ubicados en el proyecto.

El método preferido para administrar imágenes desde iOS 9 es arrastrar imágenes para cada resolución necesaria para el conjunto de imágenes del catálogo de activos correspondiente. Para obtener más información, consulte [agregar imágenes a un conjunto de imágenes de catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes de iOS 9, versiones de retina de la imagen pudieron colocarse en el **recursos** carpeta - dos y tres veces la resolución con una  **@2x**  o  **@3x** sufijos en el nombre de archivo antes de la extensión de archivo (p. ej. **myimage@2x.png**). Sin embargo, este método para trabajar con imágenes en una aplicación de iOS está en desuso por Apple. Para obtener más información, consulte [tamaños de las imágenes y los nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Imágenes de resolución alternativo Android deben colocarse en [directorios especial denominado](http://developer.android.com/guide/practices/screens_support.html) en el proyecto Android, como se muestra en la captura de pantalla siguiente:

[![Ubicación de la imagen de varias resoluciones Android](images-images/xs-highdpisolution-sml.png "ubicación de la imagen de varias resoluciones Android")](images-images/xs-highdpisolution.png "ubicación de la imagen de varias resoluciones Android")

### <a name="additional-controls-that-display-images"></a>Controles adicionales que mostrar imágenes

Algunos controles tienen propiedades que mostrar una imagen, como:

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -Cualquier tipo que deriva de la página `Page` tiene [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) y [ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/) propiedades, que se pueden asignar una referencia de archivo local. En determinadas circunstancias, como cuando un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) se están mostrando un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), el icono se mostrará si se admite la plataforma.

  > [!IMPORTANT]
  > En iOS, el [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) propiedad no se puede rellenar desde una imagen en un conjunto de imágenes del catálogo de activos. En su lugar, cargar imágenes de icono para el `Page.Icon` propiedad desde el **recursos** carpeta en el proyecto de iOS.

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) -Tiene un [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) propiedad que se puede establecer en una referencia de archivo local.
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) -Tiene un [ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/) recupera de la propiedad que se puede establecer en una imagen desde un archivo local, un recurso incrustado o un URI.

<a name="embedded_images" />

## <a name="embedded-images"></a>Imágenes incrustadas

También se incluyen imágenes incrustadas con una aplicación (por ejemplo, imágenes locales), pero en lugar de tener una copia de la imagen en la estructura de archivos de la aplicación la imagen de archivo está incrustado en el ensamblado como un recurso. Este método de distribución de imágenes es especialmente adecuado para crear componentes, como la imagen está integrada en el código.

Para incrustar una imagen en un proyecto, haga clic en para agregar nuevos elementos y seleccione la imagen/s que se va a agregar. De forma predeterminada la imagen tendrá **acción de compilación: Ninguno**; esta tarea debe establecerse en **acción de compilación: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "Establecer la acción de compilación: EmbeddedResource")

El **acción de compilación** se pueden ver y cambiar en el **propiedades** ventana para un archivo.

En este ejemplo es el identificador de recurso **WorkingWithImages.beach.jpg**.
El IDE ha generado este comportamiento predeterminado mediante la concatenación de la **predeterminado Namespace** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "Establecer la acción de compilación: EmbeddedResource")

**Acción de compilación** también se pueden ver y cambiar en el **propiedades** controlador para un archivo.
Este panel muestra la **Id. de recurso** que se usa para hacer referencia al recurso en el código. En la captura de pantalla siguiente, la **Id. de recurso** es **WorkingWithImages.beach.jpg**.
El IDE ha generado este comportamiento predeterminado mediante la concatenación de la **predeterminado Namespace** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
Este identificador se puede editar en el **propiedades** panel, pero para estos ejemplos el valor **WorkingWithImages.beach.jpg** se usará.

![](images-images/xs-embeddedproperties.png "Panel de propiedades EmbeddedResource")

-----

Si coloca las imágenes incrustadas en carpetas dentro de su proyecto, los nombres de carpeta también separados por puntos (.) en el identificador de recurso. Mover el **beach.jpg** la imagen en una carpeta denominada **MyImages** daría lugar a un identificador de recurso de **WorkingWithImages.MyImages.beach.jpg**

El código para cargar una imagen incrustada simplemente pasa el **Id. de recurso** a la [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) método tal como se muestra a continuación:

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg") };
```

Actualmente no hay ninguna conversión implícita para los identificadores de recursos. En su lugar, debe usar [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) o `new ResourceImageSource()` para cargar imágenes incrustadas.

Las capturas de pantalla siguientes muestran el resultado de la presentación de una imagen incrustada en cada plataforma:

[ ![ResourceImageSource](images-images/resource-sml.png "mostrar una imagen incrustada de aplicación de ejemplo")](images-images/resource.png "mostrar una imagen incrustada de aplicación de ejemplo")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>Uso de XAML

Porque no hay ningún convertidor de tipos integrados de `string` a `ResourceImageSource`, estos tipos de imágenes no se puede cargar de forma nativa mediante XAML. En su lugar, se puede escribir una extensión de marcado XAML personalizada simple para cargar imágenes con un **Id. de recurso** especificado en XAML:

```csharp
[ContentProperty ("Source")]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }
   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source);

   return imageSource;
 }
}
```

Para usar esta extensión agrega un personalizado `xmlns` al XAML, con los valores de espacio de nombres y ensamblado correctos para el proyecto. A continuación, se puede establecer el origen de la imagen utilizando esta sintaxis: `{local:ImageResource WorkingWithImages.beach.jpg}`. A continuación, se muestra un ejemplo completo en XAML:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshooting-embedded-images"></a>Solución de problemas de imágenes incrustadas

<a name="Debugging_Embedded_Images" />

#### <a name="debugging-code"></a>Depurar código

Dado que a veces es difícil de entender por qué no se puede cargar un recurso de imagen en particular, el siguiente código de depuración puede agregarse temporalmente a una aplicación para comprobar que los recursos están configurados correctamente. Dará como resultado conocidos todos los recursos incrustados en el ensamblado especificado para el <span class="UIItem">consola</span> para ayudar a depurar problemas de carga de recursos.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects-dont-appear"></a>No aparecen imágenes incrustadas en otros proyectos

`Image.FromResource` solo busca imágenes en el mismo ensamblado que el código que llama `FromResource`. Con el código de depuración anterior puede determinar qué ensamblados contienen un recurso específico cambiando el `typeof()` instrucción a una `Type` sabe que están en cada ensamblado.

<a name="Downloading_Images" />

## <a name="downloading-images"></a>Descargar imágenes

Las imágenes se pueden descargar automáticamente para su presentación, como se muestra en el código XAML siguiente:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

El código equivalente en C# es como sigue:

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

El [ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) método requiere un `Uri` de objetos y devuelve un nuevo [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) que lee de la `Uri`.

También hay una conversión implícita para las cadenas URI, por lo que también funcionará en el ejemplo siguiente:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Las capturas de pantalla siguientes muestran el resultado de la presentación de una imagen remota en cada plataforma:

[![Descargar ImageSource](images-images/download-sml.png "mostrar una imagen descargada de aplicación de ejemplo")](images-images/download.png "mostrar una imagen descargada de aplicación de ejemplo")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>Almacenamiento en caché de imagen descargado

A [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) también admite el almacenamiento en caché de imágenes descargadas, configuradas a través de las siguientes propiedades:

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) -Si está habilitado el almacenamiento en caché (`true` de forma predeterminada).
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A `TimeSpan` que define cuánto tiempo la imagen se almacenarán localmente.

Almacenamiento en caché está habilitado de forma predeterminada y almacenará la imagen de forma local durante 24 horas. Para deshabilitar el almacenamiento en caché para una imagen determinada, crear una instancia del origen de la imagen como se indica a continuación:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

Para establecer un período de caché específica (por ejemplo, 5 días) crear instancias en el origen de la imagen como se indica a continuación:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

Almacenamiento en caché integrado facilita admitir escenarios, como el desplazamiento de listas de imágenes, donde puede establecer (o enlazar) una imagen en cada celda y permitir que la memoria caché integrada ocuparse de volver a cargar la imagen cuando se desplaza la celda en la vista.

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>Iconos y las pantallas de presentación

Aunque no se refiere a la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) vista, los iconos de aplicación y las pantallas de presentación también son un uso importante de las imágenes en proyectos de Xamarin.Forms.

Configuración de iconos y las pantallas de presentación para las aplicaciones de Xamarin.Forms se realiza en cada uno de los proyectos de aplicación. Esto significa generar correctamente un tamaño de imágenes para UWP, iOS y Android. Estas imágenes deben asignar un nombre y encuentra según los requisitos de cada plataforma.

## <a name="icons"></a>Iconos

Consulte la [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md), [Google Iconography](http://developer.android.com/design/style/iconography.html), y [directrices para los activos de icono y el icono](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) para obtener más información acerca de cómo crear estos recursos de la aplicación.

## <a name="splashscreens"></a>Pantallas de presentación

Solo iOS y las aplicaciones de UWP requieren una pantalla de presentación (también denominado una imagen de pantalla o el valor predeterminado de inicio).

Consulte la documentación para [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) y [pantallas de presentación](/windows/uwp/launch-resume/splash-screens/) en el centro de desarrollo de Windows.

## <a name="summary"></a>Resumen

Xamarin.Forms ofrece una serie de diferentes maneras de incluir imágenes en una aplicación multiplataforma, lo que para la misma imagen que se usará en plataformas o imágenes específicos de la plataforma que se especifique. Imágenes descargadas se almacenan también automáticamente, automatizar un escenario de codificación comunes.

Imágenes de icono y de pantalla de presentación de la aplicación están configurados y configurar que las aplicaciones no Xamarin.Forms - sigan las mismas instrucciones que se usa para aplicaciones específicas de la plataforma.


## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithImages (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md)
- [Iconography Android](http://developer.android.com/design/style/iconography.html)
- [Directrices para los activos de icono y el icono](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
