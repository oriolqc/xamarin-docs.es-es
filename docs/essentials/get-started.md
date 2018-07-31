---
title: Introducción a Xamarin.Essentials
description: Xamarin.Essentials proporciona una API única multiplataforma que funciona con cualquier iOS, Android o UWP compartida de aplicación que se puede acceder desde código sin importar cómo se crea la interfaz de usuario.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c72c1c66a465075770ce739270cb4b1f2c6fba7a
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353781"
---
# <a name="get-started-with-xamarinessentials"></a>Introducción a Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials proporciona una API única multiplataforma que funciona con cualquier iOS, Android o UWP compartida de aplicación que se puede acceder desde código sin importar cómo se crea la interfaz de usuario.

## <a name="platform-support"></a>Compatibilidad de la plataforma

Xamarin.Essentials admite los siguientes sistemas operativos y plataformas:

| Plataforma | Versión |
| --- | --- |
| Android | 4.4 (API 19) o superior |
| iOS |10.0 o posterior |
| UWP | 10.0.16299.0 o superior |

## <a name="installation"></a>Instalación

Xamarin.Essentials está disponible como un paquete de NuGet que se puede agregar a cualquier proyecto nuevo o existente mediante Visual Studio.

1. Descargue e instale [Visual Studio](http://visualstudio.com) con el [Visual Studio tools para Xamarin](~/cross-platform/get-started/installation/index.md).

2. Abra un proyecto existente o crear un nuevo proyecto mediante la plantilla aplicación vacía bajo **Visual Studio C#** (Android, iPhone y iPad o multiplataforma). **Importante**: si agregar a un proyecto UWP Asegúrese compilación 16299 o superior está establecido en las propiedades del proyecto.

3. Agregar el **Xamarin.Essentials** paquete NuGet para cada proyecto:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    En el panel Explorador de soluciones, haga clic con el botón derecho en el nombre de la solución y seleccione **administrar paquetes de NuGet**. Busque **Xamarin.Essentials** e instale el paquete en **todas** proyectos, incluidas las bibliotecas de Android, iOS, UWP y .NET Standard.

    > [!TIP]
    > Compruebe el **incluir versión preliminar** cuadro mientras el [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está en versión preliminar.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    En el panel Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto y seleccione **Agregar > Agregar paquetes NuGet...** . Busque **Xamarin.Essentials** e instale el paquete en **todas** proyectos, incluidas las bibliotecas de Android, iOS y .NET Standard.

    > [!TIP]
    > Compruebe el **mostrar los paquetes preliminares** cuadro mientras el [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está en versión preliminar.

    -----

4. Agregue una referencia a Xamarin.Essentials en cualquier clase de C# para hacer referencia a las API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials requiere una configuración específica de la plataforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials es compatible con una versión de Android mínima de 4.4, correspondiente a nivel de API 19, pero la versión de Android de destino de compilación debe ser 8.1, correspondiente a nivel de API 27. (En Visual Studio, estas dos versiones se establecen en el cuadro de diálogo Propiedades del proyecto para el proyecto Android, en la pestaña manifiesto de Android. En Visual Studio para Mac, establecerlos en el cuadro de diálogo Opciones de proyecto para el proyecto Android, en la pestaña de la aplicación de Android.) 
    
    Xamarin.Essentials instala la versión 27.0.2.1 de las bibliotecas de Xamarin.Android.Support que necesita. Otras bibliotecas de Xamarin.Android.Support que requiera la aplicación también deben actualizarse a la versión 27.0.2.1 con el Administrador de paquetes de NuGet. Todas las bibliotecas de Xamarin.Android.Support usadas por la aplicación debe ser el mismo y debe ser al menos la versión 27.0.2.1. Hacer referencia a la [página de solución de problemas](troubleshooting.md) si tiene problemas al agregar el paquete Xamarin.Essentials NuGet o actualizar paquetes NuGet en la solución.

    En el proyecto de Android `MainLauncher` o cualquier `Activity` que es iniciada Xamarin.Essentials debe inicializarse en el `OnCreate` método:

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    Para controlar los permisos en tiempo de ejecución en Android, debe recibir Xamarin.Essentials cualquier `OnRequestPermissionsResult`. Agregue el código siguiente a todos los `Activity` clases:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    No se requiere ninguna configuración adicional.

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    No se requiere ninguna configuración adicional.

    -----

6. Siga el [Xamarin.Essentials guías](index.md) que le permiten copiar y pegar los fragmentos de código para cada característica.

## <a name="other-resources"></a>Otros recursos

Se recomienda que los programadores nuevos en Xamarin visita [Introducción al desarrollo de Xamarin](~/cross-platform/getting-started/index.md).

Visite el [repositorio de GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) ver actual de código fuente, lo que vendrá después, ejecutar los ejemplos y cierre el repositorio. ¡Contribuciones de la Comunidad son bienvenidas!

Examine el [documentación de API](xref:Xamarin.Essentials) para todas las características de Xamarin.Essentials.
