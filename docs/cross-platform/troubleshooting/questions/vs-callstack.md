---
title: ¿Cómo se puede recopilar las pilas de llamadas actual del proceso de Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: e81c28f0610a0df2e4fe06349685ef5e0744071a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919766"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>¿Cómo se puede recopilar las pilas de llamadas actual del proceso de Visual Studio?

Cuando la interfaz gráfica de usuario se bloquea (deja de responder, se bloquea) en Visual Studio, una parte importante de información de diagnóstico para recopilar es el conjunto de pilas de llamadas de todos los subprocesos del proceso de Visual Studio. Para guardar esta información para una instancia de Visual Studio, puede usar una segunda instancia de Visual Studio:

1. Inicie una segunda instancia (una nueva ventana) de Visual Studio.

2. Cierre las soluciones que estén abiertas en la nueva instancia de Visual Studio.

3. Seleccione **Depurar > Asociar al proceso**.

  ![](vs-callstack-images/image1.png "Seleccione Depurar > asociar al proceso")

4. Seleccione la instancia bloqueada original de `devenv.exe` de la lista de **Procesos disponibles**.

5. Seleccione **Depurar > Interrumpir todo**.

  ![](vs-callstack-images/image2.png "Seleccione Depurar > interrumpir todos")

6. Seleccione **Depurar > Guardar volcado como**.

  ![](vs-callstack-images/image3.png "Seleccione Depurar > Guardar volcado como")

7. Cambio **Guardar como tipo** a **minivolcado (\*.dmp)**. Esto creará un archivo mucho más pequeño que **minivolcado con montón**, y el montón normalmente no es relevante para diagnosticar inmovilizaciones.

  ![](vs-callstack-images/image4.png "Esto creará un archivo de minivolcado con montón mucho menor y el montón normalmente no es relevante para diagnosticar inmovilizaciones")

8. Guarde el archivo de volcado de memoria. Si envía el archivo en línea, puede comprimir para reducir el tamaño.
