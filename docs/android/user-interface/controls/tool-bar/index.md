---
title: Barra de herramientas
description: 'La barra de herramientas es un componente de la barra de acción que proporciona más flexibilidad que la barra de acción predeterminada: puede colocarse en cualquier lugar en la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores que es diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas.'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 08fa00b539bd5baca4f5d61b04419a76a4a72ab1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766655"
---
# <a name="toolbar"></a>Barra de herramientas

_La barra de herramientas es un componente de la barra de acción que proporciona más flexibilidad que la barra de acción predeterminada: puede colocarse en cualquier lugar en la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores que es diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas._

 
## <a name="overview"></a>Información general

Un elemento clave del diseño de cualquier actividad Android es un *barra de acciones*. La barra de acciones es el componente de interfaz de usuario que se utiliza para navegación, búsqueda, menús y personalización de marca en una aplicación Android. En las versiones de Android anteriores a Android 5.0 círculo, la barra de acción (también conocido como el *barra de la aplicación*) era el componente recomendado para proporcionar esta funcionalidad. 

El `Toolbar` widget (introducido en el círculo 5.0 Android) puede considerarse como una generalización de la interfaz de la barra de acción &ndash; está diseñado para reemplazar la barra de acciones. La `Toolbar` puede usarse en cualquier lugar en un diseño de la aplicación y es mucho más personalizable que una barra de acción. Captura de pantalla siguiente muestra la personalizada `Toolbar` en el ejemplo se crean en esta guía: 

[![Captura de pantalla de ejemplo de una barra de herramientas con Editar, guardar y elementos de menú de desbordamiento](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Hay algunas diferencias importantes entre el `Toolbar` y la barra de acciones: 

-   Un `Toolbar` se puede colocar en cualquier parte de la interfaz de usuario.

-   Varias barras de herramientas se pueden mostrar en la misma pantalla.

-   Si se utilizan fragmentos, cada fragmento puede tener su propio `Toolbar`. 

-   Un `Toolbar` puede configurarse para abarcar sólo un ancho parcial de la pantalla. 

-   Dado que el `Toolbar` no está enlazado a la combinación de colores de decoración de ventana de la actividad, puede tener una combinación de colores visualmente distintos. 

-   A diferencia de la barra de acción, el `Toolbar` no incluye un icono de la izquierda. Los menús de la derecha utilizan menos espacio. 

-   El `Toolbar` alto es ajustable. 

-   Otras vistas pueden incluirse dentro del `Toolbar`. 

Un `Toolbar` puede contener uno o varios de los elementos siguientes: 

-   Botón de navegación

-   Una imagen de logotipo

-   Título y el subtítulo

-   Vistas personalizadas

-   Menú Acción

-   Menú de desbordamiento

Google [directrices de diseño de Material](https://material.google.com/) recomienda sacar partido de estos elementos para conceder a aplicaciones de un vistazo distinto (en lugar de confiar únicamente en un icono de la aplicación y el título). 

Esta guía describe la más usada `Toolbar` escenarios:

-   Reemplazar la barra de acción predeterminada de una actividad con un `Toolbar`. 

-   Agregar un segundo `Toolbar` a una actividad.

-   Mediante el **biblioteca de compatibilidad de Android v7 AppCompat** biblioteca (denominados *AppCompat* en el resto de esta guía) para implementar `Toolbar` en versiones anteriores de Android. 

 
 
## <a name="requirements"></a>Requisitos

`Toolbar` está disponible en Android 5.0 círculo (API de 21) y versiones posteriores. Cuando se dirige a Android versiones anteriores a Android 5.0, utilice la [biblioteca admite Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), que proporciona compatible `Toolbar` admitir en un paquete de NuGet. 
[Compatibilidad de la barra de herramientas](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) explica cómo usar esta biblioteca. 




## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas del círculo (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de herramientas de AppCompat (ejemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
