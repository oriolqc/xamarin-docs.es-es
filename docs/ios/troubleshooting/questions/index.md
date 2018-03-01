---
title: "Preguntas más frecuentes"
ms.topic: article
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 1477d991fadfe381032e4009fa0bf0faf0518b8e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="frequently-asked-questions"></a>Preguntas más frecuentes

## <a name="general-questions"></a>Preguntas generales

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[¿Puedo usar una máquina virtual Mac con Xamarin?](mac-vm.md)
Sí, pero solo en hardware de Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[¿Cómo puedo cambiar Xcode?](downgrade-xcode.md)
Esta guía proporcionan vínculos para obtener acceso a versiones anteriores de Xcode, así como la versión más reciente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[¿Dónde puedo configurar mi ubicaciones de SDK de iOS?](ios-sdk.md)
Para la mayoría de los usuarios se establecen en las ubicaciones correctas automáticamente. Esta guía enumera las ubicaciones predeterminadas de SDK y cómo cambiarlas si es necesario.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[¿Cómo puedo habilitar opciones del desarrollador después de actualizar iOS?](update-developer-options.md)
Un error de iOS puede hacer que las opciones del desarrollador que desaparezcan tras la actualización de versiones de iOS, esto se ha observado cuando se cambia a iOS 8.x. Esta guía describe cómo se pueden volver a habilitar las opciones.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[Ubicación de usuario no funciona en iOS 8](ios8-user-location.md)
Esta guía explica cómo editar info.plist para habilitar la ubicación del usuario en iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[¿Dónde puedo encontrar el archivo de .dSYM a symbolicate registros de bloqueo de iOS?](symbolicate-ios-crash.md)
Esta guía describe los pasos básicos para symbolicating registros de bloqueo de iOS para ayudar a diagnosticar los bloqueos. También incluye vínculos a recursos adicionales para más avanzados técnicas symbolication & información acerca de cómo interpretar los registros de bloqueo de iOS.


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[¿Cómo se puede establecer variables de entorno en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?](xs-mono-runtime.md)
Si tiene que establecer variables de entorno de cualquier runtime para Mono, se pueden establecer el **Project Options > Ejecutar > General** página.

## <a name="publishing-questions"></a>Preguntas de publicación

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Error al enviar a la tienda de aplicaciones: "Agrupación no válido - opciones no permitidas para incrustar en bitcode se detectan en el envío"](invalid-bundle-bitcode.md)

Enviar aplicaciones que _requieren_ bitcode, como aplicaciones de watchOS y tvOS, debe realizarse con Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[¿Puedo cambiar la ruta de acceso de salida del archivo IPA?](ipa-output-path.md)
A partir de Xamarin ciclo 7, puede usar los destinos de MSBuild personalizados para lograr esto.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[¿Cómo puedo copiar los archivos de salida de IPA a la carpeta de entrega TFS?](ipa-tfs.md)
Sí, esta guía se describe cómo.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[¿Puedo agregar archivos a o quitar archivos de un archivo IPA después de compilar en Visual Studio?](modify-ipa.md)
Sí, es posible, pero normalmente habrá que volver a firmar el `.app` paquete después de realizar el cambio. Tenga en cuenta que la modificación del `.ipa` archivo no es necesario en el uso normal. En este artículo se proporciona exclusivamente con fines informativos.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[¿Es posible crear un archivo .xcarchive desde Visual Studio?](create-xcarchive.md)
A partir de Xamarin 4, es posible crear un `.xcarchive` desde Windows estableciendo la `ArchiveOnBuild` propiedad `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[¿Por qué se realiza el envío de mi aplicación por error con: "No permite rutas de acceso ("iTunesMetadata.plist") se encuentra en..."?](itunesmetadata-disallowed-paths.md)
Este error es el resultado de un cambio en el proceso de comprobación de App Store de Apple. Este error específico es _no_ relacionado con la versión concreta de Xamarin que se ha instalado, por lo que degradar le _no_ ayuda. Vínculos de esta guía para obtener más información sobre cómo corregir el problema.


## <a name="diagnosing-specific-error-messages"></a>Diagnosticar mensajes de Error específicos

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Error del diseñador con RegisterServicePort de iOS](error-registerserviceport.md)
Errores con `RegisterServicePort` y mensajes de error similares al igual que anteriormente suelen ser un problema con spyware o software malintencionado en el equipo. Esta guía se detalla confirmar el diagnóstico y la información acerca de cómo quitar el spyware o software malintencionado.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[¿Por qué se realiza la compilación de iOS por error con: ningún código de iPhone válido claves de firma que se encuentra en las llaves?](no-codesigning-keys.md)
Este mensaje de error se produce cuando se examina el proyecto en cuestión unas credenciales válidas de firma de código, pero no puede encontrarlos. Firma de código es necesaria para las pruebas y las implementaciones en los dispositivos de E/s físicas; así como aplicaciones y actualizaciones Ad hoc almacenan compilaciones.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[¿Por qué se realiza la aplicación de iOS 9 por error con: System.Exception: no se pudo serializar el objeto Objective-C?](exception-marshal-obj-c.md)
Cambios en la API de iOS 9 requieren que se use un constructor de devolución de llamada al llamar a código no administrado, como la API subyacente ahora lo espere.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Error en tiempo de ejecución: ensamblado mscorlib.dll no se encontró o no se pudo cargar](error-mscorlib-not-found.md)
Este problema se produce cuando el *oculto* `.monotouch-32` y `.monotouch-64` faltan carpetas en el `.xcarchive` para firmar / creación de IPA, desencadena el error en tiempo de ejecución.

## <a name="deprecated"></a>En desuso

> [!IMPORTANT]
> Los artículos siguientes se aplican a los problemas que se han resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[Archivo IPA es de 0 bytes](ipa-zero-bytes.md)
Hay algunos problemas conocidos en versiones anteriores de Xamarin que podría provocar que el archivo IPA en ventanas de 0 bytes.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[IBTool Error: No se pudo completar la operación.](error-ibtool.md)
Apple [fijo](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) esto `ibtool` error en Xcode 6.1.1, por lo que actualiza a Xcode 6.1.1 o versiones posteriores es la solución más sencilla.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[MT1009 de error: No se pudo copiar el ensamblado](error-mt1009.md)
Esto afecta a los usuarios que ejecutan Xamarin.iOS 7.2.6. Este problema es debido a permisos de archivo que necesiten privilegios más elevados cuando se instala Xamarin.iOS con otra cuenta de usuario, a continuación, cuenta principal del desarrollador.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[Devuelve System.Exception AMDeviceNotificationSubscribe...](exception-amddevicenotificationsubscribe.md)
Este mensaje puede aparecer en un cuadro de diálogo de error al iniciar por primera vez Visual Studio para Mac o en el `mtbserver.log` archivo. Tenga en cuenta que se trata de un problema habitual. Si Visual Studio está teniendo problemas para conectarse al host de compilación de Mac, hay otros errores que están más probables que aparezcan en el `mtbserver.log` archivo.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe no se encuentra rver. BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Este error puede aparecer en el `Mac Server Log` en Visual Studio.
