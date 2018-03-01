---
title: "Agregar una aplicación de Windows"
ms.topic: article
ms.prod: xamarin
ms.assetid: ADF99B78-F1BC-48DF-9128-01B93C4411C1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 369e8005c1d398c90b14a95ac36fc8659e462fbf
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="adding-a-windows-app"></a>Agregar una aplicación de Windows


1. **Haga doble clic en la solución > Agregar > Nuevo proyecto...**  y agregue un **aplicación vacía (Windows)**

 ![](tablet-images/add-wu.png "Agregar cuadro de diálogo nuevo proyecto")

2. **Haga doble clic en el proyecto > Administrar paquetes de NuGet...**  y agregue el **Xamarin.Forms** paquete.

3. **Haga doble clic en el proyecto > Agregar > referencia** y crear una referencia de proyecto al proyecto de aplicación compartida de Xamarin.Forms.

  ![](tablet-images/addref.png "Cuadro de diálogo Administrador de referencias")

4. Editar **App.xaml.cs** para incluir el `Init()` llamada a un método dentro de la `OnLaunched` método alrededor de la línea 65

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . Editar **MainPage.xaml** -cambiar el elemento raíz `<Page` a `<forms:WindowsPage` *y* definir la `xmlns:forms` que utiliza:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPage>
```


 6 . Editar **MainPage.xaml.cs** para quitar el `: Page` especificador de herencia para el nombre de clase.

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 7 . Todavía en **MainPage.xaml.cs**, agregue el `LoadApplication` llamar a en el `MainPage` constructor (alrededor de la línea 28) para iniciar la aplicación de Xamarin.Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . Haga doble clic en **Package.appxmanifest** para establecer estas capacidades que suelen ser necesarias:

  Conjunto de capacidades:

  * Internet (cliente)
  * Ubicación

9 . Por último, agregue los recursos locales (p. ej. archivos de imagen) de los proyectos existentes de plataforma que son necesarios.

