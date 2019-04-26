---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparación de las tareas comunes
description: Este documento compara cómo realizar varias tareas comunes en WPF y Xamarin.Forms. Examina los botones, los temporizadores, los tamaños de fuente, abrir un identificador URI y mostrar una hoja de acción.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269600"
---
# <a name="common-tasks-comparison"></a>Comparación de las tareas comunes

| Tarea | WPF | Xamarin.Forms |
|--- |--- |--- |
|Mostrar un mensaje en la pantalla con botones|`MessageBox`|`Page.DisplayAlert`|
|Crear un temporizador|Clase `DispatcherTimer`|`Device.StartTimer` método estático|
|Obtener un tamaño de fuente predeterminado|`SystemFonts` clase estática|`Device.GetNamedSize` método estático|
|Abra una URI o dirección URL|`Process.Start`|`Device.OpenUri`|
|Mostrar la hoja de acción (lista de botones)|N/D|`Page.DisplayActionSheet`|
