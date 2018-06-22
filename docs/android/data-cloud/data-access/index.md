---
title: Acceso a datos Xamarin.Android
description: Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos es pequeña trivial, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos.  El sistema Android tiene el motor de base de datos de SQLite 'integrado' y acceso para almacenar y recuperar los datos se simplifica con la plataforma de Xamarin. Este documento muestra cómo obtener acceso a una base de datos de SQLite de forma multiplataforma.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 508a8f54723bfdd30b1c8ea8d4b6c1d422ae24e9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763255"
---
# <a name="xamarinandroid-data-access"></a>Acceso a datos Xamarin.Android

_Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos es pequeña trivial, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos.  El sistema Android tiene el motor de base de datos de SQLite 'integrado' y acceso para almacenar y recuperar los datos se simplifica con la plataforma de Xamarin. Este documento muestra cómo obtener acceso a una base de datos de SQLite de forma multiplataforma._

## <a name="data-access-overview"></a>Información general sobre el acceso de datos

Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos es pequeña trivial, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos. Android ambos tiene el motor de base de datos de Sqlite "integrado" y acceso a los datos se simplifica con la plataforma de Xamarin que se incluye con el proveedor de datos de SQLite.

Xamarin.Android admiten las API de acceso de base de datos, como:

-  Marco de trabajo ADO.NET.
-  Biblioteca de terceros de SQLite NET 3rd.

La mayoría del código de esta sección es completamente multiplataforma y se ejecutará en iOS o Android sin cambios. Hay dos aplicaciones de ejemplo que se describe:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; las operaciones de datos Simple escribe los resultados en un texto mostrarán control;

-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; integra las operaciones de datos en una aplicación pequeña que se enumera y edita una estructura de datos simple.

Ambas soluciones de ejemplo contienen proyectos de aplicación de ejemplo de Android y iOS.

Para las aplicaciones de Xamarin.Forms, leer [trabajar con bases de datos](~/xamarin-forms/app-fundamentals/databases.md) que explica cómo trabajar con código en una biblioteca PCL con Xamarin.Forms.

Los temas de esta sección describen acceso a datos en Xamarin.Android mediante código como el motor de base de datos. Puede tener acceso a la base de datos "directamente" mediante la sintaxis ADO.NET o puede incluir las ORM SQLite.NET y realizar operaciones de datos en C#.

Se revisan los dos ejemplos: uno que contiene código de acceso de datos muy simple que salidas para un campo de texto y una aplicación simple que incluye creación, leer, actualizar y eliminar funciones. También se trata los subprocesos y cómo inicializar la aplicación con una base de datos de SQLite rellenada previamente.

Para obtener ejemplos adicionales de acceso a datos entre plataformas, vea nuestra [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) caso práctico.


## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Datos de Android recetas](https://developer.xamarin.com/recipes/android/data/)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
