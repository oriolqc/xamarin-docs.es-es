---
redirect_url: /xamarin/cross-platform/troubleshooting/questions/
title: ¿Puedo usar a 2017 Release Candidate de Visual Studio con Xamarin?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8E752F36-F73A-4EFC-9F82-4E18FDE1C9E2
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4c93653793c7c8cce0d2257107097c89f73fc726
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>¿Puedo usar a 2017 Release Candidate de Visual Studio con Xamarin?

## <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>¿Puedo usar a 2017 Release Candidate de Visual Studio con Xamarin?

Sí. Es posible aprovechar Xamarin a través de Visual Studio de 2017 Release Candidate. Sin embargo, tenga en cuenta que en la actualidad, la instalación de Xamarin para Visual Studio de 2017 RC se desinstala cualquier versión anterior de Xamarin instalado en Visual Studio 2015/2013. Xamarin para Visual Studio de 2017 puede no instalarse al mismo tiempo como Xamarin para la Visual Studio 2015/2013 como resultado de Visual Studio de 2017 alejándose de empaquetado de MSI hacia la utilización del sistema instalador de Visual Studio.

Mientras el equipo está estudiando actualmente formas para omitir este comportamiento esperado, se recomienda a los usuarios elegir su entorno de desarrollo según sus necesidades. 

> [!IMPORTANT]
> Si va a crear proyectos de Xamarin.iOS, asegúrese de que Visual Studio para Mac en el sistema Mac emparejado se encuentra en la misma versión de canal de Xamarin.iOS.

## <a name="how-do-i-install-xamarin-to-visual-studio-2017-release-candidate"></a>¿Cómo se instala Xamarin para Visual Studio de 2017 Release Candidate?

### <a name="installing-during-the-visual-studio-2017-rc-installer"></a>Instalar durante el instalador de Visual Studio de 2017 RC

* Seleccione el **Xamarin** componente como parte de la nueva **instalador de Visual Studio**

  [![](visualstudio-2017-rc-images/install1-sml.png "Pantalla del instalador de RC de 2017 de Visual Studio")](visualstudio-2017-rc-images/install1-orig.png#lightbox)

Este modo se instalará la extensión de Visual Studio para el desarrollo de Xamarin.iOS y Xamarin.Android.

### <a name="installing-or-reinstalling-xamarin-in-an-existing-installation-of-visual-studio-2017-rc"></a>Instalar o volver a instalar Xamarin en una instalación existente de Visual Studio de 2017 RC

#### <a name="using-the-visual-studio-installer"></a>Con el instalador de Visual Studio:

1. Busque la aplicación del instalador de Visual Studio

  [![](visualstudio-2017-rc-images/reinstall1-sml.png "Resultados de búsqueda para la aplicación del instalador de Visual Studio")](visualstudio-2017-rc-images/reinstall1-orig.png#lightbox)

2. Seleccione: un. **Desarrollo móvil con .NET (versión preliminar)** en la pestaña de las cargas de trabajo, o

  [![](visualstudio-2017-rc-images/reinstall2-sml.png "Ficha de cargas de trabajo de VS Installer") ](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b. **Xamarin** en el **componentes individuales** ficha

  [![](visualstudio-2017-rc-images/reinstall3-sml.png "Ficha de componentes del instalador de VS")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)

#### <a name="using-the-visual-studio-installer-within-visual-studio"></a>Con el instalador de Visual Studio en Visual Studio:
1. Vaya a la página de inicio de Visual Studio de 2017
2. Haga clic en **más plantillas de proyecto** en el **nuevo proyecto** sección

    [![](visualstudio-2017-rc-images/reinstall4-sml.png "Página de inicio de Visual Studio")](visualstudio-2017-rc-images/reinstall4-orig.png#lightbox)
3. Haga clic en `Open Visual Studio Installer` en el panel izquierdo

    [![](visualstudio-2017-rc-images/reinstall5-sml.png "Nuevo proyecto, pantalla")](visualstudio-2017-rc-images/reinstall5-orig.png#lightbox)
4. Seleccione:
    
    a. **Desarrollo móvil con .NET (versión preliminar)** en la pestaña de las cargas de trabajo, o

    [![](visualstudio-2017-rc-images/reinstall2-sml.png "Ficha de cargas de trabajo de VS Installer") ](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b. **Xamarin** en el **componentes individuales** ficha

    [![](visualstudio-2017-rc-images/reinstall3-sml.png "Ficha de componentes del instalador de VS")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)
