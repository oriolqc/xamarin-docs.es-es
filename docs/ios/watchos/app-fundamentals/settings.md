---
title: Trabajar con la configuración
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 6de70eae1eb1c498336a62b4d7be5e2805de11f9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-settings"></a>Trabajar con la configuración

Aplicaciones de Apple Watch pueden utilizar la misma funcionalidad de configuración como aplicaciones de iOS: la interfaz de usuario de configuración se muestra en el **de Apple Watch** aplicación de iPhone, pero los valores son accesibles en la aplicación de iPhone y también la extensión de inspección.

![](settings-images/intro.png "Aplicaciones de Apple Watch pueden usar la misma funcionalidad de configuración como aplicaciones de iOS")

La configuración se almacenará en una ubicación de archivo compartido que sea accesible para la aplicación de iOS y la extensión de la aplicación de inspección, definido por un **grupo de aplicación**. Debería [configurar un grupo de aplicación](~/ios/watchos/app-fundamentals/app-groups.md) antes de agregar la configuración mediante las instrucciones siguientes.

## <a name="add-settings-in-a-watch-solution"></a>Agregar la configuración en una solución de inspección

En el **aplicación de iPhone** en la solución (*no* la aplicación de inspección o extensión):

1. Haga clic en **Agregar > nuevo archivo...**  y elija **Settings.bundle** (no se puede editar el nombre de la **nuevo archivo** cuadro de diálogo):

   [![](settings-images/settings-add-sml.png "Agregar un nuevo lote de configuración")](settings-images/settings-add.png#lightbox)

2. Cambie el nombre a **configuración Watch.bundle** (seleccione y escriba **comando + R** para cambiar el nombre):

   ![](settings-images/settings-rename.png "Cambiar el nombre de la agrupación")

3. Agregue una nueva clave `ApplicationGroupContainerIdentifier` a la **Root.plist** con el valor establecido para el grupo de aplicación ha configurado, (p. ej. `group.com.xamarin.WatchSettings` en el ejemplo):

   [ ![](settings-images/settings-appgroup-sml.png "Agregar una clave de ApplicationGroupContainerIdentifier a la Root.plist")](settings-images/settings-appgroup.png#lightbox)

4. Editar la **Settings-Watch.bundle/Root.plist** para que contenga las opciones que se va a usar: el archivo de plantilla contiene un grupo.
  campo de texto, modificador para alternar y control deslizante predeterminada (lo que se puede eliminar y reemplazar con su propia configuración):

  [![](settings-images/rootplist-sml.png "Editar el Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Usar la configuración de la aplicación de inspección

Para obtener acceso a los valores seleccionados por el usuario, cree una `NSUserDefaults` instancia mediante el grupo de aplicación y la especificación `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Aplicación de Apple Watch

[![](settings-images/settings-app-sml.png "La nueva aplicación de Apple Watch en el iPhone")](settings-images/settings-app.png#lightbox)

Los usuarios interactúan con la configuración a través del nuevo **de Apple Watch** aplicación en su iPhone. Esta aplicación permite al usuario mostrar u ocultar aplicaciones en la inspección y también editar la configuración expone mediante el **Watch.bundle configuración**.

![](settings-images/applewatch-1.png "Ejemplo de configuración de la aplicación") ![ ] (settings-images/applewatch-2.png "ejemplo de configuración de la aplicación")



## <a name="related-links"></a>Vínculos relacionados

- [Documento de configuración de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
