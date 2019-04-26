---
title: Editar metadatos de NuGet
description: Este documento describe cómo usar las opciones del proyecto para modificar los metadatos de NuGet para bibliotecas multiplataforma. Describe los metadatos necesarios y opcionales.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3680b02003a844668b0b5c97e5d4c0d296ae3500
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266887"
---
# <a name="editing-nuget-metadata"></a>Editar metadatos de NuGet

_Use las opciones de proyecto para editar los metadatos de NuGet para bibliotecas multiplataforma_

Tipos de proyecto de biblioteca (por ejemplo, PCL o .NET Standard o el nuevo tipo de proyecto de NuGet) tienen un **paquete NuGet** sección la **opciones de proyecto** ventana.

El **metadatos** sección configura los valores utilizados en el [ **.nuspec** archivo de manifiesto de paquete de NuGet](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Información necesaria

El **General** pestaña contiene cuatro campos que se deben especificar para generar un paquete de NuGet:

[![](metadata-images/metadata-general-sml.png "Ventana de metadatos necesarios del paquete de NuGet")](metadata-images/metadata-general.png#lightbox)

- **Id. de** : el identificador del paquete, que debe ser único en Nuget.org (o dondequiera que se distribuirá el paquete). Siga este [orientación](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y utilizar únicamente caracteres que son válidos en una dirección URL (sin espacios y evitar la mayoría de caracteres especiales).
- **Versión** : elija un número de versión coherente con [reglas de control de versiones de NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Los autores** : lista separada por comas de nombres.
- **Descripción** : Introducción a las características del paquete que se muestra cuando los usuarios seleccionan el paquete.

> [!NOTE]
> Acuérdese de incrementar el número de versión al crear nuevas versiones para la distribución de NuGet u otros usuarios.

Para obtener más información, consulte el [referencia de elementos necesario](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) para obtener más información, así como estas instrucciones detallan en [elegir un identificador de paquete único y establecer el número de versión](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y [ Establecimiento de un tipo de paquete](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Se deben especificar todos los campos de esta pestaña; en caso contrario, aparecerá un mensaje de error: _"El proyecto no tiene metadatos de NuGet por lo que no se creará un paquete de NuGet. Los metadatos del paquete NuGet pueden especificarse en la sección de metadatos en las opciones de proyecto"_

## <a name="optional-metadata"></a>Metadatos opcionales

El **detalles** pestaña contiene los campos opcionales que se incluirán en el archivo de manifiesto del paquete de NuGet.

[![](metadata-images/metadata-detail-sml.png "Ventana de metadatos opcionales del paquete de NuGet")](metadata-images/metadata-detail.png#lightbox)

Hacer referencia a la [opcional de elementos de referencia](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) para obtener más información acerca de los campos obligatorios y opcionales.

> [!NOTE]
> Si el paquete de NuGet se distribuye en [NuGet.org](https://www.nuget.org) , se recomienda proporcionar tanta información como sea posible.


## <a name="related-links"></a>Vínculos relacionados

- [Referencia de .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
