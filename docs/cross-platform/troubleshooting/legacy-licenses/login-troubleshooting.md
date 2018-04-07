---
title: ¿Por qué no se puede registrar en Xamarin en Visual Studio o Visual Studio para Mac?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6EF2B553-5DF9-41CC-B68F-77A7F64572FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: cb750a7c282ecab6e2193bb554e470086868e018
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/07/2018
---
# <a name="why-cant-i-log-into-xamarin-in-visual-studio-or-visual-studio-for-mac"></a>¿Por qué no se puede registrar en Xamarin en Visual Studio o Visual Studio para Mac?

> [!IMPORTANT]
> Esta guía no se aplica a la mayoría de los usuarios MSDN porque no son necesarias para el propietario o de registro en las cuentas de Xamarin a menos que utilice el [almacenar componentes de Xamarin](https://components.xamarin.com/) o [Visual Studio para Mac (Mac)](~/cross-platform/get-started/requirements.md). Los titulares de licencia MSDN deben hacer referencia a este [opciones de licencia de guía](~/cross-platform/get-started/requirements.md) en su lugar.



## <a name="overview"></a>Información general
Hay algunas causas comunes que pueden impedir que inicie sesión en su cuenta de Xamarin en el IDE. A continuación se describen los problemas conocidos y correcciones.

### <a name="finding-the-login-screen"></a>Búsqueda de la pantalla de inicio de sesión

Como referencia, las pantallas de inicio de sesión se encuentran aquí:

- Visual Studio para Mac
   - Esquina superior derecha de la pantalla de bienvenida
   - **Visual Studio para Mac > cuenta** (Mac)
   - **Herramientas > cuenta** (Windows)
- Programa para la mejora
   - **Herramientas > cuenta de Xamarin**

## <a name="the-ide-is-connecting-but-the-account-screen-isnt-showing-correct-login-information"></a>Se está conectando el IDE, pero la pantalla de la cuenta no aparece la información de inicio de sesión correcto

Este problema se resuelve normalmente una resincronización de licencia manual.
Siga las instrucciones de este artículo: [¿cómo puedo manualmente resyncronize licencias de Xamarin?](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)

## <a name="invalid-account-information"></a>Información de cuenta no válida

Si va al sitio Web de Xamarin [página de inicio de sesión](https://store.xamarin.com/Login?from=%2faccount%2f), puede intente iniciar sesión con sus credenciales de cuenta actual.
La página también contiene vínculos para restablecer la contraseña y para crear una nueva cuenta.

## <a name="account-is-valid-but-the-ide-cant-connect"></a>La cuenta es válida, pero no se puede conectar el IDE

Esto ocurre normalmente cuando el IDE tengan acceso a los puntos de conexión necesarios se bloquean firewall u otras opciones de seguridad.
Los servidores de activación deben tener acceso a lo siguiente:

> Activation.xamarin.com store.xamarin.com auth.xamarin.com

Sin embargo, varios puntos de conexión son importantes para los procesos de desarrollo general, como actualizaciones, obtener paquetes de NuGet, etcetera. Por este motivo, se recomienda para asegurarse de que *todos los* de los puntos de conexión se agregan desde la [Guía de configuración de firewall de Xamarin](~/cross-platform/get-started/installation/firewall.md).

### <a name="ios-in-xamarin-studio-windows"></a>iOS en ventanas de Xamarin Studio
no se admite el desarrollo de iOS en Xamarin Studio para Windows. Al acceder a la pantalla de inicio de sesión aquí, no se mostrará la licencia de iOS.

En su lugar, inicio de sesión a través de Xamarin Studio (Mac) o Visual Studio con una licencia heredada. Tenga en cuenta que los usuarios MSDN en Windows no es necesario iniciar sesión.

## <a name="additional-support"></a>Soporte adicional

Si los escenarios anteriores no describen su situación o resolver el problema, consulte estos [opciones de soporte técnico](https://www.xamarin.com/support).
