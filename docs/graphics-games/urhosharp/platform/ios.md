---
title: IOS UrhoSharp y compatibilidad con tvOS
description: iOS y tvOS el programa de instalación específicas y características para UrhoSharp.
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 81adda3df55a51c375332e1cbe4b2e34574f98bf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="urhosharp-ios-and-tvos-support"></a>IOS UrhoSharp y compatibilidad con tvOS

_iOS y tvOS el programa de instalación específicas y características_

Mientras se Urho es una biblioteca de clases portables y permite a las mismas API que se usará en la plataforma distintos para la lógica de juego, todavía tiene que inicializar Urho en el controlador específico de plataforma y en algunos casos, puede aprovechar las características específicas de plataforma .

En las páginas siguientes, se asume que `MyGame` es un sublcass de la `Application` clase.

## <a name="ios-and-tvos"></a>iOS y tvOS

**Admite arquitecturas:** armv7, arm64, i386

## <a name="creating-a-project"></a>Crear un proyecto

Crear un proyecto de iOS y, a continuación, agregar datos en el directorio de recursos y asegúrese de que todos los archivos tienen **BundleResource** como el **acción de compilación**.

![Configuración del proyecto](ios-images/image-4.png "agregar datos en el directorio de recursos")

## <a name="configuring-and-launching-urho"></a>Configurar e iniciar Urho

Agregar mediante sentencias para la `Urho` y `Urho.iOS` espacios de nombres y, a continuación, agregue este código para inicializar Urho, así como para iniciar la aplicación:

```csharp
new MyGame().Run();
```

Tenga en cuenta que, puesto que iOS espera `FinishedLaunching` para completar, debe poner en cola la llamada a `Run()` para ejecutar una vez finalizado el método, se trata de una forma habitual:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

Es importante deshabilitar optimizaciones PNG porque el optimizador PNG predeterminado iOS generará las imágenes que Urho no puede consumir actualmente correctamente

## <a name="custom-embedding-of-urho"></a>Incrustar personalizado de Urho

Se puede también a tener Urho ocupe la pantalla de toda la aplicación, y para usarlo como un componente de la aplicación, puede crear un `UrhoSurface` que es un `UIView` que se puede incrustar en la aplicación existente.

Esto es lo que necesita hacer:

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Esto va a hospedar la clase Urho, por lo que, a continuación, debe realizar:

```csharp
new MyGame().Run ();
```

