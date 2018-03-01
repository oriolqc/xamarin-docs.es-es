---
title: "Xamarin.Mac por delante de la compilación de tiempo"
description: "Hacia delante de las consideraciones y los inconvenientes de la compilación de tiempo (AOT)"
ms.topic: article
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: f9ace41380987472b6957c94719e6ae9b6f7995d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin.Mac por delante de la compilación de tiempo

## <a name="overview"></a>Información general

Hacia delante (AOT) de tiempo de compilación es una técnica de optimización eficaces para mejorar el rendimiento de inicio. Sin embargo, también afecta a su tiempo de compilación, el tamaño de la aplicación y la ejecución del programa de maneras significativas. Para entender las ventajas e inconvenientes, impone, vamos a profundizar un poco en la compilación y ejecución de una aplicación.

El código escrito en lenguajes administrados, como C# y F #, se compila en una representación intermedia denominada lenguaje intermedio. Este IL, almacenado en los ensamblados de biblioteca y un programa, es relativamente compacta y portables entre arquitecturas de procesador. IL, sin embargo, es solo intermedios conjunto de instrucciones y en algún momento que necesitará IL que se deben traducir en código máquina específico del procesador.

Hay dos puntos en el que se puede realizar este procesamiento:

- **Justo a tiempo (JIT)** : durante el inicio y la ejecución de la aplicación se compila el IL en la memoria con código máquina.
- **Anticipada de tiempo (AOT)** : durante la compilación IL se compila y escriben en bibliotecas nativas y almacenado en el paquete de aplicación.

Cada opción tiene una serie de ventajas e inconvenientes:

- **JIT**
  - **Tiempo de inicio** : la compilación JIT debe realizarse durante el inicio. Para la mayoría de las aplicaciones se trata en el orden de 100 ms., pero para aplicaciones de gran tamaño tiempo puede ser mucho más.
  - **Ejecución** : código como el JIT se puede optimizar para el procesador específico que se va a usar, puede generar un código ligeramente mejor. En la mayoría de las aplicaciones es a lo sumo algunos puntos de porcentaje con mayor rapidez.
- **AOT**
  - **Tiempo de inicio** – cargar dylibs precompiladas es significativamente más rápida que los ensamblados JIT.
  - **Espacio en disco** – esos dylibs hacer una cantidad significativa de espacio en disco. Dependiendo de qué ensamblados AOTed, puede duplicarse o más el tamaño de la parte correspondiente al código de la aplicación.
  - **Tiempo de compilación** : compilación AOT es considerablemente más lenta que JIT y ralentizará compilaciones usarlo. Esta degradación puede oscilar entre segundos a un minuto o más, según el tamaño y el número de ensamblados compilados.
  - **Ofuscación** : como el IL, lo que es mucho más fácil de ingeniería que el código máquina inversa, no es necesariamente requerida puede eliminarse para ayudar a ofuscar el código sensible. Para ello, la opción se describe a continuación "híbrido".

## <a name="enabling-aot"></a>Habilitar AOT

Opciones de AOT se agregará al panel de compilación de Mac en una futura actualización. Hasta entonces, habilitar AOT requiere pasar un argumento de línea de comandos a través del campo "argumentos mmp adicionales" en la compilación de Mac. Las opciones son las siguientes:


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



## <a name="hybrid-aot"></a>Híbrido AOT

Durante la ejecución de una aplicación de macOS el tiempo de ejecución utiliza código máquina de forma predeterminada se carga desde las bibliotecas nativas generadas por la compilación de AOT. Sin embargo, hay algunas áreas del código como de camas elásticas, donde la compilación JIT puede generar resultados notablemente más optimizadas. Esto requiere el IL de los ensamblados administrados que estén disponibles. En iOS, las aplicaciones están restringidas de cualquier uso de la compilación JIT; esos sección de código son AOT compilado así.

La opción híbrida indica al compilador que ambos compilación estos sección (por ejemplo, iOS), pero también en se supone que el IL no estará disponible en tiempo de ejecución. A continuación, puede eliminarse este IL. registrar la compilación. Como se mencionó anteriormente, el tiempo de ejecución se verán obligado a utilizar menos rutinas optimizados en algunos lugares.

## <a name="further-considerations"></a>Otras consideraciones

Las consecuencias negativas de escala AOT con el tamaño y el número de ensamblados procesados. Toda la [.NET framework de destino](~/mac/platform/target-framework.md) de ejemplo contiene una biblioteca de clases de Base (BCL) mucho mayores que moderno, y, por tanto, se requieren mucho más tiempo y generar agrupaciones mayor AOT. Esto se produce por incompatibilidad de la plataforma de destino completa con vinculación, lo que elimina código no utilizado. Considere la posibilidad de mover la aplicación moderna y habilitar vinculación para obtener los mejores resultados.

Una ventaja adicional de AOT incluye interacciones mejoradas con depuración y generación de perfiles toolchains nativo. Puesto que una mayoría del código base se compilará antes de tiempo, tendrán nombres de función y símbolos que son más fáciles de leer en informes de bloqueo nativo, la generación de perfiles y depuración. Funciones JIT generado no tienen estos nombres y a menudo se muestran como desplazamientos hexadecimales sin nombre que son muy difíciles de resolver.
