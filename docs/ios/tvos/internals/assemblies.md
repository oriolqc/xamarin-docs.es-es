---
title: Ensamblados
ms.topic: article
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 509d4f465956d56e8efa5b69153764f5ae0949a9
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="assemblies"></a>Ensamblados

## <a name="supported-assemblies"></a>Ensamblados compatibles

Se trata de una lista de los ensamblados compatibles con Xamarin para las aplicaciones de Xamarin.tvOS. A continuación, se muestra una lista detallada de estos.  Incluyen algunas omisiones notables `System.EnterpriseServices`, la pila ASP.NET y Windows.Forms.

|Ensamblado|Agregado|Compatibilidad con la API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Los autores de compiladores.|
|Mono.Data.Sqlite.dll|1.2|Proveedor de ADO.NET para SQLite; vea [limitaciones](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Soporte de protocolo TDS; utilizado para [System.Data.SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/) admite dentro de [System.Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|Interfaces API criptográficas.|
|monotouch.dll|1.0|Este ensamblado contiene el [enlace C# a la API de CocoaTouch](https://developer.xamarin.com/api/root/ios-unified/).|
|mscorlib.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|El objeto de OpenGL/OpenAL orientada a servicios de API, [extendido para proporcionar compatibilidad con dispositivos de iPhone](https://developer.xamarin.com/api/namespace/OpenGLES/).|
|System.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), además de los tipos de los espacios de nombres siguientes: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx), [con alguna funcionalidad quitado](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Cliente de oData completa.|
|System.Drawing|1.0|System.Drawing API - solo API clásico.<br />_System.Drawing no se admite en la API unificada para la Xamarin.Mac .NET 4.5 o marcos de trabajo móviles._|
|System.Json.dll|1.1|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) tal como está presente en la pila [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), además de los tipos de los espacios de nombres siguientes: <ul><li>Sistema</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); forma parte de [System.Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) admite.|
|System.Web.Services|1.1|[Los servicios Web de básicos](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) del perfil de .NET 3.5, con las características de servidor quitado.|
|System.Xml.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

Además de los enlaces de Mac, puede consumir Xamarin.tvOS [bibliotecas de clases Portable de .NET](~/cross-platform/app-fundamentals/pcl.md).



## <a name="related-links"></a>Vínculos relacionados

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
