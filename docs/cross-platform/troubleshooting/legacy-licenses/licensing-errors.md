---
title: "Algunos errores de licencias específicos"
ms.topic: article
ms.prod: xamarin
ms.assetid: D26BDF2D-923B-4BC1-841A-74583155DB71
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 80006ce594db5baef5d295537f198181fe0b0fe1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="some-specific-licensing-errors"></a>Algunos errores de licencias específicos

> [!IMPORTANT]
> La siguiente información no se aplica a los usuarios MSDN, ya que estos son los problemas específicos de licencias de Xamarin heredados. Si es un usuario MSDN y ve errores similares a las que a continuación, intente [actualización a la versión más reciente de Xamarin](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) & hacer referencia a este [opciones de licencia de guía](~/cross-platform/get-started/requirements.md).



## <a name="invalid-license"></a>"Licencia no válida"

> Licencia no válida. Por favor, reactive Xamarin.Android (XA9999) no se puede determinar la edición de licencia. (XA9010)

Estos mensajes pueden aparecer en algunas circunstancias diferentes.

-   La licencia actual en el disco puede ser de la sincronización con la información del usuario actual en el equipo. [Actualizar los archivos de licencia](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) resolverá este problema en muchos casos.

-   El equipo podría tener 2 activaciones duplicadas en conflicto. Ya no se utiliza este tipo de licencia por Xamarin. Si tiene problemas que parecen estar relacionado con este error, vuelva [soporte del correo electrónico](https://www.xamarin.com/support).

-   La cuenta de Xamarin que no se reciba una invitación a una licencia de Xamarin. Vea también: [equipo de administración de licencias](~/cross-platform/troubleshooting/legacy-licenses/team-management.md).

## <a name="failed-to-load-android-entitlements"></a>"No se pudo cargar derechos Android"

> Mono.VisualStudio.Shell.ShellPackage Error: 0: no se pudo cargar derechos Android: licencia no válida. Por favor, reactive Xamarin.Android (XA9999) Mono.VisualStudio.Shell.ShellPackage Error: 0: error de actualización de la licencia: licencia no válida. Por favor, reactive Xamarin.Android (XA9999)

Se trata de una versión anterior del problema "Licencia no válida" anterior. Se aplican los mismos pasos de solución de problemas.

## <a name="this-version-was-released-after-your-subscription-expired"></a>"Esta versión se libera cuando la suscripción ha expirado"

> Error XA9000: Esta versión se publicó después de la suscripción ha expirado (11/11/2014 5:11:41 PM). (XA9000) (Mobile.Android.Utilities) Error XA9010: no se puede determinar la edición de licencia. (XA9010) (Mobile.Android.Utilities)

Estos mensajes suelen producirse en dos situaciones:

-   Las licencias en el disco no están actualizadas. [Actualizar los archivos de licencia](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) resolverá este problema en muchos casos.

-   La suscripción de Xamarin ya no está activa y la versión instalada actualmente de Xamarin es más reciente que la fecha de caducidad de la suscripción. En este caso es la solución adecuada para [degradar](http://kb.xamarin.com/customer/portal/articles/1699777) a una versión de Xamarin que se publicaron antes de que caduque la suscripción. No dude en enviar un correo electrónico a [ hello@xamarin.com ](mailto:hello@xamarin.com) para solicitar la última versión válida de su suscripción. Si todavía aparece el error después de degradar, asegúrese de intente actualizar los archivos de licencia demasiado.

## <a name="additional-references"></a>Referencias adicionales

-   [Cómo actualizar licencias](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)
-   [Cómo degradar Xamarin](http://kb.xamarin.com/customer/portal/articles/1699777-downgrading)
-   [Lista de códigos de error de Xamarin.Android](~/android/troubleshooting/errors.md)
-   [Lista de códigos de error de MonoTouch (Xamarin.iOS)](~/ios/troubleshooting/mtouch-errors.md)

### <a name="next-steps"></a>Pasos siguientes
Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, vea [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo registre un error nuevo si es necesario.
