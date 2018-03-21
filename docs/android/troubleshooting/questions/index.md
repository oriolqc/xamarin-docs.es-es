---
title: "Preguntas más frecuentes"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/19/2018
ms.openlocfilehash: 69a5c905367f9e6dbd38acb664ad21b9dbe63efc
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2018
---
# <a name="frequently-asked-questions"></a>Preguntas más frecuentes

## <a name="installation--setup"></a>Instalación y el programa de instalación

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[¿Qué paquetes de Android SDK debo instalar?](install-android-sdk-packages.md)

Instalar el SDK de Android no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Mientras que necesitan varían para desarrolladores individuales, esta guía describe los paquetes que suele ser necesarios para el desarrollo con Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[¿Dónde puedo configurar mi ubicaciones de Android SDK?](android-sdk-location.md)

Esta guía describe tanto la configuración predeterminada de Android SDK, que debería funcionar para la mayoría de las instalaciones; y cómo cambiar estos valores predeterminados en Visual Studio para Mac o en Visual Studio, si es necesario.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?](update-jdk.md)

Este artículo explica cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9jdk9-errorsmd"></a>[¿Cómo se puede usar la versión 9 del kit de desarrollo de Java (JDK)?](jdk9-errors.md)

Xamarin.Android requiere JDK 8 en lugar de JDK 9. Este artículo enumeran algunos mensajes de error comunes que puede ver si está instalado JDK 9, así como instrucciones para comprobar la versión JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?](install-android-support-library.md)

Esta guía proporcionan ejemplos paso a paso para instalar el `Xamarin.Android.Support.v4` biblioteca de compatibilidad de Windows & Mac.

### <a name="how-do-i-install-google-play-services-in-an-emulatorinstall-gpsmd"></a>[¿Cómo se puede instalar Google Play Services en un emulador?](install-gps.md)

Vínculos de esta guía para obtener información acerca de cómo instalar servicios de Google Play emulador de Android de Visual Studio.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[¿Qué controladores USB son necesario para depurar Android en Windows?](android-drivers-debug-windows.md)

Para depurar en un dispositivo Android para el desarrollo en Windows; debe instalar a un controlador USB compatible. El Administrador de Android SDK incluye el "controlador USB de Google" de forma predeterminada, que agrega compatibilidad para dispositivos Nexus.
Otros dispositivos requieren controladores USB específicamente publicados por el fabricante del dispositivo. Esta guía proporciona información sobre cómo buscar a estos controladores alternativos, así como métodos de pruebas.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?](connect-android-emulator-mac-windows.md)

Esta guía trata métodos al utilizar un emulador de Google Android.

## <a name="general-questions"></a>Preguntas generales

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[¿Cómo se automatiza un proyecto de prueba de NUnit de Android?](automate-android-nunit-test.md)

Esta guía enumeran los pasos para configurar un proyecto de prueba NUnit Android, _no_ un proyecto Xamarin.UITest. Encontrará guías Xamarin.UITest [aquí](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[¿Cómo se habilita Intellisense en archivos .axml de Android?](enable-axml-intellisense.md)

Esta guía describe cómo activar Intellisense de Visual Studio para Android .axml archivos.

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[¿Por qué no se puede conectar la compilación de versión de Android a Internet?](android-internet.md)

La causa más común de este problema es que la **INTERNET** permiso se incluye automáticamente en una compilación de depuración, pero se debe establecer manualmente para una versión de lanzamiento. Esta guía describe cómo habilitar el permiso en versiones de lanzamiento.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Paquetes NuGet más inteligentes de Xamarin Android Support v4/v13](android-support-v4v13-libraries.md)

`Support-v4` y `Support-v13` no pueden usarse juntas en la misma aplicación, es decir, son mutuamente excluyentes. Esto es porque `Support-v13` realmente contiene todos los tipos y la implementación de `Support-v4`. Si intenta hacer referencia tanto en el mismo proyecto se producirán errores de tipo duplicado.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[¿Cómo se puede resolver un PathTooLongException Error?](path-too-long-exception.md)

Este artículo explica cómo resolver un **PathTooLongException** error que puede producirse durante la creación de un proyecto de Xamarin.Android.



## <a name="deprecated"></a>En desuso

> [!NOTE]
> Los artículos siguientes se aplican a los problemas que se han resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?](xa-lollipop.md)

Esta guía se escribió originalmente para la vista previa de L Android. Xamarin.Android 4.17 agrega compatibilidad con vista previa de Android L & Xamarin.Android 4.20 agregan compatibilidad con círculo Android.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat: no se encontró ningún recurso que coincida con el nombre especificado: attr 'android: actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Este error puede producirse en versiones anteriores de Xamarin si faltan algunos de los pacakages de SDK de Android requiere.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Ajuste de los parámetros de memoria de Java para Android Designer](android-designer-java-memory.md)

Los parámetros de memoria predeterminados que se utilizan cuando se inicia el `java` procesar para el Diseñador de Android podría ser incompatible con algunas configuraciones del sistema. A partir de Xamarin Studio 5.7.2.7 y Xamarin para Visual Studio 3.9.344 esta configuración puede personalizarse según un por proyecto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Mi archivo Resource.designer.cs de Android no se actualizará](resource-designer-wont-update.md)

Un error en Xamarin.Studio 5.1 previamente había dañado archivos .csproj eliminando parcial o totalmente el código xml en el archivo .csproj. Esto provocaría que importante partes de Android que el sistema (por ejemplo, para actualizar la Resource.designer.cs Android) de compilación genere un error. A partir de la 5.1.4 estable de la versión en el 15 de julio, este error se ha corregido; pero en muchos casos, el archivo de proyecto tiene que repararse manualmente, tal y como se describe en esta guía.



