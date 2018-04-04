---
title: Introducción a iOS
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 34afdd9e91ebfbe7ad57c7eec6ba7f05fff1a2aa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-ios"></a>Introducción a iOS


## <a name="requirements"></a>Requisitos

Además de los requisitos de nuestro [introducción con Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guía también necesitará:

* [Xamarin.iOS 10.11](https://www.visualstudio.com/xamarin/) o posterior

## <a name="hello-world"></a>Hola a todos

Primero vamos a compilar un ejemplo del mundo Hola simple en C#.

### <a name="create-c-sample"></a>Crear el ejemplo de C#

Abra Visual Studio para Mac, cree un nuevo proyecto de biblioteca de clases de iOS, asígnele el nombre `hello-from-csharp`y guárdelo en `~/Projects/hello-from-csharp`.

Reemplace el código en el `MyClass.cs` archivo con el siguiente fragmento:

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

Compile el proyecto, el ensamblado resultante se guardará como `~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll`.

### <a name="bind-the-managed-assembly"></a>Enlazar el ensamblado administrado

Ejecute el embeddinator para crear un marco nativo para el ensamblado administrado:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

El marco de trabajo se colocará en `~/Projects/hello-from-csharp/output/hello-from-csharp.framework`.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar el resultado generado en un proyecto de Xcode

Abra Xcode y crear una única aplicación de vista de iOS nuevo, asígnele el nombre `hello-from-csharp` y seleccione la **Objective-C** language.

Abra la `~/Projects/hello-from-csharp/output` directorio en Finder, seleccione `hello-from-csharp.framework`, arrástrelo al proyecto Xcode y colóquela justo encima el `hello-from-csharp` carpeta del proyecto.

! [Arrastrar y colocar framework] Images/Hello-from-CSharp-IOS-Drag-DROP-Framework.png)

Asegúrese de que `Copy items if needed` está activada en el cuadro de diálogo que aparece y haga clic en `Finish`.

![Copiar elementos si es necesario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Seleccione el `hello-from-csharp` proyecto y navegue hasta la `hello-from-csharp` del destino **ficha General**. En el **incrustado binario** sección, agregue `hello-from-csharp.framework`.

![Archivos binarios incrustados](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Abra ViewController.m y reemplace el contenido con:

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

Por último, ejecute el proyecto de Xcode, y se mostrará a algo parecido a esto:

![Hola de ejecución en el simulador del ejemplo de C#](ios-images/hello-from-csharp-ios.png)
