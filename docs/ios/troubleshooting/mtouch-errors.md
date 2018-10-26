---
title: Errores de Xamarin.iOS
description: Este documento describe varios errores generados por mtouch, la herramienta usada para empaquetar aplicaciones de Xamarin.iOS. Los errores se enumeran por código y proporcionado una descripción completa.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 01359d8b5fc402c83006c1c227375557fd2d7149
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122457"
---
# <a name="xamarinios-errors"></a>Errores de Xamarin.iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mensajes de error de mtouch

P. ej., parámetros, entorno, falta de herramientas.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpbugzillaxamarincom"></a>MT0000: Error inesperado - complete en un informe de errores http://bugzilla.xamarin.com

Se ha producido una condición de error inesperado. Por favor, [un informe de errores de archivo](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con tanta información como sea posible, incluidos:

* Compilación de los registros, se completa con el máximo nivel de detalle (por ejemplo, `-v -v -v -v` en el **argumentos mtouch adicionales**);
* Un caso de prueba mínima que reproduzca el error; y
* Todos los datos de versión

Para obtener información de versión exacto, lo más sencillo es usar el **Visual Studio para Mac** menú, **acerca de Visual Studio para Mac** elemento, **mostrar detalles** botón y copiar y pegar el información de versión (puede usar el **copiar información** botón).

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: '-nombredisp ' se ha proporcionado sin ninguna acción específica del dispositivo

Esta es una advertencia que se genera si se pasa - nombredisp a mtouch cuando ninguna acción específica del dispositivo (-logdev/installdev/killdev/launchdev /-listapps) se ha solicitado.

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: No se pudo analizar la variable de entorno *.

Este error se produce si se intenta establecer una clave de entorno no válido = par de variables de valor. El formato correcto es: `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: Nombre de la aplicación ' * .exe ' entra en conflicto con un nombre de ensamblado (.dll) de producto o SDK.

Nombre del ensamblado ejecutable y el nombre de la aplicación no pueden coincidir con el nombre de cualquier archivo dll en la aplicación. Modifique el nombre del archivo ejecutable.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: Nueva lógica de refcounting requiere SGen habilitarse demasiado.

Si habilita la extensión de refcounting también debe habilitar el recolector de elementos no utilizados SGen en las opciones de compilación (pestaña Avanzadas) de iOS del proyecto.

Comenzando con Xamarin.iOS 7.2.1 este requisito se soluciona, la nueva lógica de refcounting se puede habilitar con Boehm y recolectores de elementos no utilizados de SGen.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: El directorio de salida * no existe.

Cree el directorio.

Ya no se genera este error, mtouch automáticamente creará el directorio si no existe.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: No hay ninguna plataforma de desarrollo en *, utilice--plataforma = PLAT para especificar el SDK.

Xamarin.iOS no se encuentra el directorio del SDK en la ubicación mencionada en el mensaje de error. Compruebe que la ruta de acceso es correcta.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: El ensamblado raíz * no existe.

Xamarin.iOS no se encuentra el ensamblado en la ubicación mencionada en el mensaje de error. Compruebe que la ruta de acceso es correcta.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: Se deben proporcionar una raíz ensamblado # única, que se encuentra ensamblados: *.

Más de un ensamblado de raíz se pasó a mtouch, aunque puede haber un ensamblado solo una raíz.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Error al cargar los ensamblados: *.

Se produjo un error al cargar los ensamblados de las referencias de ensamblado raíz. Es posible que se proporciona más información en la salida de compilación.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: No se pudo analizar los argumentos de línea de comandos: *.

Se produjo un error al analizar los argumentos de línea de comandos. Compruebe que son todos correctos.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * se compiló un tiempo de ejecución más reciente (*) que admite MonoTouch.

Esta advertencia se muestra normalmente porque el proyecto tiene una referencia a una biblioteca de clases que no se compiló con la BCL de Xamarin.iOS.

La misma manera que una aplicación mediante el SDK de .NET 4.0 puede no funcionar en un sistema que solo admiten .NET 2.0, una biblioteca compilada con .NET 4.0 puede no funcionar en Xamarin.iOS, puede usar API no está presente en Xamarin.iOS.

La solución general consiste en generar la biblioteca como una biblioteca de clases de Xamarin.iOS. Esto puede realizarse mediante la creación de un nuevo proyecto de biblioteca de clases de Xamarin.iOS y agregue todos los archivos de origen en él. Si no tiene el código fuente de la biblioteca, debe ponerse en contacto con el proveedor y solicitar que proporcionan una versión compatible con Xamarin.iOS de su biblioteca.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: Datos incompletos se proporcionan para completar *.

Este error no se notifica ya en la versión actual de Xamarin.iOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: Compatibilidad de generación de perfiles requiere sgen habilitarse demasiado.

SGen (--sgen) debe estar habilitada si la generación de perfiles (--generación de perfiles) está habilitada.

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: IOS * SDK no admite la creación de aplicaciones destinadas a *.

Esto puede ocurrir en las siguientes circunstancias:

*  ARMv6 está habilitado y Xcode 4.5 o posterior está instalado.
*  ARMv7s está habilitado y 4.4 o una versión anterior de Xcode está instalado.

Compruebe que la versión instalada de Xcode es compatible con las arquitecturas seleccionadas.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: La ABI no válida: *. ABI admitidos es: i386, x86_64, armv7, armv7 + llvm, armv7 + llvm + thumb2, armv7s, armv7s + llvm, armv7s + llvm + thumb2, arm64 y arm64 + llvm.

Una ABI no válida se pasó a mtouch. Especifique una ABI válida.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: La opción * está desusado.

La opción de mtouch mencionados en desuso y se omitirá.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: Debe proporcionar un ensamblado raíz.

Es necesario especificar un ensamblado de raíz (normalmente el archivo ejecutable principal) al compilar una aplicación.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Argumento de línea de comandos desconocido: *.

Mtouch no reconoce el argumento de línea de comandos que se menciona en el mensaje de error.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: Solo uno--[log | instalar | kill | iniciar] dev o--[iniciar | depurar] se puede usar la opción de sim.

Hay varias opciones para mtouch que no pueden usarse simultáneamente:

-  --logdev
-  --installdev
-  --killdev
-  --launchdev
-  --launchdebug
-  --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: Las opciones válidas para '\*'son'\*'.

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: No se puede compilar con gcc / g ++ (--use gcc) cuando se usa el registrador estático (es el valor predeterminado cuando se compila para dispositivo). Quite el--use-gcc marcar o usar el registrador de dinámico (--registrador: dinámico).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: La opciones '--no admitido: enable-genéricos-en-registrar' y '--registrador ' no son compatibles.

Quitar las opciones `--unsupported--enable-generics-in-registrar` y `--registrar`. A partir de Xamarin.iOS 7.2.1, el registrador predeterminado admite genéricos.

Ya no se muestra este error (el argumento de línea de comandos `--unsupported--enable-generics-in-registrar` se ha quitado de mtouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: Nombre de la aplicación ' * .exe ' entra en conflicto con otro ensamblado de usuario.

Nombre del ensamblado ejecutable y el nombre de la aplicación no pueden coincidir con el nombre de cualquier archivo dll en la aplicación. Modifique el nombre del archivo ejecutable.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: No se encuentra el archivo ' *'.

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: Se ha proporcionado ninguna versión del SDK. Agregue `--sdk=X.Y` para especificar qué iOS se debe usar el SDK para compilar la aplicación.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: No se pudo analizar el argumento de línea de comandos ' *': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: Las opciones\*'y'\*' no son compatibles.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: No se puede habilitar circular (-circular) cuando el destino es iOS 4.1 o una versión anterior. Deshabilite circular (-circular: false) o establecer el destino de implementación en al menos iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (--enable-repl) solo se admite en el simulador (--sim).

REPL solo se admite si va a compilar para el simulador. Esto significa que si se pasa `--enable-repl` a mtouch, también se debe pasar `--sim`.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: El nombre del archivo ejecutable (\*) y el nombre de la aplicación (\*) son diferentes, esto puede impedir que los registros de bloqueo de introducción simbólicos correctamente.

Cuando symbolicates Xcode (traduce las direcciones de memoria para los nombres de función y la línea del archivo/números) el proceso puede producir un error si el archivo ejecutable y la aplicación tienen nombres diferentes (sin la extensión).

Para corregir este cambie "Application Name" en Opciones de la aplicación de iOS/compilación del proyecto o cambio 'Assembly Name' en las opciones de compilación y de salida del proyecto.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Los argumentos de línea de comandos '--enable-captura de fondo ' y '--inicio para la captura de fondo ' requiere '--launchsim' demasiado.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: La opción '--debugtrack' se omite a menos que '--debug' también se especifica.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Un proyecto de Xamarin.iOS debe hacer referencia a monotouch.dll o Xamarin.iOS.dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: No puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS - '\*' es hacer referencia explícita, mientras que '\*' hace referencia a ' *'.

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: No se puede iniciar un * simulador para una * aplicación. Habilite el architecture(s) correcto en las opciones de compilación (página avanzadas) de iOS de su proyecto.

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037: monotouch.dll es compatible con 64 bits no. Puede hacer referencia a Xamarin.iOS.dll o no se compilan para una arquitectura de 64 bits (ARM64 o x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: Los registradores antiguos (--registrador: oldstatic | olddynamic) no se admiten cuando se hace referencia Xamarin.iOS.dll.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Xamarin.iOS.dll no pueden hacer referencia las aplicaciones destinadas a ARMv6.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: No se pudo encontrar el ensamblado '\*', que se hace referencia mediante'\*'.

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: No se puede hacer referencia a 'monotouch.dll' y 'Xamarin.iOS.dll'.

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: no se encontró ninguna referencia a monotouch.dll o Xamarin.iOS.dll. Se agregará una referencia a monotouch.dll.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: El recolector de elementos no utilizados Boehm actualmente no se admite cuando se hace referencia 'Xamarin.iOS.dll'. El recolector de elementos no utilizados SGen se ha seleccionado en su lugar.

Sólo el recolector de elementos no utilizados de SGen es compatible con proyectos unificado. Asegúrese de que no hay ninguna marca de mtouch adicionales especificando Boehm como el recolector de elementos no utilizados.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim solo es compatible con Xcode 6.0 o posterior.

Instale una versión más reciente de Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--extensión solo se admite cuando se usa el iOS SDK 8.0 (o posterior).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: El destino de implementación mínima para las aplicaciones unificado es 5.1.1, el destino de implementación actual es ' *'. Seleccione un destino de implementación más reciente en las opciones de la aplicación de iOS de su proyecto.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *.framework solo se admite si el destino de implementación es 8.0 o posterior. * características no funcionen correctamente.

El marco de trabajo especificado no se admite en el destino de implementación hace referencia a la versión de iOS. Actualizar el destino de implementación a una nueva versión de iOS, o quitar el uso del marco especificado de la aplicación.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin.iOS * requiere Xcode 5.0 o posterior. La versión de Xcode actual (se encuentra en *) es *.

Instale un Xcode más reciente.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: No hay ningún comando especificado.

No se especificó ninguna acción para mtouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: No se puede normalizar la ruta de acceso ' *': *

Se trata de un error interno. Si ve este error, registre un error [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: La ruta de Xcode ' *' no existe.

La ruta de acceso de Xcode se pasa mediante `--sdkroot` no existe. Especifique una ruta válida.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: No se encuentra Xcode en la ubicación predeterminada (/ /Applications/xcode.app). Instale Xcode o pasar una ruta de acceso personalizada con--sdkroot <path>.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: No se puede determinar la ruta de acceso a Xcode.app desde la raíz del sdk ' *'. Especifique la ruta de acceso completa a la agrupación de Xcode.app.

La ruta de acceso se pasa mediante `--sdkroot` no especifica una aplicación de Xcode válida. Especifique una ruta de acceso a una aplicación de Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: El Xcode.app '\*' no es válido (el archivo '\*' no existe).

La ruta de acceso se pasa mediante `--sdkroot` no especifica una aplicación de Xcode válida. Especifique una ruta de acceso a una aplicación de Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: No se encontró Xcode seleccionada actualmente en el sistema: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: No se encontró Xcode seleccionada actualmente en el sistema. "xcode-select--print-path' devolvió ' *', pero este directorio no existe.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: Ningún Xcode.app especificado (uso de--sdkroot), con Xcode del sistema notificados por 'xcode-select--print-path': *

Esta es una advertencia informativa, que explica que Xcode se puede usar, porque se ha especificado ninguno.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: Ningún Xcode.app especificado (con--sdkroot o "xcode-select--print-path"), con el valor predeterminado de Xcode en su lugar: *

Esta es una advertencia informativa, que explica que Xcode se puede usar, porque se ha especificado ninguno.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: No se encuentra el archivo ejecutable en la extensión de * (sin entrada CFBundleExecutable en su Info.plist)

Cada archivo Info.plist debe tener un archivo ejecutable (mediante la entrada CFBundleExecutable), sin embargo, se debe generar automáticamente una entrada durante la compilación.

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin.iOS solo admite plataformas admitidas con proyectos unificado.

Xamarin.iOS solo admite plataformas admitidas al usar la API unificada; Actualice el proyecto para usar la API unificada.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin.iOS solo admite plataformas admitidas al destino de implementación es al menos 8.0 (destino de implementación actual: * incrustado marcos: *)

Xamarin.iOS solo admite plataformas admitidas cuando el destino de implementación es al menos 8.0 (porque las versiones anteriores de iOS no admite marcos incrustados).

Actualice el destino de implementación en Info.plist del proyecto a 8.0 o posterior.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Ensamblado de registrador de compilación no válido: *

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Registrador no válido: *

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Valor no válido para la plataforma de destino: *.

Se pasó un marco de destino no válido con el marco de destino argumento--. Especifique una plataforma de destino válido.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: .NET framework de destino no válido: *. Plataformas de destino válidos son: *.

Se pasó un marco de destino no válido con el marco de destino argumento--. Especifique una plataforma de destino válido.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Plataforma desconocida: *. Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en http://bugzilla.xamarin.com con un caso de prueba.

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: No se admiten extensiones para la plataforma ' *'.

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin.iOS * no se admite un destino de implementación de * (el valor mínimo es *). Seleccione un destino de implementación más reciente en Info.plist del proyecto.

El destino de implementación mínima es el especificado en el mensaje de error. Seleccione un destino de implementación más reciente en Info.plist del proyecto.

Si no es posible actualizar el destino de implementación, a continuación, use una versión anterior de Xamarin.iOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin.iOS * no se admite un destino de implementación mínima de * (el máximo es *). Seleccione un destino de implementación anterior en Info.plist del proyecto o actualizar a una versión más reciente de Xamarin.iOS.

Xamarin.iOS no admite establecer el destino de implementación mínima para una versión posterior a la versión que se creó esta versión concreta de Xamarin.iOS.

Seleccione un destino de implementación mínimo anterior en Info.plist del proyecto, o actualizar a una versión más reciente de Xamarin.iOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Arquitectura no válido ' *' para * proyectos. Las arquitecturas válidas son: *

Se especificó una arquitectura válida. Compruebe que la arquitectura es válida.

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075: Ninguna arquitectura especificada (con el argumento--abi). Es necesaria para una arquitectura * proyectos.

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076: Los proyectos de WatchOS deben ser extensiones.

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077: Las compilaciones incrementales habilitadas con un destino de implementación < 8.0 (actualmente *). Esto no se admite (la aplicación resultante no se iniciará en iOS 9), por lo que el destino de implementación se establecerá como 8.0.

Esta es una advertencia que le informa de que el destino de implementación se ha establecido en 8.0 para esta compilación para que las compilaciones incrementales funcionen correctamente.

Las compilaciones incrementales solo se admiten cuando el destino de implementación es al menos 8.0 (debido a la aplicación resultante no se iniciará en iOS 9 en caso contrario).

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078: La versión recomendada de Xcode para Xamarin.iOS * es Xcode * o una versión posterior. La versión de Xcode actual (se encuentra en *) es *.

Esta es una advertencia que le informa de que la versión actual de Xcode no es la versión recomendada de Xcode para esta versión de Xamarin.iOS.

Actualice Xcode para garantizar el comportamiento óptimo.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: Deshabilitar NewRefCount,--new-refcount:false, está en desuso.

Esta es una advertencia que le informa de que la solicitud para deshabilitar el nuevo recuento de referencias (--nuevo - refcount:false) se ha omitido.

Ahora es obligatoria para todos los proyectos de la nueva característica de recuento de referencias y, por tanto, no es posible deshabilitar ya.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: El argumento de línea de comandos: informe de bloqueo de descarga también requiere--descarga-bloqueos-informe de.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (--enable-repl) solo se admite cuando no se usa la vinculación (--nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: No se admite solo Asm bitcode en watchOS. Utilice cualquier bitcode--: marcador o--bitcode: completa.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Bitcode no se admite en el simulador. No pase--bitcode al compilar para el simulador.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: Ninguna referencia a ' *' se ha encontrado. Se agregarán automáticamente.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: Una plataforma de destino (--target framework) se debe especificar al crear para TVOS o WatchOS.

Esto normalmente indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Compilaciones incrementales (--fastdev) no es compatible con el GC Boehm. Se deshabilitarán las compilaciones incrementales.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: El catálogo global debe estar en modo cooperativo para las aplicaciones para watchOS. Quite el--argumento coop: false mtouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: La opción '\*'no se puede capturar el valor'\*' cuando está habilitado el modo cooperativo para el catálogo global.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Esta versión de Xamarin.iOS requiere el * SDK (incluido con Xcode *). O bien actualizar Xcode para obtener los archivos de encabezado necesarios o establecer el comportamiento del vinculador administrado para vincular solo SDK de marco (para tratar de evitar las nuevas API).

Xamarin.iOS requiere los archivos de encabezado de la versión SDK especificada en el mensaje de error para compilar la aplicación. La manera recomendada para corregir este error consiste en actualizar Xcode para obtener el SDK necesario, esto incluye todos los archivos de encabezado requeridos. Si tiene varias versiones de Xcode instalada, o desea usar un Xcode en una ubicación no predeterminada, asegúrese de establecer la ubicación de Xcode correcta en las preferencias de su IDE.

Una posible, solución alternativa es habilitar al vinculador administrado. Esta acción quitará sin usar API, incluidas, en la mayoría de los casos, la nueva API donde los archivos de encabezado son que faltan (o incompletos). Sin embargo esto no funcionará si el proyecto usa la API que se introdujo en un SDK más reciente del que su Xcode proporciona.

Una solución de último recurso sería utilizar una versión anterior de Xamarin.iOS, que es compatible con el SDK de su proyecto requiere.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: No se encontró 'mlaunch'.

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: No se podrían copiar los archivos de Aot en el directorio de destino {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: no se encontró ninguna referencia a Xamarin.iOS.dll.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Error interno *. Envíe un informe de errores con un caso de prueba (http://bugzilla.xamarin.com).

Este mensaje de error se notifica cuando se produce un error en una comprobación de coherencia interna en Xamarin.iOS.

Esto indica un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Destino de compilación de ensamblado no válido: ' *'. Envíe un informe de errores con un caso de prueba (http://bugzilla.xamarin.com).

Este mensaje de error se notifica cuando se produce un error en una comprobación de coherencia interna en Xamarin.iOS.

Esto es siempre un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caso de prueba.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: El ensamblado ' *' está especificada varias veces en: argumentos de destino de compilación de ensamblado.

El ensamblado que se ha mencionado en el mensaje de error está especificado varias veces en: argumentos de destino de compilación de ensamblado. Asegúrese de que cada ensamblado solo se menciona una vez.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Los ensamblados\*'y'\*' tienen el mismo nombre de destino ('\*'), pero distintos destinos ('\*' y ' *').

Los ensamblados mencionados en el mensaje de error tienen los destinos de compilación en conflicto.

Por ejemplo:

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

En este ejemplo está intentando crear una biblioteca dinámica y un marco de trabajo con la misma marca (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: El objeto estático '\*' contiene más de un ensamblado ('\*'), pero cada objeto estático debe corresponder con exactamente un ensamblado.

Todos los ensamblados mencionados en el mensaje de error se compilan en un único objeto estático. Esto no está permitido, se deben compilar todos los ensamblados en un objeto estático diferente.

Por ejemplo:

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

En este ejemplo intenta crear un objeto estático (`MyBinary`) consta de dos ensamblados (`Assembly1.dll` y `Assembly2.dll`), lo que no está permitido.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: no se especificó ningún destino de compilación de ensamblado para ' *'.

Al especificar el ensamblado de compilación de destino mediante `--assembly-build-target`, todos los ensamblados en la aplicación deben tener asignado un destino de compilación.

Este error se produce cuando el ensamblado que se ha mencionado en el mensaje de error no tiene asignado el destino de compilación un ensamblado.

Consulte la documentación sobre `--assembly-build-target` para obtener más información.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: Nombre de destino de compilación del ensamblado '\*' no es válido: el carácter '\*' no está permitido.

El nombre del destino de compilación de ensamblado debe ser un nombre de archivo válido.

Por ejemplo, estos valores desencadenarán este error:

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

Dado que `my/path.o` no es un nombre de archivo válido debido al carácter de separador de directorio.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Los ensamblados\*' tienen diferentes optimizaciones LLVM personalizadas (\*), que no se permite cuando se compilan en un solo archivo binario.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: El ensamblado de destino de compilación ' *' no coincide con todos los ensamblados.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: El ensamblado '{0}' se cargó desde una ruta diferente a la ruta proporcionada (proporciona la ruta de acceso: {1}, ruta de acceso real: {2}).

Esta es una advertencia que indica que un ensamblado al que hace referencia la aplicación se cargó desde una ubicación diferente que solicitado.

Esto podría significar que la aplicación hace referencia a varios ensamblados con el mismo nombre, pero desde distintas ubicaciones, lo cual podrían provocar resultados inesperados (se utilizará sólo el primer ensamblado).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Las compilaciones incrementales se han deshabilitado porque esta versión de Xamarin.iOS no admite las compilaciones incrementales en los proyectos que incluyen bibliotecas de enlace de terceros y que se compila a bitcode.

Las compilaciones incrementales se han deshabilitado porque esta versión de Xamarin.iOS no admite las compilaciones incrementales en los proyectos que incluyen bibliotecas de enlace de terceros y que se compila a bitcode (proyectos de tvOS y watchOS).

Se requiere ninguna acción por su parte, este mensaje es meramente informativo.

Para obtener más información, consulte Error nº[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Esta advertencia no se muestra ya.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode se habilitó porque esta versión de Xamarin.iOS no admite la creación watchOS los proyectos con LLVM sin habilitar bitcode.

Se ha habilitado automáticamente Bitcode porque esta versión de Xamarin.iOS no admite la creación watchOS los proyectos con LLVM sin habilitar bitcode.

Se requiere ninguna acción por su parte, este mensaje es meramente informativo.

Para obtener más información, consulte Error nº[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: Uso compartido de código nativo se ha deshabilitado porque *

Hay varios motivos, se puede deshabilitar el uso compartido de código:

* porque el destino de implementación de la aplicación de contenedor es anterior a iOS 8.0 (tiene *)).

Uso compartido de código nativo requiere iOS 8.0 porque el uso compartido de código nativo se implementa mediante marcos de trabajo de usuario, que se introdujo con iOS 8.0.

* Dado que la aplicación de contenedor incluye los ensamblados I18N (*).

Uso compartido de código nativo no se admite si la aplicación de contenedor incluye los ensamblados I18N.

* Dado que la aplicación de contenedor tiene definiciones xml personalizado para el vinculador administrado (*).

Uso compartido de código nativo requiere que no se admite para los proyectos que usan las definiciones xml personalizado para el vinculador administrado.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: Uso compartido de código nativo se ha deshabilitado para la extensión ' *' como *.

* Dado que las opciones de bitcode difieren entre la aplicación de contenedor (\*) y la extensión (\*).

  Uso compartido de código nativo requiere que las opciones de bitcode coinciden entre los proyectos que comparten código.

* Dado que--destino de compilación de ensamblado opciones son diferentes entre la aplicación de contenedor (\*) y la extensión (\*).

  El uso compartido de código nativo requiere que el--destino de compilación de ensamblado de las opciones son idénticas entre los proyectos que comparten código.

  Esta condición puede producirse si compilaciones incrementales son no uno habilitada o deshabilitada en todos los proyectos.

* Dado que los ensamblados I18N son diferentes entre la aplicación de contenedor (\*) y la extensión (\*).

  Uso compartido de código nativo no se admite para las extensiones que incluyen ensamblados de I18N.

* Dado que los argumentos para el compilador AOT son diferentes entre la aplicación de contenedor (\*) y la extensión (\*).

  Uso compartido de código nativo requiere que los argumentos para el compilador AOT no difieren entre los proyectos que comparten código.

* Dado que los otros argumentos para el compilador AOT son diferentes entre la aplicación de contenedor (\*) y la extensión (\*).

  Uso compartido de código nativo requiere que los argumentos para el compilador AOT no difieren entre los proyectos que comparten código.

  Esta condición se produce si el 'realizar todas las operaciones de punto flotante de 32 bits como punto flotante de 64 bits' difiere entre proyectos.

* Dado que LLVM no está habilitado o deshabilitado en la aplicación de contenedor de ambos (\*) y la extensión (\*).

  Uso compartido de código nativo requiere que LLVM está habilitado o deshabilitado para todos los proyectos que comparten código.

* Dado que la configuración del vinculador administrado es diferente entre la aplicación de contenedor (\*) y la extensión (\*).

  Uso compartido de código nativo requiere que la configuración del vinculador administrados es idéntica para todos los proyectos que comparten código.

* Dado que los ensamblados se omitió para el vinculador administrado son diferentes entre la aplicación de contenedor (\*) y la extensión (\*).

  Uso compartido de código nativo requiere que la configuración del vinculador administrados es idéntica para todos los proyectos que comparten código.

* Dado que la extensión tiene definiciones xml personalizado para el vinculador administrado (*).

  Uso compartido de código nativo requiere que no se admite para los proyectos que usan las definiciones xml personalizado para el vinculador administrado.

* Dado que no se compila la aplicación contenedora de la ABI * (mientras se está creando la extensión para esta ABI).

  Uso compartido de código nativo requiere que se compila la aplicación de contenedor para todas las arquitecturas de cualquier extensión de la aplicación se compila para.

  Por ejemplo: esta condición se produce cuando se crea una extensión para ARMv7 + ARM64, pero solo compila la aplicación de contenedor para ARM64.

* Dado que está creando la aplicación contenedora de la ABI \*, que no es compatible con ABI la extensión (\*).

  Uso compartido de código nativo requiere que todos los proyectos de compilación para la misma API exacta.

  Por ejemplo: esta condición se produce cuando se crea una extensión para ARMv7 + llvm + thumb2, pero solo compila la aplicación de contenedor para ARMv7 + llvm.

* Dado que la aplicación de contenedor es hacer referencia al ensamblado '\*'from'\*', mientras que la extensión hace referencia a una versión diferente de ' *'.

  Uso compartido de código nativo requiere que todos los proyectos que comparten código usen las mismas versiones para todos los ensamblados.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: Se recomienda hacer referencia a símbolos dinámicos mediante código (--dinámicos de modo de símbolo = código) cuando se habilita bitcode.

Los proyectos de Xamarin.iOS a menudo mencionará símbolos nativos dinámicamente, lo que significa que el enlazador nativo puede quitar estos símbolos nativos durante el proceso de vinculación nativo, porque el vinculador nativo no verá que se usan estos símbolos.

Normalmente Xamarin.iOS le preguntará al enlazador nativo a mantener estos símbolos (mediante el `-u symbol` marca de enlazador), pero al compilar para bitcode al enlazador nativo no acepta el `-u` marca.

Xamarin.iOS ha implementado una solución alternativa: se va a generar código nativo adicional que hace referencia a estos símbolos y, por tanto, el enlazador nativo verán que se usan estos símbolos. Esto se hace automáticamente cuando se compila en bitcode.

Si `--dynamic-symbol-mode=linker` se pasa a este alternativo se deshabilitará la solución y tratará de Xamarin.iOS pasar, mtouch `-u` al enlazador nativo. Esto probablemente se producirá errores de enlazador nativo.

La solución es quitar el `--dynamic-symbol-mode=linker` argumento de los argumentos mtouch adicionales en las opciones de compilación del proyecto.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Arquitectura no válido: {arch}. no se admiten las arquitecturas de 32 bits cuando el destino de implementación es 11 o posterior. Asegúrese de que el proyecto no se compila para una arquitectura de 32 bits.

iOS 11 no es compatible con las aplicaciones de 32 bits, por lo que no se admite la compilación para una aplicación de 32 bits cuando el destino de implementación es iOS 11 o posterior.

Cambie la arquitectura de destino en las opciones de compilación del proyecto de iOS a arm64 o cambiar el destino de implementación en Info.plist del proyecto a una versión anterior de iOS.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: No se puede iniciar una aplicación de 32 bits en un simulador que solo es compatible con 64 bits.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: Los archivos de Aot no se encontró en el directorio esperado '{msymdir}'.

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: El ensamblado ejecutable * no hace referencia a *.

No se encontró ninguna referencia al ensamblado de plataforma (Xamarin.iOS.dll / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) en el ensamblado ejecutable.

Esto suele suceder cuando no hay ningún código en el proyecto ejecutable que utiliza cualquier cosa, desde el ensamblado de plataforma; Por ejemplo un método Main vacío (y ningún otro código) habría que este error:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

Con la API desde el ensamblado de plataforma resolverá el error:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: No se pudo establecer el idioma actual para '{lang}' (según LANG = {LANG}): {exception}

Esta es una advertencia, que indica que se no se pudo establecer el idioma actual para el idioma del mensaje de error.

El idioma actual será el idioma del sistema.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125:--Ensamblado de destino de compilación se omite el argumento de línea de comandos en el simulador.

Se requiere ninguna acción, este mensaje es meramente informativo.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Las compilaciones incrementales se han deshabilitado porque no se admiten las compilaciones incrementales en el simulador.

Se requiere ninguna acción, este mensaje es meramente informativo.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Las compilaciones incrementales se han deshabilitado porque esta versión de Xamarin.iOS no admite las compilaciones incrementales en los proyectos que incluyen bibliotecas de enlace de más de uno de terceros.

Se han deshabilitado las compilaciones incrementales automáticamente porque esta versión de Xamarin.iOS no siempre compilar proyectos con varias bibliotecas de enlace de terceros correctamente.

Se requiere ninguna acción, este mensaje es meramente informativo.

Para obtener más información, consulte Error nº[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: No se pudo toca el archivo ' *': *

Error al tocar un archivo (que se hace para asegurarse de que las compilaciones parciales se realizan correctamente).

Probablemente se puede omitir esta advertencia; en el caso de los problemas, registre un error (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) y se investigarán.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Mensajes de error relacionados con el proyecto

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Instalador / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: No se pudo encontrar una aplicación en el directorio especificado

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: No se pudo crear vínculos simbólicos, se copiaron los archivos

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: No se pudo terminar la aplicación ' *'. Es posible que deba eliminar manualmente la aplicación.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: No se pudo obtener la lista de aplicaciones instaladas.

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: No se pudo terminar la aplicación '\*'en el dispositivo'\*': *-es posible que deba eliminar manualmente la aplicación.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: No se pudo instalar la aplicación '\*'en el dispositivo'\*': *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: No se pudo iniciar la aplicación '\*'en el dispositivo'\*': *. Todavía puede iniciar manualmente la aplicación pulsando en él.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: No se pudo iniciar el simulador

Este error se produce si no se pudo iniciar el simulador de mtouch.   Esto puede ocurrir a veces, porque ya hay un proceso obsoletas o interrumpidas simulador ejecutado.

El siguiente comando emitido en la línea de comandos de Unix se puede usar para eliminar los procesos de simulador bloqueadas:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: No se pudo copiar el ensamblado '\*'para'\*': *

Se trata de un problema conocido en ciertas versiones de Xamarin.iOS.

Si esto ocurre a usted, pruebe la siguiente solución alternativa:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Sin embargo, puesto que este problema se resolvió en la última versión de Xamarin.iOS, registre un nuevo error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con la información de versión completa y la salida del registro de compilación.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: No se pudo cargar el ensamblado ' *': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: No se pudo agregar el archivo de recursos que faltan: ' *'

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: No se pudieron enumerar las aplicaciones en el dispositivo ' *': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Dependencia error de seguimiento: no hay archivos para comparar. Envíe un informe de errores en http://bugzilla.xamarin.com con un caso de prueba.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un caes de prueba.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: No se pudo volver a usar la versión almacenada en caché de ' *': *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: No se pudo crear el archivo ejecutable ' *': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: No se pudo crear el archivo ejecutable ' *': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: No se pudo crear el archivo de aviso porque ya existe un directorio con el mismo nombre.

Quitar el directorio `NOTICE` del proyecto.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: No se pudo crear el archivo de aviso: *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: No se pudo comprobaciones de firma de código de la aplicación y no se puede instalar en el dispositivo ' *'. Compruebe los certificados, perfiles, el aprovisionamiento y los identificadores de lote. Probablemente el dispositivo no es parte del perfil de aprovisionamiento seleccionado (error: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: La aplicación tiene los derechos no compatibles con el perfil de aprovisionamiento actual y no se puede instalar en el dispositivo ' *'. Compruebe el registro de dispositivos de iOS para obtener información detallada (error: 0xe8008016).

Esto puede ocurrir si:

* La aplicación tiene los derechos que no es compatible con el perfil de aprovisionamiento actual.
  Soluciones posibles:
  - Especifique otro perfil de aprovisionamiento que es compatible con los derechos necesarios para la aplicación.
  - Quite los derechos que no se admite en el perfil de aprovisionamiento actual.
* El dispositivo que está intentando implementar para que no se incluye en el perfil de aprovisionamiento que está usando.
  Soluciones posibles:
  - Crear una nueva aplicación desde una plantilla en Xcode, seleccione el mismo perfil de aprovisionamiento e implementar al mismo dispositivo. A veces Xcode puede actualizar automáticamente los perfiles de aprovisionamiento con nuevos dispositivos (en otros casos que xcode le preguntará qué hacer).
  -Ir al centro de desarrollo de iOS y actualizar el perfil de aprovisionamiento con el nuevo dispositivo, descargue el perfil de aprovisionamiento actualizado en el equipo.

En la mayoría de los casos para obtener más información sobre el error se imprimirá en el registro de dispositivos iOS, que puede ayudarle a diagnosticar el problema.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: No se pudo iniciar la aplicación '\*'en el dispositivo'\*': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: No se pudo copiar el archivo '\*'para'\*': {2}

No se puede copiar un archivo. El mensaje de error de la operación de copia tiene más información sobre el error.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: No se pudo copiar el directorio '\*'para'\*': {2}

No se pudo copiar un directorio. El mensaje de error de la operación de copia tiene más información sobre el error.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: No se pudo comunicar con el dispositivo para encontrar la aplicación ' *': *

Se produjo un error al intentar buscar una aplicación en el dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: No se pudo comprobar la firma de la aplicación en el dispositivo ' *'. Asegúrese de que haya instalado el perfil de aprovisionamiento y no ha expirado (error: 0xe8008017).

El dispositivo rechaza la instalación de la aplicación porque no se pudo comprobar la firma.

Asegúrese de que haya instalado el perfil de aprovisionamiento y no ha expirado.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: No se pueden enumerar los informes de bloqueo en el dispositivo *.

Se produjo un error al intentar enumerar los informes de bloqueo en el dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: No se pudo descargar el informe de bloqueo * desde el dispositivo *.

Se produjo un error al intentar descargar los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: No se puede usar Xcode 7 o posterior para iniciar aplicaciones en dispositivos con iOS * (Xcode 7 solo admite iOS 6 +).

No es posible usar Xcode 7 o posterior para iniciar aplicaciones en dispositivos con iOS versión debajo 6.0.

Use una versión anterior de Xcode o pulse en la aplicación manualmente para iniciarla.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: Especificación de dispositivo no válido: ' *'. Se esperaba 'ios', 'watchos' o 'all'.

La especificación del dispositivo pasa usando--dispositivo no es válido. Los valores válidos son: 'ios', 'watchos' o 'all'.

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: No se pudo encontrar una aplicación en el directorio especificado: *

La ruta de acceso de la aplicación pasado a--launchdev no existe. Especifique un grupo de aplicaciones válido.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: Inicio de aplicaciones en el dispositivo mediante un identificador de lote está en desuso. Pase la ruta de acceso completa a la agrupación para iniciar.

Se recomienda para pasar la ruta de acceso a la aplicación para iniciar en el dispositivo en lugar de simplemente el identificador de paquete.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: No se pudo iniciar la aplicación '\*'en el dispositivo'\*' porque el dispositivo está bloqueado. Desbloquear el dispositivo y vuelva a intentarlo.

Desbloquear el dispositivo y vuelva a intentarlo.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Este archivo ejecutable de la aplicación podría ser demasiado grande (* MB) para ejecutar en el dispositivo. Si se habilitó bitcode desea deshabilitarlo para el desarrollo, solo es necesario para enviar aplicaciones a Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: No se pudo desinstalar la aplicación '\*'desde el dispositivo'\*': *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: No se incluyen diferentes versiones de framework "{name}"

No es posible crear un vínculo con distintas versiones de la misma plataforma.

Esto suele suceder cuando una extensión hace referencia a una versión diferente de un marco de trabajo de la aplicación de contenedor (posiblemente a través de un ensamblado de enlace de terceros).

Después de este error habrá varios [MT1036](#MT1036) errores enumerar las rutas de acceso para cada marco de trabajo diferente.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: Framework "{name}" incluida desde: {path} (relacionado para el error anterior)

Este error se produce solo junto con [MT1036](#MT1036). Consulte [MT1036](#MT1036) para obtener más información.

### <a name="mt11xx-debug-service"></a>MT11xx: Depuración de servicio

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: No se pudo iniciar la aplicación

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: No se pudo conectar con la aplicación (terminará): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: No se puede desasociar

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: No se pudo enviar paquetes: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Tipo de respuesta inesperada

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: No se pudo obtener la lista de aplicaciones en el dispositivo: solicitud agotó el tiempo de espera.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: Aplicación no se pudo iniciar: *

Compruebe si el dispositivo está bloqueado.

Si va a implementar una aplicación empresarial o con un perfil de aprovisionamiento gratuito, es posible que tenga el desarrollador de confianza (Esto se explica <a href="http://stackoverflow.com/a/30726375/183422">aquí</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: No pudo encontrar herramientas de desarrollo para este dispositivo XX (AA).

Algunas operaciones de mtouch requieren el <tt>DeveloperDiskImage.dmg</tt> archivo esté presente.   Este archivo es parte de Xcode y se encuentra normalmente en relación con el SDK que está usando para la compilación, en la <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>.

Este error puede producirse ya sea porque no tiene un DeveloperDiskImage.dmg que coincida con el dispositivo que se ha conectado.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: Aplicación no se pudo iniciar porque el dispositivo está bloqueado. Desbloquear el dispositivo y vuelva a intentarlo.

Compruebe si el dispositivo está bloqueado.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: Aplicación no se pudo iniciar debido a restricciones de seguridad de iOS. Asegúrese de que el programador es de confianza.

Si va a implementar una aplicación empresarial o con un perfil de aprovisionamiento gratuito, es posible que tenga el desarrollador de confianza (Esto se explica <a href="http://stackoverflow.com/a/30726375/183422">aquí</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: Aplicación se inicia correctamente, pero no es posible esperar la aplicación salir cuando se le solicite porque no es posible detectar la finalización de la aplicación al iniciar usa gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: simulador

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: No se pudo cargar el simulador: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Configuración simulador no válido: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Especificación simulador no válido: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: Especificación simulador no válido ' *': no se especifica el tiempo de ejecución.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: Especificación simulador no válido ' *': no se especifica el tipo de dispositivo.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: No se pudo encontrar el tiempo de ejecución del simulador ' *'.

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: No se pudo encontrar el tipo de dispositivo simulador ' *'.

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: No se pudo encontrar el tiempo de ejecución del simulador ' *'.

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: No se pudo encontrar el tipo de dispositivo simulador ' *'.

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Especificación simulador no válido: \*, clave desconocida '\*'

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: La versión del simulador '\*'no es compatible con el tipo de simulador'\*'

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: No se pudo crear una versión de simulador donde el tipo = * tiempo de ejecución y = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Especificación simulador no válido para Xcode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Especificación simulador no válido para Xcode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: SDK no válido especificado: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: No se pudo encontrar el simulador UDID ' *'.

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: No se pudo cargar el paquete de aplicación en ' *'.

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: Ningún identificador de paquete se encuentra en la aplicación en ' *'.

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: No se pudo encontrar el simulador para ' *'.

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: No se encontró el tiempo de ejecución más reciente de simulador de dispositivos ' *'.

Esto suele indicar un problema con Xcode.

Cosas para intentar solucionar este problema:

* Use una vez el simulador en Xcode.
* Pasar de una versión SDK explícita mediante el sdk-- <version>.
* Vuelva a instalar Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: No se pudo encontrar el simulador de iPhone emparejado para el simulador de WatchOS ' *'.

Al iniciar una aplicación para WatchOS en un simulador de WatchOS, debe haber un emparejado simulador de iOS también.

Ver simuladores se pueden emparejar con iOS simuladores con interfaz de usuario de dispositivos de Xcode (dispositivos -> menú Ventana).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: Biblioteca nativa `*` (\*) se omite porque no coincide con la actual architecture(s) de compilación (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: No se pudo extraer la biblioteca nativa ' *' de '+'. Asegúrese de que la biblioteca nativa se insertó correctamente en el ensamblado administrado (si el ensamblado se generó con un proyecto de enlace, la biblioteca nativa debe incluirse en el proyecto y su acción de compilación debe ser 'ObjcBindingNativeLibrary').

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: No se pudo descomprimir el marco nativo '\*'from'\*'. Revise el registro de compilación para obtener más información del comando nativo 'zip'.

No se pudo descomprimir el marco nativo especificado de la biblioteca de enlace.

Revise el registro bulid para obtener más información sobre este error del comando nativo 'zip'.

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: El marco de trabajo incrustado ' *' en * no es válido: no contiene un archivo Info.plist.

El marco de trabajo incrustado especificado no contiene un archivo Info.plist y, por lo tanto, no es un marco de trabajo válido.

Asegúrese de que el marco de trabajo es válido.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: La biblioteca de enlace '\*' contiene un marco de trabajo de usuario (\*), pero el usuario incrustadas marcos requieren iOS 8.0 (el destino de implementación actual es *). Establezca el destino de implementación en el archivo Info.plist para al menos 8.0.

La biblioteca de enlace especificado contiene un marco de trabajo incrustada, pero Xamarin.iOS solo admite plataformas admitidas en iOS 8.0 o posterior.

Establecer el destino de implementación en el archivo Info.plist para al menos 8.0 para resolver este error (o no usar marcos incrustados).

### <a name="mt14xx-crash-reports"></a>MT14xx: Los informes de bloqueo

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: No se pudo abrir el servicio de informes de bloqueo: AFCConnectionOpen devuelve *

Se produjo un error al intentar acceder a los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: No se pudo cerrar el servicio de informes de bloqueo: AFCConnectionClose devuelve *

Se produjo un error al intentar acceder a los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: No se pudo leer la información de archivo *: AFCFileInfoOpen devuelve *

Se produjo un error al intentar acceder a los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: No se pudo leer el informe de bloqueo: AFCDirectoryOpen (*) devuelve: *

Se produjo un error al intentar acceder a los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: No se pudo leer el informe de bloqueo: AFCFileRefOpen (*) devuelve: *

Se produjo un error al intentar acceder a los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: No se pudo leer el informe de bloqueo: AFCFileRefRead (*) devuelve: *

Se produjo un error al intentar acceder a los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: No se pueden enumerar los informes de bloqueo: AFCDirectoryOpen (*) devuelve: *

Se produjo un error al intentar acceder a los informes de bloqueo del dispositivo.

Cosas para intentar resolver este problema:

* Eliminar la aplicación del dispositivo y vuelva a intentarlo.
* Desconecte el dispositivo y volver a conectarlo.
* Reinicio del dispositivo.
* Reinicie el equipo Mac.
* Sincronizar el dispositivo con iTunes (Esto quitará todos los informes de bloqueo del dispositivo).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: No una Mach O biblioteca dinámica (encabezado desconocido '0 x *'): *.

Se produjo un error durante el procesamiento de la biblioteca dinámica en cuestión.

Asegúrese de que la biblioteca dinámica es una biblioteca dinámica de Mach O válida.

Se puede comprobar el formato de una biblioteca mediante el `file` comando desde un terminal:

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: No una biblioteca estática (encabezado desconocido ' *'): *.

Se produjo un error durante el procesamiento de la biblioteca estática en cuestión.

Asegúrese de que la biblioteca estática es una biblioteca estática de Mach O válida.

Se puede comprobar el formato de una biblioteca mediante el `file` comando desde un terminal:

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: No una Mach O biblioteca dinámica (encabezado desconocido '0 x *'): *.

Se produjo un error durante el procesamiento de la biblioteca dinámica en cuestión.

Asegúrese de que la biblioteca dinámica es una biblioteca dinámica de Mach O válida.

Se puede comprobar el formato de una biblioteca mediante el `file` comando desde un terminal:

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Formato desconocido para la entrada fat en posición * en *.

Se produjo un error al procesar el archivo fat en cuestión.

Asegúrese de que el archivo fat no es válido.

El formato de un archivo fat se pueda comprobar mediante la `file` comando desde un terminal:

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: Archivo de tipo * no es un archivo MachO (*).

Se produjo un error al procesar el archivo MachO en cuestión.

Asegúrese de que el archivo es una biblioteca dinámica Mach O válida.

El formato de un archivo se pueda comprobar mediante la `file` comando desde un terminal:

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Mensajes de error del vinculador

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: No se pudo vincular ensamblados

Este error significa que el vinculador administrado encontró un error inesperado, por ejemplo, una excepción y no se pudo completar o guarde el ensamblado que se está procesando. Para obtener más información sobre el error exacto formarán parte del registro de compilación, por ejemplo

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

Es importante en el archivo de un informe de errores para dichos problemas. En la mayoría de los casos, se puede proporcionar una solución alternativa hasta que se publique una corrección correcta. La información anterior es fundamental (junto con un caso de prueba o el ensamblado binairy) resolver el problema.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: No se puede resolver la referencia: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: Opción ' *' se omitirá porque se deshabilita la vinculación

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: Archivo de definiciones adicional del vinculador ' *' no se pudo encontrar.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: Las definiciones de ' *' no se pudo analizar.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: No se puede cargar el archivo mscorlib.dll desde: *. Vuelva a instalar Xamarin.iOS.

Esto suele indica que hay un problema con la instalación de Xamarin.iOS. Intente volver a instalar Xamarin.iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: HttpMessageHandler desconocido `*`. Los valores válidos son HttpClientHandler (valor predeterminado), CFNetworkHandler o NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: Desconocido TlsProvider `*`.  Los valores válidos son default o appletls.

El valor dado a `tls-provider=` no es un proveedor TLS (Transport Layer Security) válido.

El `default` y `appletls` son los únicos valores válidos y ambos representan la misma opción, que se usa para proporcionar SSL/TLS admite el uso de la API de TLS nativa de Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: HttpMessageHandler no válido `*` para watchOS. El único valor válido es NSUrlSessionHandler.

Esta es una advertencia que se produce porque el archivo de proyecto especifica un HttpMessageHandler no válido.

Las versiones anteriores de nuestras herramientas de vista previa generan de forma predeterminada un valor no válido en el archivo de proyecto.

Para corregir esta advertencia, abra el archivo de proyecto en un editor de texto y quite todos los nodos HttpMessageHandler desde XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: No válido TlsProvider `legacy` opción. El único valor válido `appletls` se usará.

El `legacy` proveedor, que era una SSLv3 totalmente administrada / TLSv1 único proveedor, no se incluyen con Xamarin.iOS ya. Los proyectos que usan este proveedor antiguo y cree ahora con las versiones más recientes `appletls` uno.

Para corregir esta advertencia, abra el archivo de proyecto en un editor de texto y quite todo ' MtouchTlsProvider'' nodos desde el código XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: No se pudo procesar la descripción en formato XML.

Esto significa que hay un error en la [archivo de configuración de vinculador XML personalizado](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/) proporcionó, revise el archivo.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: El ensamblado '\*' se hace referencia desde dos ubicaciones diferentes: '\*' y ' *'.

El ensamblado que se ha mencionado en el mensaje de error se carga desde varias ubicaciones. Asegúrese de usar siempre la misma versión de un ensamblado.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: No se puede cargar el ensamblado raíz ' *'

No se pudo cargar el ensamblado raíz. Compruebe que la ruta de acceso en el mensaje de error hace referencia a un archivo existente y que es un ensamblado .NET válido.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: Un error de enlace optimizador procesamiento `...`.

Algo inesperado se produjo al intentar optimizar genera código de enlace. El elemento causando el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado denominado (o que contiene el tipo o método llamada) debe proporcionarse en un [informe de errores](http://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **mtouch adicionales argumentos**).

El último dígito `x` será:
* `0` para un nombre de ensamblado;
* `1` para un nombre de tipo;
* `3` para un nombre de método;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: No se pudo quitar los recursos del usuario procesamiento `...`.

Un problema inesperado se produjo al intentar quitar los recursos del usuario. El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](http://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

Recursos de usuario son los archivos incluidos dentro de ensamblados (como recursos) que deben extraerse en tiempo de compilación para crear el paquete de aplicación. Esto incluye:

* `__monotouch_content_*` y `__monotouch_pages_*` recursos; y
* Bibliotecas nativas incrustadas dentro de un ensamblado de enlace;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: HttpMessageHandler predeterminada establecedor no se pudo procesar `...`.

Ocurrido algo inesperado al intentar establecer el valor predeterminado `HttpMessageHandler` para la aplicación. Registre un [informe de errores](http://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: Eliminador de código no pudieron procesarse `...`.

Un problema inesperado se produjo al intentar quitar el código de la BCL de envío con la aplicación. Registre un [informe de errores](http://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: No se pudo Sealer procesamiento `...`.

Algo inesperado se produjo al intentar sellar los métodos o tipos (final) o cuando devirtualizing algunos métodos. El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](http://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: Reductor de metadatos no pudieron procesarse `...`.

Un problema inesperado se produjo al intentar reducir los metadatos de la aplicación. El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](http://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: No se pudo MarkNSObjects procesamiento `...`.

Ocurrido algo inesperado al intentar marcar `NSObject` subclases de la aplicación. El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](http://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: No pudo inclusor procesamiento `...`.

Un problema inesperado se produjo al intentar código alineado desde la aplicación. El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](https://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: Conservar inteligente de conversión de Enum no pudieron procesarse `...`.

Un problema inesperado se produjo al intentar marcar los métodos de conversión para las enumeraciones inteligentes de la aplicación. El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](https://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: No se puede resolver la referencia '\*', que se hace referencia desde el método'\*' en ' *'.

Se encontró una referencia de ensamblado no válido al procesar el método mencionado en el mensaje de error.

El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](https://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Error al procesar el método '\*'en el ensamblado'\*': *

Un problema inesperado se produjo al intentar marcar el método mencionado en el mensaje de error.

El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](https://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Error al procesar el ensamblado '\*': *

Se produjo un error inesperado al procesar un ensamblado.

El ensamblado causando el problema es un nombre en el mensaje de error. Para corregir este problema en el ensamblado debe proporcionarse en un [informe de errores](https://bugzilla.xamarin.com) junto con un registro de compilación completa con el nivel de detalle habilitada (es decir, `-v -v -v -v` en el **argumentos mtouch adicionales**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: No se puede vincular el ensamblado '{0}' ya que es el modo mixto.

Ensamblados de modo mixto no pueden procesar el vinculador.

Consulte https://msdn.microsoft.com/library/x0w2664k.aspx para obtener más información sobre los ensamblados de modo mixto.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Mensajes de error AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: Podría no AOT el ensamblado ' *'

Esto suele indica un error en el compilador AOT. Registre un error [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un proyecto que se puede usar para reproducir el error.

A veces es posible solucionarlo deshabilitando las compilaciones incrementales en la opción de compilación de iOS del proyecto (pero sigue siendo un error, por lo que envíe el informe de todos modos).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002: Restricción de AOT: método ' *' debe ser estático, ya que está decorado con [MonoPInvokeCallback]. Consulte [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

Este mensaje de error procede el compilador AOT.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: En conflicto: opciones de depuración y--llvm. Depurar con Soft está deshabilitada.

No se admite la depuración cuando se habilita LLVM. Si tiene que depurar la aplicación, deshabilite primero LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: Podría no AOT el ensamblado ' *' porque no existe.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: La dependencia '\*'del ensamblado'\*' no se encontró. Revise las referencias del proyecto.

Esto suele ocurrir cuando un ensamblado hace referencia a otra versión de un ensamblado de plataforma (normalmente la versión 4 de .NET de mscorlib.dll).

Esto no es compatible y no puede crear ni ejecutar correctamente (el ensamblado puede usar API de la versión 4 de .NET de mscorlib.dll que no tiene la versión de Xamarin.iOS).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: No se pudo calcular un mapa de dependencias completo para el proyecto. Esto dará como resultado tiempos de compilación más lento porque Xamarin.iOS no pueden detectar correctamente lo que debe volver a generar (y lo que no necesita volver a generar). Revise las advertencias anteriores para obtener más detalles.

 compilar o ejecutar correctamente (el ensamblado puede usar API de la versión 4 de .NET de mscorlib.dll que no tiene la versión de Xamarin.iOS).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: No se cargarán los archivos de información de depuración (*.mdb) cuando se habilita llvm.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: Compatibilidad de Bitcode requiere el uso de back-end LLVM AOT (--llvm)

Compatibilidad de Bitcode requiere el uso de back-end LLVM AOT (--llvm).

Deshabilite la compatibilidad de Bitcode o habilitar LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Mensajes de error de generación de código

### <a name="mt40xx-main"></a>MT40xx: principal

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: No se pudo expandir la plantilla principal a `*`.

Se produjo un error al generar main.m. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: No se pudo compilar el código generado para métodos P/Invoke. Envíe un informe de errores en http://bugzilla.xamarin.com

No se pudo compilar el código generado para métodos P/Invoke. Envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt41xx-registrar"></a>MT41xx: registrador

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: El registrador no puede generar una firma para el tipo `*`.

Se encontró un tipo de API exportada que el tiempo de ejecución no sabe cómo calcular las referencias desde Objective-C.

Si cree que Xamarin.iOS debe admitir el tipo en cuestión, presente una solicitud de mejora en [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: El registrador encuentra un tipo no válido `*` en la firma del método `*`. Utilice `*` en su lugar.

Esto actualmente solo se produce con un tipo: System.DateTime. Use el equivalente de Objective-C (NSDate) en su lugar.

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: El registrador encuentra un tipo no válido `*` en la firma del método `*`: el tipo implementa INativeObject, pero no tiene un constructor que toma dos (IntPtr, bool) argumentos

Esto se produce cuando el registrador de encontrar un tipo en una firma con las características mencionadas. El registrador que tenga que crear nuevas instancias del tipo y, en este caso requiere un constructor con el (IntPtr, bool) firma: el primer argumento (IntPtr) especifica el identificador administrado, mientras que el segundo si el llamador pasa a través de la propiedad de nativo controlar (si este valor es false 'conservar' se llamará en el objeto).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: El registrador no puede serializar el valor devuelto de tipo `*` en la firma del método `*`.

Se encontró un tipo de API exportada que el tiempo de ejecución no sabe cómo calcular las referencias desde Objective-C.

Si cree que Xamarin.iOS debe admitir el tipo en cuestión, presente una solicitud de mejora en [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: El registrador no puede serializar el parámetro de tipo `*` en la firma del método `*`.

Si cree que Xamarin.iOS debe admitir el tipo en cuestión, presente una solicitud de mejora en [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: El registrador no puede serializar el valor devuelto para la estructura `*` en la firma del método `*`.

Se encontró un tipo de API exportada que el tiempo de ejecución no sabe cómo calcular las referencias desde Objective-C.

Si cree que Xamarin.iOS debe admitir el tipo en cuestión, presente una solicitud de mejora en [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: El registrador no puede serializar el parámetro de tipo `*` en la firma del método `+`.

Se encontró un tipo de API exportada que el tiempo de ejecución no sabe cómo calcular las referencias desde Objective-C.

Si cree que Xamarin.iOS debe admitir el tipo en cuestión, presente una solicitud de mejora en [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: El registrador no puede obtener el tipo ObjectiveC de tipo administrado `*`.

Se encontró un tipo de API exportada que el tiempo de ejecución no sabe cómo calcular las referencias desde Objective-C.

Si cree que Xamarin.iOS debe admitir el tipo en cuestión, presente una solicitud de mejora en [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: No se pudo compilar el código generado del registrador. Envíe un informe de errores en http://bugzilla.xamarin.com

No se pudo compilar el código generado para el registrador. El registro de compilación contendrá los resultados del compilador nativo, que explica por qué no se compila el código.

Esto es siempre un error en Xamarin.iOS; envíe un informe de errores en [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com) con el proyecto o un caso de prueba.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: El registrador no puede serializar el parámetro de salida de tipo `*` en la firma del método `*`.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: El registrador no puede generar una firma para el tipo `*` en el método `*`.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112: El registrador encuentra un tipo no válido `*`. Registrar tipos genéricos con Objective-C no se admite y puede provocar un comportamiento aleatorio o bloqueos (para hacia atrás compatibilidad con versiones anteriores de Xamarin.iOS es posible omitir este error pasando `--unsupported--enable-generics-in-registrar` como un mtouch adicionales argumento en la página de opciones de compilación del proyecto iOS. Consulte [developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar) para obtener más información).

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: El registrador encuentra un método genérico: '\*.\*'. Exportación de los métodos genéricos no se admite y dará lugar a un comportamiento aleatorio o bloqueos.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Error inesperado en el registrador para el método '\*.\*'-presente en un informe de errores http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: No se pudo registrar el ensamblado ' *': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: El registrador encontró un error de coincidencia de firma en el método '*.*'-el selector indica el método toma * parámetros, mientras que el método administrado * parámetros.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: No se puede registrar dos tipos administrados ('\*'y'\*') con el mismo nombre nativo ('* ').

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: No se pudo registrar el selector '\*'del miembro'\*. *' ya está registrado el selector en un miembro diferente.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: El registrador encontró un tipo de campo desconocido '\*'en el campo'\*. *'. Envíe un informe de errores en http://bugzilla.xamarin.com

Este error indica un error en Xamarin.iOS. Envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: No se puede usar GCC / G ++ al compilar el código generado en el registrador de dominios estático cuando se usa el marco de cuentas (los archivos de encabezado proporcionados por Apple que se usa durante la compilación requieren Clang). Use Clang (--del compilador: clang) o el registrador dinámico (--registrador: dinámico).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: No se puede usar el compilador de Clang proporcionado en el *.* SDK para compilar el código generado en el registrador de dominios estático al que no son ASCII los nombres de tipo ('* ') están presentes en la aplicación. Use GCC / G ++ (--del compilador: gcc | g ++), el registrador dinámico (--registrador: dinámico) o un SDK más reciente.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: El tipo del parámetro en la función variádica variádicas ' *' debe ser System.IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: Válido * se encuentra en ' *'. Envíe un informe de errores en http://bugzilla.xamarin.com

Este error indica un error en Xamarin.iOS. Envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: El registrador encuentra un tipo no válido '\*'en la firma de método'\*': la interfaz debe tener un atributo de protocolo que se especifica su tipo contenedor.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: No se puede registrar dos protocolos administrados ('\*'y'\*') con el mismo nombre nativo ('* ').

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: No se puede registrar más de un método de interfaz para el método '\*' (que consiste en implementar '\*').

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: El registrador encontró un tipo de parámetro genérico no válido '\*'en el método'\*'. El parámetro genérico debe tener una restricción 'NSObject'.

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: El registrador encontró un tipo de valor devuelto genérico no válido '\*'en el método'\*'. El tipo de valor devuelto genérico debe tener una restricción 'NSObject'.

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: El registrador no puede exportar los métodos estáticos de clases genéricas ('* ').

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: El registrador no puede exportar las propiedades estáticas de las clases genéricas ('\*.\*').

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: El registrador encontró un tipo de valor devuelto genérico no válido '\*'en la propiedad'\*'. El tipo de valor devuelto debe tener una restricción 'NSObject'.

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: No se puede construir una instancia del tipo ' *' desde Objective-C porque el tipo es genérico. [Excepción en tiempo de ejecución]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: La aplicación utiliza el ' *' framework, que no se incluye en el SDK usa para compilar la aplicación de iOS (este marco se introdujo en iOS *, mientras que va a compilar con iOS * SDK de.) Seleccione un SDK más reciente en las opciones de compilación de iOS de la aplicación.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: El miembro '\*.\*' tiene un atributo de exportación que no especifica un selector. Falta un selector.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: El registrador no puede serializar el tipo de parámetro '\*'del parámetro'\*'en el método'\*. *'

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: El registrador no puede serializar el tipo de propiedad '\*'de la propiedad'\*. *'.

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: El registrador no puede serializar el tipo de propiedad '\*'de la propiedad'\*. *'. Las propiedades con el atributo [Connect] deben tener un tipo de propiedad de NSObject (o una subclase de NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: El registrador encontró un error de coincidencia de firma en el método '*.*'-el selector indica el método variádicas toma * parámetros, mientras que el método administrado * parámetros.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: No se puede registrar el selector '\*'en el miembro'\*' porque Xamarin.iOS implícitamente registra este selector.

Esto se produce cuando un tipo de marco de trabajo de creación de subclases e intenta implementar un 'conservar', 'versión' o 'coinciden' método:

```csharp
class MyNSObject : NSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

Sin embargo es posible invalidar estos métodos si la clase no es la primera subclase del marco de tipo es:

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

En este caso invalidará Xamarin.iOS `retain`, `release` y `dealloc` en el `MyNSObject` (clase), y no hay ningún conflicto.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: No se pudo registrar el tipo de ' *'.

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: La clase de ObjectiveC ' *' no se pudo registrado, no parece que se derivan de cualquier clase ObjectiveC conocido (incluido NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: No se puede registrar el método ' *' porque no tiene una cama elástica asociado. Envíe un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Enumeración no válido ' *': las enumeraciones con el atributo [nativo] deben tener un tipo de enumeración subyacente de "long" o "ulong".

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: El parámetro Name del atributo registrador de la clase\*' ('\*") contiene un carácter no válido: '\*' (\*).

El nombre de una clase Objectice C no puede contener espacios en blanco, lo que significa que el `Register` atributo en la clase administrada correspondiente no puede tener un `Name` parámetro no puede contener espacios en blanco o bien.

Compruebe que el `Register` atributo en la clase administrada se ha mencionado en el mensaje de error no contiene ningún espacio en blanco.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Se detectó un protocolo heredar el protocolo JSExport al usar al registrador dinámico. No es posible exportar protocolos a JavaScriptCore dinámicamente; se debe usar el registrador estático (agregar '--registrador: static a los argumentos mtouch adicionales en las opciones de compilación para seleccionar el registrador estático de iOS del proyecto).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: El registrador encuentra un protocolo genérico: ' *'. No se admite la exportación protocolos genéricos.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: No se puede registrar el método '\*.\*' porque el tipo del primer parámetro ("\*') no coincide con el tipo de categoría ('\*').

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: No se puede registrar el tipo '\*' porque la propiedad Type ('\*') en su categoría de atributo no se hereda de NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: No se puede registrar el tipo de ' *' porque no se establece la propiedad Type en su atributo de categoría.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: No se puede registrar el tipo de ' *' como una categoría porque implementa INativeObject o deriva de nsobject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: No se puede registrar el tipo '\*' como una categoría porque es genérico.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: No se puede registrar el método '\*' como un método de la categoría porque es genérico.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: No se puede registrar el método '\*'con el selector de'\*' como un método de categoría en ' *' como Objective-C ya tiene una implementación para este selector.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: No se puede registrar dos categorías ('\*'y'\*') con el mismo nombre nativo ('* ').

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: No se puede registrar el método de la categoría '\*' porque se requiere al menos un parámetro (y su tipo debe coincidir con el tipo de categoría '\*')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: No se puede registrar el constructor * en la categoría * porque no se admiten constructores en categorías.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: No se puede registrar el método ' *' como un método de la categoría porque los métodos de la categoría deben ser estáticos.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Un carácter no válido '\*' (\*) se encuentra en el selector '\*'for'\*'.

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: El registrador encuentra una estructura no admitida '\*': todos los campos en una estructura también deben ser estructuras (campo '\*'con el tipo'{2}' no es una estructura).

El registrador encuentra una estructura con campos no compatibles.

Todos los campos en una estructura que se expone a Objective-C también deben ser estructuras (no de clases).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: El tipo '\*"(usar como * {2}) no está disponible en ** (que se presentó en * *)\* compile con otra más reciente * SDK (normalmente se realiza mediante la versión más reciente de Xcode.

El registrador encuentra un tipo que no está incluido en el SDK actual.

Actualice Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Error interno en el registrador (*). Envíe un informe de errores en http://bugzilla.xamarin.com

Este error indica un error en Xamarin.iOS. Envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: No se puede exportar la propiedad '\*' porque su selector '\*' es una palabra clave de C de objetivo. Use un nombre diferente.

El selector para la propiedad en cuestión no es un identificador válido de C de objetivo.

Use un identificador válido de Objective-C como selectores.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: El registrador no pudo encontrar el tipo 'System.Void' en cualquiera de los ensamblados de referencia.

Este error probablemente indica un error en Xamarin.iOS. Envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: No se puede registrar el método '\*' porque la firma contiene un tipo (\*) que no es un tipo de referencia.

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: No se puede registrar el método '\*' porque la firma contiene un tipo genérico (\*) con un tipo de argumento genérico que no es una subclase de NSObject (*).

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: No se puede registrar el tipo ' {administrados\_nombre}' porque su nombre de Objective-C ' {exportado\_nombre}' es una palabra clave de C de objetivo. Use un nombre diferente.

El nombre de Objective-C para el tipo en cuestión no es un identificador válido de C de objetivo.

Use un identificador válido de C de objetivo.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: No se pudo generar un contenedor de P/Invoke para {{el método}: {message}

Xamarin.iOS no se pudo generar una función de contenedor de P/Invoke para la mencionada.
Compruebe el mensaje de error notificado para la causa subyacente.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: El registrador no se puede convertir de '{tipo administrado}' en '{tipo nativo}' para el valor devuelto del método {método}.

Vea la descripción del error <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: El atributo BindAs en el miembro {miembro} no es válido: el tipo BindAs {type} es diferente del tipo de propiedad {type}.

Asegúrese de que el tipo en el atributo BindAs coincide con el tipo del miembro a que se adjunta.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: El registrador no se puede convertir de '{tipo nativo}' a '{tipo administrado}' para el parámetro '{parameter name}' en el método {método}.

El registrador no admite la conversión entre los tipos mencionados.

Esto es un error en Xamarin.iOS si no se proporciona la API en cuestión por Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ] [ 1].

Si experimenta esto al desarrollar un proyecto de enlace para una biblioteca nativa, estamos dispuestos a agregar compatibilidad con nuevas combinaciones de tipos. Si este es el caso, registre una solicitud de mejora ([http://bugzilla.xamarin.com][2]) con una prueba de caso y se podrá evaluarla.

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: Mensajes de error GCC y cadena de herramientas

### <a name="mt51xx-compilation"></a>MT51xx: compilación

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Falta ' *' compilador. Instale el componente "Herramientas de línea de comandos" Xcode

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: No se pudo montar el archivo ' *'. Envíe un informe de errores en http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: No se pudo compilar el archivo ' *'. Envíe un informe de errores en http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: No se pudo encontrar ninguna de ellas el '\*'ni'\*' compilador. Instale el componente "Herramientas de línea de comandos" Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: No se pudo compilar los archivos de ' *'. Envíe un informe de errores en http://bugzilla.xamarin.com

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt52xx-linking"></a>MT52xx: vinculación

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Vinculación nativo no se pudo. Revise el registro de compilación y las marcas de usuario proporcionadas para gcc: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Vinculación nativo no se pudo. Revise el registro de compilación.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: Nativo advertencia de vinculación: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Nativo error de vinculación: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Vinculación nativo no se pudo, símbolo no definido: *. Compruebe que ha hecho referencia a todos los marcos necesarios y se vinculan bibliotecas nativas correctamente.

Esto sucede cuando el enlazador nativo no encuentra un símbolo que se hace referencia en algún lugar. Hay varias razones, que esto puede ocurrir:

* Un enlace de terceros requiere un marco de trabajo, pero el enlace no especificar esto en su `[LinkWith]` atributo. Soluciones:
  - Si es el autor del enlace de aplicaciones de terceros, o tener acceso a su origen, modifique el enlace `[LinkWith]` atributo debe incluir el marco de trabajo que necesita:

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - Si no se puede modificar el enlace de aplicaciones de terceros, puede vincular manualmente con el marco necesario pasando `-gcc_flags '-framework SystemFramework'` a `mtouch` (Esto se hace mediante la modificación de los argumentos mtouch adicionales en la página de opciones de compilación del proyecto iOS. Recuerde que debe realizarse para cada configuración de proyecto).
* En algunos casos un enlace administrado se compone de varias bibliotecas nativas, y debe estar todo incluido en los enlaces. Es posible tener más de una biblioteca nativa en cada proyecto de enlace, por lo que la solución es agregar todas las bibliotecas nativas necesarias para el proyecto de enlace.</li>
* Un enlace administrado hace referencia a los símbolos nativos que no existen en la biblioteca nativa.
    Esto suele ocurrir cuando un enlace que ha existido durante algún tiempo y se ha modificado el código nativo durante ese tiempo para que una determinada clase nativa se haya eliminado o cambiado de nombre, aunque no se ha actualizado el enlace.
* P/Invoke se refiere a un símbolo nativo que no existe. A partir de Xamarin.iOS 7.4 un <a href="#MT5214">MT5214</a> se notifica el error para este caso (consulte MT5214 para obtener más información).
* Un enlace de terceros / biblioteca se compiló con C++, pero el enlace no especificar esto en su `[LinkWith]` atributo. Esto suele ser bastante fácil de reconocer, porque tienen los símbolos son símbolos de C++ alterados (un ejemplo común es `__ZNKSt9exception4whatEv`).
  - Si es el autor del enlace de aplicaciones de terceros, o tener acceso a su origen, modifique el enlace `[LinkWith]` atributo para establecer el `IsCxx` marca:

            [LinkWith ("mylib.a", IsCxx = true)]

  - Si no se puede modificar el enlace de aplicaciones de terceros o va a vincular manualmente con una biblioteca de terceros, puede establecer la marca equivalente pasando <code>-cxx</code> a mtouch (Esto se hace mediante la modificación de los argumentos mtouch adicionales en la página de opciones de compilación del proyecto iOS . Recuerde que debe realizarse para cada configuración de proyecto).

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Vinculación nativo no se pudo, clase de Objective-C no definida: \*. El símbolo '\*' no se encontró en cualquiera de las bibliotecas o marcos de trabajo vinculados con la aplicación.

Esto sucede cuando el enlazador nativo no puede encontrar una clase de Objective-C que se hace referencia en algún lugar. Hay varios motivos, esto puede ocurrir: los mismos que para [MT5210](#MT5210) y además:

* Un enlace de terceros enlazado un protocolo de Objective-C, pero no puede anotarlo con el <code>[Protocol]</code> atributo en su definición de api. Soluciones:
  - Agregue el campo que falta `[Protocol]` atributo:

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Vinculación nativo no se pudo, símbolo duplicado: *.

Esto sucede cuando el enlazador nativo encuentra símbolos duplicados entre todas las bibliotecas nativas. Después de este error puede haber uno o varios [MT5213](#MT5213) errores con la ubicación de cada aparición del símbolo. Posibles razones para este error:

* La misma biblioteca nativa se incluye dos veces.
* Se producen dos bibliotecas nativas distintas definir los mismos símbolos.
* Una biblioteca nativa no se compila correctamente y contiene más de una vez el mismo símbolo.
  Puede confirmarlo utilizando el siguiente conjunto de comandos desde un terminal (reemplace i386 por x86_64/armv7 y armv7s/arm64 según qué arquitectura va a compilar para):

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  Hay varias maneras posibles de solucionar este problema:

  - Solicitud que el proveedor de la biblioteca nativa corregirlo y proporciona la versión actualizada.
  - Corregir manualmente mediante la eliminación de los archivos de objeto adicional (Esto solo funciona si el problema es en realidad los archivos de objeto duplicados)

            # Find out if the library is a fat library, and which
            # architectures it contains.
            lipo -info libNative.a

            # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
            lipo libNative.a -thin ARCH -output libNative.ARCH.a

            # Extract the object files for the offending architecture
            # This will remove the duplicates by overwriting them
            # (since they have the same filename)
            mkdir -p ARCH
            cd ARCH
            ar -x ../libNative.ARCH.a

            # Reassemble the object files in an .a
            ar -r ../libNative.ARCH.a *.o
            cd ..

            # Reassemble the fat library
            lipo *.a -create -output libNative.a

  - Pregunte al vinculador que quite el código no utilizado. Xamarin.iOS realizará esta acción automáticamente si se cumplen las condiciones siguientes:
    - Todos los enlaces de terceros `[LinkWith]` atributos habilitó SmartLink:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - No `-gcc_flags` se pasa a mtouch (en el campo de argumentos mtouch adicionales de las opciones de compilación de iOS del proyecto).
    - También es posible formular el vinculador directamente para quitar el código sin usar mediante la adición de `-gcc_flags -dead_strip` opciones de compilación a los argumentos mtouch adicionales en iOS del proyecto.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Duplicar símbolo en: * (ubicación relacionada con error anterior)

Este error se produce solo junto con [MT5212](#MT5212). Consulte [MT5212](#MT5212) para obtener más información.

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Vinculación nativo no se pudo, símbolo no definido: *. Este símbolo se hizo referencia al miembro administrado *. Compruebe que todos los marcos necesarios han sido las bibliotecas nativas y que se hace referencia vinculadas.

Este error se produce cuando el código administrado contiene un P/Invoke a un método nativo que no existe. Por ejemplo:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Hay varias soluciones posibles:

  -  Quite el código fuente P/Invoke en cuestión.
  -  Habilitación del enlazador administrado para todos los ensamblados (Esto se hace en las opciones de compilación de iOS del proyecto estableciendo "Comportamiento del enlazador" en "Todos los ensamblados"). Esto quitará eficazmente todas la P/Invoke no usa de la aplicación (automáticamente, en lugar de forma manual como el punto anterior). La desventaja es que esto hará que las compilaciones del simulador un poco más lento y se puede interrumpir la aplicación si está utilizando la reflexión, puede encontrar más información sobre el vinculador [aquí](~/ios/deploy-test/linker.md) )
  -  Cree una segunda biblioteca nativa que contiene los símbolos nativos que faltan. Tenga en cuenta que esto es simplemente una solución alternativa (si resulta que intenta llamar a esas funciones, se bloqueará la aplicación).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: Las referencias a ' *' podría requerir adicional - framework = XXX o - lXXX instrucciones al enlazador nativo

Esta es una advertencia, que indica que se ha detectado un P/Invoke para hacer referencia a la biblioteca en cuestión, pero la aplicación no se vincula con él.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Vinculación nativo error para *. Envíe un informe de errores en http://bugzilla.xamarin.com

Este error se produce cuando se vinculan los resultados del compilador AOT.

Este error probablemente indica un error en Xamarin.iOS. Envíe un informe de errores en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: Posiblemente vinculación nativo no se pudo la línea de comandos del vinculador es demasiado larga (* caracteres).

Vinculación nativo no se pudo, y es posible que esto se produce porque el comando del vinculador era demasiado largo.

Los proyectos de Xamarin.iOS a menudo mencionará símbolos nativos dinámicamente, lo que significa que el enlazador nativo puede quitar estos símbolos nativos durante el proceso de vinculación nativo, porque el vinculador nativo no verá que se usan estos símbolos.

Normalmente, Xamarin.iOS le preguntará al enlazador nativo a mantener estos símbolos con el `-u symbol` marca de enlazador, pero si hay muchos de estos símbolos, toda la línea de comandos puede superar la longitud máxima de línea de comandos según lo especificado por el sistema operativo.

Hay algunos posibles orígenes para estos símbolos dinámicos:

* P/Invokes a los métodos de las bibliotecas vinculadas estáticamente (donde es el nombre de dll `__Internal` en el atributo DllImport `[DllImport ("__Internal")]`).
* Las referencias a las ubicaciones de memoria en las bibliotecas vinculadas estáticamente desde proyectos de enlace de campo (`[Field]` atributos).
* Clases de Objective-C hace referencia en las bibliotecas vinculadas estáticamente desde proyectos de enlace (cuando se usa en las compilaciones incrementales o cuando no se usa al registrador estático).

Soluciones posibles:

* Habilitación del enlazador administrado (si es posible que todos los ensamblados en lugar de sólo los ensamblados SDK). Esto podría quitar los orígenes de datos lo suficiente para símbolos dinámicos para que el vinculador 's de línea de comandos no supera el máximo.
* Reducir el número de P/Invoke, referencias de campo o clases de C de objetivo.
* Vuelva a escribir los símbolos dinámicos para que los nombres más cortos.
* Pasar `-dlsym:false` opciones de compilación como argumentos mtouch adicionales en iOS del proyecto. Con esta opción, Xamarin.iOS generará una referencia nativa en el código de la compilación AOT y no tendrá que pedir al vinculador que debe mantener este símbolo. Sin embargo, solo funciona para el dispositivo genera y provocará errores del vinculador si hay P/Invokes a las funciones que no existen en la biblioteca estática.
* Pasar `--dynamic-symbol-mode=code` opciones de compilación como argumentos mtouch adicionales en iOS del proyecto. Con esta opción, Xamarin.iOS generará código nativo adicional que hace referencia a estos símbolos en lugar de pedir al enlazador nativo a mantener estos símbolos con argumentos de línea de comandos. La desventaja de este enfoque es que, aumentará el tamaño del archivo ejecutable un poco.
* Habilitar el registrador estático pasando `--registrar:static` como argumentos mtouch adicionales en iOS del proyecto las opciones de compilación (para las compilaciones del simulador, ya que el registrador estático ya es la predeterminada para compilaciones de dispositivo). El registrador estático generará código que hace referencia a clases de Objective-C de forma estática, por lo que no hay ninguna necesidad de solicitar al enlazador nativo a mantener estas clases.
* Deshabilitar las compilaciones incrementales (para las compilaciones de dispositivo). Cuando están habilitadas las compilaciones incrementales, el código generado por el registrador estático no considerará al enlazador nativo, lo que significa que Xamarin.iOS todavía debe pedir al vinculador que mantener hace referencia a clases de C de objetivo. Deshabilitando las compilaciones incrementales de este modo, evitará esta necesidad.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: No se puede omitir el símbolo dinámico {symbol} (--símbolo dinámicos omitir = {symbol}) porque no se detectó como un símbolo dinámico.

El argumento de línea de comandos `--ignore-dynamic-symbol=symbol` se ha pasado, pero este símbolo no es un símbolo que se ha reconocido como un símbolo dinámico que se debe mantener manualmente.

Hay dos razones principales para esto:

* El nombre del símbolo es incorrecto.
    * No anteponga un carácter de subrayado en el nombre del símbolo.
    * El símbolo de clases de Objective-C es `OBJC_CLASS_$_<classname>`.
* El símbolo es correcto, pero es un símbolo que ya se conserva por medios normales (algunos compilación a causas de opciones la lista exacta de símbolos dinámicos para variar).

### <a name="mt53xx-other-tools"></a>MT53xx: Otras herramientas

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Falta la herramienta 'Quitar'. Instale el componente "Herramientas de línea de comandos" Xcode

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Herramienta de 'dsymutil' que falta. Instale el componente "Herramientas de línea de comandos" Xcode

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: No se pudo generar los símbolos de depuración (directorio de dSYM). Revise el registro de compilación.

Se produjo un error al ejecutar dsymutil en el directorio .app final para crear los símbolos de depuración. Revise el registro de compilación para ver el resultado de dsymutil y ver cómo se puede corregir.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: No se pudo eliminar el archivo binario final. Revise el registro de compilación.

Se produjo un error al ejecutar la herramienta 'Quitar' para quitar información de depuración de la aplicación.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Herramienta de 'lipo' que falta. Instale el componente "Herramientas de línea de comandos" Xcode

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: No se pudo crear el una biblioteca fat. Revise el registro de compilación.

Se produjo un error al ejecutar la herramienta 'lipo'. Revise el registro de compilación para ver el error notificado por 'lipo'.

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: No se pudo firmar el ejecutable. Revise el registro de compilación.

Se produjo un error al iniciar la aplicación. Revise el registro de compilación para ver el error notificado por 'firma de código'.

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mensajes de error de las herramientas de mtouch interno

### <a name="mt600x-stripper"></a>MT600x: eliminador

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: Versión de la ejecución de Cecil no admite la eliminación del ensamblado

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: No se pudo quitar el ensamblado `*`.

Se produjo un error cuando la eliminación de código administrado (quitando el código IL) desde los ensamblados en la aplicación.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [Message UnauthorizedAccessException]

Seguridad se produjo un error durante la eliminación de símbolos de la aplicación de depuración.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Mensajes de error de MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: No se pudo resolver direcciones IP de host para la configuración del depurador Wi-Fi.

*Tarea de MSBuild: DetectDebugNetworkConfigurationTaskBase*

Pasos para solucionar problemas:

- intenta ejecutar `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (que debería proporcionarle una dirección IP y no un error obviamente).
- Intente ejecutar "ping \`hostname\`" que podría proporcionar más información, como: `cannot resolve MyHost.local: Unknown host`

En algunos casos, es un problema de "red local" y se puede resolver agregando el host desconocido `127.0.0.1   MyHost.local` en `/etc/hosts`.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Este equipo no tiene ningún adaptador de red. Esto es necesario al depurar o generación de perfiles de dispositivo a través de Wi-Fi.

*Tarea de MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: La extensión de la aplicación ' *' no contiene un archivo Info.plist.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: La extensión de la aplicación ' *' no especifica un CFBundleIdentifier.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: La extensión de la aplicación ' *' no especifica un CFBundleExecutable.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: La extensión de la aplicación '\*' tiene un CFBundleIdentifier no válido (\*), no comienza con CFBundleIdentifier del paquete aplicación principal (*).

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: La extensión de la aplicación '\*' tiene un CFBundleIdentifier (\*) que termina con el sufijo no válido ".key".

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: La extensión de la aplicación ' *' no especifica un CFBundleShortVersionString.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: La extensión de la aplicación ' *' tiene un archivo Info.plist no válido: no contiene un diccionario NSExtension.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: La extensión de la aplicación ' *' tiene un archivo Info.plist no válido: el diccionario NSExtension no contiene un valor NSExtensionPointIdentifier.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: La WatchKit extensión ' *' tiene un archivo Info.plist no válido: el diccionario NSExtension no contiene un diccionario NSExtensionAttributes.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: La WatchKit extensión ' *' no tiene exactamente una aplicación del reloj.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: La WatchKit extensión ' *' tiene un archivo Info.plist no válido: UIRequiredDeviceCapabilities debe contener la capacidad de 'watch-complementaria'.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: La aplicación del reloj ' *' no contiene un archivo Info.plist.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: La aplicación del reloj ' *' no especifica un CFBundleIdentifier.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: La aplicación del reloj '\*' tiene un CFBundleIdentifier no válido (\*), no comienza con CFBundleIdentifier del paquete aplicación principal (*).

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: La aplicación del reloj '\*' no tiene un valor válido de UIDeviceFamily. Pero se esperaba 'Watch (4)"se encontró '\* (*)'.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: La aplicación del reloj ' *' no especifica un CFBundleExecutable

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: La aplicación del reloj '\*' tiene un valor WKCompanionAppBundleIdentifier no válido ('\*'), no coincide con CFBundleIdentifier del paquete aplicación principal ('* ').

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: La aplicación del reloj ' *' tiene un archivo Info.plist no válido: la clave WKWatchKitApp debe estar presente y tener un valor 'true'.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: La aplicación del reloj ' *' tiene un archivo Info.plist no válido: la clave LSRequiresIPhoneOS no debe estar presente.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: La aplicación del reloj ' *' no contiene una extensión de inspección.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: La extensión de inspección ' *' no contiene un archivo Info.plist.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: La extensión de inspección ' *' no especifica un CFBundleIdentifier.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: La extensión de inspección ' *' no especifica un CFBundleExecutable.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: La extensión de inspección '\*' tiene un CFBundleIdentifier no válido (\*), no comienza con CFBundleIdentifier del paquete aplicación principal (*).

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: La extensión de inspección '\*' tiene un CFBundleIdentifier (\*) que termina con el sufijo no válido ".key".

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: La extensión de inspección ' *' tiene un archivo Info.plist no válido: no contiene un diccionario NSExtension.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: La extensión de inspección ' *' tiene un archivo Info.plist no válido: el NSExtensionPointIdentifier debe ser "com.apple.watchkit".

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: La extensión de inspección ' *' tiene un archivo Info.plist no válido: el diccionario NSExtension debe contener NSExtensionAttributes.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: La extensión de inspección ' *' tiene un archivo Info.plist no válido: el diccionario NSExtensionAttributes debe contener un WKAppBundleIdentifier.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: La WatchKit extensión ' *' tiene un archivo Info.plist no válido: UIRequiredDeviceCapabilities no debe contener la capacidad de 'watch-complementaria'.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: La aplicación del reloj ' *' no contiene un archivo Info.plist.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: La aplicación del reloj ' *' no especifica un CFBundleIdentifier.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: La aplicación del reloj '\*' no tiene un valor válido de UIDeviceFamily. Se esperaba '\*', pero se encontraron'\* (\*)'.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: La aplicación del reloj ' *' no especifica un CFBundleExecutable.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: La WatchKit extensión '{extensionName}' tiene un valor WKAppBundleIdentifier no válido ('\*'), no coincide con CFBundleIdentifier la aplicación de inspección ('\*').

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: La aplicación del reloj ' *' tiene un archivo Info.plist no válido: el WKCompanionAppBundleIdentifier debe existir y debe coincidir con CFBundleIdentifier del paquete aplicación principal.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: La aplicación del reloj ' *' tiene un archivo Info.plist no válido: la clave LSRequiresIPhoneOS no debe estar presente.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: El grupo de aplicaciones {AppBundlePath} no contiene un archivo Info.plist.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Ruta de acceso del archivo Info.plist Main no especifica un CFBundleIdentifier.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Ruta de acceso del archivo Info.plist Main no especifica un CFBundleExecutable.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Ruta de acceso del archivo Info.plist Main no especifica un CFBundleSupportedPlatforms.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Ruta de acceso del archivo Info.plist Main no especifica un UIDeviceFamily.

*Tarea de MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: No se reconoce el formato: *.

*Tarea de MSBuild: PropertyListEditorTaskBase*

Donde * pueden ser:

- cadena
- array
- dict
- bool
- reales
- enteros
- date
- datos

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Agregar: entrada, *, se ha especificado incorrectamente.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Agregar: entrada, *, contiene el índice de matriz no válido.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Agregar: * entrada ya existe.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Agregar: no se puede agregar la entrada, *, al elemento primario.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Eliminar: no se puede eliminar la entrada, *, de elemento primario.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Eliminar: entrada, *, contiene el índice de matriz no válido.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Eliminar: entrada, *, no existe.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Importar: entrada, *, se ha especificado incorrectamente.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Importar: entrada, *, contiene el índice de matriz no válido.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Importar: Error al leer el archivo: *.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Importar: no se puede agregar la entrada, *, al elemento primario.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Fusionar mediante combinación: no se puede agregar entradas de la matriz al diccionario

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Fusionar mediante combinación: especifica la entrada debe ser un contenedor.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Fusionar mediante combinación: entrada, *, contiene el índice de matriz no válido.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Fusionar mediante combinación: entrada, *, no existe.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Fusionar mediante combinación: Error al leer el archivo: *.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Establecer: entrada, *, se ha especificado incorrectamente.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Establecer: entrada, *, contiene el índice de matriz no válido.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Establecer: entrada, *, no existe.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Acción del editor PropertyList desconocido: *.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Error al cargar ' *': *.

*Tarea de MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Error al guardar ' *': *.

*Tarea de MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Mensajes de error de tiempo de ejecución

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Discrepancia de versiones entre el tiempo de ejecución nativo de Xamarin.iOS y monotouch.dll. Vuelva a instalar Xamarin.iOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: No se encontró el método '\*'en el tipo'\*'.

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: No se pudo encontrar el método genérico cerrado '\*'en el tipo'\*'.

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: No se puede crear una instancia de * para el objeto nativo de 0 x * (de tipo ' *'), porque ya existe otra instancia para este objeto nativo (de tipo *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: El tipo de contenedor '\*'falta su clase ObjectiveC nativo'\*'.

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: No se pudo encontrar el selector '\*'en el tipo'\*'

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: No se puede obtener el descriptor del método para el selector '\*'en el tipo'\*', porque el selector no corresponde a un método

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: La versión cargada de Xamarin.iOS.dll se compiló para * bits, mientras que el proceso es * bits. Registre un error en http://bugzilla.xamarin.com.

Esto indica que algo no funciona correctamente en el proceso de compilación. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: No se puede encontrar el bloque de método de conversión para el método de delegado *.*' s parámetro #*. Registre un error en http://bugzilla.xamarin.com.

Esto indica que una API no se ha enlazado correctamente. Si se trata de una API expuesta por Xamarin, registre un error en nuestro bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)), si es un enlace de aplicaciones de terceros, póngase en contacto con el proveedor.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Discrepancia de tamaño de tipo nativo entre Xamarin. [iOS | Mac] .dll y la arquitectura de ejecución. Xamarin. [iOS | Mac] .dll se creó para *-bits, mientras que el proceso actual es *-bit.

Esto indica que algo no funciona correctamente en el proceso de compilación. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: No se puede encontrar el delegado para el atributo block de conversión ([DelegateProxy]) para el valor devuelto del método *.*. Registre un error en http://bugzilla.xamarin.com.

Xamarin.iOS no pudo encontrar un método necesario en tiempo de ejecución (para convertir a un delegado en un bloque).

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: DelegateProxyAttribute no válido para el valor devuelto del método *.*: DelegateType es null. Registre un error en http://bugzilla.xamarin.com.

El atributo DelegateProxy para el método en cuestión no es válido.

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: DelegateProxyAttribute no válido para el valor devuelto del método *.*: DelegateType ({2}) especifica un tipo sin un campo 'Controlador'. Registre un error en http://bugzilla.xamarin.com.

El atributo DelegateProxy para el método en cuestión no es válido.

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: DelegateProxyAttribute no válido para el valor devuelto del método *.*: del The DelegateType ({2}) 'Controlador' campo es null. Registre un error en http://bugzilla.xamarin.com.

El atributo DelegateProxy para el método en cuestión no es válido.

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: DelegateProxyAttribute no válido para el valor devuelto del método *.*: del The DelegateType ({2}) campo 'Controlador' no es un delegado, es un *. Registre un error en http://bugzilla.xamarin.com.

El atributo DelegateProxy para el método en cuestión no es válido.

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: No se puede convertir el delegado se va a bloquear para el valor devuelto del método *.*, ya que la entrada no es un delegado, es un *. Registre un error en http://bugzilla.xamarin.com.

El atributo DelegateProxy para el método en cuestión no es válido.

Esto normalmente indica un error en Xamarin.iOS; registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Error de coherencia interna. Envíe un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: No se pudo encontrar el ensamblado * en los ensamblados cargados.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: No se pudo encontrar el módulo con MetadataToken * en el ensamblado *.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Tipo de token implícito desconocido: *.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: Se esperaba la referencia del token * como un *, pero es un *. Envíe un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: Se requiere un objeto de instancia para construir un método genérico cerrado para el método genérico abierto: * (referencia de tokens: *). Envíe un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: No se encontró un tipo de extensión válido para la enumeración inteligente '{smart_type}'. Registre un error en https://bugzilla.xamarin.com.

Esto indica un error en Xamarin.iOS. Registre un error en [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).
