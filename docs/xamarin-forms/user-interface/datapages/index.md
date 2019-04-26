---
title: Xamarin.Forms DataSourceControl
description: Este artículo presentan DataSourceControl Xamarin.Forms, que proporcionan una API para rápidamente y enlazar fácilmente un origen de datos a las vistas precompiladas.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2a74b636a41a72b26776157a774f0a33ef45a075
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61407685"
---
# <a name="xamarinforms-datapages"></a>Xamarin.Forms DataSourceControl

![](~/media/shared/preview.png "Esta API está actualmente en versión preliminar")

> [!IMPORTANT]
> DataSourceControl requiere un [Xamarin.Forms tema](~/xamarin-forms/user-interface/themes/index.md) referencia a representar.

Xamarin.Forms DataSourceControl se anunciaron en Evolve 2016 y están disponible como una vista previa para clientes probar y proporcionar comentarios.

DataSourceControl proporciona una API para enlazar de forma rápida y sencilla un origen de datos a vistas precompiladas. Elementos de lista y páginas de detalle representarán los datos de forma automática y se pueden personalizar utilizando temas.

Para ver cómo funciona la demostración del discurso de apertura evolucionan, consulte el [Guía de introducción](get-started.md).

[![](images/demo-sml.png "Aplicación de ejemplo DataSourceControl")](images/demo.png#lightbox "aplicación de ejemplo DataSourceControl")

## <a name="introduction"></a>Introducción

Orígenes de datos y las páginas de datos asociadas permiten a los desarrolladores rápida y fácilmente consumir un origen de datos admitidos y para representarlo mediante integradas se puede personalizar la interfaz de usuario de scaffolding que con los temas.

DataSourceControl se agrega a una aplicación de Xamarin.Forms mediante la inclusión de la **Xamarin.Forms.Pages** paquete Nuget.

### <a name="data-sources"></a>Orígenes de datos

La versión preliminar tiene algunos orígenes de datos creada previamente disponibles para su uso:

* **JsonDataSource**
* **AzureDataSource** (separe Nuget)
* **AzureEasyTableDataSource** (separe Nuget)

Consulte la [Guía de introducción](get-started.md) para obtener un ejemplo con un `JsonDataSource`.


### <a name="pages--controls"></a>Las páginas y controles

Las siguientes páginas y controles se incluyen para permitir el enlace sencillo para los orígenes de datos proporcionado:

* **ListDataPage** : vea la [Introducción al ejemplo](get-started.md).
* **DirectoryPage** : una lista con agrupación habilitada.
* **PersonDetailPage** : un elemento de datos únicos en la vista personalizada para un tipo de objeto específico (una entrada de contacto).
* **DataView** : una vista para exponer datos procedentes del origen de forma genérica.
* **CardView** : una con el estilo de vista que contiene una imagen, el texto del título y el texto de descripción.
* **HeroImage** : una vista de la representación de imágenes.
* **ListItem** : un pregeneradas vista con un diseño similar a los elementos de lista de Android y iOS nativo.

Consulte la [DataSourceControl controla referencia](controls.md) para obtener ejemplos.



### <a name="under-the-hood"></a>En segundo plano

Un origen de datos de Xamarin.Forms se adhiere a la `IDataSource` interfaz.

La infraestructura de Xamarin.Forms interactúa con un origen de datos a través de las siguientes propiedades:

* `Data` : una lista de solo lectura de elementos de datos que se pueden mostrar.
* `IsLoading` : un valor booleano que indica si los datos se cargan y están disponibles para la representación.
* `[key]` – un indizador para recuperar elementos.

Hay dos métodos `MaskKey` y `UnmaskKey` que puede utilizarse para ocultar (o mostrar) las propiedades de elementos de datos (es decir impida que se procesa).
La clave corresponde a la una propiedad con nombre en el objeto de elemento de datos.
