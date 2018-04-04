---
title: Ensamblados disponibles
description: Ensamblados disponibles en Xamarin.iOS y Xamarin.Android, Xamarin.Mac
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: e27ad3469a37634f5829f9c4c903808a74e4d6ae
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="available-assemblies"></a>Ensamblados disponibles

_Ensamblados disponibles en Xamarin.iOS y Xamarin.Android, Xamarin.Mac_

Xamarin.iOS, Xamarin.Android y Xamarin.Mac todos se suministran con más de una docena ensamblados. Igual que Silverlight es un subconjunto de los ensamblados .NET escritorio extendido, plataformas de Xamarin también es un subconjunto extendido de varios ensamblados de .NET de escritorio y Silverlight.

Las plataformas de Xamarin no son ABI compatible con los ensamblados existentes compilados para un perfil diferente. Debe volver a compilar el código fuente para generar ensamblados como destino el perfil correcto (igual que necesita compilar el código de origen que tenga como destino .NET 3.5 y Silverlight por separado).

Se pueden compilar aplicaciones de Xamarin.Mac en tres modos: uno que usa Xamarin del seleccionada perfil móvil, Xamarin.Mac .NET 4.5 Framework que permite tener como destino los ensamblados de escritorio completa existentes y uno compatible que usa la API de .NET se encuentra en un sistema Mono instalación. Para obtener más información, vea nuestra [.NET Framework de destino](~/mac/platform/target-framework.md) documentación.


## <a name="net-standard-libraries"></a>Bibliotecas estándar de .NET

Además de iOS, Android y Mac enlaces, pueden utilizar los proyectos de Xamarin [bibliotecas estándar de .NET](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables
 
También pueden usar los proyectos de Xamarin [bibliotecas de clases Portable de .NET](~/cross-platform/app-fundamentals/pcl.md), aunque esta tecnología está en desuso en favor de .NET estándar.

## <a name="supported-assemblies"></a>Ensamblados compatibles

> [!div class="mx-tdCol2BreakAll"]
> |Ensamblado|Compatibilidad con la API|IOS de Xamarin|Xamarin para Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|Incluye CJK, Oriente, otros, poco frecuentes, oeste|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|Proveedor de ADO.NET para SQLite; vea las limitaciones.|✓|✓|✓|
> |Mono.Data.Tds.dll|Soporte de protocolo TDS; utilizado para [System.Data.SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/) admite dentro de [System.Data](https://developer.xamarin.com/api/namespace/System.Data/).|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|Interfaces API criptográficas.|✓|✓|✓|
> |monotouch.dll|Este ensamblado contiene el enlace de C# a la API CocoaTouch. Esto solo está disponible en proyectos de iOS clásico.|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|El objeto de OpenGL/OpenAL orientados a las API, que se ha ampliado para proporcionar compatibilidad con dispositivos de iPhone.|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx), además de los tipos de los espacios de nombres siguientes:<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](http://msdn.microsoft.com/en-us/library/ms229335.aspx) , con [alguna funcionalidad eliminado](~/ios/data-cloud/system.data.md).|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|Cliente de oData completa.|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|Pila WCF que se dan en [Silverlight](http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx), además de los tipos de los espacios de nombres siguientes: <br />Sistema<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](http://msdn.microsoft.com/en-us/library/ms229335.aspx); forma parte de [System.Data](~/ios/data-cloud/system.data.md) admite.|✓|✓|✓|
> |System.Web.&#8203;Services.dll|Servicios Web básicos del perfil de .NET 3.5, con las características de servidor quitado.|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](http://msdn.microsoft.com/en-us/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](http://msdn.microsoft.com/en-us/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|Este ensamblado contiene el enlace de C# a la API CocoaTouch. Solo se utiliza en los proyectos de iOS unificado.|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Los autores de compiladores.| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|System.Drawing API - solo API clásico. System.Drawing no se admite en la API unificada para la Xamarin.Mac .NET 4.5 o marcos de trabajo móviles. Puede agregarse compatibilidad de System.Drawing para iOS y OS X mediante el [sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics) biblioteca|✓| |✓|
