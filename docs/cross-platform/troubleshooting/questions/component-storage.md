---
title: ¿Dónde se almacenan los componentes en mi equipo?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.openlocfilehash: a53045a6179a26b30d824976d11fd2769a84811e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>¿Dónde se almacenan los componentes en mi equipo?

Siempre que se instala un componente de Xamarin en un proyecto de aplicación, se coloca en dos lugares:

1. En una carpeta de componentes en el nivel raíz de la carpeta de soluciones. Si se quita el componente de todos los proyectos de la solución, obtener quitará desde esta carpeta también.

2. También se almacena una copia en las siguientes ubicaciones:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Por lo que para quitar completamente un componente de su equipo, elimínelo de las soluciones de proyectos y de la carpeta de caché anterior.
