---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: f43147d98599d36aa136e4ddc2975205e0e69e26
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122847"
---
# <a name="textureview"></a>TextureView

La `TextureView` clase es una vista que usa la representación 2D y acelerados por hardware para habilitar un vídeo o una secuencia de contenido de OpenGL para mostrarse. Por ejemplo, la captura de pantalla siguiente muestra el `TextureView` mostrar una fuente directa desde la cámara del dispositivo:

[![Captura de pantalla de ejemplo de una imagen en directo desde la cámara del dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

A diferencia de la `SurfaceView` (clase), que también se puede utilizar para mostrar el contenido de vídeo o de OpenGL, no se procesa el TextureView en una ventana independiente.
Por lo tanto, `TextureView` es capaz de admitir las transformaciones de vista como cualquier otra vista. Por ejemplo, girar un `TextureView` puede realizarse estableciendo simplemente su `Rotation` propiedad, su transparencia estableciendo su `Alpha` propiedad y así sucesivamente.

Por lo tanto, con el `TextureView` ahora podemos hacer cosas como mostrar una secuencia en directo de la cámara y transformarlos, tal como se muestra en el código siguiente:

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;
       
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;
           
        SetContentView (_textureView);
    }
       
    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }
           
        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

El código anterior crea un `TextureView` instancia de la actividad `OnCreate` método y establece la actividad como la `TextureView`del `SurfaceTextureListener`. Sea el `SurfaceTextureListener`, la actividad se implementa el `TextureView.ISurfaceTextureListener` interfaz. Se llamará el sistema la `OnSurfaceTextAvailable` método cuando el `SurfaceTexture` está listo para su uso. En este método, tomamos el `SurfaceTexture` que se pasa y establézcalo como textura de vista previa de la cámara. A continuación, se libre para realizar operaciones basadas en vistas normales, como establecer el `Rotation` y `Alpha`, como en el ejemplo anterior. La aplicación resultante, que se ejecuta en un dispositivo, se muestra a continuación:

[![Ejemplo de la aplicación se ejecuta en un dispositivo, mostrar una imagen](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Para usar el `TextureView`, aceleración de hardware debe estar habilitada, que será de forma predeterminada a partir del 14 de nivel de API. También, ya que este ejemplo utiliza la cámara, tanto el `android.permission.CAMERA` permiso y el `android.hardware.camera` característica debe establecerse el **AndroidManifest.xml**.



## <a name="related-links"></a>Vínculos relacionados

- [TextureViewDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [Introducción a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android de 4.0](http://developer.android.com/sdk/android-4.0.html)
