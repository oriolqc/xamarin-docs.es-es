---
title: Utilizar bibliotecas estándar de .NET para compartir código
description: Este documento describe cómo utilizar bibliotecas estándar de .NET para compartir código. Describe la creación de una biblioteca .NET Standard, modifique su configuración y utilizarla en una aplicación.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: d07b248b36feee909db9c863eb17f1a900f58e60
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105836"
---
# <a name="net-standard-library-code-sharing"></a>Uso compartido de código de biblioteca estándar de .NET

Bibliotecas de .NET standard tienen una API uniforme para todas las plataformas. NET, incluidos Xamarin y .NET Core. Cree una sola biblioteca de .NET Standard y usarlo desde cualquier runtime compatible con la plataforma .NET Standard. Consulte [este gráfico](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support) para obtener información detallada de las plataformas compatibles.

Mientras que las versiones de .NET Standard 1.0 a 1.6 proporcionan subconjuntos incrementalmente mayores de .NET Framework, .NET Standard 2.0 proporciona el mejor nivel de compatibilidad para las aplicaciones de Xamarin y para la portabilidad de bibliotecas de clases Portable existente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

Esta sección se explica cómo crear y usar una biblioteca de .NET Standard con Visual Studio para Mac.

### <a name="creating-a-net-standard-library"></a>Creación de una biblioteca .NET Standard

Puede agregar una biblioteca .NET Standard a la solución con estos pasos:

1. En el **Agregar nuevo proyecto** cuadro de diálogo, seleccione el **.NET Core** categoría y, a continuación, seleccione **biblioteca .NET Standard**:

    ![Crear una biblioteca .NET Standard](net-standard-images/vsm01-m157.png "crear una biblioteca nueva .NET Standard")

2. En la siguiente pantalla, elija la plataforma de destino - **.NET Standard 2.0** se recomienda:

    [![Elija .NET Standard 2.0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. En la última pantalla, escriba el nombre del proyecto y haga clic en **crear**.

4. El proyecto de biblioteca estándar de .NET aparecerá como se muestra en el Explorador de soluciones. El nodo dependencias indicará que la biblioteca usa la [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![Nodo de dependencias de la solución indica .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Editar configuración de la biblioteca estándar de .NET

La configuración de la biblioteca estándar de .NET se puede ver y cambiar con el botón secundario en el proyecto y seleccionando `Options` tal como se muestra en esta captura de pantalla:

![Edición estándar de .NET framework de destino en las opciones de proyecto](net-standard-images/vsm03-m157.png "editar la versión de la plataforma de destino de .NET estándar en las opciones de proyecto")

Puede cambiar en la versión de `netstandard` cambiando el `Target Framework` valor de la lista desplegable.

**Además:** puede editar el `.csproj` directamente para cambiar este valor.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

Esta sección se explica cómo crear y usar una biblioteca de .NET Standard con Visual Studio.

### <a name="creating-a-net-standard-library"></a>Creación de una biblioteca .NET Standard

Agregar una biblioteca .NET Standard en la solución es bastante sencillo.

1. En el **nuevo proyecto** cuadro de diálogo, seleccione el **.NET Standard** categoría y, a continuación, seleccione **biblioteca de clases (.NET Standard)**.

    ![Crear una nueva biblioteca de clases de .NET Standard](net-standard-images/vs01-w157.png "crear nueva biblioteca de clases .NET Standard")

2. El proyecto de biblioteca estándar de .NET aparecerá como se muestra en el Explorador de soluciones. El nodo dependencias indicará que la biblioteca usa la [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![NETStandard.Library en la carpeta del proyecto](net-standard-images/vs02-w157.png "proyecto .NET Standard en la solución")

### <a name="editing-net-standard-library-settings"></a>Editar configuración de la biblioteca estándar de .NET

La configuración de la biblioteca estándar de .NET se puede ver y cambiar con el botón secundario en el proyecto y seleccionando **propiedades** tal como se muestra en esta captura de pantalla:

![Editar plataformas de destino estándar de .NET en las propiedades del proyecto](net-standard-images/vs03-w157.png "hacen referencia a una biblioteca .NET Standard del mismo modo que otros proyectos")

**Además:** puede editar el `.csproj` directamente para editar el `TargetFramework` elemento y cuál es el cambio de destino (p ej. `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>Uso de un proyecto de biblioteca estándar de .NET

Una vez que se ha creado una biblioteca .NET Standard, puede agregar una referencia a él desde cualquier aplicación compatible o biblioteca de proyectos de la misma manera normalmente agregar referencias. En Visual Studio, haga doble clic en el nodo referencias y elija **Agregar referencia...**  , a continuación, cambie a la **proyectos > solución** pestaña tal como se muestra:

![Hacer referencia a una biblioteca .NET Standard](net-standard-images/vs04.png "en Visual Studio, haga doble clic en el nodo referencias y elija Agregar referencia …, a continuación, cambie a la pestaña proyectos de solución tal como se muestra")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET standard y Xamarin.Forms para el desarrollador de .NET (vídeo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>Vínculos relacionados

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -información detallada y comparación con la PCL.
