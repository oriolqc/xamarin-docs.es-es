---
title: información general sobre la distribución de aplicaciones de tvOS
description: Este documento proporciona información general sobre las técnicas de distribución que están disponibles para la aplicación Xamarin.tvOS y actúa como un puntero a documentos más detallados sobre el tema.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0a40d50d02008439e81d5db19bcda0647203e2da
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110705"
---
# <a name="tvos-app-distribution-overview"></a>información general sobre la distribución de aplicaciones de tvOS

_Este documento proporciona información general sobre las técnicas de distribución que están disponibles para la aplicación Xamarin.tvOS y actúa como un puntero a documentos más detallados sobre el tema._


Una vez que se ha desarrollado la aplicación Xamarin.tvOS, el siguiente paso del ciclo de vida de desarrollo de software es distribuirla a los usuarios, como se muestra en la sección resaltada de en el diagrama siguiente:


[![Introducción al ciclo de vida de desarrollo de software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple proporciona los siguientes métodos para distribuir una aplicación de tvOS, que son compatibles con Xamarin.tvOS:

1. [**La App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interna (Enterprise)**](#In-House-Distribution) 
2. [**Ad Hoc**](#Ad_Hoc_Distribution) 

Todos estos escenarios requieren que las aplicaciones se aprovisionen mediante el correspondiente *perfil de aprovisionamiento*. Los perfiles de aprovisionamiento son archivos que contienen información de firma de código, así como la identidad de la aplicación y el mecanismo de distribución previsto. También contienen información sobre en qué dispositivos se puede implementar la aplicación para la distribución que no se realice a través del App Store.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribución del App Store de Apple TV

Esto es la principal manera que las aplicaciones de tvOS se distribuyen a los consumidores en dispositivos de Apple TV. Todas las aplicaciones enviadas al Store de Apple TV App requieren la aprobación de Apple.

Las aplicaciones se envían a la App Store a través de un portal llamado *iTunes Connect*. Consulte nuestra [configuración de la aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guía para obtener información sobre los temas siguientes:

- Administrar contratos, impuestos y banca.
- Crear un iTunes Connect registro.
- Administración de capturas de pantalla y vídeos de la aplicación.
- Administrar aplicación nombres, descripciones, novedades, palabras clave y direcciones URL.
- Mantenimiento de la información General de la aplicación.
- Mantener la información del Game Center.
- Mantener la información de revisión de la aplicación.
- Mantener la información de precios.
- Mantener la información de compra en la aplicación.
- Ver las revisiones de la aplicación.

Mientras no se ha diseñado específicamente para tvOS, este documento proporciona información sobre cómo configurar y usar este portal para preparar la aplicación para su publicación en el Store de Apple TV App. Muchos de los mismos pasos son necesarios si está configurando una aplicación iOS o tvOS.

Una vez completados todos los pasos enumerados anteriormente, consulte nuestra [configurar la aplicación tvOS en iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) para agregar la información específica de aplicación de tvOS que será necesaria.

Es importante tener en cuenta que solo los desarrolladores que pertenecen al **Programa para desarrolladores de Apple** tienen acceso a iTunes Connect. Los miembros del **Programa para desarrolladores empresariales de Apple** no tienen acceso.

Si tiene problemas para enviar su aplicación Xamarin.tvOS al Store de Apple TV App, consulte nuestra [Troubleshooting](~/ios/tvos/troubleshooting.md) guía. Contiene varios problemas conocidos que pueden surgir y cómo resolverlos en el Xamarin.tvOS.

Para obtener más información, visite la [publicar en el Store de Apple TV App](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) guía.

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribución interna

A veces denominada *Distribución empresarial*, la distribución interna permite a los miembros del **Programa para desarrolladores empresariales de Apple** distribuir aplicaciones internamente a otros miembros de la misma organización. La distribución interna tiene las ventajas de no requerir una revisión de la App Store y no tener ningún límite en el número de dispositivos en los que se puede instalar una aplicación. Sin embargo, es importante tener en cuenta que los miembros del **Programa para desarrolladores empresariales de Apple** **no** tienen acceso a iTunes Connect y, por lo tanto, el licenciatario es responsable de distribuir la aplicación.

Para obtener más información sobre cómo configurar y cómo distribuir la aplicación de forma interna, consulte el [Guía de distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md). Este documento es específica de iOS, pero se usan las mismas técnicas para aplicaciones de tvOS.

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribución ad hoc

Aplicaciones Xamarin.tvOS pueden ser la prueba de usuario a través de la distribución ad hoc, que está disponible tanto en el **Apple Developer Program**y el **Apple Developer Enterprise Program**y permite hasta 100 dispositivos de Apple TV va a probar. El mejor caso de uso para la distribución ad hoc es distribución dentro de su empresa cuando iTunes Connect no es una opción.

Para obtener más información sobre cómo configurar y cómo distribuir la aplicación de forma interna, consulte el [Guía de distribución Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Nuevamente, este documento es específica de iOS, pero se usan las mismas técnicas para aplicaciones de tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo le da una breve descripción de los mecanismos de distribución que están disponibles para aplicaciones Xamarin.tvOS. Introdujo el Store de aplicación de TV de Apple, interna y Ad Hoc implementación y proporciona vínculos a información más detallada.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
