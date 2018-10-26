---
title: Uso de recursos de Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 1e9a71de7725c8382e133d85977407bcc859fc58
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114865"
---
# <a name="using-android-assets"></a>Uso de recursos de Android

_Activos_ proporcionan una manera de incluir archivos arbitrarios como texto, xml, fuentes, música y vídeo en la aplicación. Si intenta incluir estos archivos como "resources", Android los procesará en su sistema de recursos y no podrá obtener los datos sin procesar. Si desea tener acceso a datos intactos, activos son una manera de hacerlo.

Agregado al proyecto de activos se mostrarán igual que un sistema de archivos que puede leer desde su aplicación mediante [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
En esta sencilla demo, vamos a agregar un recurso de archivo de texto a nuestro proyecto, léalo con `AssetManager`y mostrarlo en una vista de texto.


## <a name="add-asset-to-project"></a>Agregar recursos al proyecto

Van de activos el `Assets` carpeta del proyecto. Agregue un nuevo archivo de texto en esta carpeta denominada `read_asset.txt`. Coloque algún texto en él como "proviene de un recurso!".

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio debe ha establecido la **acción de compilación** para este archivo **AndroidAsset**:

![Configuración de la acción de compilación para AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac debería ha establecido la **acción de compilación** para este archivo **AndroidAsset**:

[![Configuración de la acción de compilación para AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Seleccionar el valor correcto **BuildAction** garantiza que el archivo se empaquetan en el APK en tiempo de compilación.


## <a name="reading-assets"></a>Lectura de activos

Los activos se leen utilizando una [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Una instancia de la `AssetManager` está disponible mediante el acceso a la [activos](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) propiedad en un `Android.Content.Context`, por ejemplo, una actividad.
En el código siguiente, abrimos nuestro **read_asset.txt** leer el contenido activo y mostrarlo utilizando un objeto TextView.

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```


## <a name="running-the-application"></a>Ejecutar la aplicación

Ejecute la aplicación y verá lo siguiente:

![Captura de pantalla de ejemplo](android-assets-images/screenshot.png)


## <a name="related-links"></a>Vínculos relacionados

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Contexto](https://developer.xamarin.com/api/type/Android.Content.Context/)
