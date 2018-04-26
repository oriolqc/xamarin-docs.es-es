---
title: Agregar una aplicación de Windows Phone
ms.prod: xamarin
ms.assetid: B598FA9D-6818-4CC9-8191-838C156DB9DA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 55bd4bdcfde4c91ad5c9b94bef486207466e135d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-windows-phone-app"></a>Agregar una aplicación de Windows Phone


En primer lugar, si se usa la plantilla Xamarin.Forms PCL, [actualizar el perfil](~/xamarin-forms/platform/windows/installation/index.md), a continuación, siga las instrucciones siguientes:

1. **Haga doble clic en la solución > Agregar > Nuevo proyecto...**  y agregue un **aplicación vacía (Windows Phone)**

  ![](phone-images/add-wp81.png "Agregar cuadro de diálogo nuevo proyecto")

2. **Haga doble clic en el proyecto recién creado > Administrar paquetes de NuGet...**  y agregue el **Xamarin.Forms** paquete.

3. **Haga doble clic en el proyecto > Agregar > referencia** y crear una referencia de proyecto al proyecto de aplicación compartida de Xamarin.Forms.

  ![](phone-images/addref.png "Cuadro de diálogo Administrador de referencias")

4. Editar **App.xaml.cs** para incluir el `Init()` llamada al método, en la `OnLaunched` método alrededor de la línea 67:

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . Editar **MainPage.xaml** -cambiar el elemento raíz `<Page` a `<forms:WindowsPhonePage` *y* definir la `xmlns:forms` que utiliza:

```xaml
<forms:WindowsPhonePage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPhonePage>
```

 6 . Editar **MainPage.xaml.cs** para quitar el `: PhonePage` especificador de herencia para el nombre de clase.

```csharp
public sealed partial class MainPage  // REMOVE ": PhonePage"
```

 7 . Todavía en **MainPage.xaml.cs**, agregue el `LoadApplication` llamar a en el `MainPage` constructor (alrededor de la línea 28) para iniciar la aplicación de Xamarin.Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . Haga doble clic en **Package.appxmanifest** para establecer estas capacidades que suelen ser necesarias:

  * Internet (cliente y servidor)

9 . Por último, agregue los recursos locales (p. ej. archivos de imagen) de los proyectos existentes de plataforma que son necesarios.

