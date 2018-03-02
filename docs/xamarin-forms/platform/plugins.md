---
title: Complementos
description: "Agregar fácilmente la funcionalidad nativa a las aplicaciones de Xamarin.Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2016
ms.openlocfilehash: ad338e655c1aeb475122c837ca5c805e491f84bc
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="plugins"></a>Complementos

Hay muchas características de plataforma nativa que existen en todas las plataformas, pero tienen API ligeramente diferentes. Los desarrolladores escribir complementos para crear una interfaz abstracta de multiplataforma para aquellas características que también pueden compartir con otros usuarios.

Estas características incluyen: estado de la batería, brújula, sensores de movimiento, ubicación geográfica, texto a voz y mucho más. Complementos permiten estas características para tener acceso fácilmente a las aplicaciones de Xamarin.Forms.

## <a name="finding-and-adding-plugins"></a>Buscar y agregar complementos

La Comunidad de Xamarin creada muchos complementos entre plataformas compatibles con Xamarin.Forms - una colección grande puede encontrarse en:

[**Complementos de Xamarin**](https://github.com/xamarin/plugins)

Para obtener una guía para agregar paquetes de NuGet al proyecto, vea nuestro tutorial en [incluido un paquete de NuGet en el proyecto](/visualstudio/mac/nuget-walkthrough/).


## <a name="creating-plugins"></a>Crear complementos

También es posible crear y publicar sus propios complementos como paquetes de Nuget (y componentes de Xamarin). Muchos complementos existentes son código abierto para que pueda revisar el código para entender cómo han sido writtern.

Por ejemplo, la lista de complementos siguiente es todo el código fuente abierto y corresponden a los ejemplos disponibles en la [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) sección:

- **Texto a voz** por James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/TextToSpeech) y [NuGet  ](https://www.nuget.org/packages/Xam.Plugin.Battery)
- **Estado de la batería** por James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery) y [NuGet](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech/)

Los proyectos de Github pueden proporcionar un buen punto de partida para crear sus propios complementos multiplataforma, como seguir estas instrucciones para [crear un complemento para Xamarin](https://github.com/xamarin/plugins#create-a-plugin-for-xamarin).

### <a name="structuring-cross-platform-plugin-projects"></a>Estructura de proyectos de complemento de multiplataforma

Aunque no hay ningún requisito concreto para diseñar un paquete de NuGet, hay algunas directrices para crear un paquete de aplicaciones multiplataforma.

Un complemento de multiplataforma generalmente debe constar de los siguientes componentes:

- PCL con una interfaz que representa la API para el complemento
- bibliotecas con una implementación de la interfaz de clases de Windows, iOS y Android.

Lectura James Montemagno [entrada de blog](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/) que describe el proceso de creación de complementos para Xamarin.

Es preferible evitar hacer referencia a Xamarin.Forms directamente desde un complemento.
Esto puede crear problemas de conflicto de versiones cuando otros desarrolladores intentan usar el complemento. En su lugar, intente la API de diseño para que se puede utilizar cualquier aplicación Xamarin o. NET.

### <a name="publishing-nuget-packages"></a>Publicación de paquetes de NuGet

Paquetes de NuGet tienen un **nuspec** archivo, que es un archivo xml que define qué partes del proyecto se publican en el paquete. El **nuspec** archivo también incluye información sobre el paquete, como el Id., título y los autores.

Vea [documentación de NuGet](http://docs.nuget.org/create/creating-and-publishing-a-package) para obtener más información sobre cómo crear y publicar paquetes de NuGet.


## <a name="related-links"></a>Vínculos relacionados

- [Crear complementos reutilizables para Xamarin.Forms](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [Usar & desarrollar complementos de Xamarin (vídeo)](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
