---
ms.openlocfilehash: aee7c7eb494af76bb450038453c19ee1ed83f2d3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388901"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **WebServiceTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **WebServiceTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, seleccione el proyecto **WebServiceTutorial**, haga clic con el botón derecho y, después, seleccione **Administrar paquetes NuGet…**:

    ![Captura de pantalla del elemento de menú Agregar paquetes NuGet seleccionado](../images/vs/add-nuget-packages.png "Elemento de menú Agregar paquetes NuGet")

1. En el **Administrador de paquetes NuGet**, seleccione la pestaña **Examinar**, busque el paquete NuGet **Newtonsoft.Json**, selecciónelo y haga clic en el botón **Instalar** para agregarlo al proyecto:

    ![Captura de pantalla del paquete NuGet Newtonsoft.Json en el Administrador de paquetes NuGet](../images/vs/add-package.png "Paquete NuGet Newtonsoft.JsonT")

    Este paquete se usará para incorporar la deserialización de JSON en la aplicación.

1. Compile la solución para asegurarse de que no haya errores.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **WebServiceTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **WebServiceTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En **Panel de solución**, seleccione el proyecto **WebServiceTutorial**, haga clic con el botón derecho y seleccione **Agregar > Agregar paquetes NuGet…**:

    ![Captura de pantalla del elemento de menú Agregar paquetes NuGet seleccionado](../images/vsmac/add-nuget-packages.png "Elemento de menú Agregar paquetes NuGet")

1. En la ventana **Agregar paquetes**, busque el paquete NuGet **Newtonsoft.Json**, selecciónelo y haga clic en el botón **Agregar paquete** para agregarlo al proyecto:

    ![Captura de pantalla del paquete NuGet Newtonsoft.Json en el Administrador de paquetes NuGet](../images/vsmac/add-package.png "Paquete NuGet Newtonsoft.JsonT")

    Este paquete se usará para incorporar la deserialización de JSON en la aplicación.

1. Compile la solución para asegurarse de que no haya errores.
