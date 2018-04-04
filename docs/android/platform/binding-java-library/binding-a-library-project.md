---
title: Enlace de un proyecto de biblioteca de Eclipse
description: En este tutorial se explica cómo utilizar plantillas de proyecto Xamarin.Android para enlazar un proyecto de biblioteca de Eclipse Android.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1c3c33f1b958aff5818b26e4408e60f449b46f41
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="binding-an-eclipse-library-project"></a>Enlace de un proyecto de biblioteca de Eclipse

_En este tutorial se explica cómo utilizar plantillas de proyecto Xamarin.Android para enlazar un proyecto de biblioteca de Eclipse Android._


## <a name="overview"></a>Información general

Aunque. Archivos AAR cada vez más se están convirtiendo en la norma para la distribución de la biblioteca de Android, en algunos casos es necesario crear un enlace para un *proyecto de biblioteca de Android*. Los proyectos de biblioteca de Android son proyectos Android especiales que contienen código que se pueden compartir y recursos que pueden hacer referencia a proyectos de aplicación de Android. Por lo general, enlaza a un proyecto de biblioteca de Android cuando se crea la biblioteca en el IDE de Eclipse.
Este tutorial proporciona ejemplos de cómo crear un proyecto de biblioteca de Android. Código postal de la estructura de directorios de un proyecto de Eclipse.

Proyectos de biblioteca de Android son diferentes de proyectos Android normales en que no se compilan en un APK y no lo están, por sí mismos, puede implementar en un dispositivo. En su lugar, un proyecto de biblioteca de Android está pensado para hacer referencia a un proyecto de aplicación de Android. Cuando se compila un proyecto de aplicación de Android, en primer lugar se compila el proyecto de biblioteca de Android. El proyecto de aplicación de Android, a continuación, se se absorba en el proyecto de biblioteca de Android compilado e incluya el código y los recursos en el APK para la distribución. Debido a esta diferencia, crear un enlace para un proyecto de biblioteca de Android es ligeramente diferente de la creación de un enlace para un Java. JAR o. Archivos AAR.



## <a name="walkthrough"></a>Tutorial

Para utilizar un proyecto de biblioteca de Android en un proyecto de enlace de Java de Xamarin.Android es necesario para compilar el proyecto de biblioteca de Android en Eclipse. Captura de pantalla siguiente muestra un ejemplo de un proyecto de biblioteca de Android después de la compilación: 

[![Proyecto de biblioteca de ejemplo en Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Tenga en cuenta que se ha compilado el código fuente desde el proyecto de biblioteca de Android en un archivo temporal. Archivo JAR denominado **android mapviewballoons.jar**, y que los recursos se han copiado en el **res/bin/delicadas** carpeta. 

Una vez que se ha compilado el proyecto de biblioteca de Android en Eclipse, a continuación, se puede enlazar mediante un proyecto de enlace de Java de Xamarin.Android. Primer una. Se debe crear el archivo ZIP que contiene el **bin** y **res** carpetas del proyecto de biblioteca de Android. Es importante que quite el intermedias **delicadas** subdirectorio para que residen los recursos en **bin/res**. Captura de pantalla siguiente muestra el contenido de un este tipo. Archivo ZIP: 

[![Contenido de .zip del proyecto de biblioteca de Android](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Esto. Archivo ZIP, a continuación, se agrega al proyecto de enlace de Java de Xamarin.Android, como se muestra en la captura de pantalla siguiente:

[![Código postal agregado al proyecto de enlace de Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Tenga en cuenta que la acción de compilación de la. Archivo ZIP se ha establecido de forma automática en **LibraryProjectZip**.

Si hay alguno. Archivos JAR que son necesarios para el proyecto de biblioteca de Android, se debe agregar a la **archivos JAR** carpeta del proyecto de biblioteca de enlace de Java y la **acción de compilación** establecido en **ReferenceJar**. Un ejemplo de esto se puede ver en la captura de pantalla siguiente: 

[![Acción que se establece en ReferenceJar de compilación](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Una vez completados estos pasos, el proyecto de enlace de Java de Xamarin.Android puede utilizarse tal y como se describe anteriormente en este documento.

> [!NOTE]
> Compilar los proyectos de biblioteca de Android en otros IDE. no se admite en este momento. No puede crear otros IDE la misma estructura de directorios o archivos de la **bin** carpeta como Eclipse. 


## <a name="summary"></a>Resumen

En este artículo, se le guía por el proceso de enlazar un proyecto de biblioteca de Android. Se generó el proyecto de biblioteca de Android en Eclipse, a continuación, se crea un archivo zip de la **bin** y **res** carpetas del proyecto de biblioteca de Android. A continuación, se usa este archivo zip para crear un proyecto de enlace de Java de Xamarin.Android. 

