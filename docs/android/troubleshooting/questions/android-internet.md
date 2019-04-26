---
title: ¿Por qué no se puede conectar la compilación de versión de Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: cd27d5c884086cd0fade4364851039fd0cd915a0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945466"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>¿Por qué no se puede conectar la compilación de versión de Android a Internet?

## <a name="cause"></a>Motivo

La causa más común de este problema es que la **INTERNET** permiso se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una versión de lanzamiento. Esto es porque el permiso de Internet se usa para permitir que el depurador adjunte al proceso, según se describe en "DebugSymbols" [aquí](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Solución

Para resolver el problema, puede requerir el permiso de Internet en el manifiesto de Android. Esto puede hacerse mediante el editor de manifiestos o sourcecode del manifiesto:

-   Corrección en el Editor: En el proyecto Android, vaya a **AndroidManifest.xml de -> Propiedades -> permisos necesarios** y comprobar **Internet**

-   Corrección de código fuente: Abra el AndroidManifest en un editor de código fuente y agregue la etiqueta de permiso dentro de la `<Manifest>` etiquetas:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
