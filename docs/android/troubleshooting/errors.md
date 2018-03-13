---
title: Matriz de errores de Xamarin.Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1c9284f3db1c503b5c88f0d310f916f4c9e3363d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="xamarinandroid-errors-matrix"></a>Matriz de errores de Xamarin.Android

## <a name="errors-reference"></a>Referencia de errores

Este documento proporciona información sobre los distintos códigos de error de Xamarin.

<table>
    <thead>
        <tr>
            <td>Categoría</td>
            <td>Descripción</td>
        </tr>
    </thead>
    <tbody>
        <tr><td>XA0xxx</td><td><code>mandroid</code> errores</td></tr>
        <tr><td>XA1xxx</td><td>Copiar el archivo / vínculos simbólicos (relacionadas con el proyecto) errores</td></tr>
        <tr><td>XA2xxx</td><td>Errores del vinculador</td></tr>
        <tr><td>XA3xxx</td><td>Errores AOT</td></tr>
        <tr><td>XA4xxx</td><td>Errores de generación de código</td></tr>
        <tr><td>XA5xxx</td><td>GCC y errores de la cadena de herramientas</td></tr>
        <tr><td>XA6xxx</td><td><code>mandroid</code> Errores internos de herramientas</td></tr>
        <tr><td>XA7xxx</td><td>Reservada</td></tr>
        <tr><td>XA8xxx</td><td>Reservada</td></tr>
        <tr><td>XA9xxx</td><td>Errores de licencia</td></tr>
    </tbody>
</table>

## <a name="error-codes"></a>Códigos de error

### <a name="xa0xxx-errors"></a>Errores de XA0xxx

<table>
    <thead>
        <tr><td>Código de error</td><td>Descripción</td></tr>
    </thead>
    <tbody>
        <tr><td>XA0000</td><td>Error inesperado: rellene un informe de errores en <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA0001</td><td>'-nombredisp se proporcionó sin ninguna acción específica del dispositivo</td></tr>
        <tr><td>XA0002</td><td>No se pudo analizar la variable de entorno '{0}'.</td></tr>
        <tr><td>XA0003</td><td>Aplicación nombre '{0} .exe' entra en conflicto con un nombre de ensamblado (.dll) SDK o producto.</td></tr>
        <tr><td>XA0004</td><td>La nueva lógica de refcounting requiere sgen habilitarse demasiado.</td></tr>
        <tr><td>XA0005</td><td>El directorio de salida '{0}' no existe</td></tr>
        <tr><td>XA0006</td><td>No hay ninguna plataforma de desarrollo en '{0}', use--plataforma = PLAT para especificar el SDK</td></tr>
        <tr><td>XA0007</td><td>El ensamblado raíz '{0}' no existe</td></tr>
        <tr><td>XA0008</td><td>Debe proporcionar un ensamblado de raíz solo</td></tr>
        <tr><td>XA0009</td><td>Error al cargar los ensamblados: {0}</td></tr>
        <tr><td>XA0010</td><td>No se pudo analizar los argumentos de línea de comandos: {0}</td></tr>
        <tr><td>XA0011</td><td>{0} se creó con un tiempo de ejecución más reciente ({{1}) que admite MonoTouch</td></tr>
        <tr><td>XA0012</td><td>Proporcionan datos incompletos para completar `{0}`.</td></tr>
        <tr><td>XA0013</td><td>Compatibilidad de generación de perfiles requiere sgen habilitarse demasiado</td></tr>
        <tr><td>XA0014</td><td>iOS {0} no admite la creación de aplicaciones de destino ARMv6</td></tr>
        <tr><td>XA0020</td><td>No se pudo determinar la ruta de acceso de mandroid.</td></tr>
        <tr><td>XA0100</td><td>EmbeddedNativeLibrary '{0}' no es válido en el proyecto de aplicación para Android. Use AndroidNativeLibrary en su lugar.</td></tr>
    </tbody>
</table>

### <a name="xa1xxx-errors"></a>Errores de XA1xxx

<table>
    <thead>
        <tr><td>Código de error</td><td>Descripción</td></tr>
    </thead>
    <tbody>
        <tr><td>XA1001</td><td>No se pudo encontrar una aplicación en el directorio especificado</td></tr>
        <tr><td>XA1002</td><td>No se pudo crear vínculos simbólicos, se copian archivos</td></tr>
        <tr><td>XA1003</td><td>No se pudo eliminar la aplicación '{0}'. Tendrá que eliminar manualmente la aplicación.</td></tr>
        <tr><td>XA1004</td><td>No se pudo obtener la lista de aplicaciones instaladas.</td></tr>
        <tr><td>XA1005</td><td>No se pudo eliminar la aplicación '{0}' en el dispositivo '{1}': {2}. Tendrá que eliminar manualmente la aplicación.</td></tr>
        <tr><td>XA1006</td><td>No se pudo instalar la aplicación '{0}' en el dispositivo '{1}': {2}.</td></tr>
        <tr><td>XA1007</td><td>No se pudo iniciar la aplicación '{0}' en el dispositivo '{1}': {2}. Todavía puede iniciar manualmente la aplicación pulsando en él.</td></tr>
        <tr><td>XA1008</td><td>No se pudo iniciar el simulador: {0}</td></tr>
        <tr><td>XA1009</td><td>No se pudo copiar el ensamblado '{0}' a '{1}': {2}</td></tr>
        <tr><td>XA1010</td><td>No se pudo cargar el ensamblado '{0}': {1}</td></tr>
        <tr><td>XA1011</td><td>No se pudo agregar el archivo de recursos que faltan: '{0}'</td></tr>
        <tr><td>XA1101</td><td>No se pudo iniciar la aplicación</td></tr>
        <tr><td>XA1102</td><td>No se pudo conectar a la aplicación (para terminar,): {0}</td></tr>
        <tr><td>XA1103</td><td>No se pudieron desasociar</td></tr>
        <tr><td>XA1104</td><td>Error al enviar paquete: {0}</td></tr>
        <tr><td>XA1105</td><td>Tipo de respuesta inesperado</td></tr>
        <tr><td>XA1106</td><td>No se pudo obtener la lista de aplicaciones en el dispositivo: solicitud agotó el tiempo de espera.</td></tr>
        <tr><td>XA1107</td><td>No se pudo iniciar la aplicación</td></tr>
        <tr><td>XA1201</td><td>No se pudo cargar el simulador: {0}</td></tr>
        <tr><td>XA1301</td><td>La biblioteca nativa `{0}` ({{1}) se ha omitido porque no coincide con la architecture(s) de compilación actual ({2})</td></tr>
    </tbody>
</table>

### <a name="xa2xxx-errors"></a>Errores de XA2xxx

<table>
    <thead>
        <tr><td>Código de error</td><td>Descripción</td></tr>
    </thead>
    <tbody>
        <tr><td>XA2001</td><td>No se pudo vincular ensamblados</td></tr>
        <tr><td>XA2002</td><td>No se puede resolver la referencia: {0}</td></tr>
        <tr><td>XA2003</td><td>Se omitirá la opción '{0}' porque se deshabilita la vinculación</td></tr>
        <tr><td>XA2004</td><td>No se pudo encontrar el archivo de definiciones de vinculador adicional '{0}'.</td></tr>
        <tr><td>XA2005</td><td>Las definiciones de '{0}' no se pudieron analizar.</td></tr>
        <tr><td>XA2006</td><td>No se pudo resolver la referencia al elemento de metadatos (definido en '{1}') ' {0}' de '{2}'.</td></tr>
    </tbody>
</table>

### <a name="xa3xxx-errors"></a>Errores de XA3xxx

Se trata de errores AOT.

<table>
    <thead>
        <tr><td>Código de error</td><td>Descripción</td></tr>
    </thead>
    <tbody>
        <tr><td>XA3001</td><td>Podría no AOT el ensamblado '{0}'</td></tr>
        <tr><td>XA3002</td><td>Restricción de AOT: el método '{0}' debe ser estático, ya que se decora con [MonoPInvokeCallback].</td></tr>
        <tr><td>XA3003</td><td>En conflicto: opciones de depuración y--llvm. Depuración de software está deshabilitada.</td></tr>
    </tbody>
</table>

### <a name="xa4xxx-errors"></a>Errores de XA4xxx

Se trata de errores de generación de código.

<table>
    <thead>
        <tr><td>Código de error</td><td>Descripción</td></tr>
    </thead>
    <tbody>
        <tr><td>XA4001</td><td>No se pudo expansed a la plantilla principal `{0}`.</td></tr>
        <tr><td>XA4101</td><td>El registrador no puede crear una firma de tipo `{0}`.</td></tr>
        <tr><td>XA4102</td><td>El registrador de encontrar un tipo no válido `{0}` de firma para el método `{2}`. Utilice `{1}` en su lugar.</td></tr>
        <tr><td>XA4103</td><td>El registrador de encontrar un tipo no válido `{0}` de firma para el método `{2}`: el tipo implementa INativeObject, pero no tiene un constructor que toma dos (IntPtr, bool) argumentos</td></tr>
        <tr><td>XA4104</td><td>El registrador no puede serializar el valor devuelto de tipo `{0}` de firma para el método `{1}`.</td></tr>
        <tr><td>XA4105</td><td>El registrador no puede serializar el parámetro de tipo `{0}` de firma para el método `{1}`.</td></tr>
        <tr><td>XA4106</td><td>El registrador no puede serializar el valor devuelto para la estructura `{0}` de firma para el método `{1}`.</td></tr>
        <tr><td>XA4107</td><td>El registrador no puede serializar el parámetro de tipo `{0}` de firma para el método `{1}`.</td></tr>
        <tr><td>XA4108</td><td>El registrador no puede obtener el tipo de ObjectiveC para el tipo administrado `{0}`.</td></tr>
        <tr><td>XA4109</td><td>Error al compilar el código generado del registrador. Registre un informe de errores en <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA4110</td><td>El registrador no puede serializar el parámetro de salida de tipo `{0}` de firma para el método `{1}`.</td></tr>
        <tr><td>XA4111</td><td>El registrador no puede crear una firma para el tipo `{0}' in method `{1} '.</td></tr>
        <tr><td>XA4200</td><td>Solo se puede generar de ACW para los tipos 'claas'.</td></tr>
        <tr><td>XA4201</td><td>No se puede determinar el nombre JNI para el tipo {0}.</td></tr>
        <tr><td>XA4203</td><td>El nombre de tipo especificado debe ser completo.</td></tr>
        <tr><td>XA4204</td><td>No se puede resolver el tipo de interfaz '{0}'. ¿Falta una referencia de ensamblado?</td></tr>
        <tr><td>XA4205</td><td>[ExportField] solo puede usarse en métodos con parámetros de 0.</td></tr>
        <tr><td>XA4206</td><td>[Exportar] no se puede usar en un tipo genérico.</td></tr>
        <tr><td>XA4207</td><td>[ExportField] no se puede usar en un tipo genérico.</td></tr>
        <tr><td>XA4208</td><td>[Java.Interop.ExportFieldAttribute] no se puede usar en un método que devuelve void.</td></tr>
        <tr><td>XA4209</td><td>No se pudo crear JavaTypeInfo para la clase: {0} debido a {1}</td></tr>
        <tr><td>XA4210</td><td>Debe agregar una referencia a Mono.Android.Export.dll cuando usas ExportAttribute o ExportFieldAttribute.</td></tr>
        <tr><td>XA4211</td><td>AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}' es menor que $(TargetFrameworkVersion) '{1}'. Con API-\ {1\\} para la compilación de ACW.</td></tr>
    </tbody>
</table>

### <a name="xa5xxx-errors"></a>Errores de XA5xxx

<table>
    <thead>
        <tr><td>Código de error</td><td>Descripción</td></tr>
    </thead>
    <tbody>
        <tr><td>XA5101</td><td>Falta '{0}' compilador. Instale NDK de Android.</td></tr>
        <tr><td>XA5102</td><td>Error de conversión de ensamblado a código nativo. Registre un informe de errores en <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5103</td><td>Error al compilar el archivo '{0}'. Registre un informe de errores en <a href="http://bugzilla.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5201</td><td>No se pudo vincular nativo. Revise las marcas de usuario proporcionadas para gcc: {0}</td></tr>
        <tr><td>XA5202</td><td>No se pudo vincular nativo. Revise el registro de compilación.</td></tr>
        <tr><td>XA5303</td><td>Nativo vinculación advertencia: {0}</td></tr>
        <tr><td>XA5300</td><td>SDK de Android no se encuentra o no completamente instalado.</td></tr>
        <tr><td>XA5301</td><td>Falta 'Quitar' herramienta. Instale Xcode componente 'Herramientas de línea de comandos'</td></tr>
        <tr><td>XA5302</td><td>Herramienta de 'dsymutil' que falta. Instale Xcode componente 'Herramientas de línea de comandos'</td></tr>
        <tr><td>XA5203</td><td>No se pudo generar los símbolos de depuración (directorio dSYM). Revise el registro de compilación.</td></tr>
        <tr><td>XA5204</td><td>No se pudo quitar el archivo binario final. Revise el registro de compilación.</td></tr>
        <tr><td>XA5205</td><td>Herramienta de 'aapt' que falta. Instale el paquete de herramientas de compilación del SDK de Android.</td></tr>
        <tr><td>XA5206</td><td>{0}. No existe {1} del directorio de recursos de Android.</td></tr>
        <tr><td>XA5207</td><td>{0}. No existe {1} de archivo de biblioteca de Java.</td></tr>
        <tr><td>XA5208</td><td>Error de descarga. Descargue el {0} y colóquelo en el directorio de {1}.</td></tr>
        <tr><td>XA5209</td><td>No se pudo descomprimir. Descargue {0} y extraerlo en el directorio de {1}.</td></tr>
        <tr><td>XA5210</td><td>{0}. No existe {1} del archivo de biblioteca nativa.</td></tr>
    </tbody>
</table>

### <a name="xa6xxx-errors"></a>Errores de XA6xxx

<table>
    <thead>
        <tr><td>Código de error</td><td>Descripción</td></tr>
    </thead>
    <tbody>
        <tr><td>XA6001</td><td>Versión en ejecución de Cecil no admite la eliminación de ensamblado</td></tr>
        <tr><td>XA6002</td><td>No se pudo quitar el ensamblado `{0}`.</td></tr>
        <tr><td>XA6003</td><td>UnauthorizedAccessException mensaje]</td></tr>
    </tbody>
</table>

### <a name="xa9xxx-errors"></a>Errores de XA9xxx

Este códigos de error son errores de licencia y la activación.



#### <a name="xa9000"></a>XA9000

 **Causa:** licencia ha expirado

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9001"></a>XA9001

 **Causa:** caducó

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
            <td>ADVERTENCIA</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>

 **Causa:** AndroidJavaLibrary

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>

 **Si un ensamblado de enlace tiene la .jar incrustados, esto se detecta en el momento de paquete, no en tiempo de compilación.**

 **Causa:** AndroidNativeLibrary

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9003"></a>XA9003

 **Causa:** System.Runtime.Serialization

 **Activada durante:** paquete

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>

 **Cause:** System.ServiceModel.Web

 **Activada durante:** paquete

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>

 **Causa:** Mono.Data.Tds

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>

 **Esto se hace referencia a System.Data.dll, lo que está permitido**

 **Causa:** Mono.Android.Export

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9004"></a>XA9004

 **Causa:** : generación de perfiles

 **Activada durante:** paquete

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9005"></a>XA9005

 **Causa:** el límite de tamaño (32 kb)

 **Activada durante:** paquete

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>-</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9006"></a>XA9006

 **Causa:** espacio de nombres System.Data.SqlClient

 **Activada durante:** paquete

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9008"></a>XA9008

 **Causa:** compilar desde la línea de comandos

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
            <td>Aceptar</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9009"></a>XA9009

 **Causa:** número de serie que faltan

 **Activada durante:** Untestable

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId no válido

 **Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>

Equivalente a XA9018



#### <a name="xa9011"></a>XA9011

 **Causa:** no se pudo actualizar el archivo de licencia (para el nuevo formato de archivo)

 **Activada durante:** activación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9012"></a>XA9012

 **Causa:** No a internet

 **Activada durante:** activación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9013"></a>XA9013

 **Causa:** Error desconocido

 **Activada durante:** activación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9014"></a>XA9014

 **Causa:** código de activación no válido

 **Activada durante:** activación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9017"></a>XA9017

 **Causa:** el servidor de activación no devuelve una licencia válida

 **Activada durante:** activación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>


#### <a name="xa9018"></a>XA9018

**Causa:** licencia no válida

**Activada durante:** de compilación

<table>
    <thead>
        <tr>
            <th>Inicio</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Trabajo</th>
            <th>Empresa</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>-</td>
            <td>-</td>
            <td>ERROR</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>
