---
title: .NET Framework de destino para Xamarin.Mac
description: En este artículo se trata los marcos de destino (bibliotecas de clases Base) disponibles para Xamarin.Mac y las implicaciones de su uso en el proyecto de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 15c93126f80917df45a5b80fb84397dc6ef0d5fd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075641"
---
# <a name="target-framework-for-xamarinmac"></a>.NET Framework de destino para Xamarin.Mac

_En este artículo se trata los marcos de destino (bibliotecas de clases Base) disponibles para Xamarin.Mac y las implicaciones de su uso en el proyecto de Xamarin.Mac._

![Destino de las opciones de marco de trabajo para Xamarin.Mac](target-framework-images/select-target.png "opciones de marco de trabajo para Xamarin.Mac de destino")

## <a name="background"></a>Fondo

Cada programa de .NET o la biblioteca depende de la funcionalidad proporcionada por la biblioteca de clases Base (BCL). Este BCL incluye ensamblados como mscorlib, System, System.Net.Http y System.Xml que proporcionan la funcionalidad común integrada en todos los lenguajes de. NET.

En los años, han desarrollado varias versiones diferentes de este BCL, optimizado para distintos casos de uso. La BCL "escritorio" incluye un conjunto más completo de bibliotecas que podría ser demasiado pesados para otros casos de uso, aunque mobile se centra en lo que garantiza que las API son seguras para vincular, lo que elimina sin usar código para reducir la superficie de la aplicación.

Una de las repercusiones de estas plataformas de destino diferentes, más importantes es que todos los ensamblados en un determinado programa *debe* ensamblados BCL compatibles de destino. Si este no era el caso, podría tener dos ensamblados vinculados con distintas versiones de la **System.dll** desacuerdo sobre la firma de un tipo determinado. Una biblioteca compartida puede cualquier destino [.NET Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), que es el subconjunto común de las plataformas de destino o un marco de destino específica.

Hay tres opciones de .NET Framework de destino para Xamarin.Mac, cada uno con sus propias ventajas y desventajas:

- **Moderno** (denominado Mobile en la documentación anterior): un subconjunto muy similar a qué poderes Xamarin.iOS, altamente optimizados para rendimiento y tamaño. Este marco de destino es vinculador seguro, por lo que estos proyectos pueden tener su superficie final que se redujo drásticamente mediante la eliminación de código no utilizado.

- **Completa** (denominado XM 4.5 en la documentación anterior): un subconjunto muy similar a la BCL "escritorio", con unas pocas eliminaciones pequeño. Como la plataforma de destino es casi idéntica a net45 (y versiones posteriores), fácilmente puede consumir muchos paquetes de NuGet que no proporcionan cualquier netstandard2 o compilaciones Xamarin.Mac específico. Sin embargo, debido al uso de System.Configuration es incompatible con la vinculación.

- **No compatible** (denominado sistema en la documentación anterior): en su lugar de vincular a una BCL proporcionada por Xamarin.Mac, utilice el mono actual del sistema instalado. Esto proporciona el conjunto más completo de ensamblados, incluidos algunos que se sabe que es problemático (System.Drawing por ejemplo). Esta opción solo existe tiene "último recurso" y se recomienda encarecidamente a otras opciones de escape antes de usarlo. Como el nombre implica, uso no es compatible con canales de soporte técnico oficial.

## <a name="setting-the-target-framework"></a>Establecer la plataforma de destino

Para cambiar el tipo de .NET Framework de destino para un proyecto de Xamarin.Mac, realice lo siguiente:

1. Abra el proyecto de Xamarin.Mac en Visual Studio para Mac.
2. En el **Explorador de soluciones**, haga doble clic en el archivo de proyecto para abrir el cuadro de diálogo **Opciones de proyecto**.
3. Desde el **General** pestaña, seleccione el tipo de **.NET Framework de destino** que se adapte a las necesidades de su aplicación:

  [![Uso de la ventana de opciones de proyecto para elegir una plataforma de destino](target-framework-images/select-target-full.png "utilizando la ventana de opciones de proyecto para elegir una plataforma de destino")](target-framework-images/select-target-full-large.png#lightbox)

4. Haga clic en el botón **Aceptar** para guardar los cambios.

Debería **Clean** y, a continuación, **recompilar** su proyecto de Xamarin.Mac después de cambiar el tipo de .NET Framework de destino.

## <a name="summary"></a>Resumen

Este artículo trata brevemente los diferentes tipos de plataformas de destino (bibliotecas de clases Base) disponibles para una aplicación de Xamarin.Mac y cuándo se debe usar cada tipo de marco de trabajo.


## <a name="related-links"></a>Vínculos relacionados

- [iOS y Mac uso compartido de código](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)
- [Ensamblados](~/cross-platform/internals/available-assemblies.md)
- [Actualización de aplicaciones Mac existentes](~/cross-platform/macios/unified/updating-mac-apps.md)
