---
title: "Android.Support.v7.AppCompat - encontró ningún recurso que coincida con el nombre especificado: attr 'android: actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 07655587642c3e1aa94d035e76f6f6758340546d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774901"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - encontró ningún recurso que coincida con el nombre especificado: attr 'android: actionModeShareDrawable'

1. Asegúrese de que descargue los extras más recientes, así como el Android 5.0 (API de 21) mediante el Administrador de SDK de Android SDK.

2. Asegúrese de que se está compilando la aplicación con compileSdkVersion establece en 21. Se puede configurar opcionalmente la targetSdkVersion en 21 así.

3. Si necesita una versión anterior como de API 19, descargue la versión correspondiente que se encuentra en la página de Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Tenga en cuenta*: si instala manualmente esto a través de la consola de administrador de paquetes, asegúrese de instalar también la misma versión de Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Referencia de desbordamiento de pila: [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Vea también

- [¿Qué paquetes de Android SDK debo instalar?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

