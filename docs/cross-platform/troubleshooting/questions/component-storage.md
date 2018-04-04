---
title: ¿Dónde se almacenan los componentes en mi equipo?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: f0dad6e6219d373eaa9f8410aea7d96c81eceb6b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>¿Dónde se almacenan los componentes en mi equipo?

Siempre que se instala un componente de Xamarin en un proyecto de aplicación, se coloca en dos lugares:

1. En una carpeta de componentes en el nivel raíz de la carpeta de soluciones. Si se quita el componente de todos los proyectos de la solución, obtener quitará desde esta carpeta también.

2. También se almacena una copia en las siguientes ubicaciones:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Por lo que para quitar completamente un componente de su equipo, elimínelo de las soluciones de proyectos y de la carpeta de caché anterior.
