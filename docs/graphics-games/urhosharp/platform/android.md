---
title: Compatibilidad de UrhoSharp Android
description: Este documento describe la configuración específica de Android y la información relacionada con la característica de UrhoSharp. En concreto, se tratan las arquitecturas compatibles, cómo crear un proyecto, configura e inicia Urho e incrustación personalizado de Urho.
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: e7371fa85fd5955e9a0fd285adb32844001821b3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285186"
---
# <a name="urhosharp-android-support"></a>Compatibilidad de UrhoSharp Android

_Características y configuración específica de android_

Aunque Urho es una biblioteca de clases portable, y permite la misma API que se usará en toda la plataforma distintos para la lógica de juego, deberá inicializar Urho en su controlador específico de plataforma y en algunos casos, deseará aprovechar las ventajas de las características específicas de plataforma .

En las páginas siguientes, se supone que `MyGame` es una subclase de la `Application` clase.

## <a name="architectures"></a>Arquitecturas

**Arquitecturas compatibles**: x86, armeabi, armeabi-v7a

## <a name="create-a-project"></a>Crear un proyecto

Cree un proyecto de Android y agregue el paquete UrhoSharp NuGet.

Agregar datos que contiene los recursos para la **activos** directory y asegúrese de que todos los archivos tienen **AndroidAsset** como el **acción de compilación**.

![Configuración del proyecto](android-images/image-3.png "agregar datos que contiene los activos en el directorio de recursos")

## <a name="configure-and-launching-urho"></a>Configurar e iniciar Urho

Agregar mediante las instrucciones para la `Urho` y `Urho.Android` espacios de nombres y, a continuación, agregue este código para inicializar Urho, así como para iniciar la aplicación.

La manera más sencilla de ejecutar un juego, tal como se implementa en la clase MyGame es llamar a

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

Se abrirá una actividad de pantalla completa con el juego como un contenido.

## <a name="custom-embedding-of-urho"></a>Inserción personalizada de Urho

Se puede como alternativa a tener Urho ocupe la pantalla de toda la aplicación, y para usarlo como un componente de la aplicación, puede crear un `SurfaceView` a través de:

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

También deberá reenviar algunos eventos de su actividad a UrhoSharp, p. ej.:

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

Debe hacer lo mismo: `OnResume`, `OnPause`, `OnLowMemory`, `OnDestroy`, `DispatchKeyEvent` y `OnWindowFocusChanged`.

Esto muestra una actividad típica que se inicia el juego:

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```

