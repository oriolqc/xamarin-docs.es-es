---
title: "Localización de aplicaciones y recursos de cadena"
ms.topic: article
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: a8d25d8780a62e54780d7aa03d81f89fa0f668a4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="application-localization-and-string-resources"></a>Localización de aplicaciones y recursos de cadena

Localización de la aplicación es el acto de proporcionar recursos alternativos para una región específica o la configuración regional de destino. Por ejemplo, podría proporcionar cadenas de idioma localizado en diversos países, o puede cambiar los colores ni el diseño para que coincida con determinada según la referencia cultural. Android se cargarán y usar los recursos adecuados para la configuración regional del dispositivo en tiempo de ejecución sin realizar ningún cambio en el código fuente.

Por ejemplo, la imagen siguiente muestra la misma aplicación en ejecución en tres configuraciones regionales de otro dispositivo, pero el texto mostrado en cada botón es específico de la configuración regional que cada dispositivo se establece en:

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

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cadenas de recursos para tres idiomas](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Cadenas de recursos para tres idiomas](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Localizar aplicaciones Android

Leer la [Introducción a la localización](~/cross-platform/app-fundamentals/localization.md) para sugerencias e instrucciones sobre la localización de aplicaciones móviles.

El [localizar aplicaciones Android](~/android/app-fundamentals/localization.md) guía incluyen ejemplos más específicos sobre cómo traducir cadenas y localizar imágenes con Xamarin.Android.



## <a name="related-links"></a>Vínculos relacionados

- [Localizar aplicaciones Android](~/android/app-fundamentals/localization.md)
- [Información general de localización entre plataformas](~/cross-platform/app-fundamentals/localization.md)
