---
title: Ensamblados disponibles
description: Ensamblados disponibles en Xamarin.iOS y Xamarin.Android, Xamarin.Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 3a4e39582a389774e5d17327cefecb0f676adfc6
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="available-assemblies"></a>Ensamblados disponibles

_Ensamblados disponibles en Xamarin.iOS y Xamarin.Android, Xamarin.Mac_

Xamarin.iOS, Xamarin.Android y Xamarin.Mac todos se suministran con más de una docena ensamblados. Igual que Silverlight es un subconjunto de los ensamblados .NET escritorio extendido, plataformas de Xamarin también es un subconjunto extendido de varios ensamblados de .NET de escritorio y Silverlight.

Las plataformas de Xamarin no son ABI compatible con los ensamblados existentes compilados para un perfil diferente. Debe volver a compilar el código fuente para generar ensamblados de destino correctos. perfil (solo según sea necesario compilar el código de origen a destino Silverlight y .NET 3.5 por separado).

Se pueden compilar aplicaciones de Xamarin.Mac en tres modos: uno que usa Xamarin del seleccionada perfil móvil, Xamarin.Mac .NET 4.5 Framework que permite tener como destino los ensamblados de escritorio completa existentes y uno compatible que usa la API de .NET se encuentra en un sistema Mono instalación. Para obtener más información, vea nuestra [.NET Framework de destino](~/mac/platform/target-framework.md) documentación.


## <a name="portable-class-libraries"></a>Bibliotecas de clases portables
 
Además de iOS, Android y Mac enlaces, Xamarin pueden consumir plataformas [bibliotecas de clases Portable de .NET](~/cross-platform/app-fundamentals/pcl.md).

## <a name="supported-assemblies"></a>Ensamblados compatibles

<style type="text/css"> .TG {borde-contraer: Contraer; borde-espaciado: 0;} td .tg {5px relleno: 10px; borde-ancho: 1px; desbordamiento: oculta; word-salto: normal;} .tg th {fuente-peso: normal; 5px relleno: 10px; borde-ancho: 1px; desbordamiento: oculta; word-salto: normal;} .tg .tg-zx05 {espesor de fuente: negrita; color de fondo: #d1d9dd; vertical-align: superior} .tg .tg-w6qd {color de fondo: #91e2f4; alinear texto: center; vertical-align: superior} .tg .tg-yw4l {vertical-align: top} .tg .tg-q3ci {color de fondo: #cfefa7; alinear texto: center; vertical-align: top} .tg .tg-p7et {color de fondo: #cec0ec; alinear texto: center; vertical-align: top} </style>
<table class="tg">
  <tr>
    <th class="tg-zx05">Ensamblado</th>
    <th class="tg-zx05">Compatibilidad con la API</th>
    <th class="tg-zx05">Xamarin.iOS</th>
    <th class="tg-zx05">Xamarin.Android</th>
    <th class="tg-zx05">Xamarin.Mac</th>
  </tr>
  <tr>
    <td class="tg-yw4l">FSharp.Core.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">l18N.dll</td>
    <td class="tg-yw4l">Incluye CJK, Oriente, otros, poco frecuentes, oeste</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Microsoft.CSharp.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.CSharp.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Data.Sqlite.dll</td>
    <td class="tg-yw4l">Proveedor de ADO.NET para SQLite; vea <a href="~/ios/data-cloud/system.data.md">limitaciones</a>.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Data.Tds.dll</td>
    <td class="tg-yw4l">Soporte de protocolo TDS; utilizado para <a href="https://developer.xamarin.com/api/namespace/System.Data.SqlClient/">System.Data.SqlClient</a> admite dentro de <a href="https://developer.xamarin.com/api/namespace/System.Data/">System.Data</a>.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Dynamic.Interpreter.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Security.dll</td>
    <td class="tg-yw4l">Interfaces API criptográficas.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">monotouch.dll</td>
    <td class="tg-yw4l">Este ensamblado contiene el enlace de C# a la API CocoaTouch. Esto solo está disponible en proyectos de iOS clásico.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">MonoTouch.Dialog-1.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">MonoTouch.NUnitLite.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">mscorlib.dll</td>
    <td class="tg-yw4l"><a ref="https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">OpenTK-1.0.dll</td>
    <td class="tg-yw4l">El objeto de OpenGL/OpenAL orientados a las API, que se ha ampliado para proporcionar compatibilidad con dispositivos de iPhone.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.dll</td>
    <td class="tg-yw4l"><a href="https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a>, además de los tipos de los espacios de nombres siguientes:<br>System.Collections.Specialized<br>System.ComponentModel<br>System.ComponentModel.Design<br>System.Diagnostics<br>System.IO<br>System.IO.Compression<br>System.IO.Compression.FileSystem<br>System.Net<br>System.Net.Cache<br>System.Net.Mail<br>System.Net.Mime<br>System.Net.NetworkInformation<br>System.Net.Security<br>System.Net.Sockets<br>System.Runtime.InteropServices<br>System.Runtime.Versioning<br>System.Security.AccessControl<br>System.Security.Authentication<br>System.Security.Cryptography<br>System.Security.Permissions<br>System.Threading<br>System.Timers</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ComponentModel.Composition.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ComponentModel.DataAnnotations.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Core.dll</td>
    <td class="tg-yw4l"><a ref="https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Data.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a> , <a href="~/ios/data-cloud/system.data.md">con alguna funcionalidad quitado</a>.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Data.Services.Client.dll</td>
    <td class="tg-yw4l">Cliente de oData completa.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.IO.Compression</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.IO.Compression.FileSystem</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Json.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Net.Http.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Numerics.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Runtime.Serialization.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ServiceModel.dll</td>
    <td class="tg-yw4l">Pila WCF que se dan en <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ServiceModel.Internals.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ServiceModel.Web.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a>, además de los tipos de los espacios de nombres siguientes: <br>Sistema<br>System.ServiceModel.Channels<br>System.ServiceModel.Description<br>System.ServiceModel.Web</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Transactions.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a>; forma parte de <a href="~/ios/data-cloud/system.data.md">System.Data</a> admite.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Web.Services.dll</td>
    <td class="tg-yw4l">Servicios Web básicos del perfil de .NET 3.5, con las características de servidor quitado.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Windows.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Xml.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Xml.Linq.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Xml.Serialization.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Xamarin.iOS.dll</td>
    <td class="tg-yw4l">Este ensamblado contiene el enlace de C# a la API CocoaTouch. Solo se utiliza en los proyectos de iOS unificado.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Java.Interop.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Android.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Android.Export.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Posix.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.EnterpriseServices.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Xamarin.Android.NUnitLite.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.CompilerServices.SymbolWriter.dll</td>
    <td class="tg-yw4l">Los autores de compiladores.</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Xamarin.Mac.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Drawing.dll</td>
    <td class="tg-yw4l">System.Drawing API - solo API clásico.<br>System.Drawing no se admite en la API unificada para la Xamarin.Mac .NET 4.5 o marcos de trabajo móviles.<br>Puede agregarse compatibilidad de System.Drawing para iOS y OS X mediante el <a href="https://github.com/mono/sysdrawing-coregraphics">sysdrawing coregraphics</a> biblioteca</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-p7et">✓</td>
  </tr>
</table>