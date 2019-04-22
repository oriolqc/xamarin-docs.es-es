---
title: Los ensamblados admitidos por Xamarin para tvOS
description: Con el fin de ayudar a aclarar las características disponibles para las aplicaciones de tvOS, este documento proporciona una lista de ensamblados compatible con Xamarin para el desarrollo de tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: df50b4280335001f2d27ff23a91e4098eed3ba99
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870214"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Los ensamblados admitidos por Xamarin para tvOS

## <a name="supported-assemblies"></a>Ensamblados admitidos

Se trata de una lista de los ensamblados compatibles con Xamarin para las aplicaciones Xamarin.tvOS. A continuación, se muestra una lista detallada de estos.  Incluyen algunas omisiones notables `System.EnterpriseServices`, la pila ASP.NET y Windows.Forms.

|Ensamblado|Agregado|Compatibilidad con la API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Los programadores de compiladores.|
|Mono.Data.Sqlite.dll|1.2|Proveedor de ADO.NET para SQLite; consulte [limitaciones](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Compatibilidad de protocolo TDS; utilizado para [System.Data.SqlClient](xref:System.Data.SqlClient) soporte técnico en [System.Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|API criptográficas.|
|monotouch.dll|1.0|Este ensamblado contiene el [enlace C# a la API CocoaTouch](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|El objeto de OpenGL/OpenAL orientada a las API, [extendido para proporcionar compatibilidad con dispositivos de iPhone](xref:OpenGLES).|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), además de los tipos de los espacios de nombres siguientes: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx), [con alguna funcionalidad quitado](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Cliente de oData completa.|
|System.Drawing|1.0|System.Drawing API - API clásica solo.<br />_System.Drawing no se admite en la API unificada para Xamarin.Mac .NET 4.5 o plataformas móviles._|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) tal como está presente en la pila [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), además de los tipos de los espacios de nombres siguientes: <ul><li>Sistema</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx); forma parte de [System.Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) admite.|
|System.Web.Services|1.1|[Servicios Web básicos](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) del perfil de .NET 3.5, con las características de servidor quitado.|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

Además de los enlaces de Mac, puede consumir Xamarin.tvOS [bibliotecas de clases portables de .NET](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Vínculos relacionados

- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
