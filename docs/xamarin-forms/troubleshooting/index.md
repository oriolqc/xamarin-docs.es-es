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
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850450"
---
# <a name="troubleshooting"></a>Solución de problemas

_Condiciones de error comunes y cómo resolverlos_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Error: "No se puede encontrar una versión de Xamarin.Forms compatible con..."

Pueden aparecer los errores siguientes en el **consola paquetes** ventana cuando se actualiza todos los paquetes de Nuget en una solución de Xamarin.Forms o en un proyecto de aplicación de Xamarin.Forms Android:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>¿Qué provoca este error?

Visual Studio para Mac (o Visual Studio) puede indicar que las actualizaciones están disponibles para el paquete de Xamarin.Forms Nuget *y todas sus dependencias*. En Xamarin Studio, la solución **paquetes** nodo podría tener este aspecto (los números de versión podrían ser diferentes):

![](images/updates-available.png "Carpeta de paquetes del proyecto de Android")

Este error puede producirse si intenta actualizar _todas_ los paquetes.

Esto es porque con Android, los proyectos se establecen en una versión de compilación o de destino de Android 6.0 (API 23) o a continuación, Xamarin.Forms tiene una dependencia fuerte *específico* paquetes de soporte de versiones de Android. Aunque las versiones actualizadas de estos paquetes pueden estar disponibles, Xamarin.Forms no es necesariamente compatible con ellos.

En este caso debe actualizar _sólo_ el **Xamarin.Forms** como así se asegurará de que las dependencias permanecen en las versiones compatibles del paquete. Otros paquetes que ha agregado a su proyecto también se actualizará individualmente siempre que no hacen que los paquetes de compatibilidad con Android que se va a actualizar.


> [!NOTE]
> Si usa Xamarin.Forms 2.3.4 o posterior **y** versión de compilación o de destino del proyecto Android está establecida en Android 7.0 (API 24) o versiones posteriores, a continuación, las dependencias de disco duras mencionadas anteriormente ya no se aplican y puede actualizar la compatibilidad paquetes independientemente del paquete de Xamarin.Forms.


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Corrección: Quitar todos los paquetes y volver a agregar a Xamarin.Forms

Si el **Xamarin.Android.Support** paquetes se han actualizado a las versiones incompatibles, la solución más sencilla consiste en:

1. Elimine manualmente todos los paquetes de Nuget en el proyecto Android, a continuación
2. Volver a agregar el **Xamarin.Forms** paquete.

Esto descargará automáticamente la *correcta* versiones de los otros paquetes.

Para ver un vídeo de este proceso, consulte este [foros post](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
