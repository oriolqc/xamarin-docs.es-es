---
title: ¿Puedo actualizar la plantilla predeterminada de Xamarin.Forms para un paquete de NuGet más reciente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: e439d39dd8591cad14485e64aabab2d6016a8e27
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668236"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>¿Puedo actualizar la plantilla predeterminada de Xamarin.Forms para un paquete de NuGet más reciente?

Esta guía usa la plantilla de biblioteca de Xamarin.Forms .NET Standard como ejemplo, pero también funcionará el mismo método general para la plantilla de proyecto compartido de Xamarin.Forms. Esta guía está escrita con el ejemplo de actualización de Xamarin.Forms 1.5.1.6471 a 2.1.0.6529, pero los mismos pasos son posibles establecer otras versiones como el valor predeterminado en su lugar.

1.  Copie la plantilla original `.zip` desde:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  Descomprima el `.zip` en una ubicación temporal.

3.  Cambie todas las apariciones de la versión anterior del paquete de formularios a la nueva versión que le gustaría utilizar.
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Ejemplo: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  Cambie el elemento "name" de los principales [archivo de plantilla de varios proyectos](https://msdn.microsoft.com/library/ms185308.aspx) (`Xamarin.Forms.PCL.vstemplate`) para que sea único. Por ejemplo:
    > <Name>Aplicación vacía (Xamarin.Forms Portable) - 2.1.0.6529</Name>

5.  Volver a comprimir la carpeta de plantillas completas. Asegúrese de que coincida con la estructura del archivo original de la `.zip` archivo. El `Xamarin.Forms.PCL.vstemplate` archivo debe estar en la parte superior de la `.zip` archivo, no dentro de las carpetas.

6.  Cree un subdirectorio "Mobile Apps" en la carpeta de plantillas de Visual Studio por usuario:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  Copie la nueva carpeta de plantillas de comprimir copia de seguridad en el nuevo directorio "Mobile Apps".

8.  Descargue el paquete de NuGet que coincida con la versión del paso 3. Por ejemplo, [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (Vea también [ https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) y cópiela en la subcarpeta correspondiente de la carpeta de extensiones de Xamarin Visual Studio:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
