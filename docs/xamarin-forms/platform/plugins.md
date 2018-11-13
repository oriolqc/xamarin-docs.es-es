---
title: Consumir y crear complementos de Xamarin.Forms
description: En este artículo se explica cómo utilizar y crear complementos de Xamarin.Forms. Complementos normalmente se usan para exponer fácilmente las características de plataforma nativa.
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/05/2018
ms.openlocfilehash: ac8e5323a2a2e05ac03294bb6919e8dfadc93655
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526525"
---
# <a name="consuming-and-creating-xamarinforms-plugins"></a>Consumir y crear complementos de Xamarin.Forms

Hay muchas características de plataforma nativa que existen en todas las plataformas, pero tienen API ligeramente diferentes. Es una manera para que los desarrolladores pueden usar estas características mediante la creación de una interfaz de multiplataforma abstracta y, a continuación, implementar esa interfaz en las diferentes plataformas. La aplicación de Xamarin.Forms, a continuación, tiene acceso a estas implementaciones de plataforma mediante [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Los desarrolladores pueden compartir este trabajo escribiendo un _complemento_ y publicarlo en NuGet.

> [!NOTE]
> Muchas características multiplataforma estaban disponibles solo a través de complementos ahora forman parte de código abierto **[Xamarin.Essentials](~/essentials/index.md)** biblioteca. Estas características incluyen: estado de la batería, brújula, sensores de movimiento, ubicación geográfica, texto a voz y mucho más. En el futuro, **Xamarin.Essentials** será el origen principal de las características multiplataforma para aplicaciones de Xamarin.Forms. Aunque los desarrolladores todavía pueden crear y publicar complementos, considere la posibilidad de que contribuyen a **Xamarin.Essentials**.

## <a name="finding-and-adding-plugins"></a>Buscar y agregar complementos

La Comunidad de Xamarin creado muchos complementos multiplataforma compatible con Xamarin.Forms. Una colección grande puede encontrarse en:

[**Complementos de Xamarin**](https://github.com/xamarin/XamarinComponents)

Para obtener una guía para agregar paquetes de NuGet al proyecto, consulte nuestro tutorial en [incluir un paquete de NuGet en el proyecto](/visualstudio/mac/nuget-walkthrough/).

## <a name="creating-plugins"></a>Creación de complementos

También es posible crear y publicar sus propios complementos como paquetes de Nuget (y componentes de Xamarin). Muchos complementos existentes son de código abierto para que pueda revisar su código para comprender cómo han sido writtern.

Por ejemplo, la lista de los complementos siguientes son todas de código abierto y que se corresponden con algunos ejemplos en los [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) sección:

- **Texto a voz** James montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/TextToSpeechPlugin) y [NuGet  ](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech)
- **Estado de la batería** James montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/BatteryPlugin) y [NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)

Los proyectos de Github pueden proporcionar un buen punto de partida para crear sus propios complementos multiplataforma, como seguir estas instrucciones para [crear un complemento para Xamarin](https://github.com/xamarin/XamarinComponents#create-a-plugin-for-xamarin).

### <a name="structuring-cross-platform-plugin-projects"></a>Estructuración de proyectos de complemento entre plataformas

Aunque no hay ningún requisito concreto para diseñar un paquete de NuGet, hay algunas directrices para crear un paquete para aplicaciones multiplataforma.

En el pasado, un complemento multiplataforma generalmente formada por los siguientes componentes:

- PCL con una interfaz que representa la API para el complemento,
- las bibliotecas con una implementación de la interfaz de clase de iOS, Android y plataforma Universal de Windows (UWP).

Lectura James Montemagno [entrada de blog](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/) que describe el proceso de creación de complementos para Xamarin.

Más recientemente, se pueden crear complementos con una sola plataforma múltiples. Este enfoque se describe en de James Montemagno [entrada de blog](https://montemagno.com/converting-xamarin-libraries-to-sdk-style-multi-targeted-projects/). Este enfoque se usa en los complementos de James Montemagno vinculados anteriormente, y es también utiliza el formato en **Xamarin.Essentials**.

Es preferible evitar hacer referencia a Xamarin.Forms directamente desde un complemento.
Esto puede crear problemas de conflicto de versiones cuando otros desarrolladores intentan usar el complemento. En su lugar, pruebe a diseñar la API para que se puede usar cualquier aplicación de Xamarin o. NET.

### <a name="publishing-nuget-packages"></a>Publicación de paquetes de NuGet

Los paquetes de NuGet tienen un **nuspec** archivo, que es un archivo xml que define qué partes del proyecto se publican en el paquete. El **nuspec** archivo también incluye información sobre el paquete, como el identificador, título y autores.

Consulte [documentación de NuGet](/nuget/create-packages/creating-a-package.md) para obtener más información sobre la creación y publicación de paquetes de NuGet.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de complementos reutilizables para Xamarin.Forms](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [Con el mi & desarrollar complementos para Xamarin (vídeo)](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
