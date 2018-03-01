---
title: "Errores de incrustación de .NET"
ms.topic: article
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 90d30b92069bcd6a5c008fa8009c0392c4d26473
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="em0xxx-binding-error-messages"></a>EM0xxx: mensajes de error de enlace

P. ej. parámetros, entorno

<!-- 0xxx: the generator itself, e.g. parameters, environment -->
<h3><a name="EM0000"/>EM0000: Error inesperado - complete un informe de errores en https://github.com/mono/Embeddinator-4000/issues</h3>

Se produjo un error inesperado. Por favor, [un problema de archivo](https://github.com/mono/Embeddinator-4000/issues) con tanta información como sea posible, incluidos:

* Registros, de compilación completa con el máximo nivel de detalle
* Un caso de prueba mínimo que reproduzca el error
* Todos los datos de versión

Para obtener información sobre la versión exacta, lo más sencillo es usar la **Xamarin Studio** menú, **sobre Xamarin Studio** elemento, **mostrar detalles** botón y la versión de copiar y pegar obtener información (puede usar el **copiar información** botón).

<h3><a name="EM0001"/>EM0001: No se pudo crear el directorio de salida `X`</h3>

El nombre del directorio especificado por `-o=DIR` no existe y no se pudo crear. Podría ser un nombre no válido para el sistema de archivos.

<h3><a name="EM0002"/>EM0002: Opción `X` no se admite</h3>

La herramienta no admite la opción `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<h3><a name="EM0003"/>EM0003: La plataforma `X` no es válido.</h3>

La herramienta no es compatible con la plataforma `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<h3><a name="EM0004"/>EM0004: El destino `X` no es válido.</h3>

La herramienta no admite el destino `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<h3><a name="EM0005"/>EM0005: El destino de compilación `X` no es válido.</h3>

La herramienta no es compatible con el destino de compilación `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<h3><a name="EM0006"/>EM0006: No se encontró la ubicación de Xcode.</h3>

La herramienta no encontró la ubicación de Xcode seleccionado actualmente mediante el `xcode-select -p` comando. Compruebe que este comando se ejecuta correctamente y devuelve la ubicación correcta de Xcode.

<h3><a name="EM0007"/>EM0007: No se pudo obtener la versión del sdk para '{sdk}'.</h3>

La herramienta no pudo obtener la versión SDK mediante la `xcrun --show-sdk-version --sdk {sdk}` comando. Compruebe que este comando se ejecuta correctamente y devuelve la versión del SDK.

<h3><a name="EM0008"/>EM0008: La arquitectura '{arch}' no es válida para la plataforma de {}. Las arquitecturas válidas para {plataforma} son: '{arquitecturas}'.</h3>

La arquitectura en el mensaje de error no es válida para la plataforma de destino. Compruebe que la opción--abi se pasa una arquitectura válida.

<h3><a name="EM0009"/>EM0009: La característica `X` no aún está implementada por el generador</h3>

Se trata de un problema conocido que se tiene la intención de corregir en una versión futura del generador. Contribuciones son bienvenidas.

<h3><a name="EM0010"/>EM0010: No se puede combinar los marcos de trabajo '{simulatorFramework}' y '{deviceFramework}' porque el archivo ' {}' no existe en ambos.</h3>

La herramienta no pudo combinar los marcos mencionados en el mensaje de error, porque no hay un archivo común entre ellos.

Esto podría indicar un error en el Embeddinator-4000; registre un informe de errores en [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con un caso de prueba.

<h3><a name="EM0011"/>EM0011: El ensamblado `X` no existe.</h3>

La herramienta no pudo encontrar el ensamblado `X` especificado en los argumentos.

<h3><a name="EM0012"/>EM0012: El nombre del ensamblado `X` no es único</h3>

Más de un ensamblado proporcionado tiene el mismo nombre interno y no sería posible distinguir entre ellos en tiempo de ejecución.

La causa más probable es que un ensamblado se especifica más de una vez en los argumentos de línea de comandos. Sin embargo un mantiene todavía ha cambiado el nombre del ensamblado es el nombre original y varias copias no coexiste.

<h3><a name="EM0013"/>EM0013: No se puede encontrar el ensamblado 'X', 'Y' al que hace referencia.</h3>

La herramienta no pudo encontrar el ensamblado 'X', al que hace referencia el ensamblado 'Y'. Asegúrese de que todos los ensamblados que se hace referencia se encuentran en el mismo directorio que el ensamblado que se va a enlazar.

<h3><a name="EM0014"/>EM0014: No se pudo encontrar {producto} ({producto} {min_version} es necesario).</h3>

No se encontró la dependencia que se ha mencionado en el mensaje de error en el sistema.

<h3><a name="EM0015"/>EM0015: No se encontró una versión no válida de {producto} ({versión}, pero al menos {min_version} es necesario).</h3>

La dependencia mencionada en el error se ha encontrado el mensaje en el sistema, pero es demasiado antigua. Actualice a una versión más reciente.

<h3><a name="EM0016">EM0016: No se pudo crear el vínculo simbólico '{archivo}' -> '{destino}': error {number}</h3>

No se pudo crear el vínculo simbólico mencionado en el mensaje de error.

<h3><a name="EM0026"/>No se pudo EM0026 analizar el argumento de línea de comandos 'A': *</h3>

La sintaxis para la opción de línea de comandos `A` no se pudo analizar la herramienta. Es probable que incorrecto, por favor, póngase en contacto con la documentación o la ayuda para ver la sintaxis correcta.

<h3><a name="EM0099"/>EM0099: Error interno *. Registre un informe de errores a un caso de prueba (https://github.com/mono/Embeddinator-4000/issues).</h3>

Este mensaje de error se notifica cuando se produce un error en una comprobación de coherencia interna en Embeddinator a 4000.

Esto indica un error en el Embeddinator-4000; registre un informe de errores en [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con un caso de prueba.


<!-- 1xxx: code processing -->

# <a name="em1xxx-code-processing"></a>EM1xxx: Procesamiento de código

<h3><a name="EM1010"/>Tipo de `T` no se genera porque `X` no son compatibles.</h3>

Se trata de un **advertencia** que el tipo `T` se pasará por alto (es decir, no se generará) porque utiliza `X`, una característica que no es compatible.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.


<h3><a name="EM1011"/>Tipo `T` no se genera porque carece de un homólogo nativo.</h3>

Se trata de un **advertencia** que el tipo `T` se pasará por alto (es decir, no se generará) porque lo utiliza exponen algo de .NET framework que no tiene ningún homólogo en la plataforma nativa.


<h3><a name="EM1011"/>Tipo `T` no se genera porque carece de código de serialización con un equivalente administrado nativo.</h3>

Se trata de un **advertencia** que el tipo `T` se pasará por alto (es decir, no se generará) porque lo utiliza exponen algo de .NET framework que requiere el cálculo de referencias adicionales.

Nota: Esto es algo que es posible que se admite get, con algunas limitaciones, en una versión futura de la herramienta.


<h3><a name="EM1020"/>Constructor `C` no se genera debido a un tipo parámetro `T` no se admite.</h3>

Se trata de un **advertencia** que el constructor `C` se pasará por alto (es decir, no se generará) porque un parámetro de tipo `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.


<h3><a name="EM1021"/>Constructor `C` tiene valores predeterminados para el que no se genera ningún contenedor.</h3>

Se trata de un **advertencia** que los parámetros predeterminados de constructor `C` no genera ningún código adicional. La causa más común es que un método existente ya tiene la misma firma. P. ej. en .net es posible tener:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

En tales casos solo dos genera `init` selectores se creará, tanto una llamada a mono, pero no existirá ningún contenedor para las versiones posteriores.


<h3><a name="EM1030"/>Método `M` no se genera como tipo de valor devuelto `T` no se admite.</h3>

Se trata de un **advertencia** que el método `M` se pasará por alto (es decir, no se generará) porque es el tipo de valor devuelto `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.


<h3><a name="EM1031"/>Método `M` no se genera debido a un tipo parámetro `T` no se admite.</h3>

Se trata de un **advertencia** que el método `M` se pasará por alto (es decir, no se generará) porque un parámetro de tipo `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.


<h3><a name="EM1032"/>Método `M` tiene valores predeterminados para el que no se genera ningún contenedor.</h3>

Se trata de un **advertencia** que los parámetros predeterminados de método `M` no genera ningún código adicional. La causa más común es que un método existente ya tiene la misma firma. P. ej. en .net es posible tener:

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

En tales casos solo dos genera `increment` selectores se creará, tanto una llamada a mono, pero no existirá ningún contenedor para las versiones posteriores.


<h3><a name="EM1033"/>Método `M` no se genera porque otro método expone el operador con un nombre descriptivo.</h3>

Se trata de un **advertencia** que el método `M` no se genera porque otro método expone el operador con un nombre descriptivo. (https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)


<h3><a name="EM1034"/>Método de extensión `M` no se genera dentro de una categoría porque no se crean en el tipo primitivo `T`. Se genera un método estático normal.</h3>

Se trata de un **advertencia** que escriba un método de extensión en un primivite (p. ej. `System.Int32`) se ha encontrado. En ObjC no es posible crear categorías de tipo primitivo. En su lugar, el generador se generará un método estático normal.



<h3><a name="EM1040"/>Propiedad `P` no se genera debido a un tipo parámetro `T` no se admite.</h3>

Se trata de un **advertencia** que la propiedad `P` se pasará por alto (es decir, no se generará) porque el tipo expuesto `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<h3><a name="EM1041"/>Propiedades indizadas en `T` no se genera porque no se admiten varias propiedades indizadas.</h3>

Se trata de un **advertencia** que las propiedades indizadas en `T` se pasará por alto (es decir, no se generará) porque no se admiten varias propiedades indizadas.


<h3><a name="EM1050"/>Campo `F` no se genera debido a un tipo campo `T` no se admite.</h3>

Se trata de un **advertencia** que el campo `F` se pasará por alto (es decir, no se generará) porque el tipo expuesto `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<h3><a name="EM1051"/>Elemento `E` se genera en su lugar como `F` porque su nombre está en conflicto con un selector de c objetivo importante.</h3>

Se trata de un **advertencia** que el elemento `E` se generará en su lugar como `F` porque su nombre está en conflicto con un selector de c objetivo importante.

Selectores en el [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) tienen un significado importante en objective c y debe reemplazarse con cuidado.

Nota: La lista de selectores reservadas evolucionará con las nuevas versiones de la herramienta.


<!-- 2xxx: code generation -->

# <a name="em2xxx-code-generation"></a>EM2xxx: Generación de código


<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
