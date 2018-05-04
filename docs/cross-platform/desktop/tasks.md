---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparación de las tareas comunes
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 84b3edc1a8cbc9ad642d94b457321786fae5fe70
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="common-tasks-comparison"></a>Comparación de las tareas comunes

| Tarea | WPF | Xamarin.Forms |
|--- |--- |--- |
|Se muestra un mensaje en la pantalla con botones|`MessageBox`|`Page.DisplayAlert`|
|Crear un temporizador|Clase `DispatcherTimer`|`Device.StartTimer` método estático|
|Obtener un tamaño de fuente predeterminado|`SystemFonts` clase estática|`Device.GetNamedSize` método estático|
|Abra una URI/URL|`Process.Start`|`Device.OpenUri`|
|Mostrar la hoja de acción (lista de botones)|N/D|`Page.DisplayActionSheet`|
