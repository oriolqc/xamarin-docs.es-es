---
title: Recursos de Android
description: "Este artículo presenta el concepto de recursos Android en Xamarin.Android e indicarán cómo utilizarlas. Se describe cómo usar los recursos en la aplicación Android para admitir varios dispositivos, incluidos las densidades y diferentes tamaños de pantalla y localización de la aplicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: 6546870d85f7b77e60dff0cb9e6075f982c9cb8e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="android-resources"></a>Recursos de Android

_Este artículo presenta el concepto de recursos Android en Xamarin.Android e indicarán cómo utilizarlas. Se describe cómo usar los recursos en la aplicación Android para admitir varios dispositivos, incluidos las densidades y diferentes tamaños de pantalla y localización de la aplicación._


## <a name="overview"></a>Información general

Una aplicación Android rara vez es solo el código de origen. A menudo hay muchos otros archivos que componen una aplicación: vídeo, imágenes, fuentes y archivos de audio solo por nombrar algunos. Colectivamente, estos archivos de código fuente no se conocen como recursos y se compilan (junto con el código fuente) durante el proceso de compilación y empaquetados como un APK de distribución y la instalación en dispositivos:

![Diagrama de empaquetado](images/packaging-diagram.png)

Recursos ofrecen varias ventajas con respecto a una aplicación Android:

-  **Separación de código** &ndash; separa el código de origen de imágenes, cadenas, menús, animaciones, colores, etcetera. Por lo tanto los recursos pueden ayudar a considerablemente al localizar.

-  **Varios dispositivos de destino** &ndash; proporciona compatibilidad con más sencillo de las configuraciones de dispositivos diferentes sin cambios en el código.

-  **Comprobación de tiempo de compilación** &ndash; recursos son estáticos y compilado en la aplicación. Esto permite el uso de los recursos que se comprueban en tiempo de compilación, cuando resultará sencillo detectar y corregir los errores, en lugar de tiempo de ejecución cuando resulte más difícil de buscar y costosa corregir.

Cuando se inicia un nuevo proyecto de Xamarin.Android, se crea un directorio especial denominado recursos, junto con algunos subdirectorios:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Contenido y la carpeta de recursos](images/resources-folder-vs.png)

En la imagen anterior, los recursos de aplicación están organizados según su tipo en estos subdirectorios: imágenes se ubicarán en el **pueden dibujar** directorio; vistas ir en la **diseño** subdirectorio, etcetera.
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Contenido y la carpeta de recursos](images/resources-folder-xs.png)

En la imagen anterior, los recursos de aplicación están organizados según su tipo en estos subdirectorios: imágenes se ubicarán en el **asignación MIP** directorio; vistas ir en la **diseño** subdirectorio, etcetera.
 
-----

Hay dos maneras de obtener acceso a estos recursos en una aplicación Xamarin.Android: *mediante programación* en el código y *mediante declaración* en XML mediante una sintaxis XML especial.

Se llaman a estos recursos *recursos predeterminados* y se usan para todos los dispositivos a menos que se ha especificado una coincidencia más específica. Además, todos los tipos de recursos podrán tener opcionalmente *recursos alternativos* que Android puede usar como destino dispositivos específicos. Por ejemplo, se pueden proporcionar recursos para tener como destino la configuración regional del usuario, el tamaño de la pantalla, o si el dispositivo se gira 90 grados de vertical a horizontal, etcetera. En cada uno de estos casos, Android cargará los recursos para su uso por la aplicación sin ningún esfuerzo de codificación adicional por el desarrollador.

Los recursos alternativos se especifican mediante la adición de una cadena corta, llama a un *calificador*, hasta el final del directorio que contiene un tipo determinado de recursos.

Por ejemplo, **recursos, puede dibujar-Alemania** especificará las imágenes para dispositivos que se establecen en una configuración regional alemana, mientras que **recursos, puede dibujar-fr** contendría las imágenes de dispositivos se establece en la configuración regional en francés. Un ejemplo de cómo proporcionar recursos alternativos puede verse en la imagen siguiente donde se ejecuta la misma aplicación con la configuración regional de los cambios de dispositivo:

![Pantallas de ejemplo para distintas configuraciones regionales](images/localized-screenshots.png)

En este artículo se echar un vistazo integral de uso de recursos y tratar los siguientes temas:

-  **Conceptos básicos de recursos Android** &ndash; con los recursos predeterminados mediante programación o mediante declaración, agregar tipos de recursos como imágenes y fuentes a una aplicación.

-  **Configuraciones específicas de dispositivo** &ndash; admitir los diferentes resoluciones de pantalla y densidades en una aplicación.

-  **Localización** &ndash; uso de recursos para admitir las diferentes regiones se puede utilizar una aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Uso de recursos de Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Principios de la aplicación](http://developer.android.com/guide/topics/fundamentals.html)
- [Recursos de la aplicación](http://developer.android.com/guide/topics/resources/index.html)
- [Compatibilidad con varias pantallas](http://developer.android.com/guide/practices/screens_support.html)
