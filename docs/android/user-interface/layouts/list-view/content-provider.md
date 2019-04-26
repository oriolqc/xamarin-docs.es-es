---
title: Uso de ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189742"
---
# <a name="using-a-contentprovider"></a>Uso de ContentProvider

CursorAdapters también puede utilizarse para mostrar los datos de un ContentProvider.
ContentProviders permiten tener acceso a los datos expuestos por otras aplicaciones (incluidos los datos del sistema de Android como contactos, información de calendario y multimedia).

Es la mejor manera de obtener acceso a un ContentProvider con un CursorLoader mediante el LoaderManager. LoaderManager se introdujo en Android 3.0 (API nivel 11, Honeycomb) para mover las tareas bloqueos desactivado el subproceso principal, y utilizando un CursorLoader permite que los datos se cargue en un subproceso antes de que se enlaza a un ListView para mostrar.

Consulte [Introducción a ContentProviders](~/android/platform/content-providers/index.md) para obtener más información.

