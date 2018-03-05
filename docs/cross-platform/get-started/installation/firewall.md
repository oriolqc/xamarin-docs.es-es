---
title: "Instrucciones de configuración del firewall para Xamarin"
description: Lista de los hosts que deben incluirse en la lista de permitidos del firewall para que la plataforma de Xamarin funcione en la empresa.
ms.topic: article
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: de68c1a8ceec381faf1b867c708e04030d39c73a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-firewall-configuration-instructions"></a>Instrucciones de configuración del firewall para Xamarin

_Lista de los hosts que deben incluirse en la lista de permitidos del firewall para que la plataforma de Xamarin funcione en la empresa._

Para que los productos de Xamarin se instalen y funcionen correctamente, determinados puntos de conexión deben ser accesibles para descargar las herramientas y las actualizaciones necesarias para el software. Si usted o la empresa tienen una configuración de firewall estricta, puede experimentar problemas con la instalación, las licencias, los componentes, etc. En este documento se indican algunos de los puntos de conexión conocidos que deben aparecer en la lista de permitidos del firewall para que Xamarin funcione.  No se incluyen los puntos de conexión necesarios para las herramientas de otros fabricantes incluidas en la descarga. Si después de repasar esta lista sigue teniendo problemas, consulte las guías de solución de problemas de instalación de Apple o Android.

## <a name="endpoints-to-whitelist"></a>Puntos de conexión que deben incluirse en la lista de permitidos

### <a name="xamarin-installer"></a>Xamarin Installer

Para que el software se instale correctamente con la versión más reciente del instalador de Xamarin, tiene que agregar las siguientes direcciones conocidas:

-  xamarin.com (manifiestos del instalador)
-  dl.xamarin.com (ubicación de descarga del paquete)
-  dl.google.com (para descargar el SDK de Android)
-  download.oracle.com (JDK)
-  visualstudio.com (configurar ubicación de descarga de paquetes)
-  go.microsoft.com (configurar URL de resolución)
-  aka.ms (configurar URL de resolución)

Si está usando un equipo Mac y tiene problemas de instalación de Xamarin.Android, asegúrese de que macOS pueda descargar Java.


### <a name="components-store-and-nuget-including-xamarinforms"></a>Components Store y NuGet (incluye Xamarin.Forms)

Para obtener acceso a Xamarin Component Store o a NuGet (Xamarin.Forms está empaquetado como NuGet), tiene que agregar las siguientes direcciones:

-  components.xamarin.com (para usar Xamarin Components Store)
-  xampubdl.blob.core.windows.net (hospeda descargas de Components Store)
-  www.nuget.org (para acceder a NuGet)
-  az320820.vo.msecnd.net (descargas de NuGet)
-  dl-ssl.google.com (componentes de Google)


### <a name="software-updates"></a>Actualizaciones de software

Para garantizar que las actualizaciones de software se descarguen correctamente, tendrá que agregar las siguientes direcciones:

-  software.xamarin.com (servicio de actualizador)
-  download.visualstudio.microsoft.com
-  dl.xamarin.com

### <a name="xamarin-insights"></a>Xamarin Insights

Para garantizar que la actividad llegue al servidor de Xamarin Insights, tendrá que agregar las siguientes direcciones:

* https://xaapi.xamarin.com


## <a name="xamarin-mac-agent"></a>Xamarin Mac Agent

Para conectar Visual Studio a un host de compilación de Mac mediante Xamarin Mac Agent, el puerto SSH tiene que estar abierto. De forma predeterminada, es el **puerto 22**.

## <a name="summary"></a>Resumen

Esta guía trata sobre los puntos de conexión que deben aparecer en la lista blanca para que los productos de Xamarin se instalen y se actualicen correctamente en un equipo.