---
title: Temas de Xamarin.Forms
description: Este artículo detallan los temas de Xamarin.Forms, que definir la apariencia visual específica para las vistas estándares.
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0f49eeba072d6aeb7ead40d5d56d4af9e9bf5e27
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245737"
---
# <a name="xamarinforms-themes"></a>Temas de Xamarin.Forms

![](~/media/shared/preview.png "Esta API está actualmente en vista previa")

Temas de Xamarin.Forms se anunciaron en evolucionar 2016 y están disponibles como una vista previa de los clientes probar y proporcionar comentarios.

Se agrega un tema a una aplicación de Xamarin.Forms mediante la inclusión de la **Xamarin.Forms.Theme.Base** paquete Nuget, además de un paquete adicional que define un tema específico (p. ej. Xamarin.Forms.Theme.Light) o else puede definirse un tema local para la aplicación.

Hacer referencia a la [el tema claro](light.md) y [el tema oscuro](dark.md) páginas para obtener instrucciones sobre cómo agregarlos a una aplicación o visite el [tema personalizado de ejemplo](custom.md).

**Importante:** también debe seguir los pasos necesarios para [cargar ensamblados de tema (abajo)](#loadtheme) agregando código reutilizable en el iOS `AppDelegate` y Android `MainActivity`. Esto se mejorará en una versión futura de vista previa.


## <a name="control-appearance"></a>Apariencia de los controles

El [luz](light.md) y [oscuro](dark.md) temas ambos definen un aspecto visual concreto para los controles estándares. Una vez que agregue un tema al diccionario de recursos de la aplicación, cambiará la apariencia de los controles estándar.

El siguiente marcado XAML muestra algunos de los controles comunes:

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

Estas capturas de pantalla muestran estos controles con:

* Ningún tema aplicado
* Tema claro (solo diferencias sutiles a no tener ningún tema)
* Tema oscuro

![](images/standard-none-sml.png "Controles sin los temas de") ![](images/standard-light-sml.png "controles con el tema claro") ![](images/standard-dark-sml.png "controles con el tema oscuro")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

El `StyleClass` propiedad permite la apariencia de una vista cambiarse según una definición de un tema.

El [luz](light.md) y [oscuro](dark.md) temas ambos definen tres diferentes aspectos de un `BoxView`: `HorizontalRule`, `Circle`, y `Rounded`. Este marcado muestra tres diferentes `BoxView`s con clases de estilo diferente al que se aplican:

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Esto se representa con claro y oscuro como sigue:

![](images/boxview-light-sml.png "BoxView con un tema claro StyleClass") ![](images/boxview-dark-sml.png "BoxView con una StyleClass el tema oscuro")

<a name="builtin" />

## <a name="built-in-classes"></a>Clases integradas

Además de aplicar un estilo a automáticamente common controla la luz y temas oscuros actualmente admiten las siguientes clases que se pueden aplicar estableciendo el `StyleClass` en estos controles:

**BoxView**

* HorizontalRule
* Círculo
* Redondea

**Image**

* Círculo
* Redondea
* Vista en miniatura

**Button**

* Default
* Principal
* Correcto
* Info
* Advertencia
* Riesgo
* Vínculo
* Pequeña
* Grandes

**Label**

* Header
* Subcabecera
* Cuerpo
* Vínculo
* Inverso


## <a name="troubleshooting"></a>Solución de problemas

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>No se pudo cargar el archivo o ensamblado 'Xamarin.Forms.Theme.Light' o uno de sus dependencias

En la versión preliminar, es podrán que algunos temas no se pueda cargar en tiempo de ejecución. Agregue el código que se muestra a continuación, en los proyectos pertinentes para corregir este error.

**iOS**

En el **AppDelegate.cs** agregue las siguientes líneas después de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

En el **MainActivity.cs** agregue las siguientes líneas después de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ThemesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
