---
title: Localización de aplicaciones y recursos de cadena
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: d9d90e371199c8587d61199240523cf0a23f5efd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116529"
---
# <a name="application-localization-and-string-resources"></a>Localización de aplicaciones y recursos de cadena

Localización de aplicaciones es el acto de proporcionar recursos alternativos para una región específica o la configuración regional de destino. Por ejemplo, podría proporcionar cadenas traducidas para distintos países, o puede cambiar los colores ni el diseño para que coincida con referencias culturales determinadas. Android cargará y usar los recursos adecuados para la configuración regional del dispositivo en tiempo de ejecución sin realizar ningún cambio en el código fuente.

Por ejemplo, la imagen siguiente muestra la misma aplicación que se ejecuta en tres configuraciones regionales de otro dispositivo, pero es específico para la configuración regional que cada dispositivo se establece en el texto mostrado en cada botón:

[![Ejemplos de las tres distintas configuraciones regionales](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

En este ejemplo, el contenido de un archivo de diseño, **Main.axml** es algo parecido a esto:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

En el ejemplo anterior, la cadena para el botón se cargó desde los recursos al proporcionar el identificador de recurso para la cadena:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cadenas de recursos para tres idiomas](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cadenas de recursos para tres idiomas](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Localización de aplicaciones Android

Leer el [Introducción a la localización](~/cross-platform/app-fundamentals/localization.md) para sugerencias e instrucciones sobre la localización de aplicaciones móviles.

El [localizar aplicaciones Android](~/android/app-fundamentals/localization.md) guía contiene ejemplos más específicos sobre cómo traducir cadenas y localizar imágenes con Xamarin.Android.



## <a name="related-links"></a>Vínculos relacionados

- [Localización de aplicaciones Android](~/android/app-fundamentals/localization.md)
- [Información general de localización de multiplataforma](~/cross-platform/app-fundamentals/localization.md)
