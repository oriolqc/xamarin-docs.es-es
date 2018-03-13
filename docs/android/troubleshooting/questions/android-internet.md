---
title: "¿Por qué no se puede conectar la compilación de versión de Android a Internet?"
ms.topic: article
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: c37a417b84db4028ccea3848a6c152b8ff8d8ead
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>¿Por qué no se puede conectar la compilación de versión de Android a Internet?

## <a name="cause"></a>Motivo

La causa más común de este problema es que la **INTERNET** permiso se incluye automáticamente en una compilación de depuración, pero se debe establecer manualmente para una versión de lanzamiento. Esto es porque el permiso de Internet se usa para permitir que un depurador se asocie al proceso, como se describe en "DebugSymbols" [aquí](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Corregir

Para resolver el problema, puede requerir el permiso de Internet en el manifiesto de Android. Esto puede hacerse mediante el editor de manifiestos o sourcecode del manifiesto:

-   Corregir en el Editor: en el proyecto de Android, vaya a **Propiedades -> AndroidManifest.xml -> permisos necesarios** y compruebe **Internet**

-   Corregir en Sourcecode: abra el AndroidManifest en un editor de código fuente y agregue la etiqueta de permiso dentro de la `<Manifest>` etiquetas:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
