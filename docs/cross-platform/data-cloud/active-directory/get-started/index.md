---
title: Azure Active Directory
description: Este documento describe los pasos que se deben seguir para permitir que una aplicación móvil para autenticarse con Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318303"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Registrar una aplicación para usar Azure Active Directory_

Azure Active Directory permite a los desarrolladores a proteger los recursos como archivos, vínculos y API Web con la misma cuenta organizativa que los empleados usan para iniciar sesión en sus sistemas o comprobar sus correos electrónicos.

Desarrollo de aplicaciones móviles que pueden autenticarse con Azure Active Directory implica tres pasos.
Los dos primeros pasos suelen ser el mismo, independientemente de qué servicios que se va a usar. El tercer paso es diferente para cada tipo de servicio:

  1. [Registrar con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) en el *windowsazure.com* portal, a continuación,
  2. [Configurar servicios](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Desarrollar aplicaciones móviles mediante los servicios.

Ejemplos de servicios diferentes que puede tener acceso a:

- [Graph API](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office365


## <a name="conclusion"></a>Conclusión

Mediante los pasos anteriores puede autenticar sus aplicaciones móviles en Azure Active Directory. La biblioteca de autenticación de Active Directory (ADAL) facilita en gran medida con menos líneas de código, al tiempo que mantiene la mayor parte del código de la misma y, por tanto, lo que puede compartir entre plataformas.



## <a name="related-links"></a>Vínculos relacionados

- [Microsoft NativeClient sample](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
