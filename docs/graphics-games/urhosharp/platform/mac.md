---
title: Compatibilidad con Mac UrhoSharp
description: Este documento explica la compatibilidad con macOS UrhoSharp. Describe cómo crear un proyecto y proporciona un vínculo a ejemplos de código.
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: aae7b09231ae0e8f88bb9435f50fadd2ff822c1a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783348"
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


