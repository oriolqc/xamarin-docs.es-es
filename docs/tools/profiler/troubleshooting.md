---
title: Solución de problemas de generador de perfiles de Xamarin
description: Solución de problemas del generador de perfiles de Xamarin
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 9e2f9a35f37513ce0cb785f2bd922aeccf42dd8e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarin-profiler-troubleshooting"></a>Solución de problemas de generador de perfiles de Xamarin

_Solución de problemas del generador de perfiles de Xamarin_

## <a name="logging-and-diagnostics"></a>Registro y diagnóstico

El equipo de Xamarin puede ayudar a realizar un seguimiento de problemas si proporcionarnos información, incluidos:

- Una presentación en pantalla del problema, bloqueo, o error y el flujo de trabajo que conduzcan a él.
- Salidas de registro (ver abajo).
- El **.mlpd** que se genera para la sesión de generación de perfiles (ver abajo).

### <a name="getting-log-outputs"></a>Obtener resultados de registro
En Mac, los registros se guardan en `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

En Windows se guardan en `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` incluya el registro más reciente cada vez que envíe un problema.

Estamos agregando un registro más como decidimos, por lo que esta salida debe crecer y ser más útil con el tiempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Generar archivos de .mlpd

Un **.mlpd** archivo es el resultado del generador de perfiles en tiempo de ejecución mono comprimido. La GUI del generador de perfiles de Xamarin lee los datos de un **.mlpd** y se muestra para el usuario. **.mlpd** archivos resultan útiles herramientas de depuración para Xamarin porque ayudan a nuestros ingenieros diagnosticar los problemas que puedan surgir el generador de perfiles con los datos.

El **.mlpd** de la sesión actual se guarda automáticamente en su Mac `/tmp` directorio y se pueden identificar por la marca de tiempo. Si activa el registro, la primera salida será la ruta de acceso a la **.mlpd** archivo. El **.mlpd** archivo normalmente se guardará en el directorio a partir de ~/var/carpetas...

El **.mlpd** para una sesión actual también pueden guardarse eligiendo **archivo > Guardar como...** desde el menú del generador de perfiles:

**Visual Studio para Mac**:

![](troubleshooting-images/image17.png "Guardar archivo de .mlpd en Visual Studio para Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Guardar archivo de .mlpd en Visual Studio")


Es importante tener en cuenta que **.mlpd** contienen una gran cantidad de información y el tamaño del archivo será grande.

## <a name="troubleshooting"></a>Solución de problemas

La siguiente lista muestra comunes problemas comunes, soluciones alternativas y sugerencias y trucos para usar el generador de perfiles.

> [!NOTE]
> **Tenga en cuenta**: debe ser un en Visual Studio **Enterprise** suscriptor para desbloquear esta característica en cualquier proyecto de Visual Studio Enterprise en Windows o Visual Studio para Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>No puedo ver la opción de generador de perfiles de iOS o está atenuada [Visual Studio y Visual Studio para Mac]

Compruebe las opciones siguientes para resolver este problema:

- Asegúrese de que está usando la configuración de depuración
- Asegúrese de que está usando el recolector de elementos no utilizados de SGen.
- Asegúrese de que la plataforma es [admite](~/tools/profiler/index.md#Profiler_Support).
- Asegúrese de que tiene la licencia correcta.
- Asegúrese de que haya iniciado sesión y correctamente autenticado.
- [Visual Studio] Debe contar con [Visual Studio Enterprise](https://www.visualstudio.com/vs/enterprise/) y tener una licencia válida de la empresa.


#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Obtengo un error al intentar iniciar el generador de perfiles

Si experimenta este cuadro de error cuando se utiliza el generador de perfiles de Visual Studio:

![](troubleshooting-images/error.png "Cuadro de error cuando se utiliza el generador de perfiles de Visual Studio")

Normalmente es debido a que no se pueda iniciar para el simulador o emulador. Pruebe y ejecutar la aplicación normalmente, corrija los problemas que se proporciona y, a continuación, intente volver a utilizar el generador de perfiles.

#### <a name="to-watch-a-specific-thread"></a>Para ver un subproceso concreto

Si tiene un subproceso que desea ver en concreto, sería ideal para denominar el subproceso en la parte a partir de su creación para que obtengan get `ThreadName` en lugar de `0x0`. Por ejemplo establecer el nombre del subproceso como interfaz de usuario, podría utilizar el siguiente código:


```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```



## <a name="related-links"></a>Vínculos relacionados

- [Tutorial: usar el generador de perfiles de Xamarin](~/tools/profiler/index.md)
- [Prácticas recomendadas de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de la versión](https://developer.xamarin.com/releases/profiler/preview/)
