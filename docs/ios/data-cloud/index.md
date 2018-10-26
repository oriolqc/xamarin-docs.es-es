---
title: Datos y servicios en la nube en aplicaciones de Xamarin.iOS
description: Este documento incluye vínculos a guías que describen cómo trabajar con datos locales, iCloud y CloudKit en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 21a6c1c0c0ceb5596a056f0818dec39041808504
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117156"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Datos y servicios en la nube en aplicaciones de Xamarin.iOS

##  <a name="data-accessiosdata-clouddataindexmd"></a>[Acceso a datos](~/ios/data-cloud/data/index.md)

Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos pequeña, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos. iOS tiene el motor de base de datos de Sqlite "integrado" y acceso a los datos se simplifica mediante la plataforma de Xamarin que se incluye con el proveedor de datos de SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

La API de almacenamiento de iCloud en iOS 5 permite a las aplicaciones guardar documentos de usuario y datos específicos de la aplicación en una ubicación central y acceso a ellos desde todos los dispositivos del usuario.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

El marco de trabajo de CloudKit simplifica el desarrollo de aplicaciones que iCloud acceso. Esto incluye la recuperación de datos de la aplicación y derechos de activos así como ser capaz de almacenar información de la aplicación de forma segura. Este kit ofrece a los usuarios un nivel de anonimato permitiendo el acceso a las aplicaciones con su identificadores de iCloud sin compartir información personal.