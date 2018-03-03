---
title: "Proyectos de instalación de Windows"
description: "Adición de nuevos proyectos de Windows a una solución de Xamarin.Forms existente"
ms.topic: article
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 2acabc68c595bfb3bbd5c3516f68cd777ba10327
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="setup-windows-projects"></a>Proyectos de instalación de Windows

_Adición de nuevos proyectos de Windows a una solución de Xamarin.Forms existente_

Proyectos de Xamarin.Forms más antiguos (o las creadas en Mac OS&nbsp;X) no tendrá estas instalación de proyectos de Windows.

Esto significa que debe agregar manualmente estos tipos de proyecto para compilar aplicaciones de Windows 8.1, Windows Phone 8.1 y 10 de Windows (UWP).

## <a name="add-a-windows-81-app"></a>Agregar una aplicación de Windows 8.1

* Si se usó la plantilla PCL, [actualizar el perfil](#pcl), a continuación,
* [Agregar una aplicación de Windows 8.1](~/xamarin-forms/platform/windows/installation/tablet.md) para factores de forma de Tablet PC/escritorio.

## <a name="add-a-windows-phone-81-app"></a>Agregar un Windows Phone 8.1 app

* Si se usó la plantilla PCL, [actualizar el perfil](#pcl), a continuación,
* [Agregar un Windows Phone 8.1 app](~/xamarin-forms/platform/windows/installation/phone.md)

## <a name="add-a-universal-windows-platform-uwp-app"></a>Agregar un Windows Universal aplicación de la plataforma (UWP)

* Creación de [UWP](https://msdn.microsoft.com/library/windows/apps/dn894631.aspx) apps requiere Visual Studio 2015 con Windows 10.
* Si se usó la plantilla PCL, [actualizar el perfil](#pcl), a continuación,
* [Agregar un Windows Universal aplicación de la plataforma](~/xamarin-forms/platform/windows/installation/universal.md)

<a name="pcl" />

### <a name="update-your-pcl-profile"></a>Actualizar su perfil PCL

Si la aplicación existente de Xamarin.Forms utiliza la plantilla de biblioteca de clases portables (PCL), debe actualizar su perfil.

1. **Haga clic en > propiedades** (puede diferir la configuración existente)

  ![](images/targets.png "Destinos PCL")

2. Haga clic en el **cambio...**  botón

3. Asegúrese del **Windows 8** y **Windows Phone 8.1** opciones están seleccionadas (y **Windows Phone Silveright** es *anular la selección*):

  ![](images/pcl.png "Opciones de destino PCL")

4. Presione **Aceptar** y guarde los cambios.

Esto equivale a **perfil 111** si va a configurar su PCL en Visual Studio para Mac mediante la lista desplegable.

  ![](images/pcl-xs.png "Perfil PCL 111")

**Nota:** si la solución aún tiene un proyecto de Silverlight de Windows Phone 8, debe establecerse la PCL en 259 de perfil. Compatibilidad con Silverlight de Windows Phone 8 está en desuso, por lo que se recomienda que se reemplace con los tipos de proyecto que se muestran en esta página.
