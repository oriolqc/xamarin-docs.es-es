---
title: Atributo Debuggable
ms.topic: article
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 65037029d01d499421fd825f72347ae1bebd9966
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="debuggable-attribute"></a>Atributo Debuggable



Para que la depuración sea posible, Android admite el protocolo JDWP (Java Debug Wire Protocol). Se trata de una tecnología que permite que herramientas como ADB se comuniquen con una JVM. Aunque JDWP es importante durante el desarrollo, se debe deshabilitar antes de la publicación.

JDWP puede ser el valor del atributo `android:debuggable` en una aplicación Android. Xamarin.Android proporciona los siguientes métodos para establecer este atributo:

1.  Se crea un archivo `AndroidManifext.xml` y se establece ahí el atributo `android:debuggable`.
1.  Se incluye `ApplicationAttribute` en un archivo `.CS`, así: `[assembly: Application(Debuggable=false)]`.


Si `AndroidManifest.xml` y `ApplicationAttribute` existen, el contenido de `AndroidManifest.xml` tiene prioridad sobre lo que se especifica mediante `ApplicationAttribute`.

Si no existe `AndroidManifest.xml` ni `ApplicationAttribute`, el valor predeterminado del atributo `android:debuggable` depende de si se generan o no símbolos de depuración. Si existen símbolos de depuración, Xamarin.Android establece el atributo `android:debuggable` en `true`.

Tenga en cuenta que el valor del atributo `android:debuggable` no depende necesariamente de la configuración de compilación. Puede que las compilaciones de lanzamiento tengan el atributo `android:debuggable` establecido en true.


## <a name="related-links"></a>Vínculos relacionados

- [Debuggable apps in the Android market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/) (Aplicaciones que se pueden depurar en Android Market)
