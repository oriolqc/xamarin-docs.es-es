---
title: Preguntas más frecuentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: ad3fc32245880f6603c63d33aac49309fd61b753
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "36935469"
---
# <a name="frequently-asked-questions"></a>Preguntas más frecuentes

## <a name="installation--setup"></a>Instalación y configuración

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[¿Qué paquetes de Android SDK debo instalar?](install-android-sdk-packages.md)

Instalar el SDK de Android no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Aunque necesitan varían para desarrolladores individuales, esta guía describe los paquetes que suele ser necesarios para el desarrollo con Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[¿Dónde puedo configurar mi ubicaciones de Android SDK?](android-sdk-location.md)

Esta guía describe la configuración de predeterminada del SDK de Android, que debería funcionar para la mayoría de las instalaciones; y cómo cambiar estos valores predeterminados en Visual Studio para Mac o Visual Studio si es necesario.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?](update-jdk.md)

En este artículo muestra cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[¿Puedo usar Java Development Kit (JDK) versión 9 o posterior?](jdk9-errors.md)

Xamarin.Android requiere JDK 8 o Microsoft Mobile OpenJDK. En este artículo se enumera algunos mensajes de error comunes que puede ver si está instalado JDK 9 o posterior, así como instrucciones para comprobar la versión JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?](install-android-support-library.md)

Esta guía proporciona pasos de ejemplo para instalar el `Xamarin.Android.Support.v4` biblioteca de soporte técnico de Windows y Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[¿Qué controladores USB son necesario para depurar Android en Windows?](android-drivers-debug-windows.md)

Para depurar en un dispositivo Android al desarrollar en Windows; deberá instalar a un controlador USB compatible. Android SDK Manager incluye "Controlador USB de Google" de forma predeterminada, lo que agrega compatibilidad para dispositivos Nexus.
Otros dispositivos requieren controladores USB publicados por el fabricante del dispositivo. Esta guía proporciona información sobre cómo buscar a estos controladores alternativos, así como métodos de prueba.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?](connect-android-emulator-mac-windows.md)

Esta guía abarca métodos cuando se usa el emulador de Android.

## <a name="general-questions"></a>Preguntas generales

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[¿Cómo se automatiza un proyecto de prueba de NUnit de Android?](automate-android-nunit-test.md)

Esta guía explica cómo configurar un proyecto de prueba NUnit de Android, _no_ un proyecto de Xamarin.UITest. Encontrará guías Xamarin.UITest [aquí](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[¿Cómo se habilita Intellisense en archivos .axml de Android?](enable-axml-intellisense.md)

Esta guía describe cómo activar Intellisense de Visual Studio para archivos .axml de Android.

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[¿Por qué no se puede conectar la compilación de versión de Android a Internet?](android-internet.md)

La causa más común de este problema es que la **INTERNET** permiso se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una versión de lanzamiento. Esta guía describe cómo habilitar el permiso en las compilaciones de versión.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13](android-support-v4v13-libraries.md)

`Support-v4` y `Support-v13` no pueden usarse juntos en la misma aplicación, es decir, son mutuamente excluyentes. Esto es porque `Support-v13` realmente contiene todos los tipos y la implementación de `Support-v4`. Si intenta hacer referencia tanto en el mismo proyecto, se producirán errores de tipo duplicado.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[¿Cómo se puede resolver un PathTooLongException Error?](path-too-long-exception.md)

En este artículo se explica cómo resolver un **PathTooLongException** error que puede producirse durante la compilación de un proyecto de Xamarin.Android.



## <a name="deprecated"></a>En desuso

> [!NOTE]
> Los artículos siguientes se aplican a los problemas que se han resuelto en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?](xa-lollipop.md)

Esta guía se escribió originalmente para la versión preliminar de L Android. 4.17 Xamarin.Android agrega compatibilidad con Android versión preliminar de L & 4.20 Xamarin.Android agregan compatibilidad de Android Lollipop.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat: no se encontró ningún recurso que coincida con el nombre especificado: attr 'android: actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Este error puede producirse en versiones anteriores de Xamarin si faltan algunos de los paquetes de Android SDK necesarios.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Ajuste de los parámetros de memoria de Java para Android Designer](android-designer-java-memory.md)

Los parámetros de memoria predeterminados que se usan al iniciar el `java` procesar para el Diseñador de Android podría ser incompatible con algunas configuraciones del sistema. A partir de Xamarin Studio 5.7.2.7 y Xamarin para Visual Studio 3.9.344 estas opciones se puede personalizar en cada proyecto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Mi archivo Resource.designer.cs de Android no se actualizará](resource-designer-wont-update.md)

Un error en la versión 5.1 Xamarin.Studio había dañado previamente archivos .csproj eliminando parcial o totalmente el código xml en el archivo .csproj. Esto provocaría que importantes del sistema (como actualizar el Resource.designer.cs de Android) de la compilación de partes de Android para producir un error. A partir de la 5.1.4 estable de la versión del 15 de julio de, este error se ha corregido; pero en muchos casos, el archivo de proyecto tiene que reparar manualmente, tal como se describe en esta guía.



