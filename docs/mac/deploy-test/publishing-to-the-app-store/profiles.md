---
title: Perfiles de aprovisionamiento
description: "En esta guía se describe el proceso de creación de los perfiles de aprovisionamiento necesarios para publicar una aplicación Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: 4bb6f0c219fc973d3d2e458445c76fd7611681ec
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="provisioning-profiles"></a>Perfiles de aprovisionamiento

Los perfiles de aprovisionamiento permiten a los desarrolladores incorporar en sus aplicaciones de Xamarin.Mac varias características específicas de macOS (antes conocido como Mac OS X), como iCloud y notificaciones de inserción. Es necesario crear, descargar e instalar un perfil de aprovisionamiento de Mac para cada aplicación que se desarrolle y que use estas características.

[ ![](profiles-images/certif13.png "El Portal de aprovisionamiento de Apple")](profiles-images/certif13.png)

<a name="Development_Provisioning_Profile" />

## <a name="development-provisioning-profile"></a>Perfil de aprovisionamiento de desarrollo

Un perfil de aprovisionamiento de desarrollo permite que una aplicación destinada a Mac App Store se pruebe en los equipos específicos que se han configurado en el perfil. Esto es especialmente importante cuando se usan características de macOS como iCloud y las notificaciones de inserción.

> [!NOTE]
> El desarrollador debe haber creado un certificado de desarrollo de Mac para poder crear un perfil de aprovisionamiento de desarrollo. Complete los detalles como se indica en la captura de pantalla para generar un **perfil de aprovisionamiento de desarrollo** que se pueda usar para crear compilaciones. Debe haber un certificado de desarrollo de Mac válido disponible para su selección en el cuadro **Certificado** y, como mínimo, un sistema registrado para las pruebas.

Haga lo siguiente:

1. Seleccione el tipo de perfil de aprovisionamiento que se va a crear y haga clic en el botón **Continuar**: 

     [ ![](profiles-images/certif14.png "Seleccionar el tipo de perfil")](profiles-images/certif14.png)
2. Seleccione el identificador de la aplicación para la que se va a crear el perfil y haga clic en el botón **Continuar**: 

     [ ![](profiles-images/certif15.png "Seleccionar el identificador de la aplicación")](profiles-images/certif15.png)
3. Seleccione el identificador de desarrollador usado para firmar el perfil y haga clic en **Continuar**: 

     [ ![](profiles-images/certif16.png "Seleccionar el identificador del desarrollador")](profiles-images/certif16.png)
4. Seleccione los equipos en los que se puede usar este perfil y haga clic en **Continuar**: 

     [ ![](profiles-images/certif17.png "Seleccionar los equipos permitidos")](profiles-images/certif17.png)
5. Ahora, especifique un **nombre de perfil** y haga clic en el botón **Generar**: 

     [ ![](profiles-images/certif18.png "Generar el perfil")](profiles-images/certif18.png)
6. Haga clic en el botón **Descargar** para descargar el nuevo perfil: 

     [ ![](profiles-images/certif19.png "Descargar el perfil")](profiles-images/certif19.png)
7. Los perfiles de aprovisionamiento de desarrollo se instalan en el panel de preferencias de perfiles de la aplicación **Preferencias del sistema** de Mac: 

     [ ![](profiles-images/certif20.png "Instalar el perfil")](profiles-images/certif20.png)
8. En el panel de preferencias de perfiles se mostrarán todos los perfiles instalados: 

     [ ![](profiles-images/image47.png "Mostrar todos los perfiles instalados")](profiles-images/image47.png)
9. El perfil también aparecerá en la **utilidad de certificados de desarrollador** en caso de que tenga que volver a descargarse: 

     [ ![](profiles-images/image48.png "La utilidad de certificado de desarrollador")](profiles-images/image48.png)

Será necesario crear un perfil de aprovisionamiento de desarrollo para cada aplicación nueva o cuando se agregue un equipo nuevo para realizar pruebas en él.

<a name="Production_Provisioning_Profile" />

## <a name="production-provisioning-profile"></a>Perfil de aprovisionamiento de producción

Los perfiles de aprovisionamiento de producción son necesarios para compilar un paquete para enviarlo a Mac App Store.

Haga lo siguiente:

1. Seleccione el tipo de perfil que se va a crear y haga clic en el botón **Continuar**: 

    [ ![](profiles-images/certif21.png "Seleccionar el tipo de perfil")](profiles-images/certif21.png)
2. Seleccione el identificador de la aplicación para la que se va a crear el perfil y haga clic en el botón **Continuar**: 

    [ ![](profiles-images/certif15.png "Seleccionar el identificador de la aplicación")](profiles-images/certif15.png)
3. Seleccione el identificador de empresa para firmar el perfil y haga clic en el botón **Continuar**: 

    [ ![](profiles-images/certif23.png "Seleccionar el identificador de empresa")](profiles-images/certif23.png)
4. Especifique un **nombre de perfil** y haga clic en el botón **Generar**: 

    [ ![](profiles-images/certif24.png "Generar el perfil")](profiles-images/certif24.png)
5. Haga clic en **Descargar** para obtener el archivo del perfil de aprovisionamiento (extensión `.provisionprofile`): 

    [ ![](profiles-images/certif25.png "Descargar el perfil")](profiles-images/certif25.png)
6. Arrástrelo al **organizador de Xcode** o haga doble clic en él para instalarlo. El perfil aparecerá en el organizador de Xcode: 

    [ ![](profiles-images/image51.png "Instalar el perfil")](profiles-images/image51.png)
7. El perfil de aprovisionamiento también aparecerá en la lista: 

    [ ![](profiles-images/certif26.png "Mostrar los perfiles instalados")](profiles-images/certif26.png)


Si el desarrollador cambia en algún momento las características que un identificador de aplicación usa (por ejemplo, habilita iCloud o las notificaciones de inserción), debe volver a crear los perfiles de aprovisionamiento para ese identificador de aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](~//mac/get-started/installation.md)
- [Ejemplo de Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guía de herramientas: firma de código de la aplicación](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
