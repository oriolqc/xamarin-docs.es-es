---
title: "¿Dónde está mi equipo Mac?"
description: Instrucciones para configurar Mac para Visual Studio para crear proyectos de Xamarin.iOS
ms.topic: article
ms.prod: xamarin
ms.assetid: 4f792690-b3b1-4d56-a5e2-363b4f246059
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 491d7cea4b88fa44bb15e76dd92ecd5216ce9984
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="wheres-my-mac"></a>¿Dónde está mi equipo Mac?

_Instrucciones para configurar Mac para Visual Studio para crear proyectos de Xamarin.iOS_

La versión actual **estable** de Xamarin para Visual Studio interactúa con el equipo Mac de una forma distinta a la de las versiones anteriores[^](#earlier-versions).

Para usar un equipo Mac como Xamarin Mac Agent, debe habilitar el **inicio de sesión remoto** en el equipo Mac.

1. Abra *Spotlight* (`Cmd-Space`), busque **Inicio de sesión remoto** y, después, seleccione el resultado **Compartir**. Se abrirá el menú **Preferencias del sistema** en el panel **Compartir**.

  ![](visual-studio-ssh-images/spotlight.png "Búsqueda de inicio de sesión remoto en Spotlight")

2. Marque la opción **Inicio de sesión remoto** en la lista **Servicio** de la izquierda con el fin de permitir que Xamarin para Visual Studio se conecte al equipo Mac.

  ![](visual-studio-ssh-images/sharing.png "Marcado de la opción Inicio de sesión remoto en la lista Servicio")

3. Asegúrese de que se establece **Inicio de sesión remoto** para permitir el acceso de **Todos los usuarios** o que el nombre de usuario de Mac o el grupo se incluye en la lista de usuarios permitidos en la lista de la derecha.

Visual Studio ya debería detectar el equipo Mac si se encuentra en la misma red.
Cuando Visual Studio intente conectarse al equipo Mac, se le pedirá iniciar sesión desde Visual Studio mediante las credenciales de usuario de Mac.

## <a name="where-can-i-find-more-information"></a>¿Dónde se puede obtener más información?

Hay una serie de guías para ayudarle a configurar y entender el nuevo agente que se enumeran a continuación:

- [Conexión al equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Solución de problemas](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Xamarin University - Xamarin Mac Agent](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)

<a name="earlier-versions" />

## <a name="migrating-from-previous-versions"></a>Migración desde versiones anteriores

Si ha usado versiones anteriores de Xamarin para Visual Studio, debería estar familiarizado con la aplicación **Host de compilación de Xamarin.iOS** que era necesario iniciar en el equipo Mac y luego *emparejar* mediante un número PIN con la instancia de Visual Studio.

El host de compilación ya no es necesario con esta versión de Xamarin para Visual Studio.
