---
title: Cambios de la App Store de iOS 11
description: Este documento analizan los cambios a la App Store de iOS 11. Describe el icono de almacén de la aplicación, promocionadas compras de la aplicación, la página de producto rediseñado, comunicación con el cliente y por fases de las versiones.
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 022d6b5c3f85863352dd1343752e934240b357aa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113695"
---
# <a name="app-store-changes-in-ios-11"></a>Cambios de la App Store de iOS 11

El App Store de iOS ha producido un diseño completamente nuevo, lo que no sólo permite a los usuarios navegar de forma eficaz el almacén, pero también le permite, los desarrolladores, promueva su aplicación a los usuarios. Estas promociones incluyen actualizaciones a las compras de la aplicación y a la página de producto. iOS 11 también agrega las actualizaciones con respecto a cómo comunicarse con los usuarios, cómo agregar el icono de la aplicación y cómo publicar la aplicación para el público.

![Nuevo diseño de la tienda de aplicaciones](app-store-changes-images/image3.jpg)

La tienda de aplicaciones rediseñada tiene las siguientes secciones:

- **Hoy en día** : esta ficha incluye las aplicaciones que son una "selección del editor" o con las características de la aplicación. Para promover la aplicación aquí rellene información en el [promover](https://developer.apple.com//contact/app-store/promote/) página.
- **Juegos** : las aplicaciones que se establece en el **juego** categoría en iTunes Connect se puede encontrar en esta pestaña.
- **Aplicaciones** : esta ficha incluye todas las demás aplicaciones. Los usuarios pueden buscar por aplicaciones destacadas, categorías, principales de pago o gratuita.
- **Las actualizaciones** : esta aplicación muestra las actualizaciones a las aplicaciones. Incluso si libera una aplicación a través de [por fases de versión](#Phased_Release), los usuarios todavía pueden ir a esta pestaña y descargar la versión más reciente.
- **Búsqueda** : esta pestaña permite a los usuarios buscar la aplicación.

## <a name="store-icon"></a>Icono de Store

Iconos de Store (o marketing iconos) ya no se administran en iTunes Connect y en su lugar, deben incluirse como un [catálogo de activos](~/ios/app-fundamentals/images-icons/app-icons.md) en el archivo binario de aplicación similar a los iconos de aplicación. Un icono de 1024 x 1024 almacén en formato PNG debe incluirse en un catálogo de recursos para el envío correcto de las aplicaciones iOS 11.

Simplificación de la aplicación, se asegura de que este catálogo de recursos adicional no aumenta el tamaño de la aplicación.


## <a name="in-app-purchases-promoted-in-the-app-store"></a>Adquisición de la aplicación promocionarse en la aplicación de Store

Apple ha realizado compras de la aplicación sean más reconocibles en la aplicación de Store. Ahora puede agregar hasta 20 _Store App promueve_ compras de la aplicación que se encuentra ahora en la página de aplicaciones, en la página de producto de la aplicación o mediante la búsqueda.

Para hacer sus compras en la aplicación aparece en el Store de la aplicación debe incluir los siguientes datos:

- **Imagen** : debe proporcionar una imagen diseñada especialmente para el icono que se describe lo que hace la compra en la aplicación. Esta imagen debe ser diferente del icono de aplicación y no puede ser una captura de pantalla.
- **Nombre** : el nombre solo puede tener un máximo de 30 caracteres.
- **Descripción** : la descripción solo puede contener un máximo de 45 caracteres.

Las promociones de compra en la aplicación están sujetos a una revisión de la tienda de aplicaciones antes de que se publique.

Para hacer sus compras en la aplicación disponibles para promover, abra la aplicación y vaya a **características > las compras de la aplicación**. Vaya a la **promoción de la aplicación Store (opcional)** sección y agregar una imagen de 1024 x 1024 y **guardar**, como se muestra en la siguiente imagen:

![Sección de la promoción de App Store en iTune Connect](app-store-changes-images/image4.png)

También deberá agregar el `ShouldAddStorePayment` método a la `SKPaymentTransactionObserver` protocolo en la aplicación.

Para obtener más información sobre las promociones de compra en la aplicación, consulte Apple [promover su en compras desde la aplicación](https://developer.apple.com/app-store/promoting-in-app-purchases/) página.

## <a name="redesigned-product-page"></a>Página de producto rediseñado

Los siguientes cambios se realizaron a la página de producto:

- Los títulos ahora se establecen en un máximo de 30 caracteres
- Se ha agregado un subtítulo
    - Debe ser un resumen breve que complementa el título.
    - Subtítulo debe tener un máximo de 30 caracteres
- Vistas previas de la aplicación
    - Ahora puede tener tres vídeos o capturas de pantalla.
    - Reproducción automática cuando un usuario visita la página de vídeos.
    - Para obtener más información, consulte Apple [vista previa de la aplicación](https://developer.apple.com/app-store/app-previews/) página.
- Texto de la promoción
    - Esta nueva característica proporciona 170 caracteres de texto, lo que le permite describir cambian con frecuencia información sobre la aplicación.
    - Se puede actualizar en cualquier momento sin necesidad de enviar una nueva versión de la aplicación.

## <a name="customer-communication"></a>Comunicación con el cliente

En 10.3, Apple lanzó una nueva forma para desarrolladores para comunicarse directamente con los usuarios de la aplicación a través de la capacidad para responder a las revisiones. Puede tener acceso a esta nueva funcionalidad en iTunes connect, vaya a **aplicación > actividad > las valoraciones y opiniones**, como se muestra en la siguiente imagen:

![Cuadro de diálogo que muestra el área para escribir la respuesta a comentarios](app-store-changes-images/image5.png)

Hay algunos aspectos que debe tener en cuenta al responder a los usuarios:

- Solo puede responder una vez, pero ambas partes pueden editar sus comentarios tanto como deseen.
- Los usuarios recibir una notificación cuando responde a un comentario.
- Una nueva compatibilidad de cliente se ha creado el rol en iTunes connect. Los usuarios con este rol o un rol de administrador pueden responder a los comentarios.

Para obtener más información, consulte Apple [responder a las revisiones](https://developer.apple.com/app-store/responding-to-reviews/) página.

<a name="Phased_Release"/>

## <a name="phased-release"></a>Lanzamiento por fases

Con iOS 11, Apple ha implementado la opción de versiones por fases para las actualizaciones a la aplicación. Puede habilitar versiones por fases permitir la actualización de la aplicación para que se lanzarán gradualmente a los clientes, lo que garantiza que la demanda no sobrecarga el entorno de producción.

Versiones por fases están habilitadas en iTunes Connect. Haga clic en la aplicación en la barra lateral, desplácese a la **por fases de versión para las actualizaciones automáticas** sección en la parte inferior y seleccione **versión actualización respecto al uso del período de 7 días por fases de versión**:

![Que muestra la opción por fases de versión para las actualizaciones automáticas](app-store-changes-images/image6.png)

La actualización está disponible inmediatamente para su descarga en la pestaña de actualizaciones de la aplicación de Store. Por fases versiones solo están disponibles para los usuarios que tienen las descargas automáticas seleccionadas.


## <a name="related-links"></a>Vínculos relacionados

- [Novedades en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada Store App (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualización de la aplicación para iOS 11 (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
