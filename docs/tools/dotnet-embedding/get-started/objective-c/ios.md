---
title: Introducción a iOS
description: Este documento describe cómo empezar a usar la inserción de .NET con iOS. Describe los requisitos y presenta una aplicación de ejemplo para demostrar cómo enlazar un ensamblado administrado y usar la salida en un proyecto de Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 009772ac88ad57bab53fb71c9705b71f0f8acc8b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118297"
---
# <a name="getting-started-with-ios"></a>Introducción a iOS

## <a name="requirements"></a>Requisitos

Además de los requisitos de nuestro [Introducción a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guía, también tendrá que:

* [Xamarin.iOS 10.11](https://visualstudio.microsoft.com/xamarin/) o posterior

## <a name="hello-world"></a>Hola a todos

En primer lugar, cree un sencilla hello world, ejemplo de C#.

### <a name="create-c-sample"></a>Crear un ejemplo de C#

Abra Visual Studio para Mac, cree un nuevo proyecto de biblioteca de clases de iOS, asígnele el nombre **hello de csharp**y guárdelo en **~/Projects/hello-from-csharp**.

Reemplace el código en el **MyClass.cs** archivo con el siguiente fragmento de código:

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

Compile el proyecto y el ensamblado resultante se guardará como **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Enlazar el ensamblado administrado

Una vez que un ensamblado administrado, puede enlazar mediante la invocación de inserción de .NET.

Como se describe en el [instalación](~/tools/dotnet-embedding/get-started/install/install.md) guía, esto puede hacerse como paso posterior a la compilación en el proyecto, con un destino de MSBuild personalizado o de forma manual:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

El marco de trabajo se colocará en **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar la salida generada en un proyecto de Xcode

Abra Xcode, cree una nueva aplicación de vista única de iOS, asígnele el nombre **hello de csharp**y seleccione el **Objective-C** lenguaje.

Abra el **~/Projects/hello-from-csharp/output** directorio en el Finder, seleccione **hello de csharp.framework**, arrástrelo al proyecto Xcode y colóquelo justo encima el **hello de csharp**  carpeta del proyecto.

![Marco de arrastrar y colocar](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Asegúrese de que **copiar elementos si es necesario** está activada en el cuadro de diálogo que aparece y haga clic en **finalizar**.

![Copiar elementos si es necesario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Seleccione el **hello de csharp** del proyecto y navegue hasta la **hello de csharp** del destino **ficha General**. En el **incrustado binario** sección, agregue **hello de csharp.framework**.

![Archivos binarios incrustados](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Abra **ViewController.m**y reemplace el contenido con:

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

Inserción de .NET no es compatible con bitcode en iOS, que está habilitado para algunas plantillas de proyecto de Xcode. 

Deshabilitar en la configuración del proyecto:

![Opción Bitcode](../../images/ios-bitcode-option.png)

Por último, ejecute el proyecto de Xcode y se mostrará algo parecido a esto:

![Hola desde el que se ejecuta en el simulador de ejemplo de C#](ios-images/hello-from-csharp-ios.png)
