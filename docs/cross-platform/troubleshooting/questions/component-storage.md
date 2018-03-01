---
title: "¿Dónde se almacenan los componentes en mi equipo?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 9549363d7aeb5ff7a5cfa79eb38443aaa80b7019
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>¿Dónde se almacenan los componentes en mi equipo?

Siempre que se instala un componente de Xamarin en un proyecto de aplicación, se coloca en dos lugares:

1. En una carpeta de componentes en el nivel raíz de la carpeta de soluciones. Si se quita el componente de todos los proyectos de la solución, obtener quitará desde esta carpeta también.

2. También se almacena una copia en las siguientes ubicaciones:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Por lo que para quitar completamente un componente de su equipo, elimínelo de las soluciones de proyectos y de la carpeta de caché anterior.
