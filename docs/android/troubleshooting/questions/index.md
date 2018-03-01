---
title: "Preguntas más frecuentes"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: d6d1a5f659317267859164181216177857a67fd2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="frequently-asked-questions"></a>Preguntas más frecuentes

## <a name="installation--setup"></a>Instalación y el programa de instalación

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[¿Qué paquetes de SDK de Android debo instalar?](install-android-sdk-packages.md)

Instalar el SDK de Android no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Mientras que necesitan varían para desarrolladores individuales, esta guía describe los paquetes que suele ser necesarios para el desarrollo con Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[¿Dónde puedo configurar mi ubicaciones de SDK de Android?](android-sdk-location.md)

Esta guía describe tanto la configuración predeterminada de Android SDK, que debería funcionar para la mayoría de las instalaciones; y cómo cambiar estos valores predeterminados en Visual Studio para Mac o en Visual Studio, si es necesario.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[¿Cómo se puede actualizar la versión del Kit de desarrollo de Java (JDK)?](update-jdk.md)

Este artículo explica cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9jdk9-errorsmd"></a>[¿Puedo usar Java Development Kit (JDK) versión 9?](jdk9-errors.md)

Xamarin.Android requiere JDK 8 en lugar de JDK 9. Este artículo enumeran algunos mensajes de error comunes que puede ver si está instalado JDK 9, así como instrucciones para comprobar la versión JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[¿Cómo instalar manualmente las bibliotecas de compatibilidad con Android requeridas por los paquetes de Xamarin.Android.Support?](install-android-support-library.md)

Esta guía proporcionan ejemplos paso a paso para instalar el `Xamarin.Android.Support.v4` biblioteca de compatibilidad de Windows & Mac.

### <a name="how-do-i-install-google-play-services-in-an-emulatorinstall-gpsmd"></a>[¿Cómo se puede instalar servicios de Google Play en un emulador?](install-gps.md)

Vínculos de esta guía para obtener información acerca de cómo instalar servicios de Google Play emulador de Android de Visual Studio.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[¿Qué controladores USB es necesario depurar Android en Windows?](android-drivers-debug-windows.md)

Para depurar en un dispositivo Android para el desarrollo en Windows; debe instalar a un controlador USB compatible. El Administrador de Android SDK incluye el "controlador USB de Google" de forma predeterminada, que agrega compatibilidad para dispositivos Nexus.
Otros dispositivos requieren controladores USB específicamente publicados por el fabricante del dispositivo. Esta guía proporciona información sobre cómo buscar a estos controladores alternativos, así como métodos de pruebas.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[¿Es posible conectarse a emuladores de Android ejecutando en un Mac de una máquina virtual de Windows?](connect-android-emulator-mac-windows.md)

Esta guía trata métodos al utilizar un emulador de Google Android.

## <a name="general-questions"></a>Preguntas generales

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[¿Cómo se automatiza un proyecto de prueba de NUnit Android?](automate-android-nunit-test.md)

Esta guía enumeran los pasos para configurar un proyecto de prueba NUnit Android, _no_ un proyecto Xamarin.UITest. Encontrará guías Xamarin.UITest [aquí](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[¿Cómo se habilita Intellisense en archivos .axml Android?](enable-axml-intellisense.md)

Esta guía describe cómo activar Intellisense de Visual Studio para Android .axml archivos.

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[¿Por qué no se puede conectar la compilación de versión de Android a Internet?](android-internet.md)

La causa más común de este problema es que la **INTERNET** permiso se incluye automáticamente en una compilación de depuración, pero se debe establecer manualmente para una versión de lanzamiento. Esta guía describe cómo habilitar el permiso en versiones de lanzamiento.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Más inteligente Xamarin para Android admite v4 / paquetes de NuGet v13](android-support-v4v13-libraries.md)

`Support-v4` y `Support-v13` no pueden usarse juntas en la misma aplicación, es decir, son mutuamente excluyentes. Esto es porque `Support-v13` realmente contiene todos los tipos y la implementación de `Support-v4`. Si intenta hacer referencia tanto en el mismo proyecto se producirán errores de tipo duplicado.


## <a name="deprecated"></a>En desuso

> [!NOTE]
> **Nota:** los artículos siguientes se aplican a los problemas que se han resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[¿Qué versión de Xamarin.Android agrega compatibilidad con círculo?](xa-lollipop.md)

Esta guía se escribió originalmente para la vista previa de L Android. Xamarin.Android 4.17 agrega compatibilidad con vista previa de Android L & Xamarin.Android 4.20 agregan compatibilidad con círculo Android.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - encontró ningún recurso que coincida con el nombre especificado: attr 'android: actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Este error puede producirse en versiones anteriores de Xamarin si faltan algunos de los pacakages de SDK de Android requiere.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Ajustar los parámetros de la memoria de Java para el Diseñador de Android](android-designer-java-memory.md)

Los parámetros de memoria predeterminados que se utilizan cuando se inicia el `java` procesar para el Diseñador de Android podría ser incompatible con algunas configuraciones del sistema. A partir de Xamarin Studio 5.7.2.7 y Xamarin para Visual Studio 3.9.344 esta configuración puede personalizarse según un por proyecto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[No se actualizará mi archivo Resource.designer.cs Android](resource-designer-wont-update.md)

Un error en Xamarin.Studio 5.1 previamente había dañado archivos .csproj eliminando parcial o totalmente el código xml en el archivo .csproj. Esto provocaría que importante partes de Android que el sistema (por ejemplo, para actualizar la Resource.designer.cs Android) de compilación genere un error. A partir de la 5.1.4 estable de la versión en el 15 de julio, este error se ha corregido; pero en muchos casos, el archivo de proyecto tiene que repararse manualmente, tal y como se describe en esta guía.



