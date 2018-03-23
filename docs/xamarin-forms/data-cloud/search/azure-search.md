---
title: Buscar datos con búsqueda de Azure
description: Búsqueda de Azure es un servicio de nube que proporciona la indización y las capacidades de los datos cargados de consulta. Esto quita los requisitos de infraestructura y la complejidad del algoritmo de búsqueda tradicionalmente asociadas con la implementación de la funcionalidad de búsqueda en una aplicación. Este artículo demuestra cómo usar la biblioteca de búsqueda de Microsoft Azure para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 24db1404e218eea86356f9bbc004e7d5850c2e7a
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="searching-data-with-azure-search"></a>Buscar datos con búsqueda de Azure

_Búsqueda de Azure es un servicio de nube que proporciona la indización y las capacidades de los datos cargados de consulta. Esto quita los requisitos de infraestructura y la complejidad del algoritmo de búsqueda tradicionalmente asociadas con la implementación de la funcionalidad de búsqueda en una aplicación. Este artículo demuestra cómo usar la biblioteca de búsqueda de Microsoft Azure para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

Datos se almacenan en la búsqueda de Azure como índices y documentos. Un *índice* es un almacén de datos que se pueden buscar por el servicio de búsqueda de Azure y es conceptualmente similar a una tabla de base de datos. A *documento* es una unidad única de datos permite realizar búsquedas en un índice y es conceptualmente similar a una fila de la base de datos. Cuándo cargar documentos y enviar consultas de búsqueda en la búsqueda de Azure, las solicitudes se realizan en un índice específico en el servicio de búsqueda.

Cada solicitud realizada a la búsqueda de Azure debe incluir el nombre del servicio y una clave de API. Hay dos tipos de clave de API:

- *Las claves de administración* conceder derechos completos para todas las operaciones. Esto incluye la administración del servicio, crear y eliminar índices y orígenes de datos.
- *Las claves de consulta* conceder acceso de solo lectura a índices y documentos y debe utilizarse con las aplicaciones que emiten solicitudes de búsqueda.

La solicitud más comunes en la búsqueda de Azure consiste en ejecutar una consulta. Hay dos tipos de consulta que se pueden enviar:

- A *búsqueda* consulta busca uno o más elementos de todos los campos de búsqueda en un índice. Las consultas de búsqueda se compilan con la sintaxis simplificada, o la sintaxis de consulta de Lucene. Para obtener más información, consulte [sintaxis de consulta Simple en búsqueda de Azure](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), y [Lucene de sintaxis de consulta de búsqueda de Azure](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- A *filtro* consulta evalúa una expresión booleana en todos los campos filtrables en un índice. Filtrar las consultas se compilan con un subconjunto del lenguaje de filtro de OData. Para obtener más información, consulte [sintaxis de expresiones de OData para búsqueda de Azure](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Las consultas de búsqueda y filtrar las consultas pueden utilizarse por separado o conjuntamente. Cuando se usan juntos, la consulta de filtro se aplica primero a todo el índice y, a continuación, se realiza la consulta de búsqueda en los resultados de la consulta de filtro.

Búsqueda de Azure también admite recuperar sugerencias basadas en la entrada de búsqueda. Para obtener más información, consulte [sugerencia consultas](#suggestions).

## <a name="setup"></a>Programa de instalación

El proceso para la integración de búsqueda de Azure en una aplicación de Xamarin.Forms es como sigue:

1. Crear un servicio de búsqueda de Azure. Para obtener más información, consulte [crear un servicio de búsqueda de Azure mediante el Portal de Azure](/azure/search/search-create-service-portal/).
1. Quitar Silverlight como una plataforma de destino de la biblioteca de clases portables (PCL) de solución de Xamarin.Forms. Esto puede realizarse si cambia el perfil PCL a cualquier perfil que admite el desarrollo multiplataforma, pero no es compatible con Silverlight, como perfil de 151 o 92.
1. Agregar el [biblioteca de búsqueda de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) paquete NuGet para el proyecto PCL en la solución de Xamarin.Forms.

Después de realizar estos pasos, la API de biblioteca de búsqueda de Microsoft puede usarse para administrar los orígenes de datos e índices de búsqueda, cargar y administrar documentos y ejecutar consultas.

## <a name="creating-the-azure-search-index"></a>Crear el índice de búsqueda de Azure

Debe definir un esquema de índice que se asigna a la estructura de los datos que se va a buscar. Esto puede ser realizado en el Portal de Azure, o mediante programación utilizando la `SearchServiceClient` clase. Esta clase administra las conexiones con búsqueda de Azure y puede usarse para crear un índice. En el ejemplo de código siguiente se muestra cómo crear una instancia de esta clase:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

El `SearchServiceClient` sobrecarga de constructor toma un nombre de servicio de búsqueda y un `SearchCredentials` objeto como argumentos, con el `SearchCredentials` ajuste de objeto el *clave de administrador* para el servicio de búsqueda de Azure. El *clave de administración* es necesario para crear un índice.

> [!NOTE]
>  Una sola `SearchServiceClient` instancia debe usarse en una aplicación para evitar que se abra demasiadas conexiones en la búsqueda de Azure.

Un índice se define mediante la `Index` del objeto, como se muestra en el ejemplo de código siguiente:

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

El `Index.Name` propiedad debe establecerse en el nombre del índice y el `Index.Fields` propiedad debe establecerse en una matriz de `Field` objetos. Cada `Field` instancia especifica un nombre, un tipo y las propiedades, que especifican cómo se utiliza el campo. Estas propiedades incluyen:

- `IsKey` : indica si el campo es la clave del índice. Sólo un campo en el índice de tipo `DataType.String`, se debe designar como el campo de clave.
- `IsFacetable` : indica si es posible realizar la navegación por facetas en este campo. El valor predeterminado es `false`.
- `IsFilterable` : indica si el campo se puede usar en las consultas de filtro. El valor predeterminado es `false`.
- `IsRetrievable` : indica si se puede recuperar el campo en los resultados de búsqueda. El valor predeterminado es `true`.
- `IsSearchable` : indica si el campo se incluye en las búsquedas de texto completo. El valor predeterminado es `false`.
- `IsSortable` : indica si el campo se puede utilizar en `OrderBy` expresiones. El valor predeterminado es `false`.

> [!NOTE]
> Cambiar un índice después de su implementación implica la regeneración y volver a cargar los datos.

Un `Index` objeto opcionalmente, puede especificar un `Suggesters` propiedad, que define los campos en el índice que se usará para admitir Autocompletar o las consultas de sugerencias de búsqueda. El `Suggesters` propiedad debe establecerse en una matriz de `Suggester` objetos que definen los campos que se utilizan para generar resultados de la sugerencia de la búsqueda.

Después de crear el `Index` de objeto, el índice se crea mediante una llamada a `Indexes.Create` en el `SearchServiceClient` instancia.

> [!NOTE]
> Al crear un índice de una aplicación que debe mantenerse con capacidad de respuesta, use el `Indexes.CreateAsync` método.

Para obtener más información, consulte [crear un índice de búsqueda de Azure mediante el SDK de .NET](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Al eliminar el índice de búsqueda de Azure

Un índice puede eliminarse mediante una llamada a `Indexes.Delete` en el `SearchServiceClient` instancia:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Cargar los datos en el índice de búsqueda de Azure

Después de definir el índice, se pueden cargar datos a mediante uno de dos modelos:

- **Modelo de extracción** : datos periódicamente es ingestión desde base de datos de Azure Cosmos, la base de datos de SQL Azure, almacenamiento de blobs de Azure o SQL Server hospedado en una máquina Virtual de Azure.
- **Modelo de inserción** – datos se envían mediante programación al índice. Éste es el modelo adoptado en este artículo.

Un `SearchIndexClient` para importar datos en el índice, se debe crear la instancia. Esto puede realizarse mediante una llamada a la `SearchServiceClient.Indexes.GetClient` método, como se muestra en el ejemplo de código siguiente:

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

Datos que se importarán en el índice se empaquetan como un `IndexBatch` objeto, que encapsula una colección de `IndexAction` objetos. Cada `IndexAction` instancia contiene un documento y una propiedad que indica qué acción va a realizar en el documento de búsqueda de Azure. En el ejemplo de código anterior, el `IndexAction.Upload` acción se especifica, los resultados en el documento que se va a insertar en el índice si es nuevo, o sustituya si ya existe. El `IndexBatch` objeto, a continuación, se envía al índice mediante una llamada a la `Documents.Index` método en la `SearchIndexClient` objeto. Para obtener información acerca de otras acciones de indización, consulte [decidir qué acción de indización que se utilizará](/azure/search/search-import-data-dotnet#ii-decide-which-indexing-action-to-use).

> [!NOTE]
> Solo 1.000 documentos pueden incluirse en una sola solicitud de indización.

Tenga en cuenta que en el ejemplo de código anterior, el `monkeyList` colección se crea como un objeto anónimo de una colección de `Monkey` objetos. Esto crea datos de la `id` campo y resuelve la asignación de mayúsculas y minúsculas Pascal `Monkey` nombres de campo de índice de búsqueda de nombres de propiedades para mayúsculas y minúsculas camel. Como alternativa, puede que esta asignación también se puede lograr mediante la adición de la `[SerializePropertyNamesAsCamelCase]` atribuir a la `Monkey` clase.

Para obtener más información, consulte [cargar datos en búsqueda de Azure con el SDK de .NET](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Consultar el índice de búsqueda de Azure

Un `SearchIndexClient` para consultar un índice, se debe crear la instancia. Cuando una aplicación ejecuta consultas, es aconsejable seguir el principio de privilegios mínimos y crear un `SearchIndexClient` directamente, pasando el *clave de consulta* como un argumento. Esto garantiza que los usuarios tengan acceso de solo lectura a índices y documentos. Este enfoque se muestra en el ejemplo de código siguiente:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

El `SearchIndexClient` sobrecarga de constructor toma un nombre de servicio de búsqueda, el nombre de índice y un `SearchCredentials` objeto como argumentos, con el `SearchCredentials` ajuste de objeto el *clave de consulta* para el servicio de búsqueda de Azure.

### <a name="search-queries"></a>Consultas de búsqueda

El índice se puede consultar mediante una llamada a la `Documents.SearchAsync` método en el `SearchIndexClient` de instancia, como se muestra en el ejemplo de código siguiente:

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

El `SearchAsync` método toma un argumento de texto de búsqueda y una función opcional `SearchParameters` objeto que puede usarse para refinar la consulta. Una consulta de búsqueda se especifica como el argumento de texto de búsqueda, aunque se puede especificar una consulta de filtro estableciendo la `Filter` propiedad de la `SearchParameters` argumento. El ejemplo de código siguiente se muestra que ambos tipos de consulta:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Esta consulta de filtro se aplica a todo el índice y quita los documentos de los resultados donde el `location` campo no es igual a China y no es igual a Vietnam. Después de filtrado, la consulta de búsqueda se realiza en los resultados de la consulta de filtro.

> [!NOTE]
> Para filtrar sin buscar, pasar `*` como el argumento de texto de búsqueda.

El `SearchAsync` método devuelve un `DocumentSearchResult` objeto que contiene los resultados de la consulta. Este objeto es de tipo enumerado con cada `Document` objeto que se crean como un `Monkey` objeto y se agrega a la `Monkeys` `ObservableCollection` para su presentación. Los siguientes capturas de pantalla muestran búsqueda resultados de la consulta procedentes de la búsqueda de Azure:

![](azure-search-images/search.png "Resultados de la búsqueda")

Para obtener más información acerca de la búsqueda y el filtrado, consulte [consultar el índice de búsqueda de Azure mediante el SDK de .NET](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Consultas de sugerencia

Búsqueda de Azure permite algunas sugerencias que se solicitó en función de una consulta de búsqueda, mediante una llamada a la `Documents.SuggestAsync` método en el `SearchIndexClient` instancia. Esto último se muestra en el ejemplo de código siguiente:

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

El `SuggestAsync` método toma un argumento de texto de búsqueda, el nombre del proveedor de sugerencias para utilizar (que se define en el índice), y una función opcional `SuggestParameters` objeto que puede usarse para refinar la consulta. El `SuggestParameters` instancia establece las siguientes propiedades:

- `UseFuzzyMatching` : cuando se establece en `true`, búsqueda de Azure encontrará sugerencias incluso si hay un carácter sustituye o falta en el texto de búsqueda.
- `HighlightPreTag` : la etiqueta que se antepone al aciertos de sugerencia.
- `HighlightPostTag` : la etiqueta que se anexa a la sugerencia de aciertos.
- `MinimumCoverage` : representa el porcentaje del índice que debe cubrirse por una consulta de sugerencia para la consulta notifica un estado correcto. El valor predeterminado es 80.
- `Top` : el número de sugerencias para recuperar. Debe ser un entero entre 1 y 100, con un valor predeterminado de 5.

El efecto general es que los 10 resultados principales del índice se devolverá con resaltado y los resultados incluirán documentos que contengan una ortografía similar términos de búsqueda.

El `SuggestAsync` método devuelve un `DocumentSuggestResult` objeto que contiene los resultados de la consulta. Este objeto es de tipo enumerado con cada `Document` objeto que se crean como un `Monkey` objeto y se agrega a la `Monkeys` `ObservableCollection` para su presentación. Las capturas de pantalla siguientes muestran los resultados de sugerencia procedentes de la búsqueda de Azure:

![](azure-search-images/suggest.png "Resultados de la sugerencia")

Tenga en cuenta que en la aplicación de ejemplo, el `SuggestAsync` método solo se invoca cuando el usuario termina de entrada de un término de búsqueda. Sin embargo, también se puede utilizar para admitir las consultas de búsqueda Autocompletar mediante la ejecución en cada keypress.

## <a name="summary"></a>Resumen

Este artículo muestra cómo usar la biblioteca de búsqueda de Microsoft Azure para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms. Búsqueda de Azure es un servicio de nube que proporciona la indización y las capacidades de los datos cargados de consulta. Esto quita los requisitos de infraestructura y la complejidad del algoritmo de búsqueda tradicionalmente asociadas con la implementación de la funcionalidad de búsqueda en una aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Búsqueda de Azure (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Documentación de búsqueda de Azure](/azure/search/)
- [Biblioteca de búsqueda de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)
