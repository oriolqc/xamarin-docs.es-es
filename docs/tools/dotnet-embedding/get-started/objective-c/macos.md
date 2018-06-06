---
title: Introducción a macOS
description: Este documento se describe cómo empezar a usar la incrustación de .NET con macOS. Explica los requisitos y presenta una aplicación de ejemplo para demostrar cómo enlazar un ensamblado administrado y el resultado generado en un proyecto de Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 38049eae5e420e5f3610341c2682fa92d2ac426e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793694"
---
# <a name="getting-started-with-macos"></a>Introducción a macOS

## <a name="what-you-will-need"></a>Lo que necesita

* Siga las instrucciones en el [introducción con Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guía.

## <a name="hello-world"></a>Hola a todos

En primer lugar, compile un ejemplo del mundo Hola simple en C#.

### <a name="create-c-sample"></a>Crear el ejemplo de C#

Abra Visual Studio para Mac, cree un nuevo proyecto de biblioteca de clases de Mac denominado **Hola de csharp**y guárdelo en **~/Projects/hello-from-csharp**.

Reemplace el código de la **MyClass.cs** archivo con el siguiente fragmento:

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

Una vez que tenga un ensamblado administrado, enlazar invocando la incrustación. NET.

Como se describe en el [instalación](~/tools/dotnet-embedding/get-started/install/install.md) guía, puede hacerlo como paso posterior a la compilación del proyecto, con un destino de MSBuild personalizado o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

El marco de trabajo se colocará en **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar el resultado generado en un proyecto de Xcode

Abra Xcode y cree una nueva aplicación de cacao. Asígnele el nombre **Hola de csharp** y seleccione la **Objective-C** language.

Abra la **~/Projects/hello-from-csharp/output** directorio en Finder, seleccione **Hola de csharp.framework**, arrástrelo al proyecto Xcode y colóquela justo encima el **Hola de csharp**  carpeta del proyecto.

![Marco de arrastrar y colocar](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Asegúrese de que **copiar elementos si es necesario** está activada en el cuadro de diálogo que aparece y haga clic en **finalizar**.

![Copiar elementos si es necesario](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Seleccione el **Hola de csharp** proyecto y navegue hasta la **Hola de csharp** del destino **General** ficha. En el **incrustado binario** sección, agregue **Hola de csharp.framework**.

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

Por último, ejecute el proyecto de Xcode, y se mostrará a algo parecido a esto:

![Hola de ejecución en el simulador del ejemplo de C#](macos-images/hello-from-csharp-mac.png)

Un ejemplo más completo y tengan una buena presentación [está disponible aquí](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
