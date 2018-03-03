---
title: Registrador de Xamarin.Mac
description: "Este documento describe el propósito del registrador de Xamarin.Mac y sus configuraciones de uso distintos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 8d5061850a4cb73a81e1bf4c93583eb5b6eeefec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinmac-registrar"></a>Registrador de Xamarin.Mac

_Este documento describe el propósito del registrador de Xamarin.Mac y sus configuraciones de uso distintos._

## <a name="overview"></a>Información general

Xamarin.Mac relaciona la diferencia entre el ámbito de recursos administrado (. NET) y en tiempo de ejecución del cacao, lo que permite a las clases administradas llamar a clases de Objective-C no administradas y se vuelve a llamar cuando se producen eventos. El trabajo necesario para realizar esta "mágica" se controla mediante el registrador y por lo general, está, oculta.

Hay implicaciones de rendimiento de este registro, concretamente en el inicio de la aplicación el tiempo, y a veces puede resultar útil comprender algo de lo que está sucediendo "under the hood".

## <a name="configurations"></a>Configuraciones

Fundamentalmente trabajo del registrador durante el inicio se puede dividir en dos categorías:

- Busque las derivadas del NSObject cada clase administrada y recopilar una lista de elementos que se exponen al runtime de C de objetivo.
- Registrar esta información con el tiempo de ejecución de C de objetivo.

Con el tiempo, se han creado tres configuraciones de registrador diferente para cubrir los casos de uso diferentes. Cada una tiene compilación diferente y consecuencias de tiempo de ejecución:

- **Registrador dinámica** : durante el inicio, usar la reflexión de .NET para analizar todos los tipos cargados, determinar la lista de elementos relevantes e informar el tiempo de ejecución nativo. Esta opción agrega un tiempo de cero a la compilación, pero es muy costosa calcular durante el inicio (hasta varios segundos).
- **Registrador estático** : durante la compilación, el conjunto de elementos para registrar y generar código Objective-C para controlar el registro de proceso. Este código se invoca durante el inicio para registrar rápidamente todos los elementos. Agrega que una pausa significativa para la compilación, pero puede corta una cantidad considerable de tiempo de inicio de la aplicación.
- **Estática "Parcial"** : un enfoque de "híbrido" más reciente que ofrece la mayoría de las ventajas de ambos. Desde las exportaciones de **Xamarin.Mac.dll** son constantes, guarde una biblioteca precalculada para controlar su registro y de vínculo. Usar la reflexión para administrar las bibliotecas de usuario, pero como bibliotecas de usuario exportar tipos mucho menos que los enlaces de plataforma suele ser rápido en su lugar. Un neglectable impacto en el tiempo de compilación y se reduce una gran mayoría de lo "costo" dinámico.

En la actualidad estático parcial es el valor predeterminado para la configuración de depuración y estático es el valor predeterminado para las configuraciones de versión.

Hay algunos escenarios:

- Complementos se cargan después del lanzamiento con las clases derivadas de NSObject
- Crea dinámicamente instancias de clase que deriva de NSObject

donde el registrador es no se puede saber que debe registrar algún tipo en el inicio. El `ObjCRuntime.Runtime.RegisterAssembly` se proporciona el método para informar al registrador que tiene tipos adicionales a tener en cuenta.
