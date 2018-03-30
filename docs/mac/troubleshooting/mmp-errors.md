---
title: Mensajes de error Xamarin.Mac (mmp)
description: Una guía de referencia de errores para mmp.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: 74f223acddbb9bfbd88ce843f26a3d6957d081b4
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="xamarinmac-error-messages-mmp"></a>Mensajes de error Xamarin.Mac (mmp)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx: mensajes de error de mmp

P. ej., parámetros de entorno, falta de herramientas.

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM0000: Error inesperado: un error de archivo notifique en http://bugzilla.xamarin.com

Se produjo un error inesperado. Por favor, [un informe de errores de archivo](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) con tanta información como sea posible, incluidos:

* Completa de los registros, de la compilación con el máximo nivel de detalle (por ejemplo, `-v -v -v -v` en el **argumentos adicionales mmp**);
* Un caso de prueba mínimo que reproduzca el error; y
* Todos los datos de versión

Para obtener información sobre la versión exacta, lo más sencillo es usar la **Xamarin Studio** menú, **sobre Xamarin Studio** elemento, **mostrar detalles** botón y la versión de copiar y pegar obtener información (puede usar el **copiar información** botón).

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001: Esta versión de Xamarin.Mac requiere Mono {0} (la versión actual de Mono es {1}). Actualice el Mono.framework desde http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003: Aplicación nombre '{0} .exe' entra en conflicto con un nombre de ensamblado (.dll) SDK o producto.

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007: El ensamblado raíz '{0}' no existe.

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008: Se deben proporcionar una raíz ensamblado {0} única, pero se encontraron ensamblados: '{1}'

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010: No se pudo analizar los argumentos de línea de comandos: {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016: La opción '{0}' está en desuso.

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017: Debe proporcionar un ensamblado de raíz

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018: Argumento de línea de comandos desconocido: '{0}'

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020: Las opciones válidas para '{0}' son '{1}'.

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023: Aplicación nombre '{0} .exe' entra en conflicto con otro ensamblado de usuario.

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026: No se pudo analizar el argumento de línea de comandos '{0}': {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043: No se admite el recolector de elementos no utilizados Boehm. El recolector de elementos no utilizados de SGen se ha seleccionado en su lugar.

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050: No se proporciona un ensamblado de raíz si--se pasa ningún ensamblado de raíz.

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051: Un directorio de salida (--salida) es necesaria si--se pasa ningún ensamblado de raíz.

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053: No se puede deshabilitar el nuevo recuento de referencias con la API unificada.

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056: No se encuentra Xcode en ninguno de nuestros ubicaciones predeterminadas. Instale Xcode o pasar una ruta de acceso personalizada mediante--sdkroot =<path>

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059: No se encontró el Xcode seleccionado actualmente en el sistema: {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060: No se encontró el Xcode seleccionado actualmente en el sistema. 'xcode-select: ruta de acceso de impresión' ha devuelto '{0}', pero ese directorio no existe.

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068: Valor no válido para la plataforma de destino: {0}.

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071: Plataforma desconocido: *. Esto normalmente indica un error en Xamarin.Mac; registre un informe de errores en https://bugzilla.xamarin.com con un caso de prueba.

Esto normalmente indica un error en Xamarin.Mac; registre un informe de errores en [ https://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) con un caso de prueba.

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079: Error interno: ningún archivo ejecutable se copió en el grupo de aplicaciones. Póngase en contacto con 'support@xamarin.com'

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080: Deshabilitar NewRefCount,--new-refcount:false, está en desuso.

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091: Esta versión de Xamarin.Mac requiere el * SDK (incluido con Xcode *). Ya sea actualizar Xcode para obtener los archivos de encabezado necesarios o use el registrador dinámico o establecer el comportamiento del vinculador administrado vínculo plataforma o vínculo Framework SDK sólo (para tratar de evitar las nuevas API).

Xamarin.Mac requiere que los archivos de encabezado de la versión SDK especificado en el mensaje de error, para compilar la aplicación con el registrador estático... La manera recomendada para corregir este error consiste en actualizar Xcode para obtener el SDK necesario, esto incluye todos los archivos de encabezado necesarios. Si tiene varias versiones de Xcode instalado, o desea usar un Xcode en una ubicación no predeterminada, asegúrese de establecer la ubicación correcta de Xcode en las preferencias de su IDE.

Es una solución alternativa, posible habilitar el vinculador administrado. Esto quitará la inclusión de API sin usar, en la mayoría de los casos, la nueva API que se encuentran los archivos de encabezado que faltan (o incompletos). Sin embargo esto no funcionará si el proyecto utiliza API que se introdujo en un SDK más reciente que aquel su Xcode proporciona.

Una solución alternativa posible en segundo lugar, se utilice en su lugar el registrador dinámico. Esto se impone un costo de inicio registre dinámicamente los tipos, pero elimina la necesidad de archivo de encabezado. 

Una solución de último paja sería utilizar una versión anterior de Xamarin.Mac, que es compatible con el SDK de su proyecto requiere.

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097: no se puede encontrar el archivo machine.config '{0}'.

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098: Compilación AOT solo está disponible en unificado

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MM0099: Error interno {0}. Registre un informe de errores con un caso de prueba (http://bugzilla.xamarin.com).

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114: Híbrida AOT compilación requiere que todos los ensamblados que se van AOT compilado.

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx: copiar el archivo / vínculos simbólicos (relacionadas con el proyecto)

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034: No se pudo crear el vínculo simbólico '{archivo}' -> '{destino}': error {number}

### <a name="mm14xx-product-assemblies"></a>MM14xx: Ensamblados de producto

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401: El ensamblado necesario '{0}' no está presente en las referencias

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402: El ensamblado '{0}' no es compatible con esta herramienta

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403: {0} '{1}' no se encontró. .NET framework de destino '{0}' no podrá usarse para empaquetar la aplicación.

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404: La plataforma de destino '{0}' no es válido.

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405: useFullXamMacFramework siempre debe destino framework .NET 4.5, no '{0}' que no es válido

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406: .NET framework de destino '{0}' no es válido cuando como destino la Xamarin.Mac 4.5 .NET Framework.

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407: Marco de '{0}' y el destino de la referencia de Xamarin.Mac no coinciden seleccionado '{1}'.

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx: Errores ensamblado recopilando (que no requieren del vinculador)

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501: No se puede resolver la referencia: {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600: No una c. O biblioteca dinámica (encabezado desconocido ' 0 x {0}'): {1}.

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601: No una biblioteca estática (encabezado desconocido '{0}'): {1}.

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602: No una c. O biblioteca dinámica (encabezado desconocido ' 0 x {0}'): {1}.

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603: Formato desconocido de entrada de fat de posición {0} en {1}.

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604: El archivo de tipo {0} no es un archivo MachO ({{1}).

## <a name="mm2xxx-linker"></a>MM2xxx: Linker

### <a name="mm20xx-linker-general-errors"></a>MM20xx: Errores de vinculador (general)

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001: No se pudo vincular ensamblados

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002: No se puede resolver la referencia: {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003: La opción '{0}' se pasará por alto porque se deshabilita la vinculación

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004: Archivo de definiciones de adicional del vinculador '{0}' no se encuentra.

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005: Las definiciones de '{0}' no se pudieron analizar.

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006: Biblioteca nativa '{0}' se ha hecho referencia, pero no se pudo encontrar.

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007: Xamarin.Mac de API unificada en un perfil de .NET completo no admite la vinculación. Pasar la marca - nolink.

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009: Referencia {0}. \ {1\\} ** este mensaje está relacionado con MM2006 **

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010: HttpMessageHandler desconocido `{0}`. Los valores válidos son HttpClientHandler (valor predeterminado), CFNetworkHandler o NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011: TLSProvider desconocido ' {0}.  Los valores válidos son default o appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012: Sólo primera {0} de {1} "hace referencia a" advertencias que se muestra. ** Este mensaje relacionado con 2009 **

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013: No se pudo resolver la referencia a "{0}", se hace referencia en "{{1}". La aplicación no incluirá el ensamblado que se hace referencia y puede producir un error en tiempo de ejecución.

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014: Xamarin.Mac extensiones no admite la vinculación. La solicitud para la vinculación se pasará por alto. ** Este mensaje es obsoleta en XM 3.6 + **

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016: TlsProvider no válido `{0}` opción. El único valor válido `{1}` se usará.

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017: No se pudo procesar la descripción en formato XML: {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x: Un error de enlace optimizador procesamiento `...`.

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100: Xamarin.Mac clásico API no admite la vinculación de plataforma.

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103: Error al procesar el ensamblado '\*': *

Se produjo un error inesperado al procesar un ensamblado.

El ensamblado causando el problema aparece en el mensaje de error. Para corregir este problema en el ensamblado deberá proporcionarse en un [informe de errores](https://bugzilla.xamarin.com) junto con un registro de compilación completa con detalle habilitado (es decir, `-v -v -v -v` en el **argumentos adicionales mtouch**).

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: No se puede vincular el ensamblado '{0}' ya que es el modo mixto.

Ensamblados de modo mixto no pueden ser procesados por el vinculador.

Consulte https://msdn.microsoft.com/en-us/library/x0w2664k.aspx para obtener más información sobre los ensamblados de modo mixto.

## <a name="mm3xxx-aot"></a>MM3xxx: AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx: Errores AOT (general)

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001: Podría no AOT el ensamblado '{0}'

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009: AOT de '{0}' se ha solicitado pero no se encontró

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010: Exclusión de AOT de '{0}' se ha solicitado pero no se encontró

## <a name="mm4xxx-code-generation"></a>MM4xxx: generación de código

### <a name="mm40xx-driverm"></a>MM40xx: driver.m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001: No se pudo expandir la plantilla principal a `{0}`.

### <a name="mm41xx-registrar"></a>MM41xx: registrador

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134: La aplicación utiliza el marco de '{0}', que no se incluye en el SDK de MacOS va a utilizar para compilar la aplicación (este marco de trabajo se introdujo en OSX {2}, mientras que va a compilar con la {1} de MacOS SDK.) Esta configuración no es compatible con el registrador estático (pass--registrador: dinámica como un argumento de mmp adicionales en la opción de Mac de compilación del proyecto para seleccionar). O bien seleccione un SDK más reciente en las opciones de compilación de Mac de la aplicación.

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx: GCC y la cadena de herramientas

### <a name="mm51xx-compilation"></a>MM51xx: la compilación

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101: Falta el compilador '{0}'. Instale Xcode componente 'Herramientas de línea de comandos'.

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103: No se pudo compilar. Código de error: {0}. Registre en un informe de errores http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx: vinculación

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202: Mono.framework MDK es que faltan. Instale el MDK para su versión Mono.framework desde http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203: No se encuentra libxammac.a, probablemente debido a una instalación dañada de Xamarin.Mac. Vuelva a instalar Xamarin.Mac.

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x8664-is-only-supported-on-non-classic-profiles"></a>MM5204: Arquitectura no válido. x86_64 solo se admite en los perfiles no estándar.

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x8664-when---profilemobile"></a>MM5205: Arquitectura no válido '{0}'. Arquitecturas válidas son i386 y x86_64 (perfil cuando--= móvil).

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218: No se puede pasar por alto el símbolo dinámico {symbol} (--símbolo dinámicos omitir = {symbol}) porque no se detectó como símbolo de dinámico.

Consulte la [mtouch equivalente advertencia](~/ios/troubleshooting/mtouch-errors.md#MT5218).

<!-- 5206 used by mtouch -->
<!-- 5207 used by mtouch -->
<!-- 5208 used by mtouch -->
<!-- 5209 used by mtouch -->
<!-- 5210 used by mtouch -->
<!-- 5211 used by mtouch -->
<!-- 5212 used by mtouch -->
<!-- 5213 used by mtouch -->
<!-- 5214 used by mtouch -->
<!-- 5215 used by mtouch -->
<!-- 5216 used by mtouch -->
<!-- 5217 used by mtouch -->

### <a name="mm53xx-other-tools"></a>MM53xx: otras herramientas

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301: no se encontró el pkg-config. Instale el Mono.framework de http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305: Herramienta de 'otool' que falta. Instale Xcode componente 'Herramientas de línea de comandos'

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306: Dependencias que faltan. Instale Xcode componente 'Herramientas de línea de comandos'

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308: Contrato de licencia de Xcode puede no se han aceptado.  Por favor, inicie Xcode.

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1--check-build-log-for-details"></a>MM5309: Vinculación nativo error con código de error 1.  Compruebe el registro de compilación para obtener más información.

<a name="MM5310" />

#### <a name="mm5310-installnametool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310: no se pudo install_name_tool con un código de error '{0}'. Compruebe el registro de compilación para obtener más información.

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx: en tiempo de ejecución

### <a name="mm800x-misc"></a>MM800x: misc

<!-- 8000 used by mtouch -->
<!-- 8001 used by mtouch -->
<!-- 8002 used by mtouch -->
<!-- 8003 used by mtouch -->
<!-- 8004 used by mtouch -->
<!-- 8005 used by mtouch -->
<!-- 8006 used by mtouch -->
<!-- 8007 used by mtouch -->
<!-- 8008 used by mtouch -->
<!-- 8009 used by mtouch -->
<!-- 8010 used by mtouch -->
<!-- 8011 used by mtouch -->
<!-- 8012 used by mtouch -->
<!-- 8013 used by mtouch -->
<!-- 8014 used by mtouch -->
<!-- 8015 used by mtouch -->
<!-- 8016 used by mtouch -->

<a name="MM8017" />

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017: No se admite el recolector de elementos no utilizados Boehm. Use en su lugar SGen.

