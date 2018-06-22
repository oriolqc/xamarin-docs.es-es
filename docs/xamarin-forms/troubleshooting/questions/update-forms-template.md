---
title: ¿Se puede actualizar la plantilla predeterminada de Xamarin.Forms para un paquete de NuGet más reciente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fc479b4b0651e3312b855673730be21c2076d833
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049839"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>¿Se puede actualizar la plantilla predeterminada de Xamarin.Forms para un paquete de NuGet más reciente?

Esta guía usa la plantilla de biblioteca estándar de .NET de Xamarin.Forms como ejemplo, pero también funcionará el mismo método general para la plantilla de proyecto compartido de Xamarin.Forms. Esta guía está escrita con el ejemplo de actualización de Xamarin.Forms 1.5.1.6471 a 2.1.0.6529, pero los mismos pasos son posibles establecer otras versiones como el valor predeterminado en su lugar.

1.  Copie la plantilla original `.zip` desde:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  Descomprima el `.zip` en una ubicación temporal.

3.  Cambie todas las apariciones de la versión anterior del paquete de formularios a la nueva versión que le gustaría usar.
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Ejemplo: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  Cambiar el elemento "name" de los principales [archivo de plantilla de varios proyectos](http://msdn.microsoft.com/library/ms185308.aspx) (`Xamarin.Forms.PCL.vstemplate`) para que sea único. Por ejemplo:
    > <Name>Aplicación vacía (Xamarin.Forms Portable) - 2.1.0.6529</Name>

5.  Volver a comprimir la carpeta de plantillas todo. Asegúrese de que coincida con la estructura del archivo original de la `.zip` archivo. El `Xamarin.Forms.PCL.vstemplate` archivo debe estar en la parte superior de la `.zip` archivo, no dentro de las carpetas.

6.  Cree un subdirectorio "Aplicaciones móviles" en la carpeta de plantillas de Visual Studio por usuario:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  Copie la nueva carpeta de plantillas de seguridad zip en el nuevo directorio "Aplicaciones móviles".

8.  Descargue el paquete de NuGet que coincida con la versión del paso 3. Por ejemplo, [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (Vea también [ http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) y se copia en la subcarpeta correspondiente de la carpeta de extensiones de Visual Studio de Xamarin:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
