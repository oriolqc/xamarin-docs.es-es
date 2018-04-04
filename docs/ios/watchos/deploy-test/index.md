---
title: Implementación y prueba
description: Pruebas en dispositivos y la carga en la tienda de aplicaciones
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 47c63a01d435626fd1637b6ed5f5bb1755c717af
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="deployment-and-testing"></a>Implementación y prueba

## <a name="deployment-checklist"></a>Lista de comprobación de implementación

Si va a implementar en una prueba de inspección, o cargar en el almacén de aplicación, debe completar los pasos de esta página:

- En el **iOS centro de desarrollo de**:
  - [Id. de aplicaciones](#App_IDs) se han creado.
  - [Grupos de aplicaciones](#App_Groups) configurado (si es necesario).
  - Perfil de aprovisionamiento de distribución creó

- En la solución:

  - Compruebe el [Id. de lote y referencias de proyecto](~/ios/watchos/get-started/installation.md) están establecidos.
  - Compruebe los iconos son [configurado correctamente](~/ios/watchos/app-fundamentals/icons.md).
  - Compruebe la coincidencia de números de versión de paquete en todos los proyectos.
  - Configurar la **Entitlements.plist** para grupos de aplicaciones (si es necesario).

* A continuación, siga las instrucciones para:
  - [Implementar en un Apple Watch para realizar pruebas](~/ios/watchos/deploy-test/device.md), o
  - [Cargar en la tienda de aplicaciones](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"/>

## <a name="app-ids"></a>Id. de aplicaciones

Como se describe en el [instrucciones de instalación](~/ios/watchos/get-started/installation.md), los tres proyectos en una aplicación de inspección han relacionado con el Id. de lote, como:

- Xamarin.iOS unificado proyecto: `com.xamarin.WatchKitCatalog`
- Proyecto de WatchKit extensión- `com.xamarin.WatchKitCatalog.watchkitextension`
- Proyecto de aplicación de inspección: `com.xamarin.WatchKitCatalog.watchkitapp`

Los tres proyectos requieren una distribución aprovisionamiento perfil coincidente, usando explícitamente los identificadores de aplicación para cada uno, o un carácter comodín de identificador de aplicación.

### <a name="explicit-app-ids"></a>Id. de aplicaciones explícita

Crear un **Id. de aplicación** identificador cada de cada proyecto de paquete (que tendrá un aspecto similar al siguiente en el centro de desarrollo de iOS):

![El identificador de lote en el centro de desarrollo de iOS](images/appids-specific-sml.png)

Al crear o configurar el Id. de aplicaciones, no olvide habilitar las características específicas de que su aplicación requiere. Esto podría incluir las notificaciones de inserción y grupos de aplicaciones.

Debe crear un perfil de aprovisionamiento de distribución para cada ID. App

### <a name="wildcard-app-id"></a>Id. de aplicación de comodines

Como alternativa, puede crear un carácter comodín **Id. de aplicación** que coincide con los tres proyectos, como `com.xamarin.*`.

Tenga en cuenta que algunas características no se puede usar con un identificador de aplicación de carácter comodín (por ejemplo, las notificaciones de inserción). Si la aplicación necesita estas características debe crear identificadores de aplicación explícita.

Para la distribución, solo debe crear un perfil de aprovisionamiento de distribución para el carácter comodín de identificador de aplicación.

<a name="App_Groups" />

## <a name="app-groups"></a>Grupos de aplicaciones

Puede usar un grupo de aplicación para compartir datos entre la aplicación de iOS y la extensión de inspección. Debe asegurarse de que tiene la solución:

- Configurar la **grupo de aplicación** en el Portal para desarrolladores de Apple **certificados, identificadores y perfiles** sección.

- Habilitado **grupos de aplicaciones** (y proporciona el **Id. de grupo de aplicaciones**) en *ambos* la aplicación de iOS y la extensión de inspección **Id. de aplicación** y  **Entitlements.plist**.

### <a name="certificates-identifiers--profiles"></a>Certificados, identificadores y perfiles

Para usar un grupo de aplicaciones, cree una entrada en el **grupos de aplicaciones** pantalla. En el ejemplo siguiente, el grupo se denomina con el mismo estilo de DNS inversa que se usa normalmente para los identificadores de aplicación, pero con la `group.` prefijo (que es necesario):

![El identificador](images/appgroups-new-sml.png)

El grupo de aplicaciones, a continuación, aparecerá en la lista:

![La lista de identificador](images/appgroups-setup-sml.png)

Una vez creado el grupo, puede hacer referencia en su **Id. de aplicación** configuración. No olvide incluir el iOS App y la extensión de inspección **Id. de aplicaciones**.

![Consifurations disponibles](images/appgroups-sml.png)

Hacer **no** habilitar los grupos de aplicación en el identificador de aplicación de Apple Watch. No es necesario que esté habilitado en el reloj propiamente dicho.

### <a name="entitlementsplist"></a>Entitlements.plist

Algunas características de la aplicación (p. ej. Grupos de aplicaciones) requieren establecer los derechos.
Haga doble clic para editar la **Entitlements.plist** archivo en estos proyectos:

- proyecto de aplicación de iOS
- Proyecto de extensión de inspección

.![El editor de Entitlements.plist](images/entitlements-plist-sml.png)

Hacer **no** habilite los derechos en el proyecto de aplicación de inspección. No es necesario que esté habilitado en el reloj propiamente dicho.

## <a name="related-links"></a>Vínculos relacionados

- [Guía de Apple WatchKit envío](https://developer.apple.com/app-store/watch/)
