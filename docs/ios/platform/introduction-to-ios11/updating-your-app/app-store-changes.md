---
title: Cambios de la tienda de aplicaciones
description: Explorar las nuevas características de iOS 11
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 9c9355c047d2e7083ca787f473515163d8e6f5f1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="app-store-changes"></a>Cambios de la tienda de aplicaciones

_Explorar las nuevas características de iOS 11_

La App Store de iOS ha tenido un cambio de diseño completa, que no sólo permite a los usuarios desplazarse eficazmente por el almacén, sino que también le permite, como desarrollador, para promover la aplicación a los usuarios. Dichas promociones incluyen actualizaciones para las compras de la aplicación y a la página del producto. iOS 11 también agrega las actualizaciones relacionadas con cómo comunicarse con los usuarios, cómo agregar el icono de la aplicación y cómo publicar la aplicación para el público.

![Nuevo diseño de la tienda de aplicaciones](app-store-changes-images/image3.jpg)

La tienda de aplicaciones rediseñado tiene las siguientes secciones:

- **Hoy en día** : esta ficha incluye aplicaciones que son una "selección del editor" o destacados de la aplicación. Para promover la aplicación aquí rellene información en el [promover](https://developer.apple.com//contact/app-store/promote/) página.
- **Juegos** : las aplicaciones que se establece en el **juego** categoría en iTunes Connect se puede encontrar en esta pestaña.
- **Aplicaciones** : esta ficha incluye todas las demás aplicaciones. Los usuarios pueden explorar por aplicaciones destacadas, categorías, principales de pago y liberación.
- **Las actualizaciones de** : esta aplicación muestra las actualizaciones a las aplicaciones. Incluso si libera una aplicación a través de [quedando versión](#Phased_Release), los usuarios todavía pueden ir a esta pestaña y descargar la versión más reciente.
- **Búsqueda** : esta pestaña permite a los usuarios buscar la aplicación.

## <a name="store-icon"></a>Icono de almacén

Iconos de almacén (o marketing) ya no se administran en iTunes Connect y en su lugar, debe incluirse como un [catálogo de activos](~/ios/app-fundamentals/images-icons/app-icons.md) en el archivo binario de aplicación similar a los iconos de la aplicación. Un icono de almacén de 1024 x 1024 en formato PNG debe incluirse en un catálogo de activos para el envío correcto de las aplicaciones iOS 11.

Simplificación de la aplicación, se asegura de que este catálogo de activos adicional no aumenta el tamaño de la aplicación.


## <a name="in-app-purchases-promoted-in-the-app-store"></a>Compras desde la aplicación promocionarse en la tienda de aplicaciones

Apple ha realizado compras dentro de la aplicación más sencillo detectar en la tienda de aplicaciones. Ahora puede agregar hasta 20 _App Store promueve_ compras desde la aplicación que se encuentra ahora en la página de aplicaciones, en la página del producto de la aplicación, o mediante la búsqueda.

Para que las compras que realice en la aplicación aparece en la tienda de aplicaciones debe incluir los siguientes datos:

- **Imagen** : debe proporcionar una imagen diseñada especialmente para el icono que describa lo que hace la compra de la aplicación. Esta imagen debe ser diferente desde el icono de la aplicación y no puede ser una captura de pantalla.
- **Nombre de** : el nombre solo puede ser un máximo de 30 caracteres.
- **Descripción** : la descripción solo puede contener un máximo de 45 caracteres.

Las promociones de compra en la aplicación se están sujetos a una revisión de almacén de la aplicación antes de poder publicarlo.

Para hacer que las compras que realice en la aplicación disponibles para promover, abra la aplicación y vaya a **características > compras desde la aplicación**. Vaya a la **promoción de almacén de aplicación (opcional)** sección y agregar una imagen de 1024 x 1024 y **guardar**, tal y como se muestra en la siguiente imagen:

![Sección de promoción de tienda de aplicaciones en iTune conectar](app-store-changes-images/image4.png)

También debe agregar la `ShouldAddStorePayment` método para el `SKPaymentTransactionObserver` protocolo en la aplicación.

Para obtener más información sobre las promociones de compra en la aplicación, consulte de Apple [promover el de compras desde la aplicación](https://developer.apple.com/app-store/promoting-in-app-purchases/) página.

## <a name="redesigned-product-page"></a>Página del producto rediseñado

Los siguientes cambios se realizaron en la página del producto:

- Títulos ahora están establecidos para un máximo de 30 caracteres
- Se ha agregado un subtítulo
    - Debe ser un breve resumen que complementa el título.
    - Subtítulo debe tener un máximo de 30 caracteres
- Vistas previas de la aplicación
    - Ahora puede tener tres vídeos o capturas de pantalla.
    - Reproducción automática cuando un usuario visita la página de vídeos.
    - Para obtener más información, consulte de Apple [vista previa de la aplicación](https://developer.apple.com/app-store/app-previews/) página.
- Texto de la promoción
    - Esta nueva característica proporciona 170 caracteres de texto, lo que le permite describir cambian con frecuencia información sobre la aplicación.
    - Se puede actualizar en cualquier momento sin necesidad de enviar una nueva versión de la aplicación.

## <a name="customer-communication"></a>Comunicación con el cliente

En 10.3, Apple inicia una nueva forma a los programadores se comunican directamente con los usuarios de la aplicación a través de la capacidad de responder a las revisiones. Puede tener acceso a esta nueva funcionalidad en iTunes conectarse a través de **aplicación > actividad > las clasificaciones y revisiones**, tal y como se muestra en la siguiente imagen:

![Cuadro de diálogo que muestra el área para escribir la respuesta para realizar comentarios](app-store-changes-images/image5.png)

Hay algunos aspectos que tener en cuenta al responder a los usuarios:

- Sólo puede responder una vez, pero ambas partes pueden editar sus comentarios lo necesite.
- Los usuarios obtendrán una notificación cuando responde a un comentario.
- Conectar un soporte al cliente nuevo que se ha creado el rol en iTunes. Los usuarios con este rol o un rol de administrador pueden responder a los comentarios.

Para obtener más información, consulte de Apple [responde a las revisiones](https://developer.apple.com/app-store/responding-to-reviews/) página.

<a name="Phased_Release"/>

## <a name="phased-release"></a>Lanzamiento por fases

Con iOS 11, Apple ha implementado la opción de versiones por fases para las actualizaciones a la aplicación. Puede habilitar versiones por fases permitir la actualización de la aplicación que se liberen gradualmente a los clientes, asegurándose de que la demanda no sobrecarga el entorno de producción.

Versiones por fases están habilitadas en iTunes Connect. Haga clic en la aplicación en la barra lateral, desplácese a la **por fases de la versión para las actualizaciones automáticas** sección en la parte inferior y seleccione **actualización de versión con respecto a 7 días período quedando versión**:

![Que muestra la opción quedando versión para las actualizaciones automáticas](app-store-changes-images/image6.png)

La actualización está disponible inmediatamente para su descarga en la ficha actualizaciones de la tienda de aplicaciones. Por fases versiones solo están disponibles para los usuarios que tienen las descargas automáticas seleccionadas.


## <a name="related-links"></a>Vínculos relacionados

- [What's New en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada tienda de aplicaciones (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualizar la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
