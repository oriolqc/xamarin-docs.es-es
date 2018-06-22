---
title: Solución de problemas
description: Condiciones de error comunes y cómo resolverlos
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fbe4fb6fce52636b59a9637ee0150c4c19fcc9da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30788528"
---
# <a name="troubleshooting"></a>Solución de problemas

_Condiciones de error comunes y cómo resolverlos_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Error: "no se puede encontrar una versión de Xamarin.Forms compatible con..."

Los siguientes errores pueden aparecer en el **consola del paquete** ventana al actualizar todos los paquetes de Nuget en una solución de Xamarin.Forms o en un proyecto de aplicación de Xamarin.Forms Android:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>¿Qué hace este error?

Visual Studio para Mac (o Visual Studio) puede indicar que las actualizaciones están disponibles para el paquete Xamarin.Forms Nuget *y todas sus dependencias*. En Xamarin Studio, la solución **paquetes** nodo podría tener un aspecto similar al siguiente (los números de versión puede variar):

![](images/updates-available.png "Carpeta de paquetes del proyecto de Android")

Este error puede producirse si intenta actualizar _todos los_ los paquetes.

Esto es porque con Android proyectos se establece en una versión de destino/compilación de Android 6.0 (API 23) o a continuación, Xamarin.Forms tiene una dependencia fuerte en *específico* versiones de la Android compatibles con paquetes. Aunque las versiones actualizadas de estos paquetes pueden estar disponibles, Xamarin.Forms no es necesariamente compatible con ellos.

En este caso debe actualizar _sólo_ el **Xamarin.Forms** como así se asegurará de que las dependencias permanecen en las versiones compatibles del paquete. Otros paquetes que ha agregado al proyecto también pueden actualizar individualmente siempre y cuando no hacen que los paquetes de compatibilidad con Android que se va a actualizar.


> [!NOTE]
> Si usas Xamarin.Forms 2.3.4 o posterior **y** versión de destino/compilación del proyecto Android está establecida en Android 7.0 (API de 24) o una versión posterior, a continuación, las dependencias de disco duras mencionadas anteriormente ya no se aplican y puede actualizar la compatibilidad paquetes de forma independiente el paquete de Xamarin.Forms.


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Corrección: Quitar todos los paquetes y vuelva a agregar Xamarin.Forms

Si el **Xamarin.Android.Support** paquetes se han actualizado a las versiones incompatibles, la solución más sencilla consiste en:

1. Elimine manualmente todos los paquetes de Nuget en el proyecto Android, a continuación
2. Volver a agregar el **Xamarin.Forms** paquete.

Se descargará automáticamente la *correcta* versiones de los otros paquetes.

Para ver un vídeo de este proceso, consulte esta [foros post](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
