---
title: Proyectos compartidos
description: Los proyectos compartidos le permiten escribir el código común que se hace referencia mediante un número de proyectos de aplicación diferente. El código se compila como parte de cada proyecto que hace referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de la plataforma en la base de código compartido.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: a7d5a25c3c6f2889bde0ff4aeaf85053bcc796fb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="shared-projects"></a>Proyectos compartidos

_Los proyectos compartidos le permiten escribir el código común que se hace referencia mediante un número de proyectos de aplicación diferente. El código se compila como parte de cada proyecto que hace referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de la plataforma en la base de código compartido._

Los proyectos compartidos (en ocasiones también denominados proyectos de recurso compartido) le permiten escribir código que se comparte entre varios proyectos de destino, incluidas las aplicaciones de Xamarin.

Admiten directivas de compilador para que puede incluir de forma condicional código específico de plataforma que se compilarán en un subconjunto de los proyectos que se hace referencia en el proyecto compartido. No hay compatibilidad con IDE para ayudar a administrar las directivas de compilador y ver cómo se verá el código en cada aplicación.

Si ha usado la vinculación de archivos en el pasado para compartir código entre proyectos, los proyectos compartidos funciona de forma similar, pero con mucho mejor compatibilidad IDE.



## <a name="what-is-a-shared-project"></a>¿Qué es un proyecto compartido?

A diferencia de muchos otros tipos de proyecto un proyecto compartido no tiene ninguna salida (en forma de archivo DLL), en su lugar, el código se compila en cada proyecto que hace referencia a él. Esto se ilustra en el diagrama siguiente: conceptualmente todo el contenido del proyecto compartido es "copia en" cada proyecto que hace referencia y compila como si fuese una parte de ellos.

 ![](shared-projects-images/sharedassetproject.png "Arquitectura de proyecto compartido")

El código en un proyecto compartido puede contener directivas de compilador que se habilitan o deshabilitan las secciones de código dependiendo de la aplicación que el proyecto está utilizando el código, que se sugiere por los cuadros de color de plataforma en el diagrama.

No se compilan un proyecto compartido por sí mismo, cuando existe únicamente como una agrupación de los archivos de código fuente que se pueden incluir en otros proyectos. Cuando se hace referencia a otro proyecto, el código se compila de forma eficaz como *parte* de ese proyecto. Proyectos compartidos no pueden hacer referencia a cualquier otro tipo de proyecto (incluidos los otros proyectos compartidos).

Tenga en cuenta que los proyectos de aplicación de Android no pueden hacer referencia a otros proyectos de aplicación de Android, por ejemplo, un proyecto de prueba unitaria Android no puede hacer referencia a un proyecto de aplicación de Android. Para obtener más información acerca de esta limitación, consulte este [foro de debate](http://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Tutorial de Visual Studio para Mac


Esta sección se explica cómo crear y usar un proyecto compartido con Visual Studio para Mac. Consulte el en [ejemplo de proyecto compartido](#Shared_Project_Example) sección para obtener un ejemplo completo.


## <a name="creating-a-shared-project"></a>Crear un proyecto compartido


Para crear un nuevo proyecto compartido vaya a **archivo > nueva solución...**  y elija un nombre.


![](shared-projects-images/xs-newsolution.png "Nueva solución")


También puede agregar un nuevo proyecto compartido a una solución, haga doble clic en el archivo de solución y elija **Agregar > Agregar nuevo proyecto...** . Una nueva apariencia de proyecto compartido, tal y como se muestra a continuación: observe no hay ninguna referencia o nodos de componente; no se admiten para los proyectos compartidos.


![](shared-projects-images/xs-empty.png "Proyecto vacío de servidor compartido")


Un proyecto compartido ser útil, necesita al menos un proyecto capaz de compilación (por ejemplo, un iOS o Android aplicación o biblioteca o un proyecto PCL) que hace referencia. Un proyecto compartido no se compilan cuando no tiene nada que hacen referencia a él, sintaxis así (o cualquier otro) no se resaltarán los errores hasta que se ha hecho referencia a otro elemento.



Agregar una referencia a un proyecto compartido se realiza del mismo modo que hace referencia a un proyecto de biblioteca normal. Esta captura de pantalla muestra un proyecto de Xamarin.iOS que hacen referencia a un proyecto compartido.


![](shared-projects-images/xs-reference.png "Referencia de proyecto a proyecto compartido")


Una vez que se hace referencia el proyecto compartido por otra biblioteca o una aplicación puede compilar la solución y ver los errores en el código. Cuando hace referencia el proyecto compartido _dos o más_ otros proyectos, aparece un menú en la parte superior izquierda del editor de código fuente que se muestra en elegir los proyectos que hacen referencia a este archivo.



## <a name="shared-project-options"></a>Comparten opciones de proyecto


Cuando haga doble clic en un proyecto compartido y elija **opciones** existe menos opciones que otros tipos de proyecto. Dado que no se compilan proyectos compartidos (por sí solas), no se puede establecer opciones de compilador o de salida, las configuraciones de proyecto, firmar el ensamblado o comandos personalizados. El código en un proyecto compartido eficazmente hereda estos valores de lo que hace referencia a ellos.



El **opciones** pantalla se muestra a continuación: el proyecto **nombre** y **Default Namespace** son la configuración solo dos que generalmente cambiará.


![](shared-projects-images/xs-sharedprojectoptions.png "Comparten opciones de proyecto")



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)



## <a name="visual-studio-walkthrough"></a>Tutorial de Visual Studio


Esta sección le guía por el proceso de crear y usar un proyecto compartido con Visual Studio. Consulte el a [ejemplo de proyecto compartido](#Shared_Project_Example) sección para una implementación completa.


### <a name="creating-a-shared-project"></a>Crear un proyecto compartido


Para crear un nuevo proyecto compartido vaya a **archivo > nueva solución...**  y elija un nombre para el proyecto y la solución.


![](shared-projects-images/vs-newsolution.png "Nueva solución")


También puede agregar un nuevo proyecto compartido a una solución, haga doble clic en el archivo de solución y elija **Agregar > Nuevo proyecto...** . Un nuevo proyecto compartido busca tal y como se muestra a continuación (después de que se ha agregado un archivo de clase): observe no hay ninguna referencia o nodos de componente; no se admiten para los proyectos compartidos.


![](shared-projects-images/vs-empty.png "Proyecto vacío de servidor compartido")


Un proyecto compartido ser útil, necesita al menos un proyecto capaz de compilación (por ejemplo, un iOS o Android aplicación o biblioteca o un proyecto PCL) que hace referencia. Un proyecto compartido no se compilan cuando no tiene nada que hacen referencia a él, sintaxis así (o cualquier otro) no se resaltarán los errores hasta que se ha hecho referencia a otro elemento.



Agregar una referencia a un proyecto compartido se realiza del mismo modo que hace referencia a un proyecto de biblioteca normal. Esta captura de pantalla muestra un proyecto de Xamarin.iOS que hacen referencia a un proyecto compartido.


![](shared-projects-images/vs-reference.png "Referencia de proyecto a proyecto compartido")


Una vez que se hace referencia el proyecto compartido por otra biblioteca o una aplicación puede compilar la solución y ver los errores en el código. Cuando hace referencia el proyecto compartido _dos o más_ otros proyectos, un menú aparece en la parte superior izquierda del editor de código fuente para ver los proyectos que hacen referencia al archivo de código actual.


### <a name="shared-project-properties"></a>Propiedades del proyecto compartido


Cuando se seleccionan un proyecto compartido menos valores de configuración en el panel de propiedades de otros tipos de proyecto. Dado que no se compilan proyectos compartidos (por sí solas), no se puede establecer opciones de compilador o de salida, las configuraciones de proyecto, firmar el ensamblado o comandos personalizados. El código en un proyecto compartido eficazmente hereda estos valores de lo que hace referencia a ellos.



El **propiedades** panel se muestra a continuación: el **raíz Namespace** es la única opción que se puede cambiar.


![](shared-projects-images/vs-sharedprojectproperties.png "Propiedades del proyecto compartido")



-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Ejemplo de proyecto compartido

El [Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky) en el ejemplo se utiliza un proyecto compartido para que contenga el código común usado por ambos iOS, Android y Windows Phone aplicaciones. Tanto el `SQLite.cs` y `TaskRepository.cs` archivos de código fuente utilizan directivas de compilador (p. ej. `#if __ANDROID__`) para generar un resultado diferente para cada una de las aplicaciones que hacen referencia a ellos.

A continuación se muestra la estructura de la solución completa (en Visual Studio para Mac y Visual Studio respectivamente):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 ![](shared-projects-images/xs-examplesolution.png "Visual Studio para la solución de Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 ![](shared-projects-images/vs-examplesolution.png "Solución de Visual Studio")

-----

El proyecto de Windows Phone se puede navegar desde dentro de Visual Studio para Mac, aunque no se admite ese tipo de proyecto para la compilación en Visual Studio para Mac.

A continuación se muestran las aplicaciones en ejecución.

 ![](shared-projects-images/example.png "ejemplos de iOS, Android, Windows Phone")



## <a name="summary"></a>Resumen

Este documento describe cómo funcionan los proyectos compartidos, cómo puede crear y usar en Visual Studio para Mac y Visual Studio e introdujo una aplicación de ejemplo simple que muestra un proyecto compartido en acción.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Bibliotecas de clases portables (ejemplo)](~/cross-platform/app-fundamentals/pcl.md)
- [Opciones (ejemplo) de código compartido](~/cross-platform/app-fundamentals/code-sharing.md)
