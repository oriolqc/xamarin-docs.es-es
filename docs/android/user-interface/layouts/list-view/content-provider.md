---
title: Uso de ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122119"
---
# <a name="using-a-contentprovider"></a>Uso de ContentProvider

CursorAdapters también puede utilizarse para mostrar los datos de un ContentProvider.
ContentProviders permiten tener acceso a los datos expuestos por otras aplicaciones (incluidos los datos del sistema de Android como contactos, información de calendario y multimedia).

Es la mejor manera de obtener acceso a un ContentProvider con un CursorLoader mediante el LoaderManager. LoaderManager se introdujo en Android 3.0 (API nivel 11, Honeycomb) para mover las tareas bloqueos desactivado el subproceso principal, y utilizando un CursorLoader permite que los datos se cargue en un subproceso antes de que se enlaza a un ListView para mostrar.

Consulte [Introducción a ContentProviders](~/android/platform/content-providers/index.md) para obtener más información.

