---
ms.openlocfilehash: cef0b8f56639e7bc8571ab01b820dfd54b074472
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277459"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **WebServiceTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **WebServiceTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, seleccione el proyecto **WebServiceTutorial**, haga clic con el botón derecho y, después, seleccione **Administrar paquetes NuGet…** :

    ![Captura de pantalla del elemento de menú Agregar paquetes NuGet seleccionado](../images/vs/add-nuget-packages.png "Elemento de menú Agregar paquetes NuGet")

1. En el **Administrador de paquetes NuGet**, seleccione la pestaña **Examinar**, busque el paquete NuGet **Newtonsoft.Json**, selecciónelo y haga clic en el botón **Instalar** para agregarlo al proyecto:

    ![Captura de pantalla del paquete NuGet Newtonsoft.Json en el Administrador de paquetes NuGet](../images/vs/add-package.png "Paquete NuGet Newtonsoft.JsonT")

    Este paquete se usará para incorporar la deserialización de JSON en la aplicación.

1. Compile la solución para asegurarse de que no haya errores.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **WebServiceTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **WebServiceTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En **Panel de solución**, seleccione el proyecto **WebServiceTutorial**, haga clic con el botón derecho y seleccione **Agregar > Agregar paquetes NuGet…** :

    ![Captura de pantalla del elemento de menú Agregar paquetes NuGet seleccionado](../images/vsmac/add-nuget-packages.png "Elemento de menú Agregar paquetes NuGet")

1. En la ventana **Agregar paquetes**, busque el paquete NuGet **Newtonsoft.Json**, selecciónelo y haga clic en el botón **Agregar paquete** para agregarlo al proyecto:

    ![Captura de pantalla del paquete NuGet Newtonsoft.Json en el Administrador de paquetes NuGet](../images/vsmac/add-package.png "Paquete NuGet Newtonsoft.JsonT")

    Este paquete se usará para incorporar la deserialización de JSON en la aplicación.

1. Compile la solución para asegurarse de que no haya errores.
