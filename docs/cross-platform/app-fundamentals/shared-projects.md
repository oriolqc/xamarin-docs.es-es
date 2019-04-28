---
title: Uso compartido de proyectos para compartir código
description: Proyectos compartidos permiten escribir código común que se hace referencia a un número de proyectos de aplicación diferentes. El código se compila como parte de cada proyecto que hace referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de plataforma en el código base compartido.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 61096635cd94d0fdd0abe6fda59c4efa41eeceb1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61193358"
---
# <a name="shared-projects-code-sharing"></a>Compartir el uso compartido de código de proyectos

_Proyectos compartidos permiten escribir código común que se hace referencia a un número de proyectos de aplicación diferentes. El código se compila como parte de cada proyecto que hace referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de plataforma en el código base compartido._

Proyectos compartidos (a veces se denominan proyectos de activos compartidos) le permiten escribir código que se comparte entre varios proyectos de destino, incluidas las aplicaciones de Xamarin.

Admiten las directivas de compilador para que condicionalmente puede incluir código específico de plataforma que se compilarán en un subconjunto de los proyectos que se hace referencia el proyecto compartido. No hay compatibilidad con IDE para ayudar a administrar las directivas de compilador y ver cuál será la apariencia del código en cada aplicación.

Si ha usado la vinculación de archivos en el pasado para compartir código entre proyectos, proyectos compartidos funciona de forma similar, pero con mucho mejor compatibilidad del IDE.

## <a name="what-is-a-shared-project"></a>¿Qué es un proyecto compartido?

A diferencia de muchos otros tipos de proyecto un proyecto compartido no tiene ningún resultado (en formato de archivo DLL), en su lugar se compila el código en cada proyecto que hace referencia a él. Esto se muestra en el diagrama siguiente: conceptualmente es todo el contenido del proyecto compartido "copian" en cada proyecto que hace referencia y compilado como si fuese una parte de ellos.

![](shared-projects-images/sharedassetproject.png "Arquitectura del proyecto compartido")

El código en un proyecto compartido puede contener las directivas de compilador que se habilitan o deshabilitan las secciones de código en función de qué aplicación el proyecto está utilizando el código, que se sugiere en los cuadros de color de plataforma en el diagrama.

No se compilan un proyecto compartido por sí mismo, cuando existe únicamente como una agrupación de los archivos de código fuente que puede incluirse en otros proyectos. Cuando se hace referencia a otro proyecto, el código se compila de forma eficaz como *parte* de ese proyecto. Proyectos compartidos no pueden hacer referencia a cualquier otro tipo de proyecto (incluidos los otros proyectos compartidos).

Tenga en cuenta que los proyectos de aplicación de Android no pueden hacer referencia a otros proyectos de aplicación de Android, por ejemplo, un proyecto de prueba unitaria Android no puede hacer referencia a un proyecto de aplicación de Android. Para obtener más información acerca de esta limitación, consulte este [foro de debate](http://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Tutorial de Visual Studio para Mac

Esta sección se explica cómo crear y usar un proyecto compartido con Visual Studio para Mac. Consulte el en [ejemplo de proyecto compartido](#Shared_Project_Example) sección para obtener un ejemplo completo.

## <a name="creating-a-shared-project"></a>Creación de un proyecto compartido

Para crear un nuevo proyecto compartido vaya a **archivo > nueva solución...**  (o haga clic en una solución existente y elegir **Agregar > Agregar nuevo proyecto...** ):

[![Nuevo proyecto compartido](shared-projects-images/xs-newsolution-sml.png "nueva solución")](shared-projects-images/xs-newsolution.png#lightbox)

En la siguiente pantalla, elija el nombre del proyecto y haga clic en **crear**.

A continuación se muestra un nuevo proyecto compartido: Observe que hay no son ninguna referencia o nodos de componente; no son compatibles para los proyectos compartidos.

![Vacío compartido proyecto](shared-projects-images/xs-empty.png "vacío proyecto compartido")

Para que un proyecto compartido sea útil, necesita al menos un proyecto capaz de compilación (por ejemplo, un iOS o Android aplicación o biblioteca o un proyecto PCL) que hace referencia. Un proyecto compartido no se compilan cuando no tiene nada que hacen referencia a él, la sintaxis es así (o cualquier otro) no se resaltarán los errores hasta que se ha hecho referencia a otro elemento.

Agregar una referencia a un proyecto compartido se realiza la misma forma que hace referencia a un proyecto de biblioteca normal. Esta captura de pantalla muestra un proyecto de Xamarin.iOS que hacen referencia a un proyecto compartido.

![](shared-projects-images/xs-reference.png "Referencia de proyecto a proyecto compartido")

Una vez que se hace referencia el proyecto compartido por otra aplicación o biblioteca puede compilar la solución y ver los errores en el código. Cuando hace referencia el proyecto compartido _dos o más_ otros proyectos, aparece un menú en la parte superior izquierda del editor de código fuente que se muestra, elija los proyectos que hacen referencia a este archivo.

## <a name="shared-project-options"></a>Compartir las opciones de proyecto

Cuando haga doble clic en un proyecto compartido y elija **opciones** hay menos opciones que otros tipos de proyecto. Dado que no se compilan proyectos compartidos (por sí mismas), no se puede establecer las opciones del compilador o de salida, las configuraciones de proyecto, firmar el ensamblado o comandos personalizados. El código en un proyecto compartido hereda de forma eficaz estos valores de todo lo que hace referencia a ellos.



El **opciones** pantalla se muestra más abajo: el proyecto **nombre** y **Default Namespace** son los valores de solo dos que generalmente cambiará.


![](shared-projects-images/xs-sharedprojectoptions.png "Compartir las opciones de proyecto")



# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)



## <a name="visual-studio-walkthrough"></a>Tutorial de Visual Studio


Esta sección se explica cómo crear y usar un proyecto compartido con Visual Studio. Consulte el a [ejemplo de proyecto compartido](#Shared_Project_Example) sección para una implementación completa.

### <a name="creating-a-shared-project"></a>Creación de un proyecto compartido

Para crear un nuevo proyecto compartido vaya a **archivo > nueva solución...**  y elija un nombre para la solución y proyecto.

![](shared-projects-images/vs-newsolution.png "Nueva solución")

También puede agregar un nuevo proyecto compartido a una solución, con el botón secundario en el archivo de solución y elija **Agregar > Nuevo proyecto...** . Un nuevo proyecto compartido es como se muestra a continuación (después de que se ha agregado un archivo de clase): tenga en cuenta que no existan referencias o nodos de componente; no son compatibles para los proyectos compartidos.

![](shared-projects-images/vs-empty.png "Proyecto vacío de servidor compartido")

Para que un proyecto compartido sea útil, necesita al menos un proyecto capaz de compilación (por ejemplo, un iOS o Android aplicación o biblioteca o un proyecto PCL) que hace referencia. Un proyecto compartido no se compilan cuando no tiene nada que hacen referencia a él, la sintaxis es así (o cualquier otro) no se resaltarán los errores hasta que se ha hecho referencia a otro elemento.

Agregar una referencia a un proyecto compartido se realiza la misma forma que hace referencia a un proyecto de biblioteca normal. Esta captura de pantalla muestra un proyecto de Xamarin.iOS que hacen referencia a un proyecto compartido.

![](shared-projects-images/vs-reference.png "Referencia de proyecto a proyecto compartido")

Una vez que se hace referencia el proyecto compartido por otra aplicación o biblioteca puede compilar la solución y ver los errores en el código. Cuando hace referencia el proyecto compartido _dos o más_ otros proyectos, un menú aparece en la parte superior izquierda del editor de código fuente para ver qué proyectos hacen referencia al archivo de código actual.


### <a name="shared-project-properties"></a>Propiedades del proyecto compartido


Al seleccionar un proyecto compartido menos valores de configuración en el panel de propiedades que otros tipos de proyecto. Dado que no se compilan proyectos compartidos (por sí mismas), no se puede establecer las opciones del compilador o de salida, las configuraciones de proyecto, firmar el ensamblado o comandos personalizados. El código en un proyecto compartido hereda de forma eficaz estos valores de todo lo que hace referencia a ellos.

El **propiedades** panel se muestra más abajo: la **raíz Namespace** es la única opción que se puede cambiar.

![](shared-projects-images/vs-sharedprojectproperties.png "Propiedades del proyecto compartido")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Ejemplo de proyecto compartido

El [Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky) en el ejemplo se usa un proyecto compartido para que contenga el código común usado por ambos iOS, Android y Windows Phone de aplicaciones. Tanto el `SQLite.cs` y `TaskRepository.cs` archivos de código fuente utilizan directivas de compilador (p ej. `#if __ANDROID__`) para producir un resultado diferente para cada una de las aplicaciones que hacen referencia a ellos.

A continuación se muestra la estructura de solución completa (en Visual Studio para Mac y Visual Studio, respectivamente):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio para la solución de Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Solución de Visual Studio")

-----

El proyecto de Windows Phone se puede navegar desde dentro de Visual Studio para Mac, aunque no se admite ese tipo de proyecto para la compilación en Visual Studio para Mac.

Las aplicaciones en ejecución se muestran a continuación:

![](shared-projects-images/example.png "ejemplos de iOS, Android, Windows Phone")

## <a name="summary"></a>Resumen

Este documento describe cómo funcionan los proyectos compartidos, cómo pueden crear y usar en Visual Studio para Mac y Visual Studio e introdujo una aplicación de ejemplo simple que muestra un proyecto compartido en acción.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Bibliotecas de clases portables (ejemplo)](~/cross-platform/app-fundamentals/pcl.md)
- [Uso compartido de las opciones de código (ejemplo)](~/cross-platform/app-fundamentals/code-sharing.md)
