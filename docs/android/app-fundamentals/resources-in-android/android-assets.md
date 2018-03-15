---
title: Mediante activos de Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: e1890575f5c3a5bd2e0c0de0712ba459607e6139
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="using-android-assets"></a>Mediante activos de Android

_Activos_ proporcionan una manera de incluir archivos de forma arbitraria como texto, xml, fuentes, música y vídeo en la aplicación. Si se intenta incluir estos archivos como "recursos", Android procesará en su sistema de recursos y no podrá obtener los datos sin procesar. Si desea tener acceso a datos intactos, los activos son una manera de hacerlo.

Se mostrarán activos que se agregan al proyecto como un sistema de archivos que puede leer de su aplicación mediante [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
En esta demostración sencilla, vamos a agregar un recurso de archivo de texto para el proyecto, leer mediante `AssetManager`y mostrarlo en un control TextView.


## <a name="add-asset-to-project"></a>Agregar activo al proyecto

Estar activos en el `Assets` carpeta del proyecto. Agregue un nuevo archivo de texto a esta carpeta denominada `read_asset.txt`. Colocar texto en ella como "proviene de un activo!".

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio debe tener definidas las **acción de compilación** para este archivo **AndroidAsset**:

![Si se establece la acción de compilación en AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Visual Studio para Mac debe tener definidas las **acción de compilación** para este archivo **AndroidAsset**:

[![Si se establece la acción de compilación en AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Al seleccionar el valor correcto **BuildAction** garantiza que el archivo se empaquetarán en el APK en tiempo de compilación.


## <a name="reading-assets"></a>Leer activos

Activos se leen utilizando una [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Una instancia de la `AssetManager` está disponible mediante el acceso a la [activos](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) propiedad en un `Android.Content.Context`, como la actividad.
En el código siguiente, se abra nuestro **read_asset.txt** activo, lea el contenido y mostrarlo mediante un control TextView.

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
