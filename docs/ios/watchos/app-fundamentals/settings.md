---
title: Trabajar con la configuración de Xamarin de watchOS
description: Este documento describe cómo trabajar con la configuración de watchOS en Xamarin. Describe Agregar configuración a una solución de aplicación de inspección con esos valores en la aplicación y la aplicación de Apple Watch en iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a8fe2c2765676db52c23fd7c475f218f14697caf
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675228"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Trabajar con la configuración de Xamarin de watchOS

Las aplicaciones de Apple Watch pueden usar la misma funcionalidad de configuración como aplicaciones de iOS: la interfaz de usuario de configuración se muestra en el **Apple Watch** aplicación de iPhone, pero los valores son accesibles en la aplicación de iPhone y también la extensión de inspección.

![](settings-images/intro.png "Las aplicaciones de Apple Watch pueden usar la misma funcionalidad de configuración como aplicaciones de iOS")

La configuración se almacenarán en una ubicación compartida que sea accesible para la aplicación de iOS y la extensión de la aplicación de inspección, definido por un **grupo de aplicaciones**. Debería [configurar un grupo de aplicaciones](~/ios/watchos/app-fundamentals/app-groups.md) antes de agregar la configuración mediante las instrucciones siguientes.

## <a name="add-settings-in-a-watch-solution"></a>Agregue la configuración en una solución de supervisión

En el **aplicación de iPhone** en la solución (*no* la aplicación del reloj o extensión):

1. Haga clic en **Agregar > nuevo archivo...**  y elija **Settings.bundle** (no se puede editar el nombre de la **nuevo archivo** cuadro de diálogo):

   [![](settings-images/settings-add-sml.png "Agregar un nuevo lote de configuración")](settings-images/settings-add.png#lightbox)

2. Cambie el nombre a **configuración Watch.bundle** (seleccione y escriba **comando + R** para cambiar el nombre):

   ![](settings-images/settings-rename.png "Cambiar el nombre de la agrupación")

3. Agregue una nueva clave `ApplicationGroupContainerIdentifier` a la **Root.plist** con el valor establecido para el grupo de aplicaciones que se haya configurado, (p ej. `group.com.xamarin.WatchSettings` en el ejemplo):

   [![](settings-images/settings-appgroup-sml.png "Agregar una clave de ApplicationGroupContainerIdentifier a la Root.plist")](settings-images/settings-appgroup.png#lightbox)

4. Editar el **Settings-Watch.bundle/Root.plist** para contener las opciones que desee usar: el archivo de plantilla contiene un grupo.
  TextField, modificador para alternar y control deslizante de forma predeterminada (que puede eliminar y reemplazar con su propia configuración):

  [![](settings-images/rootplist-sml.png "Editar el Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Usar la configuración de la aplicación del reloj

Para obtener acceso a los valores seleccionados por el usuario, cree un `NSUserDefaults` instancia mediante el grupo de aplicaciones y la especificación `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Aplicación de Apple Watch

[![](settings-images/settings-app-sml.png "La nueva aplicación de Apple Watch en iPhone")](settings-images/settings-app.png#lightbox)

Los usuarios interactúan con la configuración a través del nuevo **Apple Watch** aplicación en su iPhone. Esta aplicación permite al usuario mostrar u ocultar las aplicaciones en la inspección, además de editar la configuración expone mediante el **configuración Watch.bundle**.

![](settings-images/applewatch-1.png "Ejemplo de configuración de la aplicación") ![](settings-images/applewatch-2.png "ejemplo de configuración de la aplicación")



## <a name="related-links"></a>Vínculos relacionados

- [Documento de configuración de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
