---
title: Introducción a iOS
description: Este documento describe cómo empezar a usar la incrustación de .NET con iOS. Explica los requisitos y presenta una aplicación de ejemplo para demostrar cómo enlazar un ensamblado administrado y usar el resultado en un proyecto de Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3da76e650281cbf7320b88fa9c76948865d7a2fb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793566"
---
# <a name="getting-started-with-ios"></a>Introducción a iOS

## <a name="requirements"></a>Requisitos

Además de los requisitos de nuestro [introducción con Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guía, también necesitará:

* [Xamarin.iOS 10.11](https://www.visualstudio.com/xamarin/) o posterior

## <a name="hello-world"></a>Hola a todos

En primer lugar, compile un ejemplo del mundo Hola simple en C#.

### <a name="create-c-sample"></a>Crear el ejemplo de C#

Abra Visual Studio para Mac, cree un nuevo proyecto de biblioteca de clases de iOS, asígnele el nombre **Hola de csharp**y guárdelo en **~/Projects/hello-from-csharp**.

Reemplace el código de la **MyClass.cs** archivo con el siguiente fragmento:

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

Una vez que tenga un ensamblado administrado, enlazar invocando la incrustación. NET.

Como se describe en el [instalación](~/tools/dotnet-embedding/get-started/install/install.md) guía, puede hacerlo como paso posterior a la compilación del proyecto, con un destino de MSBuild personalizado o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

El marco de trabajo se colocará en **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar el resultado generado en un proyecto de Xcode

Abra Xcode, crear una única aplicación de vista de iOS nuevo, asígnele el nombre **Hola de csharp**y seleccione el **Objective-C** language.

Abra la **~/Projects/hello-from-csharp/output** directorio en Finder, seleccione **Hola de csharp.framework**, arrástrelo al proyecto Xcode y colóquela justo encima el **Hola de csharp**  carpeta del proyecto.

! [Arrastrar y colocar framework] Images/Hello-from-CSharp-IOS-Drag-DROP-Framework.png)

Asegúrese de que **copiar elementos si es necesario** está activada en el cuadro de diálogo que aparece y haga clic en **finalizar**.

![Copiar elementos si es necesario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Seleccione el **Hola de csharp** proyecto y navegue hasta la **Hola de csharp** del destino **ficha General**. En el **incrustado binario** sección, agregue **Hola de csharp.framework**.

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

Incrustación de .NET no se admite actualmente bitcode en iOS, que está habilitada para algunas plantillas de proyecto de Xcode. 

Deshabilitar en la configuración del proyecto:

![Opción Bitcode](../../images/ios-bitcode-option.png)

Por último, ejecute el proyecto de Xcode, y se mostrará a algo parecido a esto:

![Hola de ejecución en el simulador del ejemplo de C#](ios-images/hello-from-csharp-ios.png)
