---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047853"
---
## <a name="firewall-configuration"></a>Configuración del Firewall

En el orden de las pruebas que se envíen a Xamarin Test Cloud, el equipo de envío de las pruebas debe ser capaz de comunicarse con los servidores de prueba en la nube. Los servidores de seguridad deben configurarse para permitir el tráfico de red hacia y desde los servidores ubicados en **testcloud.xamarin.com** en los puertos 80 y 443. Este punto de conexión se administra mediante DNS y la dirección IP está sujeta a cambios. 

En algunas situaciones, una prueba (o un dispositivo que ejecuta la prueba) debe comunicarse con servidores web protegidos por un firewall. En este escenario, debe configurarse el firewall para permitir el tráfico desde las direcciones IP siguientes:

* **195.249.159.238**
* **195.249.159.239**
