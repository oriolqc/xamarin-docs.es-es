---
title: "Resolver errores de instalación de la biblioteca"
description: "En algunos casos, puede obtener errores durante la instalación de las bibliotecas de compatibilidad con Android. Esta guía proporciona soluciones para algunos errores comunes."
ms.topic: article
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 2df101615ed512d362fc065a1bb7080f3fd3bb33
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="resolving-library-installation-errors"></a>Resolver errores de instalación de la biblioteca

_En algunos casos, puede obtener errores durante la instalación de las bibliotecas de compatibilidad con Android. Esta guía proporciona soluciones para algunos errores comunes._

## <a name="overview"></a>Información general

Mientras se compila un proyecto de aplicación de Xamarin.Android, puede obtener errores de compilación cuando Visual Studio o Visual Studio para Mac intenta descargar e instalar las bibliotecas de dependencia. Muchos de estos errores se deben a problemas de conectividad de red, archivos dañados o problemas de versiones. Esta guía describe los errores de instalación de biblioteca de compatibilidad más comunes y proporciona los pasos para solucionar estos problemas así como generar de nuevo el proyecto de aplicación. 

 
<a name="m2repository" />
 
## <a name="errors-while-downloading-m2repository"></a>Errores mientras se descargaba m2Repository

Es posible que vea **m2repository** errores al hacer referencia a un paquete de NuGet de los servicios de bibliotecas de compatibilidad con Android o Google Play. El mensaje de error es similar al siguiente:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Este ejemplo corresponde a **android\_m2repository\_r16**, pero puede ver este mismo mensaje de error con una versión diferente como **android\_m2repository\_r18**  o **android\_m2repository\_r25**. 


<a name="automatic" /> 

### <a name="automatic-recovery-from-m2repository-errors"></a>Recuperación automática de errores de m2repository 

A menudo, este problema puede resolverse eliminando la biblioteca problemática y volver a generar según estos pasos: 

1. Desplácese hasta el directorio de la biblioteca de compatibilidad en el equipo:

    -   En Windows, se encuentran en bibliotecas de compatibilidad con **C:\\usuarios\\_nombre de usuario_\\AppData\\Local\\Xamarin**. 

    -   En Mac OS X, se encuentran en bibliotecas de compatibilidad con **/Users /_nombre de usuario_/.local/share/Xamarin**. 

2. Busque la carpeta de biblioteca y la versión correspondiente al mensaje de error. Por ejemplo, se encuentra en la carpeta de biblioteca y la versión para el mensaje de error anterior **Android.Support.v4\\22.2.1**:

    [![Biblioteca de compatibilidad de la ubicación de la carpeta de ejemplo para 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png)

3. Elimine el contenido de la carpeta de versión. No olvide quitar la **.zip** archivo así como el **contenido** y **incrustado** subdirectorios de esta carpeta. Para el mensaje de error de ejemplo mostrado anteriormente, los archivos y subdirectorios que se muestra en esta captura de pantalla (**contenido**, **incrustado**, y **android_m2repository_r16.zip**) son se pueden eliminar:

    [![Contenido de ejemplo de 22.2.1 compatible con la carpeta de biblioteca](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png)

   Tenga en cuenta que esto es importante eliminar el *todo* contenido de esta carpeta. Aunque esta carpeta puede contener inicialmente el "Falta" **android\_m2repository\_r16.zip** archivo, este archivo se haya descargado parcialmente o está dañada.

4. Recompile el proyecto &ndash; lo hace, el proceso de compilación volver a descargar la biblioteca que falta.

En la mayoría de los casos, estos pasos se resuelva el error de compilación y le permitirá continuar. Si eliminar esta biblioteca no resuelve el error de compilación, debe descargar e instalar manualmente el **android\_m2repository\_r_nn_.zip** archivo tal como se describe en la sección siguiente. 


<a name="download" /> 

### <a name="manually-downloading-m2repository"></a>Descargar manualmente m2repository

Si ha intentado siguiendo los pasos de recuperación automática anteriores y sigue teniendo errores de compilación, puede descargar manualmente la **android\_m2repository\_r_nn_.zip** archivo (mediante un explorador web) e instálelo según los siguientes pasos. Este procedimiento también es útil si no tiene acceso a internet en el equipo de desarrollo, pero puede descargar el archivo con un equipo diferente. 

1.  Descargue el **android\_m2repository\_r_nn_.zip** archivo que se corresponde con el mensaje de error &ndash; vínculos se proporcionan en la lista siguiente (junto con el hash MD5 correspondiente de cada vínculo DIRECCIÓN URL):

    -   [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    -   [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    -   [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    -   [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    -   [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    -   [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    -   [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    -   [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    -   [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    -   [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    -   [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    -   [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    -   [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    -   [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    -   [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    -   [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    -   [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    -   [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Si el **m2repository** archivo no se muestra en esta tabla, puede crear la dirección URL de descarga anteponiendo **https://dl-ssl.google.com/android/repository/** en el nombre de la **m2repository**  para descargar. Por ejemplo, utilice **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** para descargar **android\_m2repository\_r10.zip** .

2.  Cambie el nombre del archivo con el hash de MD5 correspondiente de la URL de descarga, como se muestra en la tabla anterior. Por ejemplo, si ha descargado **android\_m2repository\_r25.zip**, cambie su nombre a **0B3F1796C97C707339FB13AE8507AF50.zip**. Si el hash MD5 para la URL de descarga del archivo descargado no se muestra en la tabla, puede usar un [en línea generador MD5](http://www.webconfs.com/online-md5-generator.php) para convertir la dirección URL en una cadena de hash MD5. 

3.  Copie el archivo en el Xamarin **completa** carpeta: 

    -   En Windows, esta carpeta se encuentra en **C:\\usuarios\\***nombre de usuario***\\AppData\\Local\\Xamarin\\completa**. 

    -   En Mac OS X, esta carpeta se encuentra en **/Users /***nombre de usuario***/.local/share/Xamarin/zips**. 

    Por ejemplo, la captura de pantalla siguiente muestra el resultado cuando **android\_m2repository\_r16.zip** se descarga y se cambia el nombre con el hash MD5 de su URL de descarga de Windows:

    [![Ejemplo del repositorio r16.zip que se va a cambiar el nombre a 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png)


Si este procedimiento no resuelve el error de compilación, debe descargar manualmente la **android\_m2repository\_r_nn_.zip** de archivos, descomprímalo e instalar su contenido como se describe en la sección siguiente. 


<a name="install" /> 

### <a name="manually-downloading-and-installing-m2repository-files"></a>Descargar manualmente e instalar archivos de m2repository

El proceso totalmente manual para recuperarse de **m2repository** conlleva errores descargar el **android\_m2repository\_r_nn_.zip** archivo (mediante un explorador web), Descomprimiendo se y copiar su contenido en el directorio de la biblioteca de compatibilidad en el equipo. En el ejemplo siguiente, se recuperará de este mensaje de error: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Siga estos pasos para descargar **m2repository** e instalar su contenido:

1.  Elimine el contenido de la carpeta de biblioteca correspondiente al mensaje de error. Por ejemplo, en el mensaje de error anterior eliminaría el contenido de **C:\\usuarios\\***nombre de usuario***\\AppData\\Local\\Xamarin\\ Android.Support.v4\\23.1.1.0**. 
    Como se describió anteriormente, debe eliminar todo el contenido de este directorio:

    [![Eliminando el contenido, incrustado y las carpetas de android_m2repository desde el 23.1.1.0 carpeta](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png)

2.  Descargue el **android\_m2repository\_r_nn_.zip** archivo de Google que corresponde al error de mensaje (consulte la tabla en la sección anterior para vínculos).

3.  Extraer este **.zip** archivo en cualquier ubicación (por ejemplo, el escritorio). Esto debería crear un directorio que se corresponde con el nombre de la **.zip** archive. En este directorio, encontrará un subdirectorio denominado **m2repository**: 

    [![se encuentra en la carpeta de m2repository extraído el archivo zip](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png)

4.  En el directorio library con control de versiones que purga en el paso 1, volver a crear la **contenido** y **incrustado** subdirectorios. Por ejemplo, la captura de pantalla siguiente muestra **contenido** y **incrustado** subdirectorios que se creen en el **23.1.1.0** carpeta para **android \_m2repository\_r25.zip**: 

    [![Crear contenido y carpetas incrustadas en el 23.1.1.0 carpeta](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png)

5.  Copia **m2repository** desde la **.zip** en el **contenido** directorio que creó en el paso anterior: 

    [![Captura de pantalla de m2repository que se copian en la carpeta 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)

6.  En la **.zip** directory, vaya a **m2repository\\com\\android\\admite\\compatibilidad v4** y abra la carpeta correspondiente el número de versión creado anteriormente (en este ejemplo, **23.1.1**):

    [![Lista de ejemplo de archivos contenidos en la carpeta support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png)

7.  Copiar todos los archivos de esta carpeta a la **incrustado** directorio creado en el paso 4:

    [![Ejemplo de archivos copiados a la carpeta 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png)

8.  Compruebe que todos los archivos se copian. El **incrustado** directorio debe contener ahora archivos como **.jar**, **.AAR y**, y **.pom**.

En este momento, ha instalado manualmente los componentes que faltan y el proyecto debería compilarse sin errores. Si no es así, compruebe que ha descargado el **m2repository** **.zip** archivar versión que corresponde exactamente a la versión del mensaje de error y compruebe que ha instalado su contenido en el Corrija ubicaciones tal y como se describe en los pasos anteriores. 


<a name="summary" /> 

## <a name="summary"></a>Resumen 

Este artículo explica cómo recuperarse de errores comunes que pueden tener lugar durante la instalación de las bibliotecas de dependencia y descarga automática. Describe cómo eliminar la biblioteca problemática y volver a generar el proyecto como una manera de volver a descargar y volver a instalar la biblioteca. Describe cómo descargar la biblioteca e instalarlo en el **completa** carpeta. También se describe un procedimiento más complejo para descargar e instalar los archivos necesarios como una manera de solucionar problemas que no se puede resolver a través de medios automática manualmente. 
