---
title: Introducción a Xamarin.Forms Shell
description: Xamarin.Forms Shell proporciona las características fundamentales que necesitan la mayoría de las aplicaciones, como una experiencia de usuario común de navegación, un esquema de navegación basado en URI y un controlador de búsqueda integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
ms.openlocfilehash: 38553b3b30388bc64fd97a7ac96a671279d20bc5
ms.sourcegitcommit: b986460787677cf8c2fc7cc8c03f4bc60c592120
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213340"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de aplicaciones móviles, como por ejemplo:

- Un único lugar para describir la jerarquía visual de una aplicación.
- Una interfaz de usuario de navegación común.
- Un esquema de navegación basado en URI que permite la navegación a cualquier página de la aplicación.
- Un controlador de búsqueda integrado.

Además, las aplicaciones de Shell se benefician de una mayor velocidad de representación y un consumo reducido de memoria.

> [!IMPORTANT]
> El shell de Xamarin.Forms solo está disponible en iOS y Android. Las aplicaciones existentes de iOS y Android pueden adoptar Shell y aprovechar inmediatamente las mejoras de navegación, rendimiento y extensibilidad.

## <a name="shell-navigation-experience"></a>Experiencia de navegación de Shell

Shell ofrece una experiencia de navegación bien fundamentada basada en controles flotantes y pestañas. El nivel superior del panel de navegación en una aplicación de Shell es un control flotante o una barra de pestañas inferior, según los requisitos de navegación de la aplicación. El ejemplo siguiente muestra una aplicación donde el nivel superior de navegación es un control flotante:

[![Captura de pantalla de un control flotante de Shell en iOS y Android](introduction-images/flyout.png "Control flotante de Shell")](introduction-images/flyout-large.png#lightbox "Shell flyout")

Al seleccionar un elemento de control flotante, se selecciona y muestra la pestaña inferior que representa el elemento:

[![Captura de pantalla de las pestañas inferiores de Shell en iOS y Android](introduction-images/monkeys.png "Pestañas inferiores de Shell")](introduction-images/monkeys-large.png#lightbox "Shell bottom tabs")

> [!NOTE]
> Cuando el control flotante no está abierto, la barra de pestañas inferior se puede considerar el nivel superior de navegación en la aplicación.

Cada pestaña muestra un objeto [ `ContentPage`](xref:Xamarin.Forms.ContentPage). Sin embargo, si una pestaña inferior contiene más de una página, las páginas son navegables mediante la barra de pestañas superior:

[![Captura de pantalla de pestañas superiores de Shell en iOS y Android](introduction-images/cats.png "Pestañas superiores de Shell")](introduction-images/cats-large.png#lightbox "Shell top tabs")

Dentro de cada pestaña, se puede navegar a objetos adicionales [`ContentPage`](xref:Xamarin.Forms.ContentPage):

[![Captura de pantalla de navegación entre páginas de Shell en iOS y Android](introduction-images/cat-details.png "Navegación por aplicaciones de Shell")](introduction-images/cat-details-large.png#lightbox "Shell app navigation")

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
