---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparación de las tareas comunes
description: Este documento compara cómo realizar diversas tareas comunes en WPF y Xamarin.Forms. Examina botones, temporizadores, los tamaños de fuente, abra un URI y mostrar una hoja de acción.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780481"
---
# <a name="common-tasks-comparison"></a>Comparación de las tareas comunes

| Tarea | WPF | Xamarin.Forms |
|--- |--- |--- |
|Se muestra un mensaje en la pantalla con botones|`MessageBox`|`Page.DisplayAlert`|
|Crear un temporizador|Clase `DispatcherTimer`|`Device.StartTimer` método estático|
|Obtener un tamaño de fuente predeterminado|`SystemFonts` clase estática|`Device.GetNamedSize` método estático|
|Abra una URI/URL|`Process.Start`|`Device.OpenUri`|
|Mostrar la hoja de acción (lista de botones)|N/D|`Page.DisplayActionSheet`|
