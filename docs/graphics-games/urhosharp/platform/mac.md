---
title: Compatibilidad con Mac UrhoSharp
description: El programa de instalación específico de Mac y características para UrhoSharp.
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: c7210cbd5586df9018c2779bf0b92aa7c1c56e89
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="urhosharp-mac-support"></a>Compatibilidad con Mac UrhoSharp

_Características y el programa de instalación específico de Mac_

Mientras se Urho es una biblioteca de clases portables y permite a las mismas API que se usará en la plataforma distintos para la lógica de juego, todavía tiene que inicializar Urho en el controlador específico de plataforma y en algunos casos, puede aprovechar las características específicas de plataforma .

En las páginas siguientes, se asume que `MyGame` es una subclase de la `Application` clase.

## <a name="macos"></a>macOS

**Admite arquitecturas:** x86/x86-64 de 32 y 64 bits.

## <a name="creating-a-project"></a>Crear un proyecto

Crear un proyecto de consola, hacen referencia a Urho NuGet y, a continuación, asegúrese de que puede localizar los activos (es decir, los directorios que contiene el directorio de datos).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Ejemplo

[Ejemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


