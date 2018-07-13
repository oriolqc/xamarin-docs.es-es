---
title: Plantillas de datos de Xamarin.Forms
description: Un DataTemplate se utiliza para especificar la apariencia de los datos en los controles admitidos y normalmente se enlaza a los datos que se mostrará.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 771ae22c3e28a4fce758bbfd6a3bd63bafb75e53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994976"
---
# <a name="xamarinforms-data-templates"></a>Plantillas de datos de Xamarin.Forms

_Un DataTemplate se utiliza para especificar la apariencia de los datos en los controles admitidos y normalmente se enlaza a los datos que se mostrará._

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

Las plantillas de datos de Xamarin.Forms proporcionan la capacidad para definir la presentación de datos en los controles admitidos. En este artículo se proporciona una introducción a las plantillas de datos, Examinar por qué son necesarios.

## <a name="creating-a-datatemplatecreatingmd"></a>[Crear un DataTemplate](creating.md)

Plantillas de datos se pueden crear en línea, en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), o desde un tipo personalizado o un tipo de celda adecuado de Xamarin.Forms. Si no hay ninguna necesidad de volver a usar la plantilla de datos en otro lugar, debe usarse una plantilla en línea. Como alternativa, se puede reutilizar una plantilla de datos si se define como un tipo personalizado, o como un recurso de nivel de página o el nivel de aplicación de nivel de control.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Creación de un DataTemplateSelector](selector.md)

Un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) se puede usar para elegir un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) en tiempo de ejecución según el valor de una propiedad enlazada a datos. Esto permite que varios `DataTemplate` instancias al aplicarse en el mismo tipo de objeto, para personalizar la apariencia de los objetos concretos. En este artículo se muestra cómo crear y consumir un `DataTemplateSelector`.


## <a name="related-links"></a>Vínculos relacionados

- [Plantillas de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
