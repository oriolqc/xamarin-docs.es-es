---
title: Enlace de un proyecto de biblioteca de Eclipse
description: Este tutorial explica cómo usar las plantillas de proyecto de Xamarin.Android para enlazar un proyecto de biblioteca de Android de Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a5887748eda8af89b9215e3e121db592dfa73935
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957661"
---
# <a name="binding-an-eclipse-library-project"></a>Enlace de un proyecto de biblioteca de Eclipse

_Este tutorial explica cómo usar las plantillas de proyecto de Xamarin.Android para enlazar un proyecto de biblioteca de Android de Eclipse._


## <a name="overview"></a>Información general

Aunque. Archivos AAR son cada vez más la norma para la distribución de la biblioteca de Android, en algunos casos es necesario crear un enlace para un *proyecto de biblioteca Android*. Los proyectos de biblioteca Android son proyectos de Android especiales que contienen código que se puede compartir y recursos que pueden hacer referencia a los proyectos de aplicación de Android. Normalmente, enlaza a un proyecto de biblioteca de Android cuando se crea la biblioteca en el IDE de Eclipse.
En este tutorial se proporciona ejemplos de cómo crear un proyecto de biblioteca Android. Código postal de la estructura de directorios de un proyecto de Eclipse.

Los proyectos de biblioteca Android son diferentes de los proyectos de Android normales en que no se compilan en un APK y no, son por sí solos, puede implementar en un dispositivo. En su lugar, un proyecto de biblioteca Android sirve para hacer referencia a un proyecto de aplicación de Android. Cuando se compila un proyecto de aplicación de Android, en primer lugar se compila el proyecto de biblioteca de Android. El proyecto de aplicación de Android, a continuación, se se absorba en el proyecto de biblioteca de Android compilado e incluya el código y los recursos en el APK para su distribución. Debido a esta diferencia, crear un enlace para un proyecto de biblioteca de Android es ligeramente diferente de la creación de un enlace para un Java. JAR o. Archivo AAR.



## <a name="walkthrough"></a>Tutorial

Para usar un proyecto de biblioteca de Android en un proyecto de enlace de Java de Xamarin.Android es necesario para compilar el proyecto de biblioteca de Android en Eclipse. Captura de pantalla siguiente muestra un ejemplo de un proyecto de biblioteca de Android después de la compilación: 

[![Proyecto de biblioteca de ejemplo en Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Tenga en cuenta que se ha compilado el código fuente del proyecto de biblioteca de Android en un archivo temporal. Archivo JAR denominado **android mapviewballoons.jar**, y que los recursos se han copiado en el **res/bin/tardaban** carpeta. 

Una vez que se ha compilado el proyecto de biblioteca de Android en Eclipse, se puede, a continuación, enlazar con un proyecto de enlace de Java de Xamarin.Android. Primera una. Se debe crear el archivo ZIP que contiene el **bin** y **res** carpetas del proyecto de biblioteca Android. Es importante que quite el intermedia **tardaban** subdirectorio para que los recursos residen en **bin/res**. Captura de pantalla siguiente muestra el contenido de una tal. Archivo ZIP: 

[![Contenido del archivo .zip del proyecto de biblioteca de Android](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Esto. Archivo ZIP, a continuación, se agrega al proyecto de enlace de Java de Xamarin.Android, como se muestra en la captura de pantalla siguiente:

[![ZIP que se agrega al proyecto de Java de enlace](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Tenga en cuenta que la acción de compilación de la. Archivo ZIP se ha establecido automáticamente en **LibraryProjectZip**.

Si hay alguno. Archivos JAR necesarios para el proyecto de biblioteca de Android, se debe agregar a la **archivos JAR** carpeta del proyecto de biblioteca de enlaces de Java y el **acción de compilación** establecido en **ReferenceJar**. Un ejemplo de esto se aprecia en la captura de pantalla siguiente: 

[![Establecido en ReferenceJar de acción de compilación](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Una vez completados estos pasos, se puede usar el proyecto Xamarin.Android Java enlace como se describe anteriormente en este documento.

> [!NOTE]
> En este momento no se admite la compilación de los proyectos de biblioteca de Android en otros IDE. Otros IDE no puede crear la misma estructura de directorios o archivos en el **bin** carpeta como Eclipse. 


## <a name="summary"></a>Resumen

En este artículo, hemos visto el proceso de enlazar un proyecto de biblioteca Android. Se generó el proyecto de biblioteca de Android en Eclipse, a continuación, creamos un archivo zip desde el **bin** y **res** carpetas del proyecto de biblioteca Android. A continuación, se usa este archivo zip para crear un proyecto de enlace de Java de Xamarin.Android. 

