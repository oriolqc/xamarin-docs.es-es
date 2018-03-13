---
title: DataPages
ms.topic: article
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 60973068b56ea4160c3e5ae53d387b063c601afe
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="datapages"></a>DataPages

![](~/media/shared/preview.png "Esta API está actualmente en vista previa")

> [!IMPORTANT]
> DataPages requiere un [Xamarin.Forms tema](~/xamarin-forms/user-interface/themes/index.md) referencia a presentar.

Xamarin.Forms DataPages se anunciaron en evolucionar 2016 y están disponibles como una vista previa de los clientes probar y proporcionar comentarios.

DataPages proporciona una API para rápida y fácilmente enlazar un origen de datos a las vistas previamente integradas. Elementos de lista y páginas de detalles se representarán automáticamente los datos y se pueden personalizar con temas.

Para ver cómo funciona la demostración de keynote evolucionar, visite la [Guía de introducción](get-started.md).

[![](images/demo-sml.png "Aplicación de ejemplo DataPages")](images/demo.png#lightbox "aplicación de ejemplo DataPages")

## <a name="introduction"></a>Introducción

Orígenes de datos y las páginas de datos asociadas permiten a los desarrolladores rápida y fácilmente consumir un origen de datos admitidos y representar mediante integrados que se puede personalizar la interfaz de usuario que scaffolding con temas.

DataPages se agregan a una aplicación de Xamarin.Forms mediante la inclusión de la **Xamarin.Forms.Pages** paquete Nuget.

### <a name="data-sources"></a>Orígenes de datos

La vista previa tiene algunos orígenes de datos creada previamente disponibles para su uso:

* **JsonDataSource**
* **AzureDataSource** (separe Nuget)
* **AzureEasyTableDataSource** (separe Nuget)

Consulte la [Guía de introducción](get-started.md) para obtener un ejemplo de uso un `JsonDataSource`.


### <a name="pages--controls"></a>Las páginas y controles

Las siguientes páginas y controles se incluyen para permitir el enlace sencillo para los orígenes de datos proporcionado:

* **ListDataPage** : consulte la [Introducción ejemplo](get-started.md).
* **DirectoryPage** : una lista con agrupación habilitado.
* **PersonDetailPage** : un elemento de datos únicos en vista personalizada para un tipo de objeto específico (una entrada de contacto).
* **DataView** : una vista para exponer datos procedentes del origen de forma genérica.
* **CardView** : una con el estilo de vista que contiene una imagen, el texto del título y el texto de descripción.
* **HeroImage** : una vista de la representación de imágenes.
* **ListItem** : una vista con un diseño similar al nativas para iOS y Android enumere elementos han creado con anterioridad.

Consulte la [DataPages controla referencia](controls.md) para obtener ejemplos.



### <a name="under-the-hood"></a>En segundo plano

Un origen de datos de Xamarin.Forms se adhiere a la `IDataSource` interfaz.

La infraestructura de Xamarin.Forms interactúa con un origen de datos a través de las siguientes propiedades:

* `Data` : una lista de solo lectura de elementos de datos que se pueden mostrar.
* `IsLoading` : un valor booleano que indica si los datos se cargan y están disponibles para la representación.
* `[key]` : un indizador para recuperar elementos.

Existen dos métodos `MaskKey` y `UnmaskKey` que se puede utilizar para ocultar (o mostrar) las propiedades de elementos de datos (es decir impida que se representen).
La clave corresponde a la una propiedad con nombre en el objeto de elemento de datos.

