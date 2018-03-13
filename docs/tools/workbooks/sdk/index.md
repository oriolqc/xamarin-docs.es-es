---
title: "Introducción a los libros de Xamarin SDK"
ms.topic: article
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 3978b046a8ab4d42cbf86bf524452a033b5dbb4d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introducción a los libros de Xamarin SDK

Este documento proporciona a una guía rápida para empezar a trabajar con el desarrollo de aplicaciones integradas para los libros de Xamarin. Gran parte de esto funcionará con los libros de Xamarin estable, pero **cargar aplicaciones integradas a través de paquetes de NuGet solo se admite en los libros 1.3**, en el canal alfa en el momento de escribir este artículo.

# <a name="general-overview"></a>Información general

Integraciones de Xamarin libros son pequeñas bibliotecas que utilizan el [ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK para integrarse con los libros de Xamarin y el Inspector de agentes que proporcionan una experiencia mejorada.

Hay 3 pasos principales para empezar a trabajar con el desarrollo de una integración: se le indican a continuación.

## <a name="creating-the-integration-project"></a>Crear el proyecto de integración

Bibliotecas de integración se desarrollan mejor como bibliotecas multiplataforma. Dado que desea proporcionar la mejor integración en todos los agentes disponibles, pasado y futuro, desea elegir un conjunto de bibliotecas ampliamente compatible. Se recomienda usar la plantilla "Biblioteca Portable" para el soporte más amplio:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Plantilla de biblioteca Portable Visual Studio para Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Biblioteca Portable plantilla Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

En Visual Studio, querrá asegurarse de que selecciona las siguientes plataformas de destino de la biblioteca portable:

[![Biblioteca Portable plataformas Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Una vez creado el proyecto de biblioteca, agregue una referencia a nuestro `Xamarin.Workbooks.Integration` NuGet biblioteca mediante el Administrador de paquetes de NuGet.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![NuGet Visual Studio para Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Desea eliminar la clase vacía que se crea automáticamente como parte del proyecto, no va a necesitar, para esto. Tras seguir estos pasos, está listo para empezar a crear la integración.

## <a name="building-an-integration"></a>Creación de una integración

Vamos a crear una integración sencilla. Realmente querido el color verde, por lo que vamos a agregar el color verde como una representación para cada objeto. En primer lugar, cree una nueva clase denominada `SampleIntegration`y hacer que implementar nuestro [ `IAgentIntegration` ] [ integration-type] interfaz:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

Lo que queremos hacer es agregar un [representación](~/tools/workbooks/sdk/representations.md) para cada objeto que es el color verde. Haremos esto utilizando un proveedor de representación. Proveedores de heredar de la [ `RepresentationProvider` ] [ reppr] clase: para nuestra, solo es necesario invalidar [ `ProvideRepresentations` ] [ prrep]:

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

Le estamos devolver un [ `Color` ] [ color], un pregeneradas tipo de representación en nuestro SDK.
Observará que el tipo de valor devuelto es un `IEnumerable<object>` &mdash;un proveedor de representación puede devolver muchos representaciones de un objeto. Todos los proveedores de representación se llaman para cada objeto, por lo que es importante para no hacer ninguna suposición sobre qué objetos se han pasado a usted.

El paso final es realmente registrar el proveedor con el agente y saber dónde se encuentra el tipo de integración de los libros. Para registrar el proveedor, agregue este código a la `IntegrateWith` método en la `SampleIntegration` clase hemos creado con anterioridad:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

Establecer el tipo de integración se realiza a través de un atributo de todo el ensamblado. Se puede colocar en el archivo AssemblyInfo.cs o en la misma clase que el tipo de integración para su comodidad:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante el desarrollo, quizá le resulte más conveniente utilizar [ `AddProvider` sobrecargas] [ addprovider] en `RepresentationManager` que le permiten registrar una devolución de llamada simple para proporcionar representaciones dentro de un libro y, a continuación, mover ese código en su `RepresentationProvider` implementación una vez que haya terminado. Un ejemplo para representar un [ `OxyPlot` ] [ oxyplot] `PlotModel` podría ser similar a esto:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Estas API ofrecen una forma rápida de ponerse en marcha, pero no le recomendamos una integración completa de envío solamente con ellos&mdash;proporcionan un control muy preciso sobre cómo se procesan los tipos por el cliente.

Con la representación registrada, está lista para enviar la integración de!

## <a name="shipping-your-integration"></a>La integración de envío

Para enviar la integración, debe agregarlo a un paquete de NuGet.
Se puede distribuir con NuGet de la biblioteca existente, o si va a crear un nuevo paquete, puede usar este archivo de plantilla de NuSpec como punto de partida.
Debe rellenar las secciones relevantes para la integración. La parte más importante es que todos los archivos para la integración deben estar en un `xamarin.interactive` directorio en la raíz del paquete. Esto nos permite encontrar fácilmente todos los archivos pertinentes para la integración, independientemente de si utilizar un paquete existente o cree uno nuevo.

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

Una vez que haya creado el archivo NuSpec, puede empaquetar su NuGet así:

```csharp
nuget pack MyIntegration.nuspec
```

y, a continuación, publicarlo en [NuGet][nugetorg]. Una vez que está allí, podrá hacer referencia a él desde cualquier libro y véalo en acción. En la siguiente captura de pantalla, tenemos los paquetes de la integración de ejemplo que se genera en este documento e instalado el paquete NuGet en un libro:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Libro con la integración](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Libro con la integración](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Observe que no ve ningún `#r` directivas o nada para inicializar la integración, los libros se ocupa de todo esto automáticamente en segundo plano.

## <a name="next-steps"></a>Pasos siguientes

Extraer del repositorio otra documentación para obtener más información acerca de los componentes de móviles que componen el SDK, y nuestro [integraciones de ejemplo](~/tools/workbooks/samples/index.md) para acciones adicionales que puede realizar desde la integración, como proporcionar JavaScript personalizado que se ejecuta en el cliente de los libros.

[integration-type]: /api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: /api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: /api/type/Xamarin.Interactive.Representations.Color/
[xir]: /api/namespace/Xamarin.Interactive.Representations/
[reppr]: /api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: /api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: /api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
