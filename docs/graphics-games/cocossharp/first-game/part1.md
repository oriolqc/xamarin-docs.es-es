---
title: Crear un proyecto de CocosSharp multiplataforma
description: "Este tutorial muestra cómo crear una nueva solución CocosSharp de varias plataforma. El resultado de este tutorial es un en Visual Studio para la solución de Mac que incluye tres proyectos: un proyecto de biblioteca de clases portable, un proyecto de Android específico y un proyecto de iOS específico. El proyecto resultante mostrará una pantalla negra vacía cuando se ejecuta."
ms.topic: article
ms.prod: xamarin
ms.assetid: 37C97693-B0A8-4064-97B6-A6FAB5BA4FB7
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 2906035ce9bd44d111b89ccfe7443896775315b7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-multi-platform-cocossharp-project"></a>Crear un proyecto de CocosSharp multiplataforma

_Este tutorial muestra cómo crear una nueva solución CocosSharp de varias plataforma. El resultado de este tutorial es un en Visual Studio para la solución de Mac que incluye tres proyectos: un proyecto de biblioteca de clases portable, un proyecto de Android específico y un proyecto de iOS específico. El proyecto resultante mostrará una pantalla negra vacía cuando se ejecuta._

El motor de juegos 2D CocosSharp permite código y el contenido se puede compartir entre varias plataformas. Este tutorial muestra cómo crear un proyecto que puede admitir iOS y Android desarrollo. En concreto, en este tutorial se tratará los siguientes temas:

 - Instalar CocosSharp
 - Crear una nueva solución
 - Método `LoadGame`

# <a name="installing-cocossharp"></a>Instalar CocosSharp

En primer lugar, vamos a agregar CocosSharp a Visual Studio para Mac. Si se ejecuta en un equipo Mac, seleccione **Visual Studio para Mac** > **Administrador de complementos...**  . Si se ejecuta en Windows, seleccione **herramientas** > **Administrador de complementos...**  . Haga clic en el **galería** , expanda la **CocosSharp elemento**, seleccione **plantillas de proyecto CocosSharp**y por último, haga clic en **instalar...**  .

![Complemento CocosSharp](part1-images/xamarinstudioaddinsmac.png "")

# <a name="creating-a-new-solution"></a>Creación de una nueva solución

Ahora que está instalado CocosSharp, vamos a crear una solución. En Visual Studio para Mac, seleccione **archivo** > **New** > **solución...** . Seleccione el **aplicación** opción bajo el **multiplataforma** sección, seleccione **proyecto vacío de servidor CocosSharp**y, a continuación, haga clic en **siguiente**:

![](part1-images/image1.png "Seleccione la opción de la aplicación en la sección multiplataforma, seleccione proyecto vacío de servidor CocosSharp y, a continuación, haga clic en siguiente")

Escriba el nombre **BouncingGame** para el **nombre del proyecto**, a continuación, haga clic en **crear**:

![](part1-images/image2.png "Escriba el nombre BouncingGame para el nombre del proyecto, a continuación, haga clic en crear")

Una vez que se ha creado el proyecto y Visual Studio para Mac, podemos compilar y ejecútela para ver un fondo gris: 

![](part1-images/image3.png "Una vez que el proyecto se ha creado y Visual Studio para Mac, compilar y ejecutar para ver un fondo gris.")


# <a name="loadgame-method"></a>LoadGame (método)

El proyecto de CocosSharp predeterminado incluye clases específicas de iOS y Android para configurar un `CCGameView`, que se usa para iniciar CocosSharp. El `CCGameView` se crea una instancia de una manera específica de la plataforma: crea el proyecto de iOS la `CCGameView` en el `Main.storyboard` archivo mientras crea Android el `CCGameView` en el `Main.axml` archivo. Cada plataforma utiliza la instancia de CCGameView en un `LoadGame` método que realiza alguna configuración básica. Aunque se no puede modificar este código, echemos un vistazo a algunos detalles importantes:

 - El código establece el `gameView.DesignResolution` a 1024 por 768. Esto normaliza posicionamiento en todos los dispositivos, independientemente de la relación de aspecto, resolución física o la orientación del dispositivo actual. 
 - El código agrega algunas rutas de acceso de búsqueda. Rutas de búsqueda también permiten el contenido debe cargarse sin los prefijos de directorio. Por ejemplo, dado que la `"Sounds"` ruta de acceso se agrega como una ruta de acceso de búsqueda y, a continuación, un archivo en el directorio `"Content/Sounds/mysound.xnb"` simplemente se puede cargar como `"mysound.xnb"`. Son similares a las rutas de acceso de búsqueda `using` instrucciones en código: pueden reducir el código, pero también pueden presentar ambigüedad.
 - El código construye una `GameLayer` instancia. `GameLayer` es un `CCLayer`-heredar clase donde se va a agregar todos nuestra lógica de juego. Los juegos más grandes pueden requerir varios `CCLayer` instancias, o incluso varios `CCScene` instancias (que puede contener varios `CCLayer` instancias), pero nos centraremos en una sola `CCLayer` para este juego.

#  <a name="summary"></a>Resumen

Este tutorial trata cómo crear un proyecto de CocosSharp multiplataforma con Visual Studio para Mac. El resultado es una pantalla vacía que se puede usar como punto de partida para cualquier proyecto de juego.