---
title: Introducción a macOS
description: Este documento se describe cómo empezar a usar la inserción de .NET con macOS. Describe los requisitos y presenta una aplicación de ejemplo para demostrar cómo enlazar el ensamblado administrado y la salida generada en un proyecto de Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 3de47aa57df29f52f71508977ae017dff009c282
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61181552"
---
# <a name="getting-started-with-macos"></a>Introducción a macOS

## <a name="what-you-will-need"></a>Lo que necesita

* Siga las instrucciones de la [Introducción a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guía.

## <a name="hello-world"></a>Hola a todos

En primer lugar, cree un sencilla hello world, ejemplo de C#.

### <a name="create-c-sample"></a>Crear un ejemplo de C#

Abra Visual Studio para Mac, cree un nuevo proyecto de biblioteca de clases de Mac denominado **hello de csharp**y guárdelo en **~/Projects/hello-from-csharp**.

Reemplace el código en el **MyClass.cs** archivo con el siguiente fragmento de código:

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

Compile el proyecto. El ensamblado resultante se guardará como **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Enlazar el ensamblado administrado

Una vez que un ensamblado administrado, puede enlazar mediante la invocación de inserción de .NET.

Como se describe en el [instalación](~/tools/dotnet-embedding/get-started/install/install.md) guía, esto puede hacerse como paso posterior a la compilación en el proyecto, con un destino de MSBuild personalizado o de forma manual:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

El marco de trabajo se colocará en **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar la salida generada en un proyecto de Xcode

Abra Xcode y cree una nueva aplicación de cacao. Asígnele el nombre **hello de csharp** y seleccione el **Objective-C** lenguaje.

Abra el **~/Projects/hello-from-csharp/output** directorio en el Finder, seleccione **hello de csharp.framework**, arrástrelo al proyecto Xcode y colóquelo justo encima el **hello de csharp**  carpeta del proyecto.

![Marco de arrastrar y colocar](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Asegúrese de que **copiar elementos si es necesario** está activada en el cuadro de diálogo que aparece y haga clic en **finalizar**.

![Copiar elementos si es necesario](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Seleccione el **hello de csharp** del proyecto y navegue hasta la **hello de csharp** del destino **General** ficha. En el **incrustado binario** sección, agregue **hello de csharp.framework**.

![Archivos binarios incrustados](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Abra **ViewController.m**y reemplace el contenido con:

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

Por último, ejecute el proyecto de Xcode y se mostrará algo parecido a esto:

![Hola desde el que se ejecuta en el simulador de ejemplo de C#](macos-images/hello-from-csharp-mac.png)

Un ejemplo más completo y un mejor aspecto [está disponible aquí](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
