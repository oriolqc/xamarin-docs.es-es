---
title: Plataforma de destino
description: "Este artículo tratan los marcos de destino (bibliotecas de clases Base) disponibles para Xamarin.Mac y las implicaciones de su uso en el proyecto Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: ac4644f65486d70fcbb7da1a03574fb238348313
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="target-framework"></a>Plataforma de destino

_Este artículo tratan los marcos de destino (bibliotecas de clases Base) disponibles para Xamarin.Mac y las implicaciones de su uso en el proyecto Xamarin.Mac._

![Destino de opciones de marco de trabajo para Xamarin.Mac](target-framework-images/select-target.png "opciones de marco de trabajo para Xamarin.Mac de destino")

## <a name="background"></a>Fondo

Cada programa de .NET o la biblioteca depende de la funcionalidad proporcionada por la biblioteca de clases Base (BCL). Este BCL incluye ensamblados como mscorlib, sistema, System.Net.Http y System.Xml que proporcionan la funcionalidad común integrada en todos los lenguajes. NET.

En los años, han desarrollado varias versiones distintas de este BCL, optimizado para los distintos casos de uso. La BCL "escritorio" incluye un conjunto más completo de las bibliotecas que podría ser demasiado pesado para otros casos de uso, mientras que mobile se centra en la verificación de que las API son seguras para vincular, lo que elimina el código no utilizado para reducir la superficie de la aplicación.

Una de las repercusiones de estos marcos de destino diferente, más importantes es que todos los ensamblados en un programa determinado *debe* ensamblados compatibles de BCL de destino. Si este no era el caso, podría tener dos ensamblados vinculados con distintas versiones de la **System.dll** desacuerdo sobre la firma de un tipo determinado. Una biblioteca compartida puede cualquier destino [.NET estándar 2](https://blog.xamarin.com/share-code-net-standard-2-0/), que es el subconjunto común de los marcos de destino o una plataforma de destino específico.

Hay tres opciones de .NET Framework de destino disponibles para Xamarin.Mac, cada uno con sus propias ventajas y compensaciones:

- **Moderna** (denominado móviles en la documentación anterior): un subconjunto muy similar a qué potencias Xamarin.iOS, ajustada para el tamaño y rendimiento. Este marco de trabajo de destino es vinculador seguro, por lo que estos proyectos pueden tener su superficie final reduce drásticamente mediante la eliminación de código no utilizado.

- **Completa** (denominado XM 4.5 en la documentación anterior): un subconjunto muy similar a la BCL "escritorio", con algunas eliminaciones pequeñas. Como la plataforma de destino es casi idéntica a net45 (y versiones posteriores), fácilmente puede consumir muchos nugets que no proporcionan cualquier netstandard2 o compilaciones Xamarin.Mac específico. Sin embargo, debido al uso de System.Configuration no es compatible con la vinculación.

- **No compatible** (denominado sistema en la documentación anterior): en su lugar de vincular a un BCL proporcionada por Xamarin.Mac, use el mono actual del sistema instalado. Esto proporciona el conjunto más completa de ensamblados, incluidos algunos conocido para ser problemático (System.Drawing por ejemplo). Esta opción solo existe tiene "último recurso" y se recomienda encarecidamente a otras opciones de escape antes de usarlo. Como su nombre indica, uso no es compatible con canales de soporte oficial.

## <a name="setting-the-target-framework"></a>Establecer la plataforma de destino

Para cambiar el tipo de .NET Framework de destino para un proyecto Xamarin.Mac, realice lo siguiente:

1. Abra el proyecto Xamarin.Mac en Visual Studio para Mac.
2. En el **el Explorador de soluciones**, haga doble clic en el archivo de proyecto para abrir el **Project Options** cuadro de diálogo.
3. Desde el **General** ficha, seleccione el tipo de **.NET Framework de destino** que se adapte a las necesidades de su aplicación:

  [![Mediante la ventana Opciones de proyecto para elegir una plataforma de destino](target-framework-images/select-target-full.png "mediante la ventana Opciones de proyecto para elegir una plataforma de destino")](target-framework-images/select-target-full-large.png)

4. Haga clic en el **Aceptar** botón para guardar los cambios.

Debería **limpiar** y, a continuación, **volver a generar** el proyecto Xamarin.Mac después de cambiar el tipo de .NET Framework de destino.

## <a name="summary"></a>Resumen

En este artículo se ha descrito brevemente los diferentes tipos de .NET Framework de destino (bibliotecas de clases Base) disponible para una aplicación Xamarin.Mac y cuándo se debe usar cada tipo de framework.


## <a name="related-links"></a>Vínculos relacionados

- [iOS y Mac uso compartido de código](~/cross-platform/macios/index.md)
- [API unificada](~/cross-platform/macios/unified/index.md)
- [Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)
- [Ensamblados](~/cross-platform/internals/available-assemblies.md)
- [Actualizar aplicaciones existentes de Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
