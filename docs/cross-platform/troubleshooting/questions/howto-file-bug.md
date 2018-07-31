---
title: ¿Cómo y cuándo debo presentar un informe de errores?
description: Este documento describe cuándo, dónde y cómo en el archivo de un informe de errores. También proporciona prácticas recomendadas que permiten a los ingenieros que mejor diagnosticar el problema de informe de errores.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: b70fe29a79e099f1141c1295d907b48afaa2c3c7
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351611"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>¿Cómo y cuándo debo presentar un informe de errores?

Los errores de archivo de seguimiento de errores de Bugzilla de Xamarin aquí: [ https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all ](https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all).

## <a name="file-a-bug-if"></a>Archivar un error si...

Tiene un conjunto de pasos que se cree que los ingenieros de Xamarin podrán usar para reproducir un problema causado por Xamarin.

O

Puede describir cuidadosamente los síntomas visibles del problema, especialmente si también puede describir algunas circunstancias precisas relacionados con el problema. <sup> [[1]](#note-1)</sup>


## <a name="best-practices-to-help-xamarin-address-bugs-quickly-and-efficiently"></a>Prácticas recomendadas para ayudar a resolver errores de Xamarin de forma rápida y eficaz


1. <a name="ref-1" />Búsqueda [Bugzilla](https://bugzilla.xamarin.com/query.cgi?format=specific&amp;bug_status=__all__) y la web existentes sugerencias de uso que se pueden solucionar el problema directamente o de los informes de errores.<sup> [[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />Siga el [error escribir instrucciones](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html) para describir el problema como clara y concisa como sea posible, incluida una descripción de lo que sucedió y se esperaba que sucediera.

1. <a name="ref-3" />Incluyen los seguimientos de pila pertinentes, el texto del mensaje de error o los registros de bloqueo. <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />Anote los mensajes de error importante que aparecen en los datos adjuntos de captura de pantalla como texto sin formato demasiado.

1. <a name="ref-5" />Incluir un caso de prueba pequeño y autocontenido que reproduzca el error con poco código como sea posible.  Si no se puede reproducir el problema con un nuevo proyecto (creado mediante una de las plantillas integradas), a continuación, comprima un proyecto que se muestre el problema y adjuntarla al informe de errores.  Hacer que el proyecto de ejemplo tan simple como sea posible antes de adjuntarlo. <sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />Describir el entorno donde se ha detectado el error, incluido el sistema operativo y [versiones de Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) y las dependencias.

---

## <a name="additional-details"></a>Detalles adicionales

1. <a name="note-1" />[*^*](#ref-1) Lo ideal es que debe incluir la descripción de los "Síntomas visibles" suficientes detalles para que otros clientes pueden confirmar si ven el mismo problema (mismos mensajes de error, la misma degradación del rendimiento, seguimiento de la misma pila de un bloqueo, _etcetera._ ). Para "precisas circunstancias", un buen ejemplo sería si puede aparecer algo como: "normalmente seleccionaré el problema 75% del tiempo, pero si se cambia esta una cosa, a continuación, puedo evitar el problema completamente". Otro ejemplo similar de "circunstancia precisa" es si el problema deja de degradar a una versión anterior de Xamarin.

1. <a name="note-2" />[*^*](#ref-2) Como cabría esperar, fragmentos de texto de error (o cualquier otro texto descriptivo de forma exclusiva) son normalmente los mejores términos de búsqueda. Si el informe de errores existente está incompleto, son Asistente para agregar los detalles o archivo de un nuevo, mejor el informe de errores.

1. <a name="note-3" />[*^*](#ref-3) Otra buena pregunta es si se ha notificado el mismo problema para cualquier Java, Objective-C o Swift aplicaciones. Si es así, el problema es muy probable que parte de Android o iOS propia en lugar de como parte de Xamarin.

1. <a name="note-4" />[*^*](#ref-4) Algunos ejemplos de información que se incluyen:

    1. Para errores que se producen al compilar un proyecto, incluya toda [salida de compilación diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) en el informe de errores.
    
    1. Para los errores que se producen al compilar o depurar un proyecto de iOS desde Visual Studio, ejecute _Ayuda > Xamarin > comprimir registros_ después encontrarse el error e incluyen el archivo .zip resultante en el informe de errores.
    
    1. Para las excepciones o bloqueos en las aplicaciones de Android o iOS, incluya la correspondiente "[Depurar registros para aplicaciones de Xamarin.Android y Xamarin.iOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)."

1. <a name="note-5" />[*^*](#ref-5) Si es posible para su problema en específico, es una excelente opción volver a crear el problema mediante la adición de un pequeño número de archivos de la solución original en una solución completamente nueva. El equipo de Xamarin a menudo podrá investigar problemas incluso en casos de prueba más grandes (suponiendo que los pasos para reproducir se explican claramente), pero más sencillo los casos de prueba muestra que la mejor oportunidad que rápidamente se resolverá el error.


1. <a name="note-6" />[*^*](#ref-6) Si es _no_ posible reproducir el problema agregando un pequeño número de archivos a una solución completamente nueva, comprima y podrá asociar la carpeta de solución completa para la aplicación completa. Elimine el `bin`, `obj`, `Components`, y `packages` carpetas para que el archivo zip de archivos más pequeños. (El IDE y el proceso de compilación se suele restaurar o volver a crear el contenido de estas carpetas según sea necesario). También puede eliminar tantos código y archivos de recursos del proyecto como desee, siempre y cuando la solución resultante todavía muestra el problema original.

