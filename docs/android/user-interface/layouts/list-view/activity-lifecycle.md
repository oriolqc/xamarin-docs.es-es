---
title: ListView y ciclo de vida de actividad
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b2328759b3158920bc8683ec14c2aebefd7a04ae
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117725"
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView y ciclo de vida de actividad

Las actividades pasan a través de determinados Estados como la aplicación se ejecuta, como inicio, ejecución, que se pausa y está deteniendo. Para obtener más información e instrucciones específicas sobre cómo administrar las transiciones de estado, consulte el [Tutorial del ciclo de vida de actividad](~/android/app-fundamentals/activity-lifecycle/index.md).
Es importante comprender el ciclo de vida de actividad y el lugar su `ListView` código en las ubicaciones correctas.

Todos los ejemplos de este documento realicen 'las tareas de configuración' en la actividad `OnCreate` método y (cuando sea necesario) realizar 'desmontaje' en `OnDestroy`. Los ejemplos utilizan generalmente los conjuntos de datos pequeños que no cambian, por lo que no es necesario volver a cargar los datos de con más frecuencia.

Sin embargo, si los datos se cambian con frecuencia o usa una gran cantidad de memoria puede ser adecuado utilizar métodos de ciclo de vida diferente para rellenar y actualizar su `ListView`. Por ejemplo, si los datos subyacentes cambian constantemente (o pueden verse afectados por las actualizaciones de otras actividades), a continuación, crear el adaptador en `OnStart` o `OnResume` garantizará que los datos más recientes se muestran cada vez que se muestra la actividad.

Si el adaptador utiliza los recursos como memoria o un cursor administrado, no olvide liberar esos recursos en el método complementario para que se iniciaron (p ej. los objetos creados en `OnStart` puede eliminarse en `OnStop`).


## <a name="configuration-changes"></a>Cambios de configuración

Es importante recordar que la configuración cambia &ndash; especialmente visibilidad del teclado y rotación de pantalla &ndash; puede provocar que la actividad actual se destruirá y se vuelve a crear (a menos que especifique lo contrario mediante el `ConfigurationChanges` (atributo). Esto significa que en condiciones normales, rotación de un dispositivo hará que un `ListView` y `Adapter` volver a crearse y (a menos que haya escrito código `OnPause` y `OnResume`) la selección de fila y la posición de desplazamiento indica que se perderán.

El siguiente atributo evitaría que una actividad que se destruyen y se vuelven a crear como resultado de los cambios de configuración:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

La actividad, a continuación, debe reemplazar `OnConfigurationChanged` para responder a esos cambios según corresponda. Para obtener más información sobre cómo controlar los cambios de configuración, consulte la documentación.

