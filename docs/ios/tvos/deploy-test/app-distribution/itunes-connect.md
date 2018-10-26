---
title: Configurar la aplicación tvOS en iTunes Connect
description: En este artículo proporciona a una guía complementaria a configurar la aplicación en iTunes Connect para las configuraciones específicas de tvOS iOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3f4ef00cfe990de2d5afd461d7a110d32bc4a236
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108826"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurar la aplicación tvOS en iTunes Connect

_En este artículo proporciona a una guía complementaria a configurar la aplicación en iTunes Connect para las configuraciones específicas de tvOS iOS._


Además de las configuraciones y configuración que necesite realizar siguiendo el iOS [configuración de la aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guía, este documento tratan las configuraciones específicas que será necesario para liberar un Xamarin.tvOS aplicación en el Store de Apple TV App.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Adición de una versión de tvOS

Si está creando una nueva aplicación para publicarse en el Store de Apple TV App o agregar soporte técnico de Apple TV a una aplicación iOS existente, tiene que ha creado un iTunes Connect registro y configurarlo mediante el siguiente iOS guías específicas:

- [Crear un registro de iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Administración de vídeos y capturas de pantalla de aplicaciones](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Administración de nombres, descripciones, novedades, palabras clave y direcciones URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Mantener la información General](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Si lo desea, también puede requerir:

- [Mantener la información del Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Mantener la información de compra en la aplicación](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Con todos los pasos anteriores completado, abra el registro de iTunes Connect y seleccione esta opción para agregar compatibilidad de tvOS utilizando la barra lateral de la parte izquierda de la aplicación:

[![](itunes-connect-images/connect01.png "Agregar compatibilidad de tvOS utilizando la barra lateral izquierda")](itunes-connect-images/connect01.png#lightbox)

Las pantallas de información específica de tvOS, a continuación, estará disponibles para el determinado registro de iTunes Connect:

[![](itunes-connect-images/connect02.png "La pantalla de información específica de tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>Información de versión de tvOS

En la barra lateral izquierda, seleccione **1.0 preparación para el envío** en la sección aplicación tvOS:

[![](itunes-connect-images/connect03.png "Información de versión de tvOS")](itunes-connect-images/connect03.png#lightbox)

En esta pantalla, proporcione la siguiente información:

- Las necesarias las capturas de pantalla, descripción, palabras clave y direcciones URL.
- Información general de la aplicación, como el número de versión, Copyright y clasificación por edades.
- Adquisición opcional de la aplicación.
- Soporte técnico de centro de juegos opcional con marcadores y logros.
- La información de revisión de la aplicación como contacto, cuentas de demostración y notas de la necesaria.

Una vez haya escrito la información necesaria, haga clic en el **guardar** botón en la esquina superior derecha de la pantalla para guardar los cambios:

[![](itunes-connect-images/connect04.png "listo para el envío de información de versión de tvOS")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparando para enviar para revisión

Cuando haya terminado, por último enviar la aplicación Xamarin.tvOS el Store de Apple TV App para su revisión, vuelva a registro de iTunes la aplicación en el Connect y haga clic en el **enviar para revisión** situado en la esquina superior derecha de la pantalla:

[![](itunes-connect-images/connect05.png "Enviar para revisión")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo le da información general de la configuración específica de tvOS necesaria en iTunes Connect para publicar una aplicación tvOS en el Store de Apple TV App.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
