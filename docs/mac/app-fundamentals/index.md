---
title: Principios de la aplicación de Xamarin.Mac
description: Este documento incluye vínculos a guías que describen los distintos conceptos necesarios para comprender al desarrollar aplicaciones de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/17/2015
ms.openlocfilehash: e085cf33615d216e1ce9963254050ef2b623ae40
ms.sourcegitcommit: d0da5ce4158239abd2b36f67550e9b475055766a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2018
ms.locfileid: "39320809"
---
# <a name="xamarinmac-application-fundamentals"></a>Principios de la aplicación de Xamarin.Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Patrones y expresiones comunes](~/mac/app-fundamentals/patterns.md)

A lo largo de las API de Apple que se expone a través de C#, determinadas expresiones y patrones iniciará una y otra vez. Si tiene experiencia con la programación con Xamarin.iOS, estas pueden resultar familiares. Documentación a menudo hará referencia a estos patrones y expresiones varias veces, por lo que tener un conocimiento sólido de ellos le ayudarán a dar sentido a la documentación que encuentre.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Descripción de la API de Mac](~/mac/app-fundamentals/mac-apis.md)

Para gran parte de su tiempo a desarrollar con Xamarin.Mac, puede pensar, leer y escribir en C# sin preocuparse de las API de Objective-C subyacente. Sin embargo, a veces podrá debe leer la documentación de API de Apple, traducir una respuesta de Stack Overflow para una solución para su problema o comparar con un ejemplo existente.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[Aplicaciones de consola](~/mac/app-fundamentals/console.md)

También puede compilar aplicaciones de consola "sin periféricos" que tienen acceso a las API de macOS nativo con Xamarin.Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Trabajar con archivos .xib](~/mac/app-fundamentals/xib.md)

En este artículo se explica cómo trabajar con archivos .xib creados en Xcode Interface Builder para crear y mantener interfaces de usuario para una aplicación de Xamarin.Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.Storyboard/.xib menos el diseño de la interfaz de usuario](~/mac/app-fundamentals/xibless-ui.md)

En este artículo describe la creación de interfaz de usuario de la aplicación de Xamarin.Mac directamente desde el código de C# sin usar Interface Builder de Xcode con .storyboard o .xib archivos.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Trabajar con imágenes](~/mac/app-fundamentals/image.md)

En este artículo se explica cómo trabajar con imágenes e iconos en una aplicación de Xamarin.Mac. Describe la creación y mantener las imágenes necesarias para crear el icono de la aplicación y usar imágenes en el código de C# y en Interface Builder de Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Enlace de datos y la clave y valor de codificación](~/mac/app-fundamentals/databinding.md)

En este artículo se describe el uso de codificación y observación para permitir el enlace de datos a los elementos de interfaz de usuario de Interface Builder de Xcode de pares clave-valor de pares clave-valor. Con esta técnica, reducir en gran medida la cantidad de código C# que debe escribirse para su aplicación de Xamarin.Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Trabajar con bases de datos](~/mac/app-fundamentals/databases.md)

En este artículo se describe el uso de codificación y observación para permitir el enlace de datos con acceso directo a bases de datos de SQLite a elementos de interfaz de usuario en Interface Builder de Xcode de pares clave-valor de pares clave-valor. También se describe mediante el SQLite.NET ORM para proporcionar acceso a los datos de SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Trabajar con copiar y pegar](~/mac/app-fundamentals/copy-paste.md)

En este artículo se explica cómo trabajar con la mesa de trabajo para proporcionar copiar y pegar en una aplicación de Xamarin.Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados dentro de una aplicación ofrecen.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Espacio aislado de una aplicación de Xamarin.Mac](~/mac/app-fundamentals/sandboxing.md)

Este artículo trata el espacio aislado de una aplicación de Xamarin.Mac para la versión en la aplicación de Store. Abarca todos los elementos que se incluyen en el espacio aislado: directorios contenedores, los derechos, los permisos de usuario determinado, separación de privilegios y cumplimiento de kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Reproducción de sonidos con AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

En este artículo se muestra cómo usar una clase auxiliar para controlar la reproducción del sonido mediante un AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Informar de errores](~/mac/app-fundamentals/troubleshooting.md)

A veces todo un bloqueo mientras se trabaja en un proyecto, en la imposibilidad de obtener una API para trabajar como queremos o intentar solucionar un error. Nuestro objetivo en Xamarin es para que pueda tener éxito en la escritura de las aplicaciones de escritorio y móviles, y hemos proporcionado algunos recursos para ayudar a.
