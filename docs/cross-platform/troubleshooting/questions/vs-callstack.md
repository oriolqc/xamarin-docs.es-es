---
title: ¿Cómo se recopilan las pilas de llamadas actuales del proceso de Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: 6d9cabdb22425bdc057f6d52de5aa943537dd1ca
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864477"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>¿Cómo se recopilan las pilas de llamadas actuales del proceso de Visual Studio?

Cuando la interfaz gráfica de usuario se bloquea (se bloquea, se inmoviliza) en Visual Studio, una parte importante de la información de diagnóstico para recopilar es el conjunto de las pilas de llamadas de todos los subprocesos del proceso de Visual Studio. Para guardar esta información para una instancia bloqueada de Visual Studio, puede usar una segunda instancia de Visual Studio:

1. Inicie una segunda instancia (una nueva ventana) de Visual Studio.

2. Cierre las soluciones abiertas en la nueva instancia de Visual Studio.

3. Seleccione **Depurar > Asociar al proceso**.

   ![](vs-callstack-images/image1.png "Seleccione Depurar > asociar al proceso")

4. Seleccione la instancia bloqueada original de `devenv.exe` de la lista de **Procesos disponibles**.

5. Seleccione **Depurar > Interrumpir todo**.

   ![](vs-callstack-images/image2.png "Seleccione Depurar > interrumpir todo")

6. Seleccione **Depurar > Guardar volcado como**.

   ![](vs-callstack-images/image3.png "Seleccione Depurar > Guardar volcado como")

7. Cambio **Guardar como tipo** a **minivolcado (\*.dmp)** . Esto creará un archivo mucho más pequeño que **minivolcado con montón**, y el montón no suele ser relevante para el diagnóstico se inmoviliza.

   ![](vs-callstack-images/image4.png "Esto generará un archivo de minivolcado con montón mucho más pequeño, y el montón no suele ser relevante para el diagnóstico se inmoviliza")

8. Guarde el archivo de volcado de memoria. Si enviar el archivo en línea, puede comprimir para reducir el tamaño.
