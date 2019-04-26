---
title: 'Parte 1: crear un MonoGame multiplataforma'
description: Este tutorial muestra cómo crear un nuevo proyecto para iOS y Android con MonoGame. El resultado es un Visual Studio para la solución de Mac con un proyecto de código compartido multiplataforma, así como un proyecto para cada plataforma. Este proyecto mostrará una pantalla azul vacía cuando se ejecuta.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 82b1408cafedf98a8619e8e039ba00b332f74516
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61381860"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Parte 1: crear un MonoGame multiplataforma

_Este tutorial muestra cómo crear un nuevo proyecto para iOS y Android con MonoGame. El resultado es un Visual Studio para la solución de Mac con un proyecto de código compartido multiplataforma, así como un proyecto para cada plataforma. Este proyecto mostrará una pantalla azul vacía cuando se ejecuta._

MonoGame permite el desarrollo de juegos multiplataforma con gran parte de la reutilización del código. En este tutorial se centrará en la configuración de una solución que contiene los proyectos de iOS y Android, así como un proyecto de código compartido de código multiplataforma.

Cuando hayamos terminados, crearemos un proyecto con la estructura adecuada para llevar a cabo la lógica de actualización del juego y lógica de dibujo a 30 fotogramas por segundo de juegos. Se puede usar como el proyecto de base para cualquier proyecto MonoGame. Nuestro proyecto tendrá este aspecto cuando se ejecuta:

![Pantalla azul en blanco](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>Adición de MonoGame a Visual Studio para Mac

MonoGame puede agregarse como un complemento para Visual Studio para Mac. En Mac, seleccione **Visual Studio para Mac** > **Administrador de complementos...**  . En Windows, seleccione ** Herramientas ** > **Administrador de complementos...**  . Seleccione el **galería** , expanda el **desarrollo de juegos** categoría y seleccione **MonoGame Addin**, a continuación, haga clic en **instalar**:

![Visual Studio para la Galería de extensiones de Mac seleccionando MonoGame](part1-images/image2.png)

> [!IMPORTANT]
> **Nota**: Si el **desarrollo de juegos** sección no aparece en el Administrador de complementos, puede descargar manualmente e instalar la versión más reciente desde aquí: http://www.monogame.net/downloads/. Es posible que deba reiniciar Visual Studio para Mac para las plantillas que aparecen.

Una vez instalado, MonoGame aparezcan en Visual Studio para Mac, como veremos en la sección siguiente.

## <a name="creating-a-new-solution"></a>Crear una nueva solución

En Visual Studio para seleccionar Mac **archivo > nueva solución**. En el **nuevo proyecto** cuadro de diálogo, haga clic en **varios**, desplácese hasta la **General** sección, seleccione el ** aplicación Universal MonoGame Mobile ** opción y haga clic en siguiente.

![Cuadro de diálogo de proyecto nueva creación de una aplicación MonoGame](part1-images/image3.png)

Denomine el proyecto WalkingGame y haga clic en crear:

![Nuevo cuadro de diálogo de proyecto un nombre y una ubicación de selección](part1-images/image4.png)

Ahora nuestro proyecto se ejecutará al igual que cualquier otro de proyecto de Android o iOS. Mostrar un fondo azul aciano debe ejecutar el proyecto:

![Fondo de la aplicación azul en blanco](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Corrección de errores de compilación de Android

La versión actual de las plantillas de MonoGame incluye algunos errores de sintaxis en el Android `Activity1.cs` archivo. Para corregir estos problemas, reemplace el `OnCreate` función por lo siguiente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>Resumen

En este tutorial ha explicado cómo crear un proyecto de MonoGame multiplataforma con Visual Studio para Mac. El resultado de esto es una pantalla azul vacía. Este proyecto puede usarse como punto de partida para cualquier iOS y Android juego.

## <a name="related-links"></a>Vínculos relacionados

- [NuGet MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentación de API MonoGame](http://www.monogame.net/documentation/?page=main)
