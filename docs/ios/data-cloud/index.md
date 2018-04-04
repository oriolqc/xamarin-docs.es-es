---
title: Datos y servicios en la nube
description: Guías de estabilización e implementación
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 81aebe5fd7431e578b75c5b61e1d2c92ce546909
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="data-and-cloud-services"></a>Datos y servicios en la nube


##  <a name="data-accessiosdata-clouddataindexmd"></a>[Acceso a datos](~/ios/data-cloud/data/index.md)

Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos es pequeña trivial, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos. iOS tiene el motor de base de datos de Sqlite "integrado" y acceso a los datos se simplifica con la plataforma de Xamarin que se incluye con el proveedor de datos de SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

La API de almacenamiento de iCloud en iOS 5 permite a las aplicaciones guardar documentos de usuario y datos específicos de la aplicación a una ubicación central y tener acceso a los elementos de todos los dispositivos del usuario.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

El marco de trabajo CloudKit simplifica el desarrollo de aplicaciones que iCloud de acceso. Esto incluye la recuperación de datos de la aplicación y activos derechos así como la posibilidad de almacenar información de la aplicación de forma segura. Este kit ofrece a los usuarios un nivel de anonimato permitiendo el acceso a las aplicaciones con su identificadores de iCloud sin compartir información personal.