---
title: Estilos de la interfaz de usuario nueva
description: "Este artículo tratan la luz y oscuro de interfaz de usuario de temas que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 55a9e56585521a0a7b89a7b881dfecd4e1c14b08
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="new-user-interface-styles"></a>Estilos de la interfaz de usuario nueva

_Este artículo tratan la luz y oscuro de interfaz de usuario de temas que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin.tvOS._

tvOS 10 ahora admite un oscuro y la interfaz de usuario de luz que tendrán todos lo UIKit de compilación controla automáticamente el tema adaptar, según las preferencias del usuario. Además, el desarrollador puede ajustar manualmente elementos de interfaz de usuario basados en el tema seleccionado por el usuario y puede invalidar un tema determinado.


<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Acerca de los nuevos estilos de interfaz de usuario

Como se mencionó anteriormente, tvOS 10 ahora admite un oscuro y la interfaz de usuario de luz que tendrán todos lo UIKit de compilación controla automáticamente el tema adaptar, según las preferencias del usuario.

El usuario puede cambiar el tema, vaya a **configuración** > **General** > **apariencia** y cambiar entre **claro**  y **oscuro**:

[ ![](user-interface-styles-images/theme01.png "La aplicación de configuración")](user-interface-styles-images/theme01.png)

Cuando el **oscuro** tema está activado, todos los elementos de interfaz de usuario se cambiarán a texto claro sobre un fondo oscuro:

[ ![](user-interface-styles-images/theme02.png "El tema oscuro")](user-interface-styles-images/theme02.png)

El usuario tiene la opción para cambiar el tema en cualquier momento y puede realizar por lo tanto, en función de la actividad actual, donde se encuentra la TV de Apple o la hora del día.

El tema de la interfaz de usuario de luz es el tema predeterminado y las aplicaciones existentes de tvOS seguir usando el tema claro, independientemente de las preferencias del usuario, a menos que se modifican tvos 10 para aprovechar el tema oscuro. Una aplicación de tvOS 10 también tiene la capacidad de invalidar el tema actual y usar siempre el tema de la luz u oscuro para algunos o todos de su interfaz de usuario.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adopción de los temas claros y oscuros

Para admitir esta característica, Apple ha agregado una nueva API para la `UITraitCollection` clase y una aplicación de tvOS deben participar en para admitir la apariencia oscura (a través de una configuración en su `Info.plist` archivo).

Para participar en compatibilidad con el tema claro y oscuro, haga lo siguiente:

1. Haga doble clic en el archivo `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. Seleccione el **origen** vista (desde la parte inferior del editor).
3. Agregue una nueva clave y llámelo `UIUserInterfaceStyle`: 

    [ ![](user-interface-styles-images/theme03.png "La clave de UIUserInterfaceStyle")](user-interface-styles-images/theme03.png)
4. Deje el tipo establecido en `String` y escriba un valor de `Automatic`: 

    [ ![](user-interface-styles-images/theme04.png "Escriba automático")](user-interface-styles-images/theme04.png)
5. Guarde los cambios en el archivo.

Hay tres valores posibles para la `UIUserInterfaceStyle` clave:

- **Luz** -fuerza la interfaz de usuario de la aplicación tvOS para utilizar siempre el tema claro.
- **Oscuro** -fuerza la interfaz de usuario de la aplicación tvOS para utilizar siempre el tema oscuro.
- **Automática** -cambia entre el tema claro y oscuro según las preferencias del usuario en la configuración. Esta es la configuración preferida.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Compatibilidad con el tema de UIKit

Si está utilizando una aplicación de tvOS estándar e integradas `UIView` según controles, automáticamente responderá al tema de la interfaz de usuario sin la intervención del desarrollador.

Además, `UILabel` y `UITextView` cambiará automáticamente su color basado en el tema de la interfaz de usuario seleccione:

- El texto será negro en el tema claro.
- El texto estará en blanco en el tema oscuro.

Si el desarrollador cambia el color del texto manualmente (ya sea en el guión gráfico o el código), será responsables de administrar los cambios de color basados en el tema de la interfaz de usuario.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Nuevos efectos de desenfoque

Para admitir los temas claro y oscuro en una aplicación tvOS 10, Apple ha agregado dos nuevos efectos desenfoque. Estos efectos nueva ajustarán automáticamente el desenfoque basado en el tema de la interfaz de usuario que el usuario ha seleccionado como sigue:

- `UIBlurEffectStyleRegular` -Usa una luz desenfoque en el tema claro y una oscuro desenfoque en el tema oscuro.
- `UIBlurEffectStyleProminent` -Utiliza un desenfoque muy claro en el tema claro y un desenfoque muy oscuro en el tema oscuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Trabajar con colecciones de rasgo

El nuevo `UserInterfaceStyle` propiedad de la `UITraitCollection` clase puede utilizarse para obtener el tema de la interfaz de usuario actualmente seleccionado y será un `UIUserInterfaceStyle` enum de uno de los siguientes valores:

- **Luz** -se selecciona el tema de la interfaz de usuario de la luz.
- **Oscuro** -se selecciona el tema de la interfaz de usuario oscuro.
- **Sin especificar** -la vista no se ha mostrado para pantalla aún, por lo que el tema de la interfaz de usuario actual es desconocido.

Además, las colecciones de rasgo tienen las siguientes características de tvOS 10:
 
- El proxy de apariencia puede personalizarse según el `UserInterfaceStyle` de un determinado `UITraitCollection` para cambiar elementos como imágenes o colores basándose en el tema de elemento.
- Una aplicación de tvOS puede controlar los cambios en la colección de rasgo reemplazando el `TraitCollectionDidChange` método de un `UIView` o `UIViewController` clase.

> [!IMPORTANT]
> **Nota:** no son totalmente compatibles con el anticipo Xamarin.tvOS tvos 10 `UIUserInterfaceStyle` para `UITraitCollection` todavía. Compatibilidad total con se agregará en una versión futura.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizar la apariencia basándose en el tema

Para los elementos de interfaz de usuario que admiten al proxy de apariencia, su aspecto puede ajustarse en función del tema de la interfaz de usuario de su colección de rasgo. Por lo tanto, para un determinado elemento de interfaz de usuario, el desarrollador puede especificar un color para el tema claro y otro color para el tema oscuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> **Nota:** Desafortunadamente, la vista previa de Xamarin.tvOS tvos 10 no son totalmente compatibles con `UIUserInterfaceStyle` para `UITraitCollection`, por lo que este tipo de personalización no está disponible. Compatibilidad total con se agregará en una versión futura.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Responder a los cambios de tema directamente

En el programador requiere un poco más control sobre el aspecto de un elemento de interfaz de usuario en función del tema de la interfaz de usuario seleccionado, puede invalidar el `TraitCollectionDidChange` método de un `UIView` o `UIViewController` clase.

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

### <a name="overriding-a-trait-collection"></a>Reemplazar una colección de rasgo

En función del diseño de una aplicación tvOS, puede haber ocasiones cuando el desarrollador debe reemplazar la colección de rasgo de un elemento determinado de la interfaz de usuario y hacer que se utilice siempre un tema específico de la interfaz de usuario.

Esto puede hacerse mediante el `SetOverrideTraitCollection` método en la `UIViewController` clase. Por ejemplo:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Para obtener más información, consulte el [rasgos](~/ios/user-interface/storyboards/unified-storyboards.md) y [reemplazar rasgos](~/ios/user-interface/storyboards/unified-storyboards.md) secciones de nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Guiones gráficos y colecciones de rasgo

En tvOS 10, se puede establecer el guión gráfico de una aplicación para responder a las colecciones de rasgo y muchos elementos de interfaz de usuario pueden ser conscientes claro y tema oscuro. El actual Xamarin.tvOS anticipo tvos 10 todavía no admite esta característica en el Diseñador de la interfaz, por lo que deberá el guión gráfico para editarse en el generador de interfaz de Xcode para solucionar este problema.

Para habilitar la compatibilidad de la colección de rasgo, haga lo siguiente:

1. Haga doble clic en el archivo del guión gráfico en el **el Explorador de soluciones** y seleccione **abrir con** > **Xcode interfaz generador**: 

    [ ![](user-interface-styles-images/theme05.png "Abrir con el generador de interfaz de Xcode")](user-interface-styles-images/theme05.png) 
2. Para habilitar el soporte de colección de rasgo, pase a la **Inspector de archivo** y comprobar la **variaciones de rasgo de uso** propiedad en el **interfaz generador documento** sección: 

    [ ![](user-interface-styles-images/theme06.png "Habilitar la compatibilidad con la colección de rasgo")](user-interface-styles-images/theme06.png)
3. Confirmar el cambio para usar el rasgo variaciones: 

    [ ![](user-interface-styles-images/theme07.png "El uso de alerta de rasgo variaciones")](user-interface-styles-images/theme07.png)
4. Guarde los cambios en el archivo del guión gráfico.

Apple ha agregado las siguientes capacidades al editar guiones gráficos de tvOS en el generador de interfaz:

* El desarrollador puede especificar distintas variaciones de los elementos de interfaz de usuario basado en el tema de la interfaz de usuario en el **Inspector de atributo**:
    
    * Ahora tienen varias propiedades de un  **+**  junto a ellos que se puede hacer clic para agregar una versión específica de tema de interfaz de usuario: 

        [ ![](user-interface-styles-images/theme08.png "Agregar una versión específica de tema de interfaz de usuario")](user-interface-styles-images/theme08.png) 
    
    * El desarrollador puede especificar una nueva propiedad o haga clic en el **x** botón para quitar el equipo: 

        [ ![](user-interface-styles-images/theme09.png "Especifique una nueva propiedad o haga clic en el botón x para quitarlo")](user-interface-styles-images/theme09.png)
* El desarrollador puede obtener la vista previa de un diseño de interfaz de usuario en el tema de la luz u oscuro desde en el generador de interfaz:
    
    * La parte inferior de la superficie de diseño permite al desarrollador cambiar el tema actual de la interfaz de usuario: 

        [ ![](user-interface-styles-images/theme10.png "La parte inferior de la superficie de diseño")](user-interface-styles-images/theme10.png)
        
    * Se mostrará el nuevo tema en el generador de interfaz y se mostrarán los ajustes específicos de rasgo colección: 

        [ ![](user-interface-styles-images/theme11.png "El tema que se muestra en el generador de interfaz")](user-interface-styles-images/theme11.png)

Además, el simulador tvOS ahora tiene un método abreviado de teclado para permitir que el programador cambiar rápidamente entre los temas claro y oscuro cuando se depura una aplicación de tvOS. Use la **comando-Mayús-D** secuencia para alternar entre claro y oscuro del teclado.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata la luz y oscuro de interfaz de usuario de temas que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
