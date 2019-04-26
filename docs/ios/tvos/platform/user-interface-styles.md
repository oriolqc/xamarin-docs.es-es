---
title: Estilos de la interfaz de usuario de Xamarin de tvOS
description: Este artículo describe la luz y temas oscuro de la interfaz de usuario que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2536ca5d3bff3f5b7962bc4fcf58b31a130fd03c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270934"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>Estilos de la interfaz de usuario de Xamarin de tvOS

_Este artículo describe la luz y temas oscuro de la interfaz de usuario que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin.tvOS._

tvOS 10 ahora admite un oscuro y la interfaz de usuario de la luz tema todas la compilación en UIKit controla tendrá automáticamente adaptar, según las preferencias del usuario. Además, el desarrollador puede ajustar manualmente los elementos de interfaz de usuario en función del tema seleccionado por el usuario y puede invalidar un tema determinado.

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Acerca de los nuevos estilos de interfaz de usuario

Como se indicó anteriormente, tvOS 10 ahora admite un oscuro y la interfaz de usuario de la luz tema todas la compilación en UIKit controla tendrá automáticamente adaptar, según las preferencias del usuario.

El usuario puede cambiar este tema, vaya a **configuración** > **General** > **apariencia** y cambiar entre **luz**  y **oscuro**:

[![](user-interface-styles-images/theme01.png "La aplicación de configuración")](user-interface-styles-images/theme01.png#lightbox)

Cuando el **oscuro** tema está activado, todos los elementos de interfaz de usuario se cambiarán a texto claro sobre un fondo oscuro:

[![](user-interface-styles-images/theme02.png "El tema oscuro")](user-interface-styles-images/theme02.png#lightbox)

El usuario tiene la opción para cambiar el tema en cualquier momento y puede hacerlo en función de la actividad actual, donde se encuentra la TV de Apple o la hora del día.

El tema de la interfaz de usuario de la luz es el tema predeterminado y las aplicaciones existentes de tvOS seguir usando el tema claro, independientemente de las preferencias del usuario, a menos que se modifican para tvOS 10 a fin de aprovechar el tema oscuro. Una aplicación de tvOS 10 también tiene la capacidad de invalidar el tema actual y usar siempre el tema de la luz u oscuro para algunos o todos de su interfaz de usuario.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adopción de los temas claros y oscuros

Para admitir esta característica, Apple ha agregado una nueva API para la `UITraitCollection` clase y una aplicación de tvOS deben participar en para admitir la apariencia oscura (a través de una configuración en su `Info.plist` archivo).

Para participar en la compatibilidad con el tema claro y oscuro, realice lo siguiente:

1. Haga doble clic en el archivo `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. Seleccione el **origen** vista (desde la parte inferior del editor).
3. Agregue una nueva clave y llámelo `UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "La clave UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Deje el tipo establecido en `String` y escriba un valor de `Automatic`: 

    [![](user-interface-styles-images/theme04.png "Escriba automático")](user-interface-styles-images/theme04.png#lightbox)
5. Guarde los cambios en el archivo.

Hay tres valores posibles para el `UIUserInterfaceStyle` clave:

- **Luz** -fuerza la interfaz de usuario de la aplicación tvOS usar siempre el tema claro.
- **Oscuro** -fuerza la interfaz de usuario de la aplicación tvOS usar siempre el tema oscuro.
- **Automática** -cambia entre el tema claro y oscuro según las preferencias del usuario en la configuración. Esta es la configuración preferida.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Compatibilidad con el tema de UIKit

Si está usando una aplicación de tvOS estándar e integrados `UIView` en función de los controles, responderán automáticamente en el tema de la interfaz de usuario sin la intervención del programador.

Además, `UILabel` y `UITextView` cambiará automáticamente su color basado en el tema de la interfaz de usuario seleccione:

- El texto estará negro en el tema claro.
- El texto estará en blanco en el tema oscuro.

Si el desarrollador nunca cambia manualmente el color del texto (ya sea en el guión gráfico o en código), estarán responsables de controlar los cambios de color basados en el tema de la interfaz de usuario.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Nuevos efectos de desenfoque

Para ser compatible con los temas claro y oscuro en una aplicación de tvOS 10, Apple ha agregado dos nuevos efectos de desenfoque. Estos nuevos efectos ajustará automáticamente el desenfoque basándose en el tema de la interfaz de usuario que el usuario ha seleccionado como sigue:

- `UIBlurEffectStyleRegular` -En el tema oscuro de desenfoque usa desenfoque de una luz en el tema claro y un oscuro.
- `UIBlurEffectStyleProminent` : Usa un desenfoque extra-light en el tema claro y un desenfoque muy oscuro en el tema oscuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Trabajar con colecciones de rasgo

El nuevo `UserInterfaceStyle` propiedad de la `UITraitCollection` clase puede usarse para obtener el tema de la interfaz de usuario actualmente seleccionado y será un `UIUserInterfaceStyle` enum de uno de los siguientes valores:

- **Luz** -tema de la interfaz de usuario de la luz está seleccionado.
- **Oscuro** -se selecciona el tema de la interfaz de usuario oscuro.
- **Sin especificar** -la vista no se ha mostrado para pantalla aún, por lo que el tema de la interfaz de usuario actual es desconocido.

Además, las colecciones de rasgo de tener las siguientes características de tvOS 10:
 
- El proxy de apariencia puede personalizarse según el `UserInterfaceStyle` de un determinado `UITraitCollection` para cambiar las cosas, como imágenes o colores basados en el tema de elemento.
- Una aplicación de tvOS puede controlar los cambios en la colección de rasgo reemplazando el `TraitCollectionDidChange` método de un `UIView` o `UIViewController` clase.

> [!IMPORTANT]
> No admite por completo el anticipo de Xamarin.tvOS para tvOS 10 `UIUserInterfaceStyle` para `UITraitCollection` todavía. En una versión futura se agregará compatibilidad completa.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizar la apariencia basándose en el tema

Para los elementos de interfaz de usuario que admiten al proxy de apariencia, su apariencia se puede ajustar en función del tema de la interfaz de usuario de su colección de rasgo. Por lo tanto, para un elemento de interfaz de usuario determinado, el desarrollador puede especificar un color para el tema claro y otro color para el tema oscuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Por desgracia, no admite por completo la vista previa de Xamarin.tvOS para tvOS 10 `UIUserInterfaceStyle` para `UITraitCollection`, por lo que este tipo de personalización no está disponible. En una versión futura se agregará compatibilidad completa.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Responde directamente a los cambios de tema

En el desarrollador requiere mayor control sobre la apariencia de un elemento de interfaz de usuario basado en el tema de la interfaz de usuario seleccionado, puede invalidar el `TraitCollectionDidChange` método de un `UIView` o `UIViewController` clase.

Por ejemplo:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);
    
    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>Invalidación de una colección de rasgo

Según el diseño de una aplicación de tvOS, podría haber veces cuando el desarrollador debe invalidar la colección de rasgo de un elemento determinado de la interfaz de usuario y hacer que se utilice siempre un tema específico de la interfaz de usuario.

Esto puede hacerse mediante el `SetOverrideTraitCollection` método en el `UIViewController` clase. Por ejemplo:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Para obtener más información, consulte el [rasgos](~/ios/user-interface/storyboards/unified-storyboards.md) y [reemplazar rasgos](~/ios/user-interface/storyboards/unified-storyboards.md) secciones de nuestro [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Guiones gráficos y las colecciones de rasgo

En tvOS 10, se puede establecer el guión gráfico de una aplicación para responder a las colecciones de rasgo y muchos elementos de interfaz de usuario pueden tener conocimiento tema claro y oscuro. El actual Xamarin.tvOS versión preliminar para tvOS 10 todavía no admite esta característica en el Diseñador de interfaz, por lo que deberá modificarse en Interface Builder de Xcode para solucionar este problema el guión gráfico.

Para habilitar la compatibilidad colección rasgo, realice lo siguiente:

1. Haga doble clic en el archivo de guión gráfico en el **el Explorador de soluciones** y seleccione **abrir con** > **Interface Builder de Xcode**: 

    [![](user-interface-styles-images/theme05.png "Abrir con Interface Builder de Xcode")](user-interface-styles-images/theme05.png#lightbox) 
2. Para habilitar la compatibilidad colección rasgo, cambie a la **archivo Inspector** y compruebe el **las variaciones de rasgos de uso** propiedad en el **documento del generador de interfaz** sección: 

    [![](user-interface-styles-images/theme06.png "Habilitar la compatibilidad con la colección de rasgo")](user-interface-styles-images/theme06.png#lightbox)
3. Confirme el cambio para usar las variaciones de rasgos: 

    [![](user-interface-styles-images/theme07.png "El uso de alerta de las variaciones de rasgos")](user-interface-styles-images/theme07.png#lightbox)
4. Guarde los cambios en el archivo de guión gráfico.

Apple ha incorporado las siguientes capacidades al editar los guiones gráficos de tvOS en Interface Builder:

* El desarrollador puede especificar distintas variaciones de los elementos de interfaz de usuario en función del tema de la interfaz de usuario en el **Inspector de atributos**:
    
    * Ahora tienen varias propiedades de un **+** junto a ellos que se puede hacer clic para agregar una versión específica del tema de la interfaz de usuario: 

        [![](user-interface-styles-images/theme08.png "Agregar una versión específica del tema de la interfaz de usuario")](user-interface-styles-images/theme08.png#lightbox) 
    
    * El desarrollador puede especificar una nueva propiedad o haga clic en el **x** botón para quitarla: 

        [![](user-interface-styles-images/theme09.png "Especifique una nueva propiedad o haga clic en el botón x para quitarlo")](user-interface-styles-images/theme09.png#lightbox)
* El desarrollador puede obtener una vista previa en el tema de la luz u oscuro de Interface Builder dentro de un diseño de interfaz de usuario:
    
    * La parte inferior de la superficie de diseño permite al desarrollador cambiar el tema de la interfaz de usuario actual: 

        [![](user-interface-styles-images/theme10.png "La parte inferior de la superficie de diseño")](user-interface-styles-images/theme10.png#lightbox)
        
    * Se mostrará el nuevo tema en Interface Builder y se mostrarán los ajustes específicos de rasgo colección: 

        [![](user-interface-styles-images/theme11.png "El tema mostrado en el generador de interfaz")](user-interface-styles-images/theme11.png#lightbox)

Además, el simulador de tvOS ahora tiene un método abreviado de teclado que permite al desarrollador cambiar rápidamente entre los temas claro y oscuro al depurar una aplicación de tvOS. Use la **comando-Mayús-D** secuencia para alternar entre claro y oscuro del teclado.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de la luz y temas oscuro de la interfaz de usuario que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
