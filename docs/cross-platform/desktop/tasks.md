---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparación de las tareas comunes
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4d45ae61c5d7a7093d51c4440d11dd883c157a4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="common-tasks-comparison"></a>Comparación de las tareas comunes

| Tarea | WPF | Xamarin.Forms |
|--- |--- |--- |
|Se muestra un mensaje en la pantalla con botones|`MessageBox`|`Page.DisplayAlert`|
|Crear un temporizador|Clase `DispatcherTimer`|`Device.StartTimer` método estático|
|Obtener un tamaño de fuente predeterminado|`SystemFonts` clase estática|`Device.GetNamedSize` método estático|
|Abra una URI/URL|`Process.Start`|`Device.OpenUri`|
|Mostrar la hoja de acción (lista de botones)|N/D|`Page.DisplayActionSheet`|
