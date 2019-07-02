---
ms.openlocfilehash: 004d7df72103ef332f802bd0019d2a99c6cc11a2
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277453"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).
- Tutorial sobre los [botones](~/get-started/tutorials/button/index.yml) (Button).
- Tutorial sobre las [entradas](~/get-started/tutorials/entry/index.yml) (Entry).
- Tutorial sobre las [vistas de lista](~/get-started/tutorials/listview/index.yml) (ListView).

En este tutorial aprenderá a:

> [!div class="checklist"]
> - Usar el Administrador de paquetes NuGet para agregar SQLite.NET a un proyecto de Xamarin.Forms.
> - Crear las clases de acceso a datos.
> - Consumir las clases de acceso a datos.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo almacenar datos en una base de datos SQLite.NET local. En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de la persistencia de datos de la base de datos local SQLite.NET, en iOS y Android](../images/consume-data-access-classes-reduced.png "Persistencia de datos de la base de datos local")](../images/consume-data-access-classes-large.png#lightbox "Persistencia de datos de la base de datos local")
