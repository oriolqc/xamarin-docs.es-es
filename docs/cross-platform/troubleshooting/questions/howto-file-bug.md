---
title: ¿Cuándo y cómo debería presentar un informe de errores?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: asb3993
ms.author: amburns
ms.openlocfilehash: d5471195b5051725b41b8f28b3959db362297669
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>¿Cuándo y cómo debería presentar un informe de errores?


Informar de errores en el Rastreador de errores de Bugzilla de Xamarin aquí: [ https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all ](https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all).

## <a name="file-a-bug-if"></a>Registre un error si...


Tiene un conjunto de pasos que se cree que los ingenieros de Xamarin podrá usar para reproducir un problema causado por Xamarin.

O

Puede describir cuidadosamente los síntomas visibles del problema, especialmente si también puede describir algunas circunstancias precisas relacionados con el problema. <sup> [[1]](#note-1)</sup>


## <a name="best-practices-to-help-xamarin-address-bugs-quickly-and-efficiently"></a>Prácticas recomendadas para ayudar a resolver errores de Xamarin rápida y eficaz


1. <a name="ref-1" />Búsqueda [Bugzilla](https://bugzilla.xamarin.com/query.cgi?format=specific&amp;bug_status=__all__) y la web existentes error en los informes o sugerencias de uso que pueden solucionar el problema directamente.<sup> [[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />Siga el [error escribir instrucciones](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html) para describir el problema como de forma clara y concisa como sea posible, incluida una descripción de lo que ha ocurrido y se espera que se produzca.

1. <a name="ref-3" />Incluya los seguimientos de pila pertinentes, el texto del mensaje de error, o registros de bloqueo. <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />Anote los mensajes de error importante que aparecen en pantalla los datos adjuntos como texto sin formato demasiado.

1. <a name="ref-5" />Incluir un caso de prueba reducido e independiente que reproduzca el error con el código al mínimo posible.  Si no se puede reproducir el problema con un proyecto nuevo (creado mediante una de las plantillas integradas), a continuación, comprima un proyecto que se muestre el problema y adjuntarlo al informe de errores.  Convertir el proyecto de ejemplo tan simple como sea posible antes de adjuntarlo. <sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />Describir el entorno donde se ha detectado el error, incluido el sistema operativo y [versiones de Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) y todas las dependencias.

---

## <a name="additional-details"></a>Detalles adicionales

1. <a name="note-1" />[*^*](#ref-1) Lo ideal es que debe incluir la descripción de los "Síntomas visibles" suficientes detalles para que otros clientes pueden confirmar si ve el mismo problema (mismos mensajes de error, el mismo degradación del rendimiento, el mismo seguimiento de pila del bloqueo, _etcetera._ ). Para "precisas circunstancias", un buen ejemplo sería si puede aparecer algo como: "normalmente encuentre el problema 75% del tiempo, pero si se cambia este algo, a continuación, puedo evitar el problema completamente." Otro ejemplo similar de "circunstancia precisa" es si el problema deja de degradar a una versión anterior de Xamarin.

1. <a name="note-2" />[*^*](#ref-2) Como cabría esperar, los fragmentos de texto de error (o cualquier otro texto descriptivo de forma exclusiva) son normalmente el mejor término de búsqueda. Si el informe de errores existente está incompleto, son Asistente para agregar los detalles o archivos de un nuevo, mejor el informe de errores.

1. <a name="note-3" />[*^*](#ref-3) Otra buena pregunta es si el mismo problema se han notificado de cualquier Java Objective-C o aplicaciones Swift. Si es así, el problema es muy probable que parte de Android o iOS propio en lugar de como parte de Xamarin.

1. <a name="note-4" />[*^*](#ref-4) Algunos ejemplos de información que desea incluir:

    1. Para errores que se producen al compilar un proyecto, incluya la sección completa [resultados de la compilación diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) en el informe de errores.
    
    1. Para los errores que se producen al compilar o depurar un proyecto de iOS desde Visual Studio, vuelva a ejecutar _Ayuda > Xamarin > Zip registros_ después de alcanzar el error e incluya el archivo zip resultante en el informe de errores.
    
    1. Para las excepciones o se bloquea en aplicaciones de Android o iOS, incluya la correspondiente "[Depurar registros para las aplicaciones Xamarin.Android y Xamarin.iOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)."

1. <a name="note-5" />[*^*](#ref-5) Si es posible para su problema en particular, es una excelente opción volver a crear el problema agregando un pequeño número de archivos de la solución original en una nueva solución. El equipo de Xamarin a menudo podrá investigar problemas incluso en casos de prueba más grandes (suponiendo que se explican los pasos para reproducir de claramente), pero más sencillo casos de prueba muestra que la mejor oportunidad, que se resolverá el error rápidamente.


1. <a name="note-6" />[*^*](#ref-6) Si es _no_ posibles reproducir el problema mediante la adición de un número reducido de archivos a una solución nueva, a continuación, puede comprimir y adjuntar la carpeta de la solución completa para la aplicación completa. Elimine la `bin`, `obj`, `Components`, y `packages` carpetas para hacer que el archivo zip de archivos más pequeños. (El IDE y el proceso de compilación se suelen restaurar o volver a crear el contenido de estas carpetas según sea necesario). También puede eliminar tantas código archivos de recursos y del proyecto que desee, siempre y cuando la solución resultante todavía muestre el problema original.

