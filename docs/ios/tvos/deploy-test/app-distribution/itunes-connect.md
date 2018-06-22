---
title: Configure su aplicación tvOS en iTunes Connect
description: Este artículo proporciona a una guía complementaria al configurar la aplicación en iTunes Connect para tvOS determinadas configuraciones de iOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d082a980572349da1b7e6155b3aa4de41512796f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30779831"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configure su aplicación tvOS en iTunes Connect

_Este artículo proporciona a una guía complementaria al configurar la aplicación en iTunes Connect para tvOS determinadas configuraciones de iOS._


Además de las configuraciones y la configuración que debe generar si sigue el iOS [configurar la aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guía, este documento tratan las configuraciones específicas que se requerirá para liberar una Xamarin.tvOS aplicación en la tienda de aplicaciones de TV de Apple.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Agregar una versión de lanzamiento de tvOS

Si va a crear una nueva aplicación que se liberen en App Store de Apple TV, o agregar soporte técnico de Apple TV a una aplicación iOS existente, deberá ha creado un iTunes Connect registro y configurarlo con el siguiente iOS guía específica:

- [Crear un registro de iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Administración de vídeos y capturas de pantalla de aplicaciones](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Administración de nombres, descripciones, novedades, palabras clave y direcciones URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Mantener la información General](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Si lo desea, es posible que también necesite:

- [Mantener la información del Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Mantener la información de compra en la aplicación](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Con todos los pasos anteriores completados, abra la aplicación iTunes Connect registro y seleccione esta opción para agregar compatibilidad para tvOS mediante la barra lateral izquierdo:

[![](itunes-connect-images/connect01.png "Agregar compatibilidad para tvOS mediante la barra lateral izquierdo")](itunes-connect-images/connect01.png#lightbox)

Las pantallas de información específica de tvOS, a continuación, estará disponibles para iTunes Connect registro determinado:

[![](itunes-connect-images/connect02.png "La pantalla de información específica de tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS información de versión

En la barra lateral izquierdo, seleccione **1.0 preparación para el envío del** en la sección tvOS aplicación:

[![](itunes-connect-images/connect03.png "tvOS información de versión")](itunes-connect-images/connect03.png#lightbox)

En esta pantalla, proporcione la siguiente información:

- El necesario capturas de pantalla, descripción, palabras clave y las direcciones URL.
- Información de aplicación general, como el número de versión, Copyright y clasificación por edades.
- Opcional en la aplicación de las compras.
- Soporte técnico de centro de juegos opcional con tablas de líderes y logros.
- La información de revisión de aplicación como contacto, las cuentas de demostración y notas de la necesaria.

Una vez que ha escrito la información necesaria, haga clic en el **guardar** botón en la esquina superior derecha de la pantalla para guardar los cambios:

[![](itunes-connect-images/connect04.png "tvOS listo para el envío de información de versión")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparando para enviar para su revisión

Cuando haya llegado el momento de enviar tu aplicación Xamarin.tvOS a la tienda de aplicaciones de TV de Apple para su revisión, volver a iTunes Connect registro de la aplicación y haga clic en el **enviar para su revisión** situado en la esquina superior derecha de la pantalla:

[![](itunes-connect-images/connect05.png "Enviar para su revisión")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo le da información general sobre la configuración específica del tvOS necesaria en iTunes Connect para publicar una aplicación de tvOS a la tienda de aplicaciones de TV de Apple.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
