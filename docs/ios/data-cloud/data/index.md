---
title: Acceso a datos de Xamarin.iOS
description: Este documento incluye vínculos a guías que describen cómo trabajar con bases de datos en una aplicación de Xamarin.iOS. Contenido vinculado describe SQLite.NET, ADO.NET y mucho más.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 420f52a055dc1c03a017723ab34c2fc3b5363656
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650228"
---
# <a name="xamarinios-data-access"></a>Acceso a datos de Xamarin.iOS

Xamarin.iOS admite las API de acceso de base de datos, como:

-  Marco de trabajo ADO.NET.
-  Biblioteca de terceros 3rd SQLite-NET.

Esta guía proporciona información general de las bases de datos en general antes de que se describe cómo configurar SQLite.NET y ADO.NET para tener acceso a las bases de datos de SQLite en sus aplicaciones de Xamarin.iOS. 

La mayoría del código de este documento es completamente multiplataforma y se ejecuta en iOS o Android sin modificaciones. Hay dos aplicaciones de ejemplo descritas:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) : las operaciones de datos Simple escribe los resultados en un texto de mostrarán el control;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – integra las operaciones de datos en una pequeña aplicación de trabajo que muestra y edita una estructura de datos simple.

Ambas soluciones de ejemplo contienen proyectos de aplicación de ejemplo de Android y iOS.

Para las aplicaciones de Xamarin.Forms, lea [trabajar con bases de datos](~/xamarin-forms/data-cloud/data/databases.md) que explica cómo trabajar con SQLite en una biblioteca PCL con Xamarin.Forms.

## <a name="sections"></a>Secciones

-  [Introducción](introduction.md)
-  [Configuración](configuration.md)
-  [Uso de SQLite.NET ORM](using-sqlite-orm.md)
-  [Uso de ADO.NET](using-adonet.md)
-  [Uso de datos en una aplicación](using-data-in-an-app.md)

## <a name="summary"></a>Resumen

Este capítulo describe el acceso a datos en Xamarin.iOS mediante SQLite como el motor de base de datos. Puede tener acceso a la base de datos "directamente" con sintaxis ADO.NET o puede incluir el SQLite.NET ORM y realizar operaciones de datos en C#.

Revisamos dos ejemplos: uno que contiene el código de acceso a datos muy simple que salidas para un campo de texto y una aplicación simple que incluye creación, leer, actualizar y eliminar funciones. También analizamos cómo inicializar la aplicación con una base de datos de SQLite rellenada previamente y subprocesamiento.

Para obtener ejemplos adicionales de acceso a datos multiplataforma, consulte nuestra [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) caso práctico.

## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recetas de datos](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
