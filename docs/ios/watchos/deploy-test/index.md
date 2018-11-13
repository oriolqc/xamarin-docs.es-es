---
title: Implementar y probar las aplicaciones para watchOS con Xamarin
description: Este documento describe cómo implementar y probar las aplicaciones para watchOS con Xamarin. Proporciona una lista de comprobación de implementación, se analiza explícita y aplicación comodín identificadores y echa un vistazo a los grupos de aplicaciones.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7d626b8a968835813d87c93e3cead57a00c14000
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528603"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Implementar y probar las aplicaciones para watchOS con Xamarin

## <a name="deployment-checklist"></a>Lista de comprobación de implementación

Si va a implementar en una prueba de inspección, o cargar en la aplicación de Store, es preciso completar los pasos de esta página:

- En el **centro de desarrollo de iOS**:
  - [Identificadores de aplicación](#App_IDs) se han creado.
  - [Grupos de aplicaciones](#App_Groups) configurado (si es necesario).
  - Perfiles de aprovisionamiento de distribución creó

- En la solución:

  - Compruebe el [Id. de lote y referencias de proyecto](~/ios/watchos/get-started/installation.md) se establecen.
  - Comprobar los iconos son [configurado correctamente](~/ios/watchos/app-fundamentals/icons.md).
  - Compruebe la coincidencia de números de versión de paquete en todos los proyectos.
  - Configurar la **Entitlements.plist** para grupos de aplicaciones (si es necesario).

* A continuación, siga las instrucciones para:
  - [Implementar en un Apple Watch para realizar pruebas](~/ios/watchos/deploy-test/device.md), o
  - [Cargar en la aplicación de Store](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"/>

## <a name="app-ids"></a>Identificadores de aplicación

Como se describe en el [instrucciones de instalación](~/ios/watchos/get-started/installation.md), los tres proyectos en una aplicación de inspección han relacionados con el Id. de lote, como:

- Proyecto unificado de Xamarin.iOS: `com.xamarin.WatchKitCatalog`
- Proyecto de extensión de WatchKit: `com.xamarin.WatchKitCatalog.watchkitextension`
- Proyecto de aplicación de inspección: `com.xamarin.WatchKitCatalog.watchkitapp`

Los tres proyectos requieren un coincidente aprovisionamiento perfil de distribución, utilizando explícitamente los identificadores de aplicación para cada uno, o un carácter comodín de identificador de aplicación.

### <a name="explicit-app-ids"></a>Id. de aplicación explícito

Crear un **Id. de aplicación** para el Id. de lote de cada proyecto (lo que tendrá un aspecto similar al siguiente en el centro de desarrollo de iOS):

![El Id. de lote en el centro de desarrollo de iOS](images/appids-specific-sml.png)

Al crear o configurar los identificadores de aplicación, no olvide habilitar las características específicas de que la aplicación requiere. Esto puede incluir las notificaciones de inserción y grupos de aplicaciones.

Deberá crear un perfil de aprovisionamiento de distribución para cada ID. App

### <a name="wildcard-app-id"></a>Identificador de aplicación comodín

Como alternativa, puede crear un carácter comodín **Id. de aplicación** que coincide con los tres proyectos, como `com.xamarin.*`.

Tenga en cuenta que algunas características no se puede usar con un Id. de aplicación de carácter comodín (por ejemplo, las notificaciones de inserción). Si la aplicación necesita estas características debe crear identificadores de aplicación explícitos.

Para la distribución, solo necesitará crear un perfil de aprovisionamiento de distribución para el carácter comodín de identificador de aplicación.

<a name="App_Groups" />

## <a name="app-groups"></a>Grupos de aplicaciones

Puede usar un grupo de aplicaciones para compartir datos entre la aplicación de iOS y la extensión de inspección. Debe asegurarse de que tiene la solución:

- Configura el **grupo de aplicaciones** en el Portal para desarrolladores de Apple **certificados, identificadores y perfiles** sección.

- Habilitado **grupos de aplicaciones** (y proporciona el **Id. de grupo de aplicación**) en *ambos* la aplicación de iOS y la extensión de inspección **Id. de aplicación** y  **Entitlements.plist**.

### <a name="certificates-identifiers--profiles"></a>Los certificados, identificadores y perfiles

Para usar un grupo de aplicaciones, cree una entrada en el **grupos de aplicaciones** pantalla. En el ejemplo siguiente, el grupo se denomina con el mismo estilo DNS inversa que se usa normalmente para los identificadores de aplicación, pero con el `group.` prefijo (que es necesario):

![El identificador](images/appgroups-new-sml.png)

El grupo de aplicaciones, a continuación, aparecerá en la lista:

![La lista de identificadores](images/appgroups-setup-sml.png)

Una vez creado el grupo, puede hacer referencia en su **Id. de aplicación** configuración. No olvide incluir tanto la aplicación de iOS y extensión de inspección **identificadores de aplicación**.

![Configuraciones disponibles](images/appgroups-sml.png)

Hacer **no** habilitar grupos de aplicaciones en el Id. de aplicación de Apple Watch No es necesario que esté habilitado en el mismo watch.

### <a name="entitlementsplist"></a>Entitlements.plist

Algunas características de la aplicación (p ej. Grupos de aplicaciones) requieren que establezca los derechos.
Haga doble clic para editar el **Entitlements.plist** archivos en estos proyectos:

- proyecto de aplicación de iOS
- Proyecto de extensión de inspección

.![El editor de Entitlements.plist](images/entitlements-plist-sml.png)

Hacer **no** habilite los derechos en el proyecto de aplicación de inspección. No es necesario que esté habilitado en el mismo watch.

## <a name="related-links"></a>Vínculos relacionados

- [Guía de envío de WatchKit de Apple](https://developer.apple.com/app-store/watch/)
