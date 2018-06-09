---
title: Resumen del capítulo 13. Mapas de bits
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 13. Mapas de bits'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: d95e801f6a239e894c1f6baa68015659af9eba35
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241560"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumen del capítulo 13. Mapas de bits

El Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento muestra un mapa de bits. Todas las plataformas de Xamarin.Forms admiten los formatos de archivo JPEG, PNG, GIF y BMP.

Mapas de bits de Xamarin.Forms proceden de cuatro lugares:

- A través de web tal como se especifica mediante una dirección URL
- Incrusta como un recurso en la biblioteca de clases Portable comunes
- Incrusta como un recurso en los proyectos de aplicación de plataforma
- Desde cualquier lugar que pueden hacer referencia a .NET `Stream` objeto, incluidos `MemoryStream`

Recursos de mapa de bits en la PCL son independientes de la plataforma, mientras que los recursos de mapa de bits en los proyectos de plataforma son específicos de la plataforma.

El mapa de bits se especifica estableciendo el [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) propiedad de `Image` a un objeto de tipo [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/), una clase abstracta con tres derivados:

- [`UriImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) para tener acceso a un mapa de bits a través de Internet tomando como base un `Uri` objeto establecido en su [ `Uri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.Uri/) propiedad
- [`FileImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/) para tener acceso a un mapa de bits almacenado en un proyecto de aplicación de plataforma basada en una ruta de carpeta y archivo establecida en su [ `File` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FileImageSource.File/) propiedad
- [`StreamImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/) para cargar un mapa de bits mediante .NET `Stream` objeto especificado mediante la devolución de un `Stream` desde una `Func` establecido en su [ `Stream` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StreamImageSource.Stream/) propiedad

O bien (y más comúnmente) puede usar los siguientes métodos estáticos de la `ImageSource` clase todos que devuelven `ImageSource` objetos:

- [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) para tener acceso a un mapa de bits a través de Internet tomando como base un `Uri` objeto
- [`ImageSource.FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) para tener acceso a un mapa de bits almacenado como un recurso incrustado en la PCL, aplicación o [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Type/) o [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Reflection.Assembly/) para tener acceso a un mapa de bits en otro ensamblado de origen
- [`ImageSource.FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) para tener acceso a un mapa de bits de un proyecto de aplicación de plataforma
- [`ImageSource.FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) para cargar un mapa de bits tomando como base un `Stream` objeto

No hay ningún equivalente de la clase de la `Image.FromResource` métodos. La `UriImageSource` clase es útil si necesita controlar el almacenamiento en caché. La `FileImageSource` clase es útil en XAML. `StreamImageSource` es útil para la carga asincrónica de `Stream` objetos, mientras que `ImageSource.FromStream` es sincrónico.

## <a name="platform-independent-bitmaps"></a>Mapas de bits independiente de la plataforma

El [ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) proyecto carga un mapa de bits a través de web mediante `ImageSource.FromUri`. El `Image` elemento está establecido en el `Content` propiedad de la `ContentPage`, por lo que está restringido para el tamaño de la página. Independientemente del tamaño del mapa de bits, un restringida `Image` elemento se ajusta al tamaño de su contenedor, y el mapa de bits se muestra en su tamaño máximo en el `Image` elemento mientras se mantiene la relación de aspecto del mapa de bits. Áreas de la `Image` más allá del mapa de bits puede ser coloreada con [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/).

El [ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) ejemplo es similar pero simplemente establece la `Source` propiedad a la dirección URL. La conversión se controla mediante la [ `ImageSourceConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSourceConverter/) clase.

### <a name="fit-and-fill"></a>El ajuste y relleno

Puede controlar cómo se ajusta el mapa de bits estableciendo la [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) propiedad de la `Image` a uno de los siguientes miembros de la [ `Aspect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect/) enumeración:

- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/): respeta la relación de aspecto (valor predeterminado)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/): área se llena, no respeta la relación de aspecto
- [`AspectFill`](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect.AspectFill/): rellena el área pero respeta la relación de aspecto, lleva a cabo recortar parte del mapa de bits

### <a name="embedded-resources"></a>Recursos incrustados

Puede agregar un archivo de mapa de bits a una PCL, o a una carpeta en la PCL. Asígnele un **acción de compilación** de **EmbeddedResource**. El [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) ejemplo muestra cómo utilizar `ImageSource.FromResource` para cargar el archivo. El nombre de recurso que se pasa al método está formada por el nombre de ensamblado, seguido por un punto, seguido del nombre de carpeta opcional y un punto, seguido por el nombre de archivo.

El programa se establece la `VerticalOptions` y `HorizontalOptions` propiedades de la `Image` a `LayoutOptions.Center`, lo que hace el `Image` elemento sin restricciones. El `Image` y el mapa de bits tienen el mismo tamaño:

- En iOS y Android, el `Image` es el tamaño de píxel del mapa de bits. Hay una asignación uno a uno entre los píxeles del mapa de bits y pantalla.
- En las plataformas de Windows en tiempo de ejecución, el `Image` es el tamaño de píxel de mapa de bits en unidades independientes del dispositivo. En la mayoría de los dispositivos, cada píxel de mapa de bits ocupa varias píxeles de la pantalla.

El [ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) ejemplo coloca un `Image` en vertical `StackLayout` en XAML. Una extensión de marcado denominada [ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ayuda a hacer referencia al recurso incrustado en XAML. Esta clase solo carga recursos desde el ensamblado donde ubica, por lo que no se coloca en una biblioteca.

### <a name="more-on-sizing"></a>Obtener más información sobre el ajuste de tamaño

A menudo es conveniente mapas de bits de tamaño coherente entre todas las plataformas.
Experimentar con **StackedBitmap**, puede establecer un `WidthRequest` en el `Image` elemento en vertical `StackLayout` para que sean coherentes entre las plataformas, pero el tamaño solo puede reducir el tamaño mediante esta técnica.

También puede establecer el `HeightRequest` hacer que la imagen tamaños coherentes en las plataformas, pero el ancho del mapa de bits restringido, se limitará la versatilidad de esta técnica. Para una imagen en un vertical `StackLayout`, `HeightRequest` debería evitarse.

El mejor método consiste en empezar con un mapa de bits mayor que el ancho de teléfono en unidades independientes del dispositivo y establecer `WidthRequest` ancho deseado en unidades independientes del dispositivo. Esto se muestra en el [ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) ejemplo.

El [ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) muestra capítulo 7 del de Lewis Carroll *aventuras de Alicia en el país de las maravillas* con las ilustraciones originales por John Tenniel:

[![Captura de pantalla triple de entidad de té mad](images/ch13fg16-small.png "Mad texto del libro de entidad de té Hatters")](images/ch13fg16-large.png#lightbox "Mad texto del libro de entidad de té Hatters")

### <a name="browsing-and-waiting"></a>Exploración y esperando

El [ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) ejemplo permite al usuario explorar a través de varias imágenes existentes almacenadas en el sitio web de Xamarin. Usa .NET `WebRequest` clase para descargar un archivo JSON con la lista de mapas de bits.

El programa utiliza una [ `ActivityIndicator` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) para indicar que está ocurriendo algo. Tal y como se carga cada mapa de bits, solo lectura [ `IsLoading` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.IsLoading/) propiedad de `Image` es `true`. El `IsLoading` propiedad está respaldada por una propiedad enlazable, por lo que un `PropertyChanged` evento se desencadena cuando se cambia dicha propiedad. El programa se adjunta un controlador para este evento y usa el valor actual de `IsLoaded` para establecer el [ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) propiedad de la `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Mapas de bits de transmisión por secuencias

El `ImageSource.FromStream` método crea un `ImageSource` basado en .NET `Stream`. El método se debe pasar un `Func` objeto que devuelve un `Stream` objeto.

### <a name="accessing-the-streams"></a>Obtener acceso a los flujos

El [ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) ejemplo muestra cómo utilizar el `ImaageSource.FromStream` método que se va a cargar un mapa de bits almacenado como un recurso incrustado como la carga de un mapa de bits en la web.

### <a name="generating-bitmaps-at-run-time"></a>Generar mapas de bits en tiempo de ejecución

Todas las plataformas de Xamarin.Forms admiten el formato de archivo BMP sin comprimir, que es fácil construir en código y, a continuación, almacenar en un `MemoryStream`. Esta técnica permite de forma algorítmica crear mapas de bits en tiempo de ejecución, tal como se implementa en el [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) clase en el **Xamrin.FormsBook.Toolkit** biblioteca.

El "hacer que usted mismo" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) ejemplo muestra el uso de `BmpMaker` para crear un mapa de bits con una imagen de degradado.

## <a name="platform-specific-bitmaps"></a>Mapas de bits específica de la plataforma

Todas las plataformas de Xamarin.Forms permiten almacenar mapas de bits en los ensamblados de aplicación de plataforma. Cuando se recuperan mediante una aplicación de Xamarin.Forms, estos mapas de bits de la plataforma son del tipo [ `FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/). Utilizarlos para:

- el [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) propiedad de [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)
- el [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) propiedad de [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)
- el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) propiedad de `Button`

Los ensamblados de plataforma ya contienen los mapas de bits como iconos y pantallas de presentación:

- En el proyecto de iOS, en la **recursos** carpeta
- En el proyecto Android, en las subcarpetas de la **recursos** carpeta
- En los proyectos de Windows, en la **activos** carpeta (aunque las plataformas de Windows no restringen los mapas de bits a la carpeta)

El [ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) ejemplo usa código para mostrar un icono de los proyectos de aplicación de plataforma.

### <a name="bitmap-resolutions"></a>Soluciones de mapa de bits

Todas las plataformas permiten almacenar varias versiones de imágenes de mapa de bits para las resoluciones de otro dispositivo. En tiempo de ejecución, se cargará la versión adecuada en función de la resolución del dispositivo de la pantalla.

En iOS, estos mapas de bits se diferencian por un sufijo en el nombre de archivo:

- Ningún sufijo para dispositivos de 160 PPP (1 píxel a la unidad independiente del dispositivo)
- '@2x' sufijo para dispositivos de 320 DPI (2 píxeles para el DIU)
- '@3x' sufijo para dispositivos de PPP 480 (3 píxeles para el DIU)

Un mapa de bits diseñado para mostrarse como cuadrado de una pulgada existiría en tres versiones:

- MyImage.jpg de 160 píxeles cuadrados
- MyImage@2x.jpg en el cuadrado de 320 píxeles
- MyImage@3x.jpg en el cuadrado de 480 píxeles

El programa debería hacer referencia a este mapa de bits como MyImage.jpg, pero se recupera la versión correcta en tiempo de ejecución basado en la resolución de la pantalla. Cuando no presentan restricciones, el mapa de bits siempre se representará en 160 unidades independientes del dispositivo.

Para Android, mapas de bits se almacenan en varias subcarpetas de la **recursos** carpeta:

- puede dibujar-ldpi (una resolución baja) con dispositivos de 120 ppp (0,75 píxeles para el DIU)
- puede dibujar-mdpi (intermedia) con dispositivos de 160 PPP (1 píxel a la DIU)
- puede dibujar-hdpi (alto) con dispositivos de 240 ppp (1,5 píxeles para el DIU)
- puede dibujar-xhdpi (muy alto) para dispositivos de 320 DPI (2 píxeles para el DIU)
- puede dibujar-xxhdpi (extra muy alto) para dispositivos de PPP 480 (3 píxeles para el DIU)
- puede dibujar-xxxhdpi (tres puntos altos adicionales) con dispositivos de 640 PPP (4 píxeles para el DIU)

Para un mapa de bits que pretende se representan en una pulgada cuadrada, las distintas versiones del mapa de bits tendrán el mismo nombre pero con un tamaño diferente y se almacenan en estas carpetas:

- puede dibujar-ldpi/MyImage.jpg en 120 píxeles cuadrados
- puede dibujar-mdpi/MyImage.jpg de 160 píxeles cuadrados
- puede dibujar-hdpi/MyImage.jpg en 240 píxeles cuadrados
- puede dibujar-xhdpi/MyImage.jpg en 320 píxeles cuadrados
- puede dibujar-xxhdpi/MyImage.jpg en 480 píxeles cuadrados
- puede dibujar-xxxhdpi/MyImage.jpg en 640 píxeles cuadrados

El mapa de bits siempre se representará en 160 unidades independientes del dispositivo. (La plantilla de solución de Xamarin.Forms estándar solo incluye el hdpi, xhdpi y las carpetas xxhdpi.)

Los proyectos en tiempo de ejecución de Windows admiten un mapa de bits de nomenclatura de esquema que está formada por un factor de escala en píxeles por unidad independiente del dispositivo como un porcentaje, por ejemplo:

- MyImage.scale 200.jpg en 320 píxeles cuadrados

Solo algunos porcentajes son válidos. Los programas de ejemplo para este libro incluyen sólo las imágenes con **escala-200** sufijos, pero incluyen actuales plantillas de solución de Xamarin.Forms **escala 100**, **escala 125**, **150 escala**, y **escala-400**.

Al agregar mapas de bits a los proyectos de plataforma, el **acción de compilación** debe ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- En tiempo de ejecución de Windows: **contenido**

El [ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) ejemplo crea dos objetos de tipo de botón que consta de `Image` elementos con un `TapGestureRecognizer` instalado. Se pretende que los objetos están cuadrado de una pulgada. El `Source` propiedad de `Image` se establece utilizando `OnPlatform` y `On` objetos que se va a hacer referencia a nombres de archivo potencialmente diferentes en las plataformas. Las imágenes de mapa de bits incluyen números que indica su tamaño en píxeles, para que pueda ver qué mapa de bits de tamaño se recuperan y se representa.

### <a name="toolbars-and-their-icons"></a>Barras de herramientas y sus iconos

Uno de los usos principales de mapas de bits específica de la plataforma es la barra de herramientas de Xamarin.Forms, que se crea agregando [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) objetos a la [ `ToolbarItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.ToolbarItems/) colección definida por `Page`. `ToobarItem` se deriva de [ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) desde la que se hereda algunas propiedades.

El más importante `ToolbarItem` propiedades son:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) para el texto que puede aparecer en función de la plataforma y `Order`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) de tipo `FileImageSource` para la imagen que puede aparecer en función de la plataforma y `Order`
- [`Order`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Order/) de tipo [ `ToolbarItemOrder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItemOrder/), una enumeración con tres miembros [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Default/), [ `Primary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Primary/), y [ `Secondary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Secondary/).

El número de `Primary` elementos deben estar limitados a tres o cuatro. Debe incluir un `Text` establecer para todos los elementos. Para la mayoría de las plataformas, solo el `Primary` elementos requieren un `Icon` pero Windows 8.1 requiere un `Icon` para todos los elementos. Los iconos deben ser 32 unidades independientes del dispositivo cuadrados. El `FileImageSource` tipo indica que son específicos de la plataforma.

El `ToolbarItem` se activa un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/) evento cuando puntea, muy similar a un `Button`. `ToolbarItem` También es compatible con [ `Command` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Command/) y [ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.CommandParameter/) propiedades usadas en relación con MVVM. (Consulte [capítulo 18, MVVM](chapter18.md)).

IOS y Android requieren que sea una página que muestra una barra de herramientas un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) o una página que navega por un `NavigationPage`. El [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) programa establece la `MainPage` propiedad de su `App` clase a la [ `NavigationPage` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.NavigationPage.NavigationPage/p/Xamarin.Forms.Page/) con un `ContentPage` argumento y se muestra la construcción y controlador de eventos de una barra de herramientas.

### <a name="button-images"></a>Imágenes de los botones

También puede utilizar mapas de bits específica de la plataforma para establecer el [ `Image` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Image/) propiedad de `Button` un mapa de bits del cuadrado de 32 unidades independientes del dispositivo, como se muestra en el [ **ButtonImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) ejemplo.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 13 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Ejemplos de capítulo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Working with Images (Trabajo con imágenes)](~/xamarin-forms/user-interface/images.md)
