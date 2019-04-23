---
ms.openlocfilehash: 8a4fbed10fa52c08a63f3aed306fe5cde21804b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384574"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Image`](xref:Xamarin.Forms.Image) para personalizar su apariencia:

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Este código establece la propiedad [`Aspect`](xref:Xamarin.Forms.Image.Aspect), que define el modo de escalado de la imagen, en [`Fill`](xref:Xamarin.Forms.Aspect.Fill). El miembro `Fill` se define en la enumeración [`Aspect`](xref:Xamarin.Forms.Aspect) y ampliará la imagen para rellenar por completo la vista, aunque la imagen se distorsione. Para obtener más información sobre el escalado de imágenes, vea [Mostrar imágenes](~/xamarin-forms/user-interface/images.md#displaying-images) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    La extensión de marcado `OnPlatform` le permite personalizar la apariencia de la interfaz de usuario para cada plataforma. En este ejemplo, la extensión de marcado se usa para establecer las propiedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) en 300 unidades independientes de dispositivo para iOS y en 250 unidades independientes de dispositivo para Android. Para obtener más información sobre la extensión de marcado `OnPlatform`, vea [Extensión de marcado OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) en la guía [Consumo de las extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    Además, la propiedad [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que la imagen se centrará horizontalmente.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de una imagen que tiene un tamaño distinto en iOS y Android](../images/customize-appearance.png "Imagen con un tamaño distinto para cada plataforma")](../images/customize-appearance-large.png#lightbox "Imagen con un tamaño distinto para cada plataforma")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Image`](xref:Xamarin.Forms.Image) para personalizar su apariencia:

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Este código establece la propiedad [`Aspect`](xref:Xamarin.Forms.Image.Aspect), que define el modo de escalado de la imagen, en [`Fill`](xref:Xamarin.Forms.Aspect.Fill). El miembro `Fill` se define en la enumeración [`Aspect`](xref:Xamarin.Forms.Aspect) y ampliará la imagen para rellenar por completo la vista, aunque la imagen se distorsione. Para obtener más información sobre el escalado de imágenes, vea [Mostrar imágenes](~/xamarin-forms/user-interface/images.md#displaying-images) en la guía [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    La extensión de marcado `OnPlatform` le permite personalizar la apariencia de la interfaz de usuario para cada plataforma. En este ejemplo, la extensión de marcado se usa para establecer las propiedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) en 300 para iOS y en 250 para Android. Para obtener más información sobre la extensión de marcado `OnPlatform`, vea [Extensión de marcado OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) en la guía [Consumo de las extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    Además, la propiedad [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) especifica que la imagen se centrará horizontalmente.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una imagen que tiene un tamaño distinto en iOS y Android](../images/customize-appearance.png "Imagen con un tamaño distinto para cada plataforma")](../images/customize-appearance-large.png#lightbox "Imagen con un tamaño distinto para cada plataforma")
