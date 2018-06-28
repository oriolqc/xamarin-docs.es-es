---
title: Empezar a trabajar con Xamarin.Essentials
description: Xamarin.Essentials proporciona una API sencilla multiplataforma que funciona con iOS, Android o UWP aplicación que puede tener acceso desde compartir código con independencia de cómo se crea la interfaz de usuario.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a42086f70eb81a761358655b3effb9f8f934c8d4
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067382"
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
| UWP | 10.0.16299.0 o superior |

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

    Xamarin.Essentials es compatible con una versión mínima de Android de 4.4, correspondiente a nivel de API 19, pero la versión de Android de destino de compilación debe ser 8.1, correspondiente a nivel de API 27. (En Visual Studio, estas dos versiones se establecen en el cuadro de diálogo de propiedades del proyecto para el proyecto Android, en la ficha manifiesto de Android. En Visual Studio para Mac, está configurados en el cuadro de diálogo Opciones de proyecto para el proyecto Android, en la pestaña aplicación Android.) 
    
    Xamarin.Essentials instala la versión 27.0.2 de las bibliotecas de Xamarin.Android.Support que requiere. Otras bibliotecas de Xamarin.Android.Support que requiera la aplicación también deben actualizarse a la versión 27.0.2 mediante el Administrador de paquetes de NuGet. Todas las bibliotecas de Xamarin.Android.Support utilizadas por la aplicación debe ser el mismo y debe tener al menos la versión 27.0.2. Hacer referencia a la [solución de problemas de página](troubleshooting.md) si tiene problemas al agregar Xamarin.Essentials NuGet o actualizar NuGets en la solución.

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
