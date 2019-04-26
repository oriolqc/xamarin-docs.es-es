---
title: Acceso a datos de Xamarin.Android
description: Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos pequeña, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos.  Android tiene el motor de base de datos de SQLite 'integrado' y acceso para almacenar y recuperar los datos se simplifica mediante la plataforma de Xamarin. Este documento muestra cómo obtener acceso a una base de datos de SQLite de forma multiplataforma.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 08720734de73af12d8a7383fa7d523dc350c4462
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61011360"
---
# <a name="xamarinandroid-data-access"></a>Acceso a datos de Xamarin.Android

_Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos pequeña, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos.  Android tiene el motor de base de datos de SQLite 'integrado' y acceso para almacenar y recuperar los datos se simplifica mediante la plataforma de Xamarin. Este documento muestra cómo obtener acceso a una base de datos de SQLite de forma multiplataforma._

## <a name="data-access-overview"></a>Información general sobre el acceso de datos

Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos pequeña, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos. Android ambos tiene el motor de base de datos de SQLite "integrado" y el acceso a los datos se simplifica mediante la plataforma de Xamarin que se incluye con el proveedor de datos de SQLite.

Xamarin.Android admite las API de acceso de base de datos, como:

- Marco de trabajo ADO.NET.
- Biblioteca de terceros 3rd SQLite-NET.

La mayoría del código en esta sección es completamente multiplataforma y se ejecuta en iOS o Android sin modificaciones. Hay dos aplicaciones de ejemplo descritas:

- [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; las operaciones de datos Simple escribe los resultados en un texto de mostrarán el control;

- [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; integra las operaciones de datos en una pequeña aplicación de trabajo que muestra y edita una estructura de datos simple.

Ambas soluciones de ejemplo contienen proyectos de aplicación de ejemplo de Android y iOS.

Para las aplicaciones de Xamarin.Forms, lea [trabajar con bases de datos](~/xamarin-forms/app-fundamentals/databases.md) que explica cómo trabajar con SQLite en una biblioteca PCL con Xamarin.Forms.

Los temas de esta sección describen el acceso a datos en Xamarin.Android usa SQLite como el motor de base de datos. Se puede tener acceso a la base de datos "directamente" mediante la sintaxis ADO.NET o puede incluir el SQLite.NET ORM y realizar operaciones de datos en C#.

Se revisan los dos ejemplos: uno que contiene el código de acceso a datos muy simple que salidas para un campo de texto y una aplicación simple que incluye creación, leer, actualizar y eliminar funciones. También se analiza el subprocesamiento y cómo inicializar la aplicación con una base de datos de SQLite rellenada previamente.

Para obtener ejemplos adicionales de acceso a datos multiplataforma, consulte nuestra [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) caso práctico.


## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
