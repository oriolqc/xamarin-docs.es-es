---
title: Uso de un ContentProvider
ms.topic: article
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 5c90b140719e0dafb36dad1da75f52ba27e75e25
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="using-a-contentprovider"></a>Uso de un ContentProvider

CursorAdapters también puede utilizarse para mostrar los datos de un ContentProvider.
ContentProviders permiten tener acceso a los datos expuestos por otras aplicaciones (incluidos los datos de sistema Android como contactos, información multimedia y de calendario).

Es la mejor manera de tener acceso a un ContentProvider con un CursorLoader mediante el LoaderManager. LoaderManager se introdujo en Android 3.0 (API nivel 11, panal) para mover las tareas de bloqueo desactivado el subproceso principal, y usando un CursorLoader permite que los datos que se va a cargar en un subproceso antes de que se enlaza a un control ListView para su presentación.

Hacer referencia a [Introducción a ContentProviders](~/android/platform/content-providers/index.md) para obtener más información.

