---
title: IOS de UrhoSharp y compatibilidad con tvOS
description: Este documento describen iOS y tvOS compatibilidad para UrhoSharp. Describe cómo crear un proyecto, configurar e iniciar Urho y realizar una inserción de Urho personalizado.
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 3efdbfcfdd670525dbf3198deb17c4631a889c56
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832462"
---
# <a name="urhosharp-ios-and-tvos-support"></a>Compatibilidad con iOS y tvOS de UrhoSharp

Aunque Urho es una biblioteca de clases portable, y permite la misma API que se usará en toda la plataforma distintos para la lógica de juego, deberá inicializar Urho en su controlador específico de plataforma y en algunos casos, deseará aprovechar las ventajas de las características específicas de plataforma .

En las páginas siguientes, se supone que `MyGame` es un sublcass de la `Application` clase.

## <a name="ios-and-tvos"></a>iOS y tvOS

**Arquitecturas compatibles:** armv7, arm64, i386

## <a name="creating-a-project"></a>Creación de un proyecto

Crear un proyecto de iOS y, a continuación, agregar datos al directorio de recursos y asegúrese de que todos los archivos tienen **BundleResource** como el **acción de compilación**.

![Configuración del proyecto](ios-images/image-4.png "agregar datos al directorio de recursos")

## <a name="configuring-and-launching-urho"></a>Configura e inicia Urho

Agregar mediante las instrucciones para la `Urho` y `Urho.iOS` espacios de nombres y, a continuación, agregue este código para inicializar Urho, así como para iniciar la aplicación:

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

Es importante deshabilitar optimizaciones PNG porque el optimizador PNG predeterminado iOS generará las imágenes que Urho no puede usar actualmente correctamente

## <a name="custom-embedding-of-urho"></a>Inserción personalizada de Urho

Se puede como alternativa a tener Urho ocupe la pantalla de toda la aplicación, y para usarlo como un componente de la aplicación, puede crear un `UrhoSurface` que es un `UIView` que puede incrustar en la aplicación existente.

Esto es lo que necesita hacer:

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Esto va a hospedar la clase Urho, por lo tanto, a continuación, debe realizar:

```csharp
new MyGame().Run ();
```
