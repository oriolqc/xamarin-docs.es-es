---
title: ¿Cómo sincronizar manualmente licencias de Xamarin?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D0BD93E9-3A1F-4E5B-8EE8-36ADC33DCFE4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: b06a1a7d525c91d7c3973b2b02d3d2835ce482f9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-manually-resynchronize-xamarin-licenses"></a>¿Cómo sincronizar manualmente licencias de Xamarin?

> [!IMPORTANT]
> Esta guía no se aplica a la mayoría de los usuarios MSDN porque no son necesarias para el propietario o de registro en las cuentas de Xamarin a menos que utilice el [almacenar componentes de Xamarin](https://components.xamarin.com/) o [Visual Studio para Mac (Mac)](~/cross-platform/get-started/requirements.md).




## <a name="overview"></a>Información general

Normalmente la información de licencia se deben volverse a sincronizar con el servidor de licencias de Xamarin automáticamente cuando se inicia el IDE. Por ejemplo, las renovaciones y degradaciones, la actualización de licencias normalmente aparecerá automáticamente una vez reiniciado el IDE. La información de licencia que aparece en el IDE debe coincidir con la información que se muestra en su [página cuenta](https://store.xamarin.com/account/my/subscription/computers). Si la información es todavía no coinciden, puede comprobar primero que la información de su cuenta de almacén de parece correcta y, a continuación, sincronizar manualmente las licencias de cerrar la sesión, eliminando los archivos de licencia en el disco y, a continuación, en el registro.

### <a name="note-for-ios-developers-on-windows"></a>Nota para los desarrolladores de iOS en Windows

los desarrolladores de iOS en Windows también tendrá que realizar estos pasos para Visual Studio para Mac; incluso si no desarrolla directamente en el host de compilación Mac emparejado.

## <a name="quick-manual-refresh-steps"></a>Pasos de actualización manual rápido

Este proceso rápido, omite el paso de la eliminación de los archivos de licencia en el disco que puede ser suficiente en algunos casos. 

1.  Cerrar sesión en su cuenta de Xamarin a través del IDE:
    -   Visual Studio para Mac
        -   Esquina superior derecha de la pantalla de bienvenida
        -   **Visual Studio para Mac > cuenta** (Mac)
        -   **Herramientas > cuenta** (Windows)
    -   Programa para la mejora
        -   **Herramientas > cuenta de Xamarin**
2.  Inicie sesión en su cuenta de Xamarin en el IDE.

## <a name="extended-manual-license-refresh-steps"></a>Extended pasos de actualización manual de licencia

1.  Cierre sesión en su cuenta de Xamarin a través del IDE. 
2.  Cierre el IDE.
3.  Compruebe que la información de cuenta de almacenamiento está correcta. Compruebe en particular para los nombres de equipo duplicado en el [página equipos](https://store.xamarin.com/account/my/subscription/computers).

4.  Si ve un par de nombres de equipo duplicados, utilice la **desactivar** elemento de menú desplegable para quitar _ambos_ los miembros del par:
    
    ![Licencia -> desactivar en https://store.xamarin.com/account/my/subscription/computers ] (resync-licenses-images/deactivate.png "usar el elemento de menú desplegable de desactivar para quitar los miembros del par")

5.  Eliminar todas las copias restantes de los archivos de licencia siguen presentes en el disco.
    -   Windows

        Eliminar todas las carpetas siguientes:
        -   `%PROGRAMDATA%\Mono for Android`
        -   `%PROGRAMDATA%\MonoTouch`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\Mono for Android`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\MonoTouch`

        En las instalaciones predeterminadas de Windows:
        -   `%PROGRAMDATA%` se expandirá a `C:\ProgramData`
        -   `%LOCALAPPDATA%` se expandirá a `C:\Users\%USERNAME%\AppData\Local`

        La `VirtualStore` copias de las licencias se crean automáticamente por Windows en determinados escenarios. Si las copias de VirtualStore existen, se leerán _en su lugar_ de las licencias en `%PROGRAMDATA%`.

    -   Mac

        Eliminar todas las carpetas siguientes:

        -   `~/Library/MonoAndroid`
        -   `~/Library/MonoTouch`
        -   `~/Library/Xamarin.Mac`

        Puede tener acceso a estas rutas de acceso en **buscador** seleccionando el **vaya > Ir a la carpeta** menú y pegándolos en el cuadro de diálogo. Buscador reemplaza automáticamente la ~ carácter de tilde con su carpeta de usuario.

6.  Abra Visual Studio para Mac o Visual Studio y registro nuevamente en su cuenta de Xamarin.

## <a name="supplementary-information-individual-license-file-locations"></a>Información complementaria: ubicaciones de archivos de licencia individuales

### <a name="windows"></a>Windows

En Windows se almacenan los archivos de licencia individuales en las siguientes ubicaciones:

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.licx`

Licencias para *prueba* suscripciones tienen nombres distintos:

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.trial.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.trial.licx`

### <a name="mac"></a>Mac

En Mac se almacenan los archivos de licencia individuales en las siguientes ubicaciones:

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.v2`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License`

Licencias para *prueba* suscripciones tienen nombres distintos:

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License.trial`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.trial`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License.trial`

## <a name="additional-troubleshooting-steps"></a>Pasos de solución de problemas adicionales

-   Compruebe si tiene caracteres no ASCII en su nombre de usuario, en el nombre del equipo o en cualquiera de las rutas de acceso totalmente expandidos de los archivos de licencia.

-   [Póngase en contacto con soporte para desarrolladores de Xamarin](http://xamarin.com/support)
