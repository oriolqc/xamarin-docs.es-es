---
title: Xamarin.Essentials
description: Xamarin.Essentials proporciona una API sencilla multiplataforma que funciona con iOS, Android o UWP aplicación que puede tener acceso desde compartir código con independencia de cómo se crea la interfaz de usuario.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 960682c4b7776214ab303e861be4e3a8bfa9388a
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-xamarinessentials"></a>Empezar a trabajar con Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials proporciona una API sencilla multiplataforma que funciona con iOS, Android o UWP aplicación que puede tener acceso desde compartir código con independencia de cómo se crea la interfaz de usuario.

## <a name="platform-support"></a>Compatibilidad de la plataforma

Xamarin.Essentials admite los siguientes sistemas operativos y plataformas:

| Plataforma | Versión |
| --- | --- |
| Android | 4.4 (API 19) o superior |
| iOS |10,0 o superior |
| UWP | 10.0.6299.0 o superior |

## <a name="installation"></a>Instalación

Xamarin.Essentials está disponible como un paquete de NuGet que puede agregarse a cualquier proyecto nuevo o existente mediante Visual Studio.

1. Descargue e instale [Visual Studio](http://visualstudio.com) con el [Visual Studio tools para Xamarin](~/cross-platform/get-started/installation/index.md).

2. Abrir un proyecto existente o crear un nuevo proyecto mediante la plantilla aplicación vacía en **Visual Studio C#** (Android, iPhone y iPad o multiplataforma). **Importante**: si Asegúrese de agregar a un proyecto UWP compilación 16299 o superior se establece en las propiedades del proyecto.

3. Agregar el **Xamarin.Essentials** paquete NuGet para cada proyecto:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    En el panel Explorador de soluciones, haga clic con el botón secundario en el nombre de la solución y seleccione **administrar paquetes de NuGet**. Busque **Xamarin.Essentials** e instale el paquete en **todos los** proyectos incluidas las bibliotecas de Android, iOS, UWP y .NET estándar.

    > [!TIP]
    > Compruebe el **versión preliminar de inclusión** cuadro mientras el [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está en vista previa.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    En el panel Explorador de soluciones, haga clic con el botón secundario en el nombre del proyecto y seleccione **Agregar > Agregar paquetes de NuGet...** . Busque **Xamarin.Essentials** e instale el paquete en **todos los** proyectos incluidas las bibliotecas, iOS y Android .NET estándar.

    > [!TIP]
    > Compruebe el **mostrar los paquetes de versión preliminar** cuadro mientras el [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está en vista previa.

    -----

4. Agregue una referencia a Xamarin.Essentials en cualquier clase de C# para hacer referencia a las API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials requiere que el programa de instalación específico de la plataforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    En el proyecto de Android `MainLauncher` o cualquier `Activity` decir Xamarin.Essentials iniciado debe inicializarse en el `OnCreate` método:

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    Para controlar los permisos en tiempo de ejecución en Android, Xamarin.Essentials debe recibir cualquier `OnRequestPermissionsResult`. Agregue el código siguiente a todos los `Activity` clases:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    No se necesita ninguna instalación adicional.

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    No se necesita ninguna instalación adicional.

    -----

6. Siga el [Xamarin.Essentials guías](index.md) que le permiten copiar y pegar fragmentos de código para cada característica.

## <a name="other-resources"></a>Otros recursos

Se recomienda a los desarrolladores familiarizados con Xamarin visita [Introducción al desarrollo de Xamarin](~/cross-platform/getting-started/index.md).

Visite la [repositorio de GitHub de Xamarin.Essentials](http://github.com/xamarin/Essentials) para ver este código, qué viene a continuación después, ejecutar ejemplos, fuente y cierre el repositorio. Contribuciones a la Comunidad son bienvenidas.

Examine la [documentación de la API](xref:Xamarin.Essentials) para todas las características de Xamarin.Essentials.
