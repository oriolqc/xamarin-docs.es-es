---
title: Errores de incrustación de .NET
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: topgenorth
ms.author: toopge
ms.date: 04/11/2018
ms.openlocfilehash: fcfeaa2d98a28723f95a9bf417e4bed81fe0dec3
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="net-embedding-errors"></a>Incrustar los errores de .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: Mensajes de error de enlace

P. ej., parámetros, entorno

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Error inesperado: rellene en un informe de errores https://github.com/mono/Embeddinator-4000/issues

Se produjo un error inesperado. Por favor, [un problema de archivo](https://github.com/mono/Embeddinator-4000/issues) con tanta información como sea posible, incluidos:

* Registros, de compilación completa con el máximo nivel de detalle
* Un caso de prueba mínimo que reproduzca el error
* Todos los datos de versión

Para obtener información sobre la versión exacta, lo más sencillo es usar la **Xamarin Studio** menú, **sobre Xamarin Studio** elemento, **mostrar detalles** botón y la versión de copiar y pegar obtener información (puede usar el **copiar información** botón).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: No se pudo crear el directorio de salida `X`

El nombre del directorio especificado por `-o=DIR` no existe y no se pudo crear. Podría ser un nombre no válido para el sistema de archivos.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: Opción `X` no se admite

La herramienta no admite la opción `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: La plataforma `X` no es válido.

La herramienta no es compatible con la plataforma `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: El destino `X` no es válido.

La herramienta no admite el destino `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: El destino de compilación `X` no es válido.

La herramienta no es compatible con el destino de compilación `X`. Es posible que otra versión de la herramienta lo admite o que no se aplica en este entorno.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: No se encontró la ubicación de Xcode.

La herramienta no encontró la ubicación de Xcode seleccionado actualmente mediante el `xcode-select -p` comando. Compruebe que este comando se ejecuta correctamente y devuelve la ubicación correcta de Xcode.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: No se pudo obtener la versión del sdk para '{sdk}'.

La herramienta no pudo obtener la versión SDK mediante la `xcrun --show-sdk-version --sdk {sdk}` comando. Compruebe que este comando se ejecuta correctamente y devuelve la versión del SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: La arquitectura '{arch}' no es válida para la plataforma de {}. Las arquitecturas válidas para {plataforma} son: '{arquitecturas}'.

La arquitectura en el mensaje de error no es válida para la plataforma de destino. Compruebe que la opción--abi se pasa una arquitectura válida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: La característica `X` no aún está implementada por el generador

Se trata de un problema conocido que se tiene la intención de corregir en una versión futura del generador. Contribuciones son bienvenidas.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: No se puede combinar los marcos de trabajo '{simulatorFramework}' y '{deviceFramework}' porque el archivo ' {}' no existe en ambos.

La herramienta no pudo combinar los marcos mencionados en el mensaje de error, porque no hay un archivo común entre ellos.

Esto podría indicar un error en la incrustación de. NET; registre un informe de errores en [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un caso de prueba.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: El ensamblado `X` no existe.

La herramienta no pudo encontrar el ensamblado `X` especificado en los argumentos.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: El nombre del ensamblado `X` no es único

Más de un ensamblado proporcionado tiene el mismo nombre interno y no sería posible distinguir entre ellos en tiempo de ejecución.

La causa más probable es que un ensamblado se especifica más de una vez en los argumentos de línea de comandos. Sin embargo un mantiene todavía ha cambiado el nombre del ensamblado es el nombre original y varias copias no coexiste.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: No se puede encontrar el ensamblado 'X', 'Y' al que hace referencia.

La herramienta no pudo encontrar el ensamblado 'X', al que hace referencia el ensamblado 'Y'. Asegúrese de que todos los ensamblados que se hace referencia se encuentran en el mismo directorio que el ensamblado que se va a enlazar.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014: No se pudo encontrar {producto} ({producto} {min_version} es necesario).

No se encontró la dependencia que se ha mencionado en el mensaje de error en el sistema.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015: No se encontró una versión no válida de {producto} ({versión}, pero al menos {min_version} es necesario).

La dependencia mencionada en el error se ha encontrado el mensaje en el sistema, pero es demasiado antigua. Actualice a una versión más reciente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: No se pudo crear el vínculo simbólico '{archivo}' -> '{destino}': error {number}

No se pudo crear el vínculo simbólico mencionado en el mensaje de error.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>No se pudo EM0026 analizar el argumento de línea de comandos 'A': *

La sintaxis para la opción de línea de comandos `A` no se pudo analizar la herramienta. Es probable que incorrecto, por favor, póngase en contacto con la documentación o la ayuda para ver la sintaxis correcta.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: Error interno *. Registre un informe de errores con un caso de prueba (https://github.com/mono/Embeddinator-4000/issues).

Este mensaje de error se notifica cuando se produce un error en una comprobación de coherencia interna de incrustación. NET.

Esto indica un error en la incrustación de. NET; registre un informe de errores en [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un caso de prueba.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: Procesamiento de código

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: Escriba `T` no se genera porque `X` no son compatibles.

Se trata de un **advertencia** que el tipo `T` se pasará por alto (es decir, no se generará) porque utiliza `X`, una característica que no es compatible.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: Escriba `T` no se genera porque carece de código de serialización con un equivalente administrado nativo.

Se trata de un **advertencia** que el tipo `T` se pasará por alto (es decir, no se generará) porque expone algo de .NET framework que requiere el cálculo de referencias adicionales.

Nota: Esto es algo que podría obtener compatibles, con algunas limitaciones, en una versión futura de la herramienta.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: Constructor `C` no se genera debido a un tipo parámetro `T` no se admite.

Se trata de un **advertencia** que el constructor `C` se pasará por alto (es decir, no se generará) porque un parámetro de tipo `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: Constructor `C` tiene valores predeterminados para el que no se genera ningún contenedor.

Se trata de un **advertencia** que los parámetros predeterminados de constructor `C` no genera ningún código adicional. La causa más común es que un método existente ya tiene la misma firma. P. ej., en .net es posible tener:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

En tales casos solo dos genera `init` selectores se creará, tanto una llamada a Mono, pero no existirá ningún contenedor para las versiones posteriores.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: Método `M` no se genera como tipo de valor devuelto `T` no se admite.

Se trata de un **advertencia** que el método `M` se pasará por alto (es decir, no se generará) porque es el tipo de valor devuelto `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: Método `M` no se genera debido a un tipo parámetro `T` no se admite.

Se trata de un **advertencia** que el método `M` se pasará por alto (es decir, no se generará) porque un parámetro de tipo `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: Método `M` tiene valores predeterminados para el que no se genera ningún contenedor.

Se trata de un **advertencia** que los parámetros predeterminados de método `M` no genera ningún código adicional. La causa más común es que un método existente ya tiene la misma firma. P. ej., en .net es posible tener:

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

En tales casos solo dos genera `increment` selectores se creará, tanto una llamada a Mono, pero no existirá ningún contenedor para las versiones posteriores.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: Método `M` no se genera porque otro método expone el operador con un nombre descriptivo.

Se trata de un **advertencia** que el método `M` no se genera porque otro método expone el operador con un nombre descriptivo. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: Método de extensión `M` no se genera dentro de una categoría porque no se crean en el tipo primitivo `T`. Se genera un método estático normal.

Se trata de un **advertencia** que escriba un método de extensión en un primivite (p. ej. `System.Int32`) se ha encontrado. En Objective C no es posible crear categorías de tipo primitivo. En su lugar, el generador se generará un método estático normal.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: Propiedad `P` no se genera debido a un tipo parámetro `T` no se admite.

Se trata de un **advertencia** que la propiedad `P` se pasará por alto (es decir, no se generará) porque el tipo expuesto `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: Las propiedades indizadas en `T` no se genera porque no se admiten varias propiedades indizadas.

Se trata de un **advertencia** que las propiedades indizadas en `T` se pasará por alto (es decir, no se generará) porque no se admiten varias propiedades indizadas.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: Campo `F` no se genera debido a un tipo campo `T` no se admite.

Se trata de un **advertencia** que el campo `F` se pasará por alto (es decir, no se generará) porque el tipo expuesto `T` no se admite.

Debería haber una advertencia anterior que ¿por qué proporciona más información escriba `T` no se admite.

Nota: Las características admitidas evolucionará con las nuevas versiones de la herramienta.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: Elemento `E` se genera en su lugar como `F` porque su nombre está en conflicto con un selector de c objetivo importante.

Se trata de un **advertencia** que el elemento `E` se generará en su lugar como `F` porque su nombre está en conflicto con un selector de c objetivo importante.

Selectores en el [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) tienen un significado importante en objective c y debe reemplazarse con cuidado.

Nota: La lista de selectores reservadas evolucionará con las nuevas versiones de la herramienta.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: Elemento `E` no se genera su nombre entra en conflicto con otros elementos en la misma clase.

Se trata de un **advertencia** ese elemento `E` no se genera como su nombre entra en conflicto con otros elementos en la misma clase.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: Destino `E` no es compatible con Xamarin.iOS y Xamarin.Mac. Solo el `framework` opción se considera que admite y se debe usar.

Se trata de un **advertencia** que tienen como destino `E` se considera no compatible para casos de uso de Xamarin.iOS y Xamarin.Mac. 

Consumo de bibliotecas .NET incrustar estáticos o dinámicos puede requerir pasos de trabajo adicionales o ajustes y debe evitarse en la mayoría de los casos de uso.

Considere la posibilidad de quitar el `--target` parámetro o pasar `--target=framework` en su lugar.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Generación de código

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
