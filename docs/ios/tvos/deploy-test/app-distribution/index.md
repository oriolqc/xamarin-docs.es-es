---
title: Introducción a la distribución de aplicaciones
description: Este documento proporciona información general sobre las técnicas de distribución que están disponibles para la aplicación de Xamarin.tvOS y actúa como un puntero a los documentos más detalladas en el tema.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: e5be0bef158c87fe06516d9a58e34c741e6e14b1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="app-distribution-overview"></a>Introducción a la distribución de aplicaciones

_Este documento proporciona información general sobre las técnicas de distribución que están disponibles para la aplicación de Xamarin.tvOS y actúa como un puntero a los documentos más detalladas en el tema._


Una vez que se ha desarrollado su aplicación Xamarin.tvOS, es el siguiente paso en el ciclo de vida de desarrollo de software distribuir la aplicación a los usuarios, como se muestra en la sección resaltada de en el diagrama siguiente:


[![La información general del ciclo de vida de desarrollo de software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple proporciona los siguientes métodos para distribuir una aplicación tvOS, que son compatibles con Xamarin.tvOS:

1. [**La App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interna (Enterprise)**](#In-House-Distribution) 
2. [**Ad Hoc**](#Ad_Hoc_Distribution) 

Todos estos escenarios requieren que las aplicaciones se aprovisionen mediante el correspondiente *perfil de aprovisionamiento*. Los perfiles de aprovisionamiento son archivos que contienen información de firma de código, así como la identidad de la aplicación y el mecanismo de distribución previsto. También contienen información sobre en qué dispositivos se puede implementar la aplicación para la distribución que no se realice a través del App Store.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribución de TV App Store de Apple

Esta es la manera principal que tvOS se distribuyen a los consumidores en dispositivos de Apple TV. Todas las aplicaciones que se envían a la tienda de aplicaciones de Apple TV requieren la aprobación de Apple.

Las aplicaciones se envían a la App Store a través de un portal llamado *iTunes Connect*. Visite nuestro [configurar la aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guía para obtener información sobre los temas siguientes:

- Administrar acuerdos, impuestos y banca.
- Crear un iTunes Connect registro.
- Administración de capturas de pantalla y vídeos de la aplicación.
- Administración de nombre de la aplicación, descripción, ' s New, palabras clave y las direcciones URL.
- Mantener la información de aplicación General.
- Mantenimiento de la información del centro de juegos.
- Mantener la información de revisión de aplicación.
- Mantener la información de precios.
- Mantener la información de compra en la aplicación.
- Ver las revisiones de aplicación.

Mientras no se ha diseñado específicamente para tvOS, este documento proporciona información sobre cómo configurar y usar este portal para preparar la aplicación para la publicación en la tienda de aplicaciones de TV de Apple. Muchos de los mismos pasos son necesarios si está configurando una aplicación de iOS o tvOS.

Una vez completados todos los pasos enumerados anteriormente, consulte nuestro [Configure su aplicación tvOS en iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) para agregar la información específica de aplicación de tvOS que será necesaria.

Es importante tener en cuenta que solo los desarrolladores que pertenecen al **Programa para desarrolladores de Apple** tienen acceso a iTunes Connect. Los miembros del **Programa para desarrolladores empresariales de Apple** no tienen acceso.

Si tiene problemas para enviar tu aplicación Xamarin.tvOS a la tienda de aplicaciones de Apple TV, vea nuestra [solución de problemas](~/ios/tvos/troubleshooting.md) guía. Contiene varios problemas conocidos que pueden surgir y cómo resolverlos en el Xamarin.tvOS.

Para obtener más información, visite la [publicar en la tienda de aplicaciones de TV de Apple](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) guía.

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribución interna

A veces denominada *Distribución empresarial*, la distribución interna permite a los miembros del **Programa para desarrolladores empresariales de Apple** distribuir aplicaciones internamente a otros miembros de la misma organización. La distribución interna tiene las ventajas de no requerir una revisión de la App Store y no tener ningún límite en el número de dispositivos en los que se puede instalar una aplicación. Sin embargo, es importante tener en cuenta que los miembros del **Programa para desarrolladores empresariales de Apple** **no** tienen acceso a iTunes Connect y, por lo tanto, el licenciatario es responsable de distribuir la aplicación.

Para obtener más información sobre cómo obtener la instalación y cómo distribuir la aplicación internamente, consulte el [Guía de distribución internas](~/ios/deploy-test/app-distribution/in-house-distribution.md). Este documento es específica de iOS, pero se utilizan las mismas técnicas para las aplicaciones de tvOS.

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribución ad hoc

Las aplicaciones de Xamarin.tvOS pueden ser probado de usuario a través de la distribución ad hoc, que está disponible tanto en el **programa para desarrolladores de Apple**y el **Apple Developer Enterprise Program**y permite hasta 100 dispositivos de Apple TV va a probar. El caso de uso recomendado para la distribución ad hoc es distribución dentro de la compañía cuando iTunes Connect no es una opción.

Para obtener más información sobre cómo obtener la instalación y cómo distribuir la aplicación internamente, consulte el [Guía de distribución Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Nuevo, este documento es específica de iOS, pero se utilizan las mismas técnicas para las aplicaciones de tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se le asignó una breve descripción de los mecanismos de distribución que están disponibles para las aplicaciones de Xamarin.tvOS. Se han introducido la App Store de Apple TV, Ad Hoc e internamente implementación y proporciona vínculos a información más detallada.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
