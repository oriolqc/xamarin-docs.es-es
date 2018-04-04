---
title: Aspectos básicos de la aplicación
description: Vínculos de este documento guías que describen los distintos conceptos necesarios para entender al desarrollar aplicaciones de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/17/2015
ms.openlocfilehash: 807cf0e16cafc00483cecfc578367bc110657672
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>Aspectos básicos de la aplicación

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Patrones y expresiones comunes](~/mac/app-fundamentals/patterns.md)

A lo largo de las API de Apple que se expone a través de C#, determinados patrones y giros de dar una y otra vez. Si tiene experiencia con la programación con Xamarin.iOS, estos pueden ser familiares. Documentación a menudo hará referencia a estos patrones y expresiones varias veces, por lo que tener un conocimiento sólido de ellos le ayudará a tener sentido de la documentación que encuentre.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Descripción de las API de Mac](~/mac/app-fundamentals/mac-apis.md)

Durante gran parte del tiempo Xamarin.Mac al desarrollo, puede considerar, lectura y escritura en C# sin preocuparse excesivamente con las API subyacentes de C de objetivo. Sin embargo, en ocasiones, la que necesita para leer la documentación de API de Apple, traducir una respuesta de desbordamiento de la pila a una solución para su problema o comparar con un ejemplo existente.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Trabajar con archivos de .xib](~/mac/app-fundamentals/xib.md)

Este artículo tratan trabajar con archivos de .xib creados en el generador de interfaz de Xcode para crear y mantener las interfaces de usuario para una aplicación Xamarin.Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.Storyboard/.xib menos diseño de la interfaz de usuario](~/mac/app-fundamentals/xibless-ui.md)

Este artículo trata la creación de interfaz de usuario de la aplicación Xamarin.Mac directamente desde el código C# sin utilizar el generador de interfaz de Xcode con .storyboard o .xib archivos.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Trabajar con imágenes](~/mac/app-fundamentals/image.md)

Este artículo explica cómo trabajar con imágenes e iconos en una aplicación Xamarin.Mac. Abarca la creación y mantenimiento de las imágenes necesarios para crear el icono de la aplicación y utilizar imágenes en código de C# y el generador de interfaz de Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Enlace de datos y el valor de clave de codificación](~/mac/app-fundamentals/databinding.md)

Este artículo incluye el uso de codificación y observar para permitir el enlace de datos a los elementos de interfaz de usuario en el generador de interfaz de Xcode de clave y valor de clave y valor. Con esta técnica, reducir considerablemente la cantidad de código C# que es necesario que se escribirá para la aplicación Xamarin.Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Trabajar con bases de datos](~/mac/app-fundamentals/databases.md)

Este artículo incluye el uso de codificación y observar para permitir el enlace de datos con acceso directo a las bases de datos de SQLite a elementos de interfaz de usuario en el generador de interfaz de Xcode de clave y valor de clave y valor. También incluye el uso de las ORM SQLite.NET para proporcionar acceso a datos de SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Trabajar con copiar y pegar](~/mac/app-fundamentals/copy-paste.md)

Este artículo explica cómo trabajar con la mesa de trabajo para proporcionar copiar y pegar en una aplicación Xamarin.Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados dentro de una aplicación determinada.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Espacio aislado de una aplicación Xamarin.Mac](~/mac/app-fundamentals/sandboxing.md)

Este artículo trata el espacio aislado de una aplicación Xamarin.Mac para lanzamiento de la tienda de aplicaciones. Abarca todos los elementos que van en espacio aislado: directorios de contenedor, derechos, permisos de usuario determinado, separación de privilegios y cumplimiento de kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Reproducir sonido con AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

Este artículo muestra cómo utilizar una clase auxiliar para controlar la reproducción del sonido mediante un AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Informes errores](~/mac/app-fundamentals/troubleshooting.md)

A veces todos hacen bloqueados mientras se trabaja en un proyecto, en la imposibilidad de obtener una API para que funcione como queremos o intentar solucionar un error. Nuestro objetivo en Xamarin es para que sea correcta para escribir las aplicaciones de escritorio y móviles y le ofrecemos algunos recursos para ayudar a.
