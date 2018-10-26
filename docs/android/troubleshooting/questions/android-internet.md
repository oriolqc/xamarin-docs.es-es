---
title: ¿Por qué no se puede conectar mi compilación de versión de Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: cd27d5c884086cd0fade4364851039fd0cd915a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117917"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>¿Por qué no se puede conectar mi compilación de versión de Android a Internet?

## <a name="cause"></a>Motivo

La causa más común de este problema es que la **INTERNET** permiso se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una versión de lanzamiento. Esto es porque el permiso de Internet se usa para permitir que el depurador adjunte al proceso, según se describe en "DebugSymbols" [aquí](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Corregir

Para resolver el problema, puede requerir el permiso de Internet en el manifiesto de Android. Esto puede hacerse mediante el editor de manifiestos o sourcecode del manifiesto:

-   Corregir en el Editor: en el proyecto Android, vaya a **AndroidManifest.xml de -> Propiedades -> permisos necesarios** y compruebe **Internet**

-   Corrección de código fuente: abra el AndroidManifest en un editor de código fuente y agregue la etiqueta de permiso dentro de la `<Manifest>` etiquetas:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
