---
title: Matriz de errores de Xamarin.Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: f3721ad661f4b817375b0d625c9b5cc293e6d44c
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2018
ms.locfileid: "50235082"
---
# <a name="xamarinandroid-errors-matrix"></a>Matriz de errores de Xamarin.Android

## <a name="errors-reference"></a>Referencia de errores

Este documento proporciona cierta información sobre los distintos códigos de error de Xamarin.

|Categoría|Descripción|
|--- |--- |
|XA0xxx|Errores de mandroid|
|XA1xxx|Copiar el archivo o los vínculos simbólicos (proyecto relacionado) errores|
|XA2xxx|Errores del vinculador|
|XA3xxx|Errores AOT|
|XA4xxx|Errores de generación de código|
|XA5xxx|Errores de la cadena de herramientas y GCC|
|XA6xxx|errores de herramientas internas de mandroid|
|XA7xxx|Reservada|
|XA8xxx|Reservada|
|XA9xxx|Errores de licencia|


## <a name="error-codes"></a>Códigos de error

### <a name="xa0xxx-errors"></a>Errores de XA0xxx

|Código de error|Descripción|
|--- |--- |
|XA0000|Error inesperado: rellene una [informe de errores](https://github.com/xamarin/xamarin-android/issues/new).|
|XA0001|'-nombredisp proporcionó sin ninguna acción específica del dispositivo.|
|XA0002|No se pudo analizar la variable de entorno '{0}'.|
|XA0003|Nombre de la aplicación '{0}.exe' entra en conflicto con un nombre de ensamblado (.dll) de producto o SDK.|
|XA0004|Nueva lógica de refcounting requiere sgen habilitarse demasiado.|
|XA0005|El directorio de salida '{0}' no existe.|
|XA0006|No hay ninguna plataforma de desarrollo en '{0}', utilice--plataforma = PLAT para especificar el SDK|
|XA0007|El ensamblado raíz '{0}' no existe.|
|XA0008|Debe proporcionar solo un ensamblado de raíz.|
|XA0009|Error al cargar los ensamblados: {0}.|
|XA0010|No se pudo analizar los argumentos de línea de comandos: {0}.|
|XA0011|{0} se compiló un tiempo de ejecución más reciente ({1}) que admite MonoTouch.|
|XA0012|Datos incompletos se proporcionan para completar '{0}'.|
|XA0013|Compatibilidad de generación de perfiles requiere sgen habilitarse demasiado.|
|XA0014|iOS {0} no admite la creación de aplicaciones destinadas a ARMv6.|
|XA0020|No se pudo determinar la ruta de acceso de mandroid.|
|XA0100|EmbeddedNativeLibrary '{0}' no es válido en el proyecto de aplicación de Android. Use AndroidNativeLibrary en su lugar.|

### <a name="xa1xxx-errors"></a>Errores de XA1xxx

|Código de error|Descripción|
|--- |--- |
|XA1001|No se pudo encontrar una aplicación en el directorio especificado.|
|XA1002|No se pudo crear vínculos simbólicos, se copiaron los archivos.|
|XA1003|No se pudo terminar la aplicación '{0}'. Es posible que deba eliminar manualmente la aplicación.|
|XA1004|No se pudo obtener la lista de aplicaciones instaladas.|
|XA1005|No se pudo terminar la aplicación '{0}'en el dispositivo'{1}': {2}. Es posible que deba eliminar manualmente la aplicación.|
|XA1006|No se pudo instalar la aplicación '{0}'en el dispositivo'{1}': {2}.|
|XA1007|No se pudo iniciar la aplicación '{0}'en el dispositivo'{1}': {2}. Todavía puede iniciar manualmente la aplicación pulsando en él.|
|XA1008|No se pudo iniciar el simulador: {0}.|
|XA1009|No se pudo copiar el ensamblado '{0}'para'{1}': {2}.|
|XA1010|No se pudo cargar el ensamblado '{0}': {1}.|
|XA1011|No se pudo agregar el archivo de recursos que faltan: '{0}'.|
|XA1101|No se pudo iniciar la aplicación.|
|XA1102|No se pudo conectar con la aplicación (terminará): {0}.|
|XA1103|No se pudo desasociar.|
|XA1104|No se pudo enviar el paquete: {0}.|
|XA1105|Tipo de respuesta inesperado.|
|XA1106|No se pudo obtener la lista de aplicaciones en el dispositivo: solicitud agotó el tiempo de espera.|
|XA1107|No se pudo iniciar la aplicación.|
|XA1201|No se pudo cargar el simulador: {0}.|
|XA1301|Biblioteca nativa '{0}' ({1}) se omite porque no coincide con la actual architecture(s) de compilación ({2}).|

### <a name="xa2xxx-errors"></a>Errores de XA2xxx

|Código de error|Descripción|
|--- |--- |
|XA2001|No se pudo vincular ensamblados.|
|XA2002|No se puede resolver la referencia: {0}.|
|XA2003|Opción '{0}' se omitirá porque se deshabilita la vinculación.|
|XA2004|Archivo de definiciones de enlazador adicionales '{0}' no se pudo encontrar.|
|XA2005|Las definiciones de '{0}' no se pudo analizar.|
|XA2006|Referencia al elemento de metadatos '{0}' (definido en '{1}') de '{2}' no se pudo resolver.|

### <a name="xa3xxx-errors"></a>Errores de XA3xxx

Estos son errores AOT.

|Código de error|Descripción|
|--- |--- |
|XA3001|Podría no AOT el ensamblado '{0}'.|
|XA3002|Restricción de AOT: método '{0}' debe ser estático, ya que está decorado con [MonoPInvokeCallback].|
|XA3003|En conflicto: opciones de depuración y--llvm. Depurar con Soft está deshabilitada.|


### <a name="xa4xxx-errors"></a>Errores de XA4xxx

Estos son errores de generación de código.

|Código de error|Descripción|
|--- |--- |
|XA4001|No se pudo expansed a la plantilla principal '{0}'.|
|XA4101|El registrador no puede generar una firma para el tipo '{0}'.|
|XA4102|El registrador encuentra un tipo no válido '{0}'en la firma de método'{2}'. Use '{1}' en su lugar.|
|XA4103|El registrador encuentra un tipo no válido '{0}'en la firma de método'{2}': el tipo implementa INativeObject, pero no tiene un constructor que toma dos (IntPtr, bool) argumentos.|
|XA4104|El registrador no puede serializar el valor devuelto de tipo '{0}'en la firma de método'{1}'.|
|XA4105|El registrador no puede serializar el parámetro de tipo '{0}'en la firma de método'{1}'.|
|XA4106|El registrador no puede serializar el valor devuelto para la estructura '{0}'en la firma de método'{1}'.|
|XA4107|El registrador no puede serializar el parámetro de tipo '{0}'en la firma de método'{1}'.|
|XA4108|El registrador no puede obtener el tipo ObjectiveC de tipo administrado '{0}'.|
|XA4109|No se pudo compilar el código generado del registrador. Registre un [informe de errores](http://bugzilla.xamarin.com).|
|XA4110|El registrador no puede serializar el parámetro de salida de tipo '{0}'en la firma de método'{1}'.|
|XA4111|El registrador no puede generar una firma para el tipo '{0}'en el método'{1}'.|
|XA4200|Solo se pueden generar de ACW para los tipos 'claas'.|
|XA4201|No se puede determinar el nombre JNI de tipo {0}.|
|XA4203|El nombre del tipo especificado debe ser completo.|
|XA4204|No se puede resolver el tipo de interfaz '{0}'. ¿Falta una referencia de ensamblado?|
|XA4205|[ExportField] solo puede usarse en métodos con 0 parámetros.|
|XA4206|[Exportar] no se puede usar en un tipo genérico.|
|XA4207|[ExportField] no se puede usar en un tipo genérico.|
|XA4208|[Java.Interop.ExportFieldAttribute] no se puede usar en un método que devuelve void.|
|XA4209|No se pudo crear JavaTypeInfo para la clase: {0} debido a {1}.|
|XA4210|Deberá agregar una referencia a Mono.Android.Export.dll al usar ExportAttribute o ExportFieldAttribute.|
|XA4211|AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}'es menor que $(TargetFrameworkVersion)'{1}'. El uso de API -{1} para la compilación ACW.|


### <a name="xa5xxx-errors"></a>Errores de XA5xxx

|Código de error|Descripción|
|--- |--- |
|XA5101|Falta '{0}' compilador. Instale el NDK de Android.|
|XA5102|Error de conversión de ensamblados en código nativo. Registre un [informe de errores](http://bugzilla.xamarin.com).|
|XA5103|No se pudo compilar el archivo '{0}'. Registre un [informe de errores](http://bugzilla.xamarin.com).|
|XA5201|No se pudo vincular nativo. Revise las marcas de usuario proporcionadas para gcc: {0}|
|XA5202|No se pudo vincular nativo. Revise el registro de compilación.|
|XA5303|Nativo vinculación advertencia: {0}.|
|XA5300|SDK de Android no se encuentra o no totalmente instalado.|
|XA5301|Herramienta 'Quitar' que falta. Instale Xcode componente "Herramientas de línea de comandos".|
|XA5302|Herramienta de 'dsymutil' que falta. Instale Xcode componente "Herramientas de línea de comandos".|
|XA5203|No se pudo generar los símbolos de depuración (directorio de dSYM). Revise el registro de compilación.|
|XA5204|No se pudo eliminar el archivo binario final. Revise el registro de compilación.|
|XA5205|Herramienta de 'aapt' que falta. Instale el paquete de herramientas de compilación de Android SDK.|
|XA5206|{0}. Directorio de recursos Android {1} no existe.|
|XA5207|{0}. Archivo de biblioteca de Java {1} no existe.|
|XA5208|Error de descarga. Descargue {0} y colóquelo la {1} directory.|
|XA5209|No se pudo descomprimir. Descargue {0} y extráigalo a la {1} directory.|
|XA5210|{0}. Archivo de biblioteca nativa {1} no existe.|

### <a name="xa6xxx-errors"></a>Errores de XA6xxx

|Código de error|Descripción|
|--- |--- |
|XA6001|Versión en ejecución de Cecil no admite la eliminación del ensamblado.|
|XA6002|No se pudo quitar el ensamblado '{0}'.|
|XA6003|UnauthorizedAccessException mensaje.|

### <a name="xa9xxx-errors"></a>Errores de XA9xxx

Estos códigos de error son errores de activación y licencias.


#### <a name="xa9000"></a>XA9000

 **Causa:** licencia ha expirado

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|


#### <a name="xa9001"></a>XA9001

 **Causa:** prueba expirado

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|ADVERTENCIA|



#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

 **Causa:** AndroidJavaLibrary

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

 **Si un ensamblado de enlace tiene el .jar incrustado, esto se detecta en el momento de paquete, no en tiempo de compilación.**

 **Causa:** AndroidNativeLibrary

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|


#### <a name="xa9003"></a>XA9003

 **Causa:** System.Runtime.Serialization

 **Se comprueban durante:** paquete

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Causa:** System.ServiceModel.Web

 **Se comprueban durante:** paquete

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Causa:** Mono.Data.Tds

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Esto se hace referencia a System.Data.dll, que está permitido**

 **Causa:** Mono.Android.Export

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|



#### <a name="xa9004"></a>XA9004

 **Causa:** : generación de perfiles

 **Se comprueban durante:** paquete

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|



#### <a name="xa9005"></a>XA9005

 **Causa:** límite de tamaño (32 kb).

 **Se comprueban durante:** paquete

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|-|-|-|



#### <a name="xa9006"></a>XA9006

 **Causa:** espacio de nombres System.Data.SqlClient.

 **Se comprueban durante:** paquete

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|


#### <a name="xa9008"></a>XA9008

 **Causa:** compilar desde la línea de comandos.

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|


#### <a name="xa9009"></a>XA9009

 **Causa:** número de serie que faltan.

 **Se comprueban durante:** un-testable

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId no válido.

 **Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Equivalente a XA9018.



#### <a name="xa9011"></a>XA9011

 **Causa:** no se pudo actualizar el archivo de licencia (para el nuevo formato de archivo).

 **Se comprueban durante:** activación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Causa:** sin internet

 **Se comprueban durante:** activación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9013"></a>XA9013

 **Causa:** Error desconocido

 **Se comprueban durante:** activación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9014"></a>XA9014

 **Causa:** código de activación no válido

 **Se comprueban durante:** activación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9017"></a>XA9017

 **Causa:** servidor de activación no devuelve una licencia válida.

 **Se comprueban durante:** activación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9018"></a>XA9018

**Causa:** licencia no válida

**Se comprueban durante:** de compilación

|Starter|Indie|Business(Trial)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|

