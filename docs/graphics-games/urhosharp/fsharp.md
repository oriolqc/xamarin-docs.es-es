---
title: Programación de UrhoSharp conF#
description: Este documento describe cómo crear una sencilla hello world UrhoSharp aplicación mediante F# en Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 99e8f8bf04465d0d61086139ba9889eae141207e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61160613"
---
# <a name="programming-urhosharp-with-f"></a>Programación de UrhoSharp conF#

Se puede programar UrhoSharp con F# usando las mismas bibliotecas y conceptos que se usan por C# los programadores. El [uso de UrhoSharp](~/graphics-games/urhosharp/using.md) artículo ofrece información general del motor de UrhoSharp y se debe leer antes de este artículo.

Como muchas bibliotecas que se originaron en el mundo de C++, muchas funciones de UrhoSharp devuelven valores booleanos o enteros que indica éxito o error. Debe usar `|> ignore` para pasar por alto estos valores.

El [programa de ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) es un "Hello World" de UrhoSharp desde F#.

## <a name="creating-an-empty-project"></a>Crear un proyecto vacío

Hay ningún F# plantillas para UrhoSharp aún está disponible, por lo que para crear su propio proyecto de UrhoSharp puede empezar con el [ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) o siga estos pasos:

1. Desde Visual Studio para Mac, cree un nuevo **solución**. Elija **iOS > aplicación > aplicación de vista única** y seleccione **F#** como el lenguaje de implementación. 
1. Eliminar el **Main.storyboard** archivo. Abra el **Info.plist** archivo y en el **iPhone / iPod información de implementación** panel, eliminar el `Main` de cadena en el **interfaz principal** lista desplegable.
1. Eliminar el **ViewController.fs** también el archivo.

## <a name="building-hello-world-in-urho"></a>Creación de Hello World en Urho

Ahora está listo para empezar a definir las clases de su juego. Como mínimo, deberá definir una subclase de `Urho.Application` e invalidar sus `Start` método. Para crear este archivo, haga doble clic en su F# del proyecto, elija **Agregar nuevo archivo...**  y agregue un valor vacío F# clase al proyecto. El nuevo archivo se agregará al final de la lista de archivos en el proyecto, pero debe arrastrar para que aparezca *antes* se utiliza en **AppDelegate.fs**.

1. Agregue una referencia al paquete Urho NuGet.
1. Desde un proyecto existente Urho, copie los directorios (grandes) **CoreData /** y **datos /** en el proyecto **recursos /** directory. En su F# de proyecto, haga doble clic en el **recursos** carpeta y utilice **agregar / Agregar carpeta existente** para agregar todos estos archivos al proyecto.

La estructura del proyecto deberá parecerse:

![](fsharp-images/solutionpane.png "La estructura del proyecto deberá parecerse")

Definir la clase recién creado como un subtipo de `Urho.Application` e invalidar sus `Start` método:

```fsharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

El código es muy sencillo. Usa el `Urho.Gui.Text` clase para mostrar una cadena alineada en el centro con un determinado tamaño de fuente y color. 

Antes de poder ejecutar este código, sin embargo, se debe inicializar UrhoSharp. 

Abra el archivo AppDelegate.fs y modifique el `FinishedLaunching` método como sigue:

```fsharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

El `ApplicationOptions.Default` proporciona las opciones predeterminadas para una aplicación en modo horizontal. Pasar estos `ApplicationOptions` al constructor predeterminado para su `Application` subclase (tenga en cuenta que al definir el `HelloWorld` clase, la línea `inherit Application(o)` llama al constructor de clase base). 

El `Run` método de su `Application` inicia el programa. Se define como devolver un `int`, que se puede canalizar a `ignore`. 

El programa resultante debe ser similar:

![](fsharp-images/helloworldfsharp.png "El programa resultante debería ser similar a")








## <a name="related-links"></a>Vínculos relacionados

- [Explorar en GitHub (ejemplo)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
