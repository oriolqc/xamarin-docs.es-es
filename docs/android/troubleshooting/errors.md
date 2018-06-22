---
title: Matriz de errores de Xamarin.Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: e9916d8e264c202a914e6fd70e664beea276e93d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774153"
---
# <a name="xamarinandroid-errors-matrix"></a>Matriz de errores de Xamarin.Android

## <a name="errors-reference"></a>Referencia de errores

Este documento proporciona información sobre los distintos códigos de error de Xamarin.

|Categoría|Descripción|
|--- |--- |
|XA0xxx|mandroid errores|
|XA1xxx|Copiar el archivo / vínculos simbólicos (relacionadas con el proyecto) errores|
|XA2xxx|Errores del vinculador|
|XA3xxx|Errores AOT|
|XA4xxx|Errores de generación de código|
|XA5xxx|GCC y errores de la cadena de herramientas|
|XA6xxx|errores de herramientas internas mandroid|
|XA7xxx|Reservada|
|XA8xxx|Reservada|
|XA9xxx|Errores de licencia|


## <a name="error-codes"></a>Códigos de error

### <a name="xa0xxx-errors"></a>Errores de XA0xxx

|Código de error|Descripción|
|--- |--- |
|XA0000|Error inesperado: rellene un [informe de errores](http://bugzilla.xamarin.com).|
|XA0001|'-nombredisp se proporcionó sin ninguna acción específica del dispositivo.|
|XA0002|No se pudo analizar la variable de entorno '{0}'.|
|XA0003|Aplicación nombre '{0} .exe' entra en conflicto con un nombre de ensamblado (.dll) SDK o producto.|
|XA0004|La nueva lógica de refcounting requiere sgen habilitarse demasiado.|
|XA0005|El directorio de salida '{0}' no existe.|
|XA0006|No hay ninguna plataforma de desarrollo en '{0}', use--plataforma = PLAT para especificar el SDK|
|XA0007|El ensamblado raíz '{0}' no existe.|
|XA0008|Debe proporcionar un ensamblado de raíz sólo.|
|XA0009|Error al cargar los ensamblados: {0}.|
|XA0010|No se pudo analizar los argumentos de línea de comandos: {0}.|
|XA0011|{0} se creó con un tiempo de ejecución más reciente ({{1}) que admite MonoTouch.|
|XA0012|Los datos incompletos se proporcionan para completar '{0}'.|
|XA0013|Compatibilidad de generación de perfiles requiere sgen habilitarse demasiado.|
|XA0014|iOS {0} no admite la creación de aplicaciones como destino ARMv6.|
|XA0020|No se pudo determinar la ruta de acceso de mandroid.|
|XA0100|EmbeddedNativeLibrary '{0}' no es válido en el proyecto de aplicación para Android. Use AndroidNativeLibrary en su lugar.|

### <a name="xa1xxx-errors"></a>Errores de XA1xxx

|Código de error|Descripción|
|--- |--- |
|XA1001|No se pudo encontrar una aplicación en el directorio especificado.|
|XA1002|No se pudo crear vínculos simbólicos, los archivos se han copiado.|
|XA1003|No se pudo eliminar la aplicación '{0}'. Tendrá que eliminar manualmente la aplicación.|
|XA1004|No se pudo obtener la lista de aplicaciones instaladas.|
|XA1005|No se pudo eliminar la aplicación '{0}' en el dispositivo '{1}': {2}. Tendrá que eliminar manualmente la aplicación.|
|XA1006|No se pudo instalar la aplicación '{0}' en el dispositivo '{1}': {2}.|
|XA1007|No se pudo iniciar la aplicación '{0}' en el dispositivo '{1}': {2}. Todavía puede iniciar manualmente la aplicación pulsando en él.|
|XA1008|No se pudo iniciar el simulador: {0}.|
|XA1009|No se pudo copiar el ensamblado '{0}' a '{1}': {2}.|
|XA1010|No se pudo cargar el ensamblado '{0}': {1}.|
|XA1011|No se pudo agregar el archivo de recursos que faltan: '{0}'.|
|XA1101|No se pudo iniciar la aplicación.|
|XA1102|No se pudo conectar a la aplicación (para terminar,): {0}.|
|XA1103|No se pudo desasociar.|
|XA1104|Error al enviar paquete: {0}.|
|XA1105|Tipo de respuesta inesperado.|
|XA1106|No se pudo obtener la lista de aplicaciones en el dispositivo: solicitud agotó el tiempo de espera.|
|XA1107|No se pudo iniciar la aplicación.|
|XA1201|No se pudo cargar el simulador: {0}.|
|XA1301|La biblioteca nativa ({{1}) ' {0}' se omitió porque no coincide con la architecture(s) de compilación actual ({2}).|

### <a name="xa2xxx-errors"></a>Errores de XA2xxx

|Código de error|Descripción|
|--- |--- |
|XA2001|No se pudo vincular ensamblados.|
|XA2002|No se puede resolver la referencia: {0}.|
|XA2003|Puesto que se deshabilita la vinculación, se omitirá la opción '{0}'.|
|XA2004|No se pudo encontrar el archivo de definiciones de vinculador adicional '{0}'.|
|XA2005|Las definiciones de '{0}' no se pudieron analizar.|
|XA2006|No se pudo resolver la referencia al elemento de metadatos (definido en '{1}') ' {0}' de '{2}'.|

### <a name="xa3xxx-errors"></a>Errores de XA3xxx

Se trata de errores AOT.

|Código de error|Descripción|
|--- |--- |
|XA3001|Podría no AOT el ensamblado '{0}'.|
|XA3002|Restricción de AOT: el método '{0}' debe ser estático, ya que se decora con [MonoPInvokeCallback].|
|XA3003|En conflicto: opciones de depuración y--llvm. Depuración de software está deshabilitada.|


### <a name="xa4xxx-errors"></a>Errores de XA4xxx

Se trata de errores de generación de código.

|Código de error|Descripción|
|--- |--- |
|XA4001|La plantilla principal no se pudo expansed a '{0}'.|
|XA4101|El registrador no puede crear una firma para el tipo '{0}'.|
|XA4102|El registrador encontró un tipo no válido '{0}' en la firma de método '{2}'. Utilice en su lugar '{1}'.|
|XA4103|El registrador de encontrar un tipo no válido '{0}' en la firma para el método '{2}': el tipo implementa INativeObject, pero no tiene un constructor que toma dos (IntPtr, bool) argumentos.|
|XA4104|El registrador no puede serializar el valor devuelto de tipo '{0}' en la firma para el método '{1}'.|
|XA4105|El registrador no puede serializar el parámetro de tipo '{0}' en la firma para el método '{1}'.|
|XA4106|El registrador no puede serializar el valor devuelto para la estructura '{0}' en la firma para el método '{1}'.|
|XA4107|El registrador no puede serializar el parámetro de tipo '{0}' en la firma para el método '{1}'.|
|XA4108|El registrador no puede obtener el tipo de ObjectiveC para el tipo administrado '{0}'.|
|XA4109|Error al compilar el código generado del registrador. Registre un [informe de errores](http://bugzilla.xamarin.com).|
|XA4110|El registrador no puede serializar el parámetro de salida de tipo '{0}' en la firma para el método '{1}'.|
|XA4111|El registrador no puede crear una firma para el tipo '{0}' en el método '{1}'.|
|XA4200|Solo se puede generar de ACW para los tipos 'claas'.|
|XA4201|No se puede determinar el nombre JNI para el tipo {0}.|
|XA4203|El nombre de tipo especificado debe ser completo.|
|XA4204|No se puede resolver el tipo de interfaz '{0}'. ¿Falta una referencia de ensamblado?|
|XA4205|[ExportField] solo puede usarse en métodos con parámetros de 0.|
|XA4206|[Exportar] no se puede usar en un tipo genérico.|
|XA4207|[ExportField] no se puede usar en un tipo genérico.|
|XA4208|[Java.Interop.ExportFieldAttribute] no se puede usar en un método que devuelve void.|
|XA4209|No se pudo crear JavaTypeInfo para la clase: {0} debido a {1}.|
|XA4210|Debe agregar una referencia a Mono.Android.Export.dll cuando usas ExportAttribute o ExportFieldAttribute.|
|XA4211|AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}' es menor que $(TargetFrameworkVersion) '{1}'. Con API-\ {1\\} para la compilación de ACW.|


### <a name="xa5xxx-errors"></a>Errores de XA5xxx

|Código de error|Descripción|
|--- |--- |
|XA5101|Falta '{0}' compilador. Instale NDK de Android.|
|XA5102|Error de conversión de ensamblado a código nativo. Registre un [informe de errores](http://bugzilla.xamarin.com).|
|XA5103|Error al compilar el archivo '{0}'. Registre un [informe de errores](http://bugzilla.xamarin.com).|
|XA5201|No se pudo vincular nativo. Revise las marcas de usuario proporcionadas para gcc: {0}|
|XA5202|No se pudo vincular nativo. Revise el registro de compilación.|
|XA5303|Nativo vinculación advertencia: {0}.|
|XA5300|SDK de Android no se encuentra o no completamente instalado.|
|XA5301|Falta 'Quitar' herramienta. Instale Xcode componente 'Herramientas de línea de comandos'.|
|XA5302|Herramienta de 'dsymutil' que falta. Instale Xcode componente 'Herramientas de línea de comandos'.|
|XA5203|No se pudo generar los símbolos de depuración (directorio dSYM). Revise el registro de compilación.|
|XA5204|No se pudo quitar el archivo binario final. Revise el registro de compilación.|
|XA5205|Herramienta de 'aapt' que falta. Instale el paquete de herramientas de compilación del SDK de Android.|
|XA5206|{0}. No existe {1} del directorio de recursos de Android.|
|XA5207|{0}. No existe {1} de archivo de biblioteca de Java.|
|XA5208|Error de descarga. Descargue el {0} y colóquelo en el directorio de {1}.|
|XA5209|No se pudo descomprimir. Descargue {0} y extraerlo en el directorio de {1}.|
|XA5210|{0}. No existe {1} del archivo de biblioteca nativa.|

### <a name="xa6xxx-errors"></a>Errores de XA6xxx

|Código de error|Descripción|
|--- |--- |
|XA6001|Versión en ejecución de Cecil no admite la eliminación del ensamblado.|
|XA6002|No se pudo quitar el ensamblado '{0}'.|
|XA6003|UnauthorizedAccessException mensaje.|

### <a name="xa9xxx-errors"></a>Errores de XA9xxx

Estos códigos de error son errores de licencia y la activación.


#### <a name="xa9000"></a>XA9000

 **Causa:** licencia ha expirado

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|


#### <a name="xa9001"></a>XA9001

 **Causa:** caducó

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|



#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

 **Causa:** AndroidJavaLibrary

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

 **Si un ensamblado de enlace tiene la .jar incrustados, esto se detecta en el momento de paquete, no en tiempo de compilación.**

 **Causa:** AndroidNativeLibrary

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|


#### <a name="xa9003"></a>XA9003

 **Causa:** System.Runtime.Serialization

 **Activada durante:** paquete

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Cause:** System.ServiceModel.Web

 **Activada durante:** paquete

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Causa:** Mono.Data.Tds

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Esto se hace referencia a System.Data.dll, lo que está permitido**

 **Causa:** Mono.Android.Export

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|



#### <a name="xa9004"></a>XA9004

 **Causa:** : generación de perfiles

 **Activada durante:** paquete

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|



#### <a name="xa9005"></a>XA9005

 **Causa:** el límite de tamaño (32 kb).

 **Activada durante:** paquete

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|-|-|-|



#### <a name="xa9006"></a>XA9006

 **Causa:** espacio de nombres System.Data.SqlClient.

 **Activada durante:** paquete

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|


#### <a name="xa9008"></a>XA9008

 **Causa:** compilar desde la línea de comandos.

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|


#### <a name="xa9009"></a>XA9009

 **Causa:** número de serie que faltan.

 **Activada durante:** Untestable

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId no válido.

 **Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Equivalente a XA9018.



#### <a name="xa9011"></a>XA9011

 **Causa:** no se pudo actualizar el archivo de licencia (para el nuevo formato de archivo).

 **Activada durante:** activación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Causa:** No a internet

 **Activada durante:** activación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9013"></a>XA9013

 **Causa:** Error desconocido

 **Activada durante:** activación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9014"></a>XA9014

 **Causa:** código de activación no válido

 **Activada durante:** activación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9017"></a>XA9017

 **Causa:** el servidor de activación no devuelve una licencia válida.

 **Activada durante:** activación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9018"></a>XA9018

**Causa:** licencia no válida

**Activada durante:** de compilación

|Inicio|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|

