---
title: ¿Cuándo y cómo se debería presentar un informe de errores?
description: Este documento describe cuándo, dónde y cómo en el archivo de un informe de errores. También proporciona prácticas recomendadas que permiten a los ingenieros que mejor diagnosticar el problema de informe de errores.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: conceptdev
ms.author: crdun
ms.date: 08/01/2018
ms.openlocfilehash: 1224d38a2230fa2f5c7ca08f6e33c5468886c206
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61356992"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>¿Cuándo y cómo se debería presentar un informe de errores?

> [!TIP]
> Use la **notificar un problema** elemento de menú en Visual Studio &ndash; Esto enviará información de diagnóstico, junto con su informe de errores para ayudar a resolver el problema.
>
> Hay instrucciones detalladas para [2019 de Visual Studio o Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio) y [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem).
>
> Puede buscar los informes existentes en el [Comunidad de desarrolladores de Visual Studio](https://developercommunity.visualstudio.com/) sitio Web.

## <a name="file-a-bug-if"></a>Archivar un error si...

Tiene un conjunto de pasos cree que los ingenieros podrá usar para reproducir un problema.

O

Puede describir cuidadosamente los síntomas visibles del problema, especialmente si también puede describir algunas circunstancias precisas relacionados con el problema. <sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>Prácticas recomendadas para ayudar a resolver errores de forma rápida y eficaz

1. <a name="ref-1" />Búsqueda [Comunidad de desarrolladores de Visual Studio](https://developercommunity.visualstudio.com/) y la web existentes sugerencias de uso que se pueden solucionar el problema directamente o de los informes de errores.<sup> [[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />Describa el problema como clara y concisa posible, incluida una descripción de lo ocurrido y se esperaba que sucediera.

1. <a name="ref-3" />Incluyen los seguimientos de pila pertinentes, el texto del mensaje de error o los registros de bloqueo (si usa el **notificar un problema** característica, estos se pueden incluidos automáticamente). <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />Anote los mensajes de error importante que aparecen en los datos adjuntos de captura de pantalla como texto sin formato demasiado.

1. <a name="ref-5" />Incluir un caso de prueba pequeño y autocontenido que reproduzca el error con poco código como sea posible.  Si no se puede reproducir el problema con un nuevo proyecto (creado mediante una de las plantillas integradas), a continuación, comprima un proyecto que se muestre el problema y adjuntarla al informe de errores.  Hacer que el proyecto de ejemplo tan simple como sea posible antes de adjuntarlo. <sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />Describir el entorno donde se ha detectado el error, incluido el sistema operativo y [versiones de Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) y las dependencias.

## <a name="additional-details"></a>Detalles adicionales

1. <a name="note-1" />[*^*](#ref-1) Lo ideal es que debe incluir la descripción de los "Síntomas visibles" suficientes detalles para que otros clientes pueden confirmar si ven el mismo problema (mismos mensajes de error, la misma degradación del rendimiento, seguimiento de la misma pila de un bloqueo, _etcetera._ ). Para "precisas circunstancias", un buen ejemplo sería si puede aparecer algo como: "Normalmente seleccionaré el problema 75% del tiempo, pero si se cambia esta una cosa, a continuación, puedo evitar el problema completamente". Otro ejemplo similar de "circunstancia precisa" es si el problema deja de degradar a una versión anterior de Xamarin.

1. <a name="note-2" />[*^*](#ref-2) Como cabría esperar, fragmentos de texto de error (o cualquier otro texto descriptivo de forma exclusiva) son normalmente los mejores términos de búsqueda. Si el informe de errores existente está incompleto, son Asistente para agregar los detalles o archivo de un nuevo, mejor el informe de errores.

1. <a name="note-3" />[*^*](#ref-3) Otra buena pregunta es si se ha notificado el mismo problema para cualquier Java, Objective-C o Swift aplicaciones. Si es así, el problema es muy probable que parte de Android o iOS propia en lugar de como parte de Xamarin.

1. <a name="note-4" />[*^*](#ref-4) Algunos ejemplos de información que se incluyen:

    1. Para errores que se producen al compilar un proyecto, incluya toda [salida de compilación diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) en el informe de errores.

    1. Para los errores que se producen al compilar o depurar un proyecto de iOS desde Visual Studio, ejecute **Ayuda > Xamarin > comprimir registros** después encontrarse el error e incluyen el archivo .zip resultante en el informe de errores.

    1. Para las excepciones o bloqueos en las aplicaciones de Android o iOS, incluya la correspondiente [Depurar registros para aplicaciones de Xamarin.Android y Xamarin.iOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps).

1. <a name="note-5" />[*^*](#ref-5) Si es posible para su problema en específico, una opción es volver a crear el problema mediante la adición de un pequeño número de archivos de la solución original en una solución completamente nueva. El equipo de Xamarin a menudo podrá investigar problemas incluso en casos de prueba más grandes (suponiendo que los pasos para reproducir se explican claramente), pero más sencillo los casos de prueba muestra que la mejor oportunidad que rápidamente se resolverá el error.

1. <a name="note-6" />[*^*](#ref-6) Si es _no_ posible reproducir el problema agregando un pequeño número de archivos a una solución completamente nueva, comprima y podrá asociar la carpeta de solución completa para la aplicación completa. Elimine el `bin`, `obj`, `Components`, y `packages` carpetas para que el archivo zip de archivos más pequeños. (El IDE y el proceso de compilación se suele restaurar o volver a crear el contenido de estas carpetas según sea necesario). También puede eliminar tantos código y archivos de recursos del proyecto como desee, siempre y cuando la solución resultante todavía muestra el problema original.
