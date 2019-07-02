---
ms.openlocfilehash: 9143b65b2e7cb9b85195e3c20695e3034295b2d2
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277444"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).
- Tutorial sobre las [entradas](~/get-started/tutorials/entry/index.yml) (Entry).

En este tutorial aprenderá a:

> [!div class="checklist"]
> - Responder a una aplicación que se inicia, está en modo de suspensión o se reanuda.
> - Conservar datos entre los cambios de estado del ciclo de vida.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo conservar los datos entre los cambios de estado del ciclo de vida. En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de un objeto Entry cuya propiedad Text se conserva a lo largo de los cambios de estado del ciclo de vida, en iOS y Android](../images/persist-data.png "Objeto Entry cuya propiedad Text se conserva a lo largo de los cambios de estado del ciclo de vida")](../images/persist-data-large.png#lightbox "Objeto Entry cuya propiedad Text se conserva a lo largo de los cambios de estado del ciclo de vida")
