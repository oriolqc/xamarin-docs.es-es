---
title: Acceso a datos Xamarin.iOS
description: Vínculos de este documento a las guías que describen cómo trabajar con bases de datos locales en una aplicación de Xamarin.iOS. Contenido vinculado describe SQLite.NET, ADO.NET y mucho más.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: a986ea9931f62497e5a6863c84bd4041983d66d9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784581"
---
# <a name="xamarinios-data-access"></a>Acceso a datos Xamarin.iOS

Xamarin.iOS admite las API de acceso de base de datos, como:

-  Marco de trabajo ADO.NET.
-  Biblioteca de terceros de SQLite NET 3rd.

Esta guía proporciona información general de las bases de datos en general antes de describir cómo configurar SQLite.NET y ADO.NET para tener acceso a las bases de datos de SQLite en las aplicaciones de Xamarin.iOS. 

La mayoría del código de este documento completamente multiplataforma y se ejecutará en iOS o Android sin cambios. Hay dos aplicaciones de ejemplo que se describe:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) : operaciones de datos Simple escribe los resultados en un texto mostrarán control;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – integra las operaciones de datos en una aplicación pequeña que se enumera y edita una estructura de datos simple.

Ambas soluciones de ejemplo contienen proyectos de aplicación de ejemplo de Android y iOS.

Para las aplicaciones de Xamarin.Forms, leer [trabajar con bases de datos](~/xamarin-forms/app-fundamentals/databases.md) que explica cómo trabajar con código en una biblioteca PCL con Xamarin.Forms.

## <a name="sections"></a>Secciones

-  [Introducción](introduction.md)
-  [Configuración](configuration.md)
-  [Uso de SQLite.NET ORM](using-sqlite-orm.md)
-  [Uso de ADO.NET](using-adonet.md)
-  [Uso de datos en una aplicación](using-data-in-an-app.md)

## <a name="summary"></a>Resumen

Este capítulo trata el acceso a datos en Xamarin.iOS mediante código como el motor de base de datos. Puede tener acceso a la base de datos "directamente" mediante sintaxis ADO.NET o puede incluir las ORM SQLite.NET y realizar operaciones de datos en C#.

Revisamos dos ejemplos: uno que contiene código de acceso de datos muy simple que salidas para un campo de texto y una aplicación simple que incluye creación, leer, actualizar y eliminar funciones. También se ha tratado de subprocesos y de cómo inicializar la aplicación con una base de datos de SQLite rellenada previamente.

Para obtener ejemplos adicionales de acceso a datos entre plataformas, vea nuestra [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) caso práctico.

## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recetas de datos](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
