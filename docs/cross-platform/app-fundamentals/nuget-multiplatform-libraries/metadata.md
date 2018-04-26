---
title: Edición de metadatos de NuGet
description: Utilice las opciones de proyecto para modificar los metadatos de NuGet para bibliotecas multiplataforma
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6d30f564d54b96d358d37059f9dababaf8f3314e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="editing-nuget-metadata"></a>Edición de metadatos de NuGet

_Utilice las opciones de proyecto para modificar los metadatos de NuGet para bibliotecas multiplataforma_

Tipos de proyecto de biblioteca (por ejemplo, PCL o .NET estándar o el nuevo tipo de proyecto de NuGet) tienen un **paquete NuGet** sección la **Project Options** ventana.

El **metadatos** sección configura los valores utilizados en el [ **.nuspec** archivo de manifiesto de paquete de NuGet](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Información necesaria

El **General** pestaña contiene cuatro campos que deben especificarse para generar un paquete de NuGet:

[![](metadata-images/metadata-general-sml.png "Ventana de metadatos necesarios del paquete de NuGet")](metadata-images/metadata-general.png#lightbox)

- **Id. de** : el identificador del paquete, que debe ser único en Nuget.org (o siempre que se distribuirá el paquete). Siga este [instrucciones](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y utilizar únicamente caracteres que son válidos en una dirección URL (sin espacios y evitar la mayoría de caracteres especiales).
- **Versión** : elija un número de versión coherente con [reglas de control de versiones de NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Los autores** : lista separada por comas de nombres.
- **Descripción** : información general de las características del paquete que se muestra cuando los usuarios van a seleccionar el paquete.

> [!NOTE]
> Recuerde que debe incrementar el número de versión al generar nuevas versiones para distribuirlo a otros usuarios o de NuGet.

Para obtener más información, consulte el [referencia de elementos necesarios](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) para obtener más información, así como estas instrucciones detallan en [elegir un identificador de paquete único y establecer el número de versión](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) y [ Un tipo de paquete de configuración](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Deben especificarse todos los campos de esta pestaña; en caso contrario, aparecerá un mensaje de error: _"el proyecto no tiene metadatos de NuGet por lo que no se creará un paquete de NuGet. Metadatos de paquete de NuGet se pueden especificar en la sección de metadatos en las opciones de proyecto"_

## <a name="optional-metadata"></a>Metadatos opcionales

El **detalles** pestaña contiene los campos opcionales que se incluirá en el archivo de manifiesto del paquete de NuGet.

[![](metadata-images/metadata-detail-sml.png "Ventana de metadatos opcional del paquete de NuGet")](metadata-images/metadata-detail.png#lightbox)

Hacer referencia a la [opcional de elementos de referencia](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) para obtener más información acerca de los campos obligatorios y opcionales.

> [!NOTE]
> Si el paquete de NuGet se distribuye en [NuGet.org](https://www.nuget.org) se recomienda proporcionar tanta información como sea posible.


## <a name="related-links"></a>Vínculos relacionados

- [Referencia de .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
