---
title: Recursos de Android
description: Este artículo presenta el concepto de recursos de Android en Xamarin.Android y documentará cómo usarlas. Se describe cómo usar los recursos en la aplicación Android para admitir la localización de aplicaciones y varios dispositivos, incluidas las densidades y diferentes tamaños de pantalla.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: d9cd6bf3ae51c6e27be88481e412995bd4113c17
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117272"
---
# <a name="android-resources"></a>Recursos de Android

_Este artículo presenta el concepto de recursos de Android en Xamarin.Android y documentará cómo usarlas. Se describe cómo usar los recursos en la aplicación Android para admitir la localización de aplicaciones y varios dispositivos, incluidas las densidades y diferentes tamaños de pantalla._


## <a name="overview"></a>Información general

Una aplicación de Android rara vez es solo el código de origen. A menudo hay muchos otros archivos que componen una aplicación: vídeo, imágenes, fuentes y los archivos de audio solo por nombrar algunos. Colectivamente, estos archivos de código fuente no se conocen como recursos y se compilan (junto con el código fuente) durante el proceso de compilación y empaquetados como un APK para su distribución e instalación en dispositivos:

![Diagrama de empaquetado](images/packaging-diagram.png)

Recursos ofrecen varias ventajas a una aplicación de Android:

-  **Separación de código** &ndash; separa el código de origen de imágenes, cadenas, los menús, las animaciones, colores, etcetera. Por lo tanto los recursos pueden ayudar a considerablemente al localizar.

-  **Tener como destino varios dispositivos** &ndash; proporciona compatibilidad con más sencillo de distintas configuraciones de dispositivos sin cambios de código.

-  **Tiempo de compilación corriente** &ndash; recursos son estáticos y compilado en la aplicación. Esto permite el uso de los recursos que se comprueba en tiempo de compilación cuando será fácil de detectar y corregir los errores, en lugar de tiempo de ejecución cuando es más difícil encontrar y costosas de corregir.

Cuando se inicia un nuevo proyecto de Xamarin.Android, se crea un directorio especial denominado recursos, junto con algunos subdirectorios:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Contenido y la carpeta de recursos](images/resources-folder-vs.png)

En la imagen anterior, los recursos de la aplicación se organizan según su tipo en estos subdirectorios: las imágenes se ubicarán en el **drawable** directorio; vistas ir en el **diseño** subdirectorio, etcetera.
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Contenido y la carpeta de recursos](images/resources-folder-xs.png)

En la imagen anterior, los recursos de la aplicación se organizan según su tipo en estos subdirectorios: las imágenes se ubicarán en el **mipmap** directorio; vistas ir en el **diseño** subdirectorio, etcetera.
 
-----

Hay dos maneras de obtener acceso a estos recursos en una aplicación de Xamarin.Android: *mediante programación* en el código y *mediante declaración* en XML mediante una sintaxis especial de XML.

Estos recursos se denominan *recursos predeterminados* y se usan por todos los dispositivos, a menos que se ha especificado una coincidencia más específica. Además, todos los tipos de recursos pueden tener opcionalmente *recursos alternativos* que Android puede usar como destino dispositivos concretos. Por ejemplo, se pueden proporcionar recursos para tener como destino la configuración regional del usuario, el tamaño de pantalla, o si el dispositivo se gira 90 grados de vertical a horizontal, etcetera. En cada uno de estos casos, Android cargará los recursos para su uso por la aplicación sin ningún esfuerzo de codificación adicional por el desarrollador.

Recursos alternativos se especifican mediante la adición de una cadena corta, llama a un *calificador*, hasta el final del directorio que contiene un tipo determinado de recursos.

Por ejemplo, **recursos/drawable-de** especificará las imágenes para los dispositivos que se establecen en una configuración regional alemana, mientras que **recursos/drawable-fr** contendría las imágenes de dispositivos se establece en la configuración regional en francés. Un ejemplo de cómo proporcionar recursos alternativos se aprecia en la imagen siguiente donde se ejecuta la misma aplicación con la configuración regional de los cambios del dispositivo:

![Pantallas de ejemplo para distintas configuraciones regionales](images/localized-screenshots.png)

En este artículo tomará una visión integral de uso de recursos y abarcan los siguientes temas:

-  **Conceptos básicos de recursos Android** &ndash; con los recursos predeterminados mediante programación o declaración, agregar tipos de recursos como imágenes y las fuentes a una aplicación.

-  **Configuraciones específicas de dispositivo** &ndash; que admiten las diferentes resoluciones de pantalla y densidades en una aplicación.

-  **Localización** &ndash; con recursos para admitir las diferentes regiones se puede usar una aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Uso de recursos de Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Principios de la aplicación](http://developer.android.com/guide/topics/fundamentals.html)
- [Recursos de la aplicación](http://developer.android.com/guide/topics/resources/index.html)
- [Compatibilidad con varias pantallas](http://developer.android.com/guide/practices/screens_support.html)
