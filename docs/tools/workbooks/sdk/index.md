---
title: Introducción a los libros de Xamarin SDK
description: Este documento describe cómo empezar a trabajar con el SDK de los libros de Xamarin, que puede usarse para desarrollar aplicaciones integradas para Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 5800e98acbff147735ae4a6125979a4b47be2367
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382757"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introducción a los libros de Xamarin SDK

Este documento proporciona a una guía rápida introducción al desarrollo de aplicaciones integradas para Xamarin Workbooks. Gran parte de esto funcionará con los libros de Xamarin estable, pero **cargando integraciones a través de paquetes de NuGet solo se admite en los libros 1.3**, en el canal alfa en el momento de escribir.

## <a name="general-overview"></a>Información general

Integraciones de Xamarin Workbooks son pequeñas bibliotecas que utilizan el [ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK para integrar con el Inspector y Workbooks de Xamarin agentes para proporcionar experiencias mejoradas.

Hay 3 pasos principales para empezar a desarrollar una integración, que le resumimos aquí.

## <a name="creating-the-integration-project"></a>Crear el proyecto de integración

Bibliotecas de integración se desarrollan mejor como bibliotecas multiplataforma. Dado que desea proporcionar la mejor integración en todos los agentes disponibles, pasado y futuro, desea elegir un conjunto de bibliotecas ampliamente compatible. Se recomienda usar la plantilla "Biblioteca Portable" para el soporte más amplio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Plantilla de biblioteca Portable Visual Studio para Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Biblioteca Portable plantilla Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

En Visual Studio, querrá asegurarse de que seleccione las siguientes plataformas de destino para la biblioteca portable:

[![Biblioteca Portable plataformas Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Una vez creado el proyecto de biblioteca, agregue una referencia a nuestro `Xamarin.Workbooks.Integration` biblioteca NuGet a través del Administrador de paquetes de NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![NuGet Visual Studio para Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Desea eliminar la clase vacía que se crea automáticamente como parte del proyecto, no va a necesitar, para esto. Una vez que haya realizado estos pasos, está listo para empezar a crear la integración.

## <a name="building-an-integration"></a>Creación de una integración

Vamos a crear una integración sencilla. Realmente amamos el color verde, así que agregaremos el color verde como una representación de cada objeto. En primer lugar, cree una nueva clase denominada `SampleIntegration`y conviértalo en implementar nuestro [ `IAgentIntegration` ] [ integration-type] interfaz:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

Lo que queremos hacer es agregar un [representación](~/tools/workbooks/sdk/representations.md) para cada objeto que es un color verde. Haremos esto con un proveedor de representación. Proveedores de heredan de la [ `RepresentationProvider` ] [ reppr] clase — para nuestra, nos basta con reemplazar [ `ProvideRepresentations` ] [ prrep]:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

Devuelve un [ `Color` ] [ color], una compilada previamente el tipo de representación en nuestro SDK.
Observará que el tipo de valor devuelto es un `IEnumerable<object>` &mdash;un proveedor de representación puede devolver varias representaciones de un objeto. Todos los proveedores de representación se denominan para cada objeto, por lo que es importante no realizar ninguna suposición acerca de los objetos que se pasen a usted.

El último paso es registrar el proveedor con el agente realmente e indican dónde encontrar el tipo de integración de los libros. Para registrar el proveedor, agregue este código a la `IntegrateWith` método en el `SampleIntegration` clase que creamos anteriormente:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

Establecer el tipo de integración se realiza a través de un atributo de todo el ensamblado. Se puede colocar en su AssemblyInfo.cs o en la misma clase que el tipo de integración para su comodidad:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante el desarrollo, le resultará más conveniente usar [ `AddProvider` sobrecargas] [ addprovider] en `RepresentationManager` que le permiten registrar una devolución de llamada simple para proporcionar representaciones dentro de un libro, y, a continuación, pasar ese código en su `RepresentationProvider` implementación una vez que haya terminado. Un ejemplo para representar un [ `OxyPlot` ] [ oxyplot] `PlotModel` podría tener este aspecto:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Estas API ofrecen una forma rápida de empezar a trabajar, pero no lo recomendaría una integración completa de envío solo con ellos&mdash;proporcionan un control muy preciso sobre cómo se procesan los tipos por el cliente.

Con la representación registrada, la integración está lista para enviar.

## <a name="shipping-your-integration"></a>La integración de envío

Para enviar la integración, deberá agregarlo a un paquete de NuGet.
Se pueden enviar con NuGet de la biblioteca existente, o si va a crear un nuevo paquete, puede usar este archivo .nuspec de plantilla como punto de partida.
Deberá rellenar las secciones relevantes para su integración. La parte más importante es que todos los archivos para la integración deben estar en un `xamarin.interactive` directorio en la raíz del paquete. Esto nos permite encontrar fácilmente todos los archivos pertinentes para la integración, independientemente de si un paquete existente o crear uno nuevo.

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

Una vez que haya creado el archivo .nuspec, puede empaquetar su NuGet así:

```csharp
nuget pack MyIntegration.nuspec
```

y, a continuación, publicarlo en [NuGet][nugetorg]. Una vez allí, podrá hacer referencia a él desde cualquier libro y verlo en acción. En la siguiente captura de pantalla, tenemos los paquetes de la integración de ejemplo que hemos compilado en este documento y había instalado el paquete de NuGet en un libro:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Libro con la integración](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Libro con la integración](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Tenga en cuenta que no ve ningún `#r` directivas ni nada para inicializar la integración: los libros se ha ocupado de todo eso por usted en segundo plano.

## <a name="next-steps"></a>Pasos siguientes

Consulte otra documentación para obtener más información acerca de las piezas móviles que componen el SDK, y nuestro [integraciones de ejemplo](~/tools/workbooks/samples/index.md) para aspectos adicionales que puede hacer desde la integración, como proporcionar código JavaScript personalizado que se ejecuta en el cliente de los libros.

[integration-type]: https://developer.xamarin.com/api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[xir]: https://developer.xamarin.com/api/namespace/Xamarin.Interactive.Representations/
[reppr]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
