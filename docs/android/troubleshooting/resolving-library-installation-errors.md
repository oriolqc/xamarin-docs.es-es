---
title: Resolver errores de instalación de la biblioteca
description: En algunos casos, es posible que obtenga errores durante la instalación de bibliotecas de compatibilidad con Android. Esta guía proporciona soluciones para algunos errores comunes.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/14/2018
ms.openlocfilehash: 2ef81cfda92a6497e69f27b0584a97996094b1a4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107137"
---
# <a name="resolving-library-installation-errors"></a>Resolver errores de instalación de la biblioteca

_En algunos casos, es posible que obtenga errores durante la instalación de bibliotecas de compatibilidad con Android. Esta guía proporciona soluciones para algunos errores comunes._

## <a name="overview"></a>Información general

Al compilar un proyecto de aplicación de Xamarin.Android, es posible que obtenga errores de compilación cuando Visual Studio o Visual Studio para Mac intenta descargar e instalar las bibliotecas de dependencia. Muchos de estos errores están provocados por problemas de conectividad de red, archivos dañados o problemas de versiones. Esta guía describe los errores más comunes de instalación de biblioteca de soporte técnico y proporciona los pasos para solucionar estos problemas y obtener volver a compilar el proyecto de aplicación. 

 
 
## <a name="errors-while-downloading-m2repository"></a>Errores mientras se descargaba m2Repository

Es posible que vea **m2repository** errores al hacer referencia a un paquete NuGet de los servicios de Android Support Libraries o Google Play. El mensaje de error es similar al siguiente:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

En este ejemplo es para **android\_m2repository\_r16**, pero es posible que vea este mismo mensaje de error para una versión diferente, como **android\_m2repository\_r18**  o **android\_m2repository\_r25**. 



### <a name="automatic-recovery-from-m2repository-errors"></a>Recuperación automática de errores de m2repository 

A menudo, este problema puede resolverse mediante la eliminación de la biblioteca problemática y volver a generar según estos pasos: 

1. Navegue hasta el directorio de la biblioteca de soporte técnico en el equipo:

    -   En Windows, se encuentran en las bibliotecas de soporte técnico **C:\\usuarios\\_username_\\AppData\\Local\\Xamarin**. 

    -   En Mac OS X, se encuentran en las bibliotecas de soporte técnico **/Users /_username_/.local/share/Xamarin**. 

2. Busque la carpeta de biblioteca y la versión correspondiente al mensaje de error. Por ejemplo, se encuentra en la carpeta de biblioteca y la versión para el mensaje de error anterior **Android.Support.v4\\22.2.1**:

    [![Biblioteca de compatibilidad de ubicación de carpeta de ejemplo para 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Elimine el contenido de la carpeta de versión. No olvide quitar el **.zip** archivo así como el **contenido** y **incrustado** subdirectorios dentro de esta carpeta. Para el mensaje de error de ejemplo mostrado anteriormente, los archivos y subdirectorios que se muestra en esta captura de pantalla (**contenido**, **incrustado**, y **android_m2repository_r16.zip**) son para se puede eliminar:

    [![Contenido de ejemplo de 22.2.1 admite la carpeta de biblioteca](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Tenga en cuenta que es importante eliminar el *todo* contenido de esta carpeta. Aunque esta carpeta puede contener inicialmente el "Falta" **android\_m2repository\_r16.zip** archivo, este archivo se haya dañado o descargado parcialmente.

4. Recompile el proyecto &ndash; Esto hará que el proceso de compilación volver a descargar la biblioteca que falta.

En la mayoría de los casos, estos pasos se resuelva el error de compilación y podrá continuar. Si elimina dicha biblioteca no resuelve el error de compilación, debe descargar e instalar manualmente el **android\_m2repository\_r_nn_.zip** archivo tal como se describe en la sección siguiente. 



### <a name="manually-downloading-m2repository"></a>Descargar manualmente m2repository

Si ya ha intentado siguiendo los pasos de recuperación automática anteriores y aún tiene errores de compilación, puede descargar manualmente el **android\_m2repository\_r_nn_.zip** archivo (mediante un explorador web) e instálelo según los siguientes pasos. Este procedimiento también es útil si no tiene acceso a internet en el equipo de desarrollo pero se puede descargar el archivo con un equipo diferente. 

1.  Descargue el **android\_m2repository\_r_nn_.zip** archivo que se corresponde con el mensaje de error &ndash; vínculos se proporcionan en la lista siguiente (junto con el hash MD5 correspondiente de cada vínculo DIRECCIÓN URL):

    -   [Android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    -   [Android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    -   [Android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    -   [Android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    -   [Android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    -   [Android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    -   [Android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    -   [Android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    -   [Android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    -   [Android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    -   [Android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    -   [Android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    -   [Android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    -   [Android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    -   [Android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    -   [Android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    -   [Android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    -   [Android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Si el **m2repository** archivo no se muestra en esta tabla, puede crear la dirección URL de descarga anteponiendo **https://dl-ssl.google.com/android/repository/** en el nombre de la **m2repository** para descargar. Por ejemplo, usar **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** descargar **android\_m2repository\_r10.zip**.

2.  Cambie el nombre del archivo con el hash MD5 correspondiente de la dirección URL de descarga como se muestra en la tabla anterior. Por ejemplo, si descargó **android\_m2repository\_r25.zip**, cambie su nombre a **0B3F1796C97C707339FB13AE8507AF50.zip**. Si el hash MD5 para la dirección URL de descarga del archivo descargado no se muestra en la tabla, puede usar un [online generador MD5](http://www.webconfs.com/online-md5-generator.php) para convertir la dirección URL en una cadena de hash MD5. 

3.  Copie el archivo en Xamarin **zips** carpeta: 

    -   En Windows, esta carpeta se encuentra en **C:\\usuarios\\***username***\\AppData\\Local\\Xamarin\\zips**. 

    -   En Mac OS X, esta carpeta se encuentra en **/Users /***username***/.local/share/Xamarin/zips**. 

    Por ejemplo, la captura de pantalla siguiente muestra el resultado cuando **android\_m2repository\_r16.zip** se descarga y se cambia el nombre con el hash MD5 de su dirección URL de descarga en Windows:

    [![Ejemplo del repositorio r16.zip que se ha cambiado a 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)


Si este procedimiento no resuelve el error de compilación, debe descargar manualmente la **android\_m2repository\_r_nn_.zip** archivo, descomprima el archivo e instalar su contenido como se describe en la sección siguiente. 


### <a name="manually-downloading-and-installing-m2repository-files"></a>Manualmente descargando e instalando m2repository archivos

El proceso completamente manual para recuperarse de **m2repository** conlleva errores descargando el **android\_m2repository\_r_nn_.zip** archivo (mediante un explorador web), Descomprimiendo él y copiar su contenido en el directorio de biblioteca de soporte técnico en el equipo. En el ejemplo siguiente, se podrá recuperar de este mensaje de error: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Utilice los pasos siguientes para descargar **m2repository** e instalar su contenido:

1.  Elimine el contenido de la carpeta de la biblioteca correspondiente al mensaje de error. Por ejemplo, en el mensaje de error anterior eliminaría el contenido de **C:\\usuarios\\***username***\\AppData\\Local\\Xamarin\\ Android.Support.v4\\23.1.1.0**. 
    Como se describió anteriormente, debe eliminar todo el contenido de este directorio:

    [![Eliminar contenido, incrustado y las carpetas de android_m2repository desde el 23.1.1.0 carpeta](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2.  Descargue el **android\_m2repository\_r_nn_.zip** archivo de Google que se corresponde con el error mensaje (consulte la tabla en la sección anterior para vínculos).

3.  Extraer este **.zip** archivar en cualquier ubicación (por ejemplo, el escritorio). Esto debe crear un directorio que se corresponde con el nombre de la **.zip** archive. En este directorio, encontrará un subdirectorio denominado **m2repository**: 

    [![se encontró en la carpeta de m2repository extrae archivo zip](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4.  En el directorio de bibliotecas con versiones que purga en el paso 1, volver a crear la **contenido** y **incrustado** subdirectorios. Por ejemplo, se muestra la siguiente captura de pantalla **contenido** y **incrustado** subdirectorios que se crean en el **23.1.1.0** carpeta **android \_m2repository\_r25.zip**: 

    [![Creación de contenido y las carpetas incrustadas en el 23.1.1.0 carpeta](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5.  Copia **m2repository** desde el extraídos **.zip** en el **contenido** directorio que creó en el paso anterior: 

    [![Captura de pantalla de m2repository copiado en la carpeta 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6.  En el texto extraído **.zip** directorio, vaya a **m2repository\\com\\android\\admite\\soporte v4** y abra la carpeta correspondiente el número de versión que creó anteriormente (en este ejemplo, **23.1.1**):

    [![Lista de ejemplo de los archivos contenidos en la carpeta support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7.  Copiar todos los archivos de esta carpeta a la **incrustado** directorio creado en el paso 4:

    [![Ejemplo de archivos copiados a la carpeta 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8.  Compruebe que todos los archivos se copian. El **incrustado** directorio ahora debe contener los archivos como **.jar**, **.aar**, y **.pom**.

9.  Descomprima el contenido de cualquier extraído **.aar** archivos a la **incrustado** directory. En Windows, anexe un **.zip** extensión a la **.aar** archivo, ábralo y copie el contenido a la **incrustado** directory.
    En macOS, descomprima el **.aar** archivo utilizando el **descomprima** comando en el Terminal (por ejemplo, **descomprima file.aar**).

En este momento, ha instalado manualmente los componentes que faltan y su proyecto debería compilarse sin errores. Si no, compruebe que ha descargado el **m2repository** **.zip** archivar la versión que se corresponde exactamente a la versión del mensaje de error y compruebe que ha instalado su contenido en el corregir las ubicaciones, como se describe en los pasos anteriores. 



## <a name="summary"></a>Resumen 

En este artículo se explica cómo recuperarse de errores comunes que pueden tener lugar durante la instalación de bibliotecas de dependencia y descarga automática. Describe cómo eliminar la biblioteca problemática y recompile el proyecto como una manera de volver a descargar y volver a instalar la biblioteca. Describe cómo descargar la biblioteca e instalarlo en el **zips** carpeta. También se describe un procedimiento más complejo para descargar e instalar los archivos necesarios como una manera de solucionar problemas que no se puede resolver a través de medios automática manualmente. 
