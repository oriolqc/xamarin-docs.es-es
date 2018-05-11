---
title: Azure Active Directory
description: Registrar una aplicación para usar Azure Active Directory
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: decc069bd2fe31d54c886793ae4c94935b23ad02
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Registrar una aplicación para usar Azure Active Directory_

Azure Active Directory permite a los desarrolladores a recursos seguros, como archivos, vínculos y las API de Web con la misma cuenta organizativa utilizados por los empleados para iniciar sesión en sus sistemas o comprobar sus mensajes de correo electrónico.

Desarrollo de aplicaciones móviles que pueden autenticarse con Azure Active Directory implica tres pasos.
Los dos primeros pasos suelen ser el mismo, independientemente de qué servicios planea usar. El tercer paso es diferente para cada tipo de servicio:

  1. [Registrar con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) en el *windowsazure.com* portal, a continuación,
  2. [Configurar servicios](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Desarrollar aplicaciones móviles mediante los servicios.

Ejemplos de distintos servicios que puede tener acceso:

- [API Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office 365


## <a name="conclusion"></a>Conclusión

Mediante los pasos anteriores puede autenticar sus aplicaciones móviles en Azure Active Directory. La biblioteca de autenticación de Active Directory (ADAL), resulta más fácil con menos líneas de código, manteniendo la mayoría del código de la misma y, por tanto, lo que pueden compartirse entre plataformas.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
