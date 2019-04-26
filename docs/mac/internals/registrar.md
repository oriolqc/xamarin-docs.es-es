---
title: Xamarin.Mac registrar
description: Este documento describe el propósito del registrador de Xamarin.Mac y su estático parcial, estática y dinámica (híbrido) las configuraciones de uso.
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 21e1a2c6ae5a9ad8b6acf520851ea92a340da887
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032504"
---
# <a name="xamarinmac-registrar"></a>Xamarin.Mac registrar

_Este documento describe el propósito del registrador de Xamarin.Mac y sus configuraciones de uso diferentes._

## <a name="overview"></a>Información general

Xamarin.Mac salva la distancia entre el mundo administrado (. NET) y en tiempo de ejecución de Cocoa, lo que permite a las clases administradas llamar a las clases no administradas de Objective-C y se vuelve a llamar cuando se producen eventos. El trabajo necesario para realizar esta "magia" se controla mediante el registrador y es, en general, ocultas a la vista.

Hay implicaciones de rendimiento de este registro, específicamente al iniciar la aplicación el tiempo, y a veces puede resultar útil comprender algo de lo que está ocurriendo "under the hood".

## <a name="configurations"></a>Configuraciones

Fundamentalmente, los trabajos del registrador en el inicio pueden dividirse en dos categorías:

- Examinar todas las clases administradas para los que deriva de NSObject y recopilar una lista de elementos que se exponen al runtime de C de objetivo.
- Registre esta información con el tiempo de ejecución de C de objetivo.

Con el tiempo, se han creado tres configuraciones de registrador diferente para cubrir los distintos casos de uso. Cada una tiene una compilación diferente y las consecuencias de tiempo de ejecución:

- **Registrador dinámica** : durante el inicio, usar la reflexión de .NET para analizar todos los tipos cargados, determinar la lista de elementos relevantes y notificar el tiempo de ejecución nativo. Esta opción agrega el tiempo de cero a la compilación, pero es muy cara calcular durante el inicio (hasta varios segundos).
- **Registrador estático** : durante la compilación, el conjunto de elementos para registrarse y generar código de Objective-C para controlar el registro de proceso. Este código se invoca durante el inicio para registrar rápidamente todos los elementos. Agrega que una pausa significativa a la compilación, pero puede corta una cantidad considerable de tiempo de inicio de la aplicación.
- **Estática "Parcial"** : un enfoque de "híbrido" más reciente que ofrece la mayoría de las ventajas de ambos. Desde las exportaciones de **Xamarin.Mac.dll** son constantes, guarde una biblioteca precalculada para controlar su registro y que en el vínculo. Usar la reflexión para controlar las bibliotecas de usuario, pero como bibliotecas de usuario exportar mucho menos tipos que los enlaces de la plataforma suele ser bastante rápido. Un neglectable impacto en el tiempo de compilación y reduce la gran mayoría de "costo" dinámico.

Hoy en día estático parcial es el valor predeterminado para la configuración de depuración y estático es el valor predeterminado para las configuraciones de lanzamiento.

Hay algunos escenarios:

- Complementos se cargan después del lanzamiento con las clases derivadas de NSObject
- Crea dinámicamente instancias de clase que deriva de NSObject

Cuando no sepa que el registrador debe registrar de algún tipo de inicio. El `ObjCRuntime.Runtime.RegisterAssembly` se proporciona el método para informar al registrador que tiene tipos adicionales a tener en cuenta.
