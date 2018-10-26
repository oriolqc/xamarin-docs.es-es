---
title: Barra de herramientas
description: 'La barra de herramientas es un componente de barra de acción que proporciona más flexibilidad que la barra de acción predeterminada: puede colocarse en cualquier lugar en la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores que es diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas.'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 2c9de4058fdaee53671e65f49ad95c3af5e127d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107487"
---
# <a name="toolbar"></a>Barra de herramientas

_La barra de herramientas es un componente de barra de acción que proporciona más flexibilidad que la barra de acción predeterminada: puede colocarse en cualquier lugar en la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores que es diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas._

 
## <a name="overview"></a>Información general

Un elemento clave en el diseño de cualquier actividad de Android es un *barra de acciones*. La barra de acciones es el componente de interfaz de usuario que se usa para navegación, búsqueda, menús y personalización de marca en una aplicación de Android. En las versiones Android anteriores a Android 5.0 Lollipop, la barra de acciones (también conocido como el *barra de la aplicación*) era el componente recomendado para proporcionar esta funcionalidad. 

El `Toolbar` widget (introducida en Android 5.0 Lollipop) puede considerarse una generalización de la interfaz de la barra de acción &ndash; está diseñado para reemplazar la barra de acciones. El `Toolbar` se pueden usar en cualquier lugar en un diseño de la aplicación y es mucho más personalizable que una barra de acciones. Captura de pantalla siguiente ilustra la personalizada `Toolbar` creado en esta guía de ejemplo: 

[![Captura de pantalla de ejemplo de una barra de herramientas con Editar, guardar y elementos de menú de desbordamiento](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Hay algunas diferencias importantes entre el `Toolbar` y la barra de acciones: 

-   Un `Toolbar` puede colocarse en cualquier lugar en la interfaz de usuario.

-   Varias barras de herramientas se pueden mostrar en la misma pantalla.

-   Si se usan fragmentos, cada fragmento puede tener su propio `Toolbar`. 

-   Un `Toolbar` puede configurarse para abarcar sólo un ancho parcial de la pantalla. 

-   Dado que el `Toolbar` no está enlazado a la combinación de colores de decoración de la ventana de la actividad, puede tener una combinación de colores distingan visualmente. 

-   A diferencia de la barra de acciones, el `Toolbar` no incluye un icono de la izquierda. Los menús de la derecha usar menos espacio. 

-   El `Toolbar` alto es ajustable. 

-   Otras vistas pueden incluirse dentro del `Toolbar`. 

Un `Toolbar` puede contener uno o varios de los siguientes elementos: 

-   Botón de navegación

-   Una imagen de logotipo

-   Título y subtítulo

-   Vistas personalizadas

-   Menú Acción

-   Menú de desbordamiento

Google [directrices de Material Design](https://material.google.com/) recomienda sacar provecho de estos elementos para proporcionar a las aplicaciones un aspecto distinto (en lugar de confiar únicamente en un icono de la aplicación y el título). 

Esta guía explica la usa con más frecuencia `Toolbar` escenarios:

-   Reemplazar la barra de acción predeterminada de la actividad con un `Toolbar`. 

-   Agregar un segundo `Toolbar` a una actividad.

-   Mediante el **biblioteca de compatibilidad de Android v7 AppCompat** biblioteca (denominados *AppCompat* en el resto de esta guía) para implementar `Toolbar` en versiones anteriores de Android. 

 
 
## <a name="requirements"></a>Requisitos

`Toolbar` está disponible en Android 5.0 Lollipop (API 21) y versiones posteriores. Cuando el objetivo es Android versiones anteriores a Android 5.0, utilice el [biblioteca admite Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), que proporciona compatible `Toolbar` admitir en un paquete de NuGet. 
[Compatibilidad de la barra de herramientas](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) se explica cómo usar esta biblioteca. 




## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas del círculo (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de herramientas de AppCompat (ejemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
