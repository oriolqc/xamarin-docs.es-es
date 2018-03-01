---
title: Ensamblados
ms.topic: article
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 0a435e80173ca3ccb76dba0719ec2eea6eb72611
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="assemblies"></a>Ensamblados

## <a name="supported-assemblies"></a>Ensamblados compatibles

Se trata de una lista de los ensamblados compatibles con Xamarin para las aplicaciones de Xamarin.tvOS. A continuación, se muestra una lista detallada de estos.  Incluyen algunas omisiones notables `System.EnterpriseServices`, la pila ASP.NET y Windows.Forms.

<table align="center" border="1" cellpadding="1" cellspacing="1" width="90%">
      <tbody>
        <tr>
          <td>
            <strong>Ensamblado</strong>
          </td>
          <td>
            <strong>Added</strong>
          </td>
          <td>
            <strong>Compatibilidad con la API</strong>
          </td>
        </tr>
        <tr>
          <td valign="top">
Mono.CompilerServices.SymbolWriter.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td valign="top">
Los autores de compiladores.
          </td>
        </tr>
        <tr>
          <td valign="top">
Mono.Data.Sqlite.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
Proveedor de ADO.NET para SQLite; vea&nbsp;<a href="~/ios/data-cloud/system.data.md">limitaciones</a>.
          </td>
        </tr>
        <tr>
          <td valign="top">
Mono.Data.Tds.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
Soporte de protocolo TDS; utilizado para&nbsp;<a href="https://developer.xamarin.com/api/namespace/System.Data.SqlClient/" target="_blank">System.Data.SqlClient</a>&nbsp;admite dentro de&nbsp;<a href="~/ios/data-cloud/system.data.md">System.Data</a>.
          </td>
        </tr>
        <tr>
          <td>
Mono.Security.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
Interfaces API criptográficas.
          </td>
        </tr>
        <tr>
          <td valign="top">
monotouch.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
Este ensamblado contiene el&nbsp;<a href="https://developer.xamarin.com/api/root/ios-unified/" target="_blank">enlace C# a la API de CocoaTouch</a>.
          </td>
        </tr>
        <tr>
          <td valign="top">
mscorlib.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
OpenTK.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
El objeto de OpenGL/OpenAL orientada a servicios de API,&nbsp;<a href="https://developer.xamarin.com/api/namespace/OpenGLES/" target="_blank">extendido para proporcionar compatibilidad con dispositivos de iPhone</a>.
          </td>
        </tr>
        <tr>
          <td align="left" valign="top">
System.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <p><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>, además de los tipos de los espacios de nombres siguientes:</p>
    
            <ul>
              <li>System.Collections.Specialized</li>
              <li>System.ComponentModel</li>
              <li>System.ComponentModel.Design</li>
              <li>System.Diagnostics</li>
              <li>System.IO.Compression</li>
              <li>System.Net</li>
              <li>System.Net.Cache</li>
              <li>System.Net.Mail</li>
              <li>System.Net.Mime</li>
              <li>System.Net.NetworkInformation</li>
              <li>System.Net.Security</li>
              <li>System.Net.Sockets</li>
              <li>System.Security.Authentication</li>
              <li>System.Security.Cryptography</li>
              <li>System.Timers</li>
            </ul>
    
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Core.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Data.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a>&nbsp;,&nbsp;<a href="~/ios/data-cloud/system.data.md">con alguna funcionalidad quitado</a>.
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Data.Service.Client.dll </td>
          <td align="center" valign="top">
3.x </td>
          <td>
Cliente de oData completa.
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Drawing </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <p>System.Drawing API - solo API clásico.</p>
            <p><i>System.Drawing no se admite en la API unificada para la Xamarin.Mac .NET 4.5 o marcos de trabajo móviles.</i></p>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Json.dll </td>
          <td align="center" valign="top">
1.1 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Runtime.Serialization.dll </td>
          <td align="center" valign="top">
?
          </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.ServiceModel.dll </td>
          <td align="center" valign="top">
1.1 </td>
          <td>
            <a href="http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services" target="_blank">WCF</a>&nbsp;tal como está presente en la pila&nbsp;<a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.ServiceModel.Web.dll </td>
          <td align="center" valign="top">
?
          </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>, además de los tipos de los espacios de nombres siguientes: <p>&nbsp;</p>
    
            <ul>
              <li>Sistema</li>
              <li>System.ServiceModel.Channels</li>
              <li>System.ServiceModel.Description</li>
              <li>System.ServiceModel.Web</li>
            </ul>
    
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Transactions.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a>; forma parte de&nbsp;<a href="~/ios/data-cloud/system.data.md">System.Data</a>&nbsp;admite.
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Web.Services </td>
          <td align="center" valign="top">
1.1 </td>
          <td>
            <a href="http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services" target="_blank">Los servicios Web de básicos</a>&nbsp;del perfil de .NET 3.5, con las características de servidor quitado.
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Xml.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Xml.Linq.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a><br>
            <br>
&nbsp; </td>
        </tr>
      </tbody>
    </table>

<a name="Summary" />

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

Además de los enlaces de Mac, puede consumir Xamarin.tvOS [bibliotecas de clases Portable de .NET](~/cross-platform/app-fundamentals/pcl.md).



## <a name="related-links"></a>Vínculos relacionados

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
