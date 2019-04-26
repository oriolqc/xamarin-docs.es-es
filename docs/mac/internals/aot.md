---
title: Xamarin.Mac por delante de la compilación de tiempo
description: Este documento describe por delante de la compilación de tiempo en Xamarin.Mac. Compara las compilación AOT a la compilación JIT, se explica cómo habilitar AOT y echa un vistazo a la compilación AOT híbrida.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e155a394afd68d9970ee32785f6d0aeda6e2d129
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034209"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin.Mac por delante de la compilación de tiempo

## <a name="overview"></a>Información general

Con antelación de time (AOT), la compilación es una técnica eficaz de optimización para mejorar el rendimiento de inicio. Sin embargo, también afecta a su tiempo de compilación, el tamaño de la aplicación y la ejecución del programa de maneras más profundas. Para entender las ventajas e inconvenientes que impone, vamos a profundizar un poco en la compilación y ejecución de una aplicación.

El código escrito los lenguajes, administrados como C# y F#, se compila en una representación intermedia llamada IL. Este IL, almacenado en los ensamblados de biblioteca y un programa, es relativamente compacta y portátil entre arquitecturas de procesador. IL, sin embargo, es sólo un intermediario de conjunto de instrucciones y en algún momento que necesitará IL que se traducirá en código máquina específico del procesador.

Hay dos puntos en el que se puede realizar este procesamiento:

- **Just-in-time (JIT)** : durante el inicio y ejecución de la aplicación se compila el IL en memoria al código máquina.
- **Anticipada de time (AOT)** : durante la compilación el IL se compila y se escribe en las bibliotecas nativas y almacenado dentro de la agrupación de aplicaciones.

Cada opción tiene una serie de ventajas e inconvenientes:

- **JIT**
  - **Tiempo de inicio** : la compilación JIT debe realizarse durante el inicio. Para la mayoría de las aplicaciones se trata del orden de 100 ms, pero para aplicaciones de gran tamaño puede ser significativamente más este tiempo.
  - **Ejecución** – código como el JIT se puede optimizar para el procesador específico que se va a usar, se puede generar un código ligeramente mejor. En la mayoría de las aplicaciones se trata con mayor rapidez algunos puntos de porcentaje como máximo.
- **AOT**
  - **Tiempo de inicio** – dylibs compilados previamente la carga es significativamente más rápida que los ensamblados JIT.
  - **Espacio en disco** – esos dylibs no obstante puede tardar una cantidad significativa de espacio en disco. Dependiendo de qué ensamblados AOTed, puede hacer doble o más el tamaño de la parte del código de la aplicación.
  - **Tiempo de compilación** : compilación de AOT es mucho más lento que JIT y ralentizará compilaciones con él. Esta reducción de velocidad puede oscilar entre segundos hasta un minuto o más, según el tamaño y número de ensamblados compilados.
  - **Ofuscación** : como el IL, que es mucho más fácil invertir el ingeniero que el código máquina, no necesariamente se requiere se puede eliminar para ayudar a ofuscar el código confidencial. Esto requiere la opción que se describen a continuación "híbrido".

## <a name="enabling-aot"></a>Habilitación de AOT

Opciones de AOT se agregará al panel de compilación de Mac en una futura actualización. Hasta entonces, la habilitación de AOT requiere pasar un argumento de línea de comandos a través del campo "argumentos de mmp adicionales" en la compilación de Mac. Las opciones son las siguientes:


      --aot[=VALUE]          Specify assemblies that should be AOT compiled
                               - none - No AOT (default)
                               - all - Every assembly in MonoBundle
                               - core - Xamarin.Mac, System, mscorlib
                               - sdk - Xamarin.Mac.dll and BCL assemblies
                               - |hybrid after option enables hybrid AOT which
                               allows IL stripping but is slower (only valid
                               for 'all')
                                - Individual files can be included for AOT via +
                               FileName.dll and excluded via -FileName.dll

                               Examples:
                                 --aot:all,-MyAssembly.dll
                                 --aot:core,+MyOtherAssembly.dll,-mscorlib.dll



## <a name="hybrid-aot"></a>Compilación AOT híbrida

Durante la ejecución de una aplicación de macOS el tiempo de ejecución utiliza código máquina de forma predeterminada se carga desde las bibliotecas nativas generadas por la compilación de AOT. Sin embargo, hay algunas áreas de código como camas elásticas, donde la compilación JIT puede producir resultados significativamente más optimizados. Esto requiere que el IL de los ensamblados administrados a estar disponible. En iOS, las aplicaciones están restringidas de cualquier uso de la compilación JIT; esos sección de código son AOT también compilada.

La opción híbrida indica al compilador que ambos compilación estos sección (por ejemplo, iOS), pero también a suponen que el IL no estará disponible en tiempo de ejecución. A continuación, se puede quitar este IL después de la compilación. Como se mencionó anteriormente, se forzará el tiempo de ejecución para usar menos rutinas optimizadas en algunos lugares.

## <a name="further-considerations"></a>Consideraciones adicionales

Las consecuencias negativas de AOT escalan con los tamaños y el número de ensamblados que se procesan. El completo [.NET framework de destino](~/mac/platform/target-framework.md) de ejemplo contiene una biblioteca de clases de Base (BCL) mucho mayor que moderno y, por tanto, va a AOT tardan mucho más tiempo y generar paquetes más grandes. Esto se agrava por incompatibilidad de .NET framework de destino completa con vinculación, que elimina código no utilizado. Considere la posibilidad de mover su aplicación a la vinculación modernas y habilitar para los mejores resultados.

Una ventaja adicional de AOT incluye interacciones mejoradas con la depuración nativa y cadenas de herramientas de generación de perfiles. Puesto que la gran mayoría de la base de código se compilará antes de tiempo, tendrán nombres de función y símbolos que son más fáciles de leer dentro de los informes de bloqueo nativo, generación de perfiles y depuración. Funciones JIT generado no tienen estos nombres y a menudo se muestran como desplazamientos hexadecimales sin nombre que son muy difíciles de resolver.
