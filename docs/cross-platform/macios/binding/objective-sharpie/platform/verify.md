---
title: Compruebe los atributos
ms.topic: article
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 10fb2e2824a05954e19f9b483884061b217be683
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="verify-attributes"></a>Compruebe los atributos


A menudo, encontrará que enlaces producidos por objetivo Sharpie se anotará con el `[Verify]` atributo. Estos atributos indican que debe _comprobar_ que objetivo Sharpie ha hecho lo correcto comparando el enlace con la declaración C/Objective-C original (que se proporciona en un comentario sobre la declaración de enlace).

Comprobación se recomienda para _todos los_ enlazado declaraciones, pero es más probable _requiere_ para declaraciones anotan con el `[Verify]` atributo. Esto es porque en muchas situaciones, no hay suficientes metadatos en el código nativo de origen original para deducir cómo generar más de un enlace. Debe hacer referencia a documentación o los comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Una vez que haya comprobado que el enlace es corregir o resueltos para que sea correcta, _quitar_ el `[Verify]` atributo desde el enlace.

> [!IMPORTANT]
> `[Verify]` atributos intencionadamente provocar errores de compilación de C#, por lo que tiene que comprobar el enlace. Debe quitar la `[Verify]` cuando se ha revisado (y posiblemente se ha corregido) el código de atributo.

## <a name="verify-hints-reference"></a>Comprobar la referencia de sugerencias

El argumento de sugerencia proporcionado al atributo puede ser cruzado hace referencia con la documentación siguiente. Documentación para cualquier generado `[Verify]` después de que el enlace se ha completado, se proporcionará en la consola también atributos.

|Compruebe la sugerencia|Descripción|
|---|---|
|InferredFromPreceedingTypedef|El nombre de esta declaración se inferida por convención común de la inmediatamente precedente `typedef` en el código nativo de origen original. Compruebe que el nombre deducido es correcto como esta convención es ambigua.|
|ConstantsInterfaceAssociation|No hay ninguna manera de prueba de errores para determinar qué interfaz Objective-C puede asociarse una declaración de variable de extern. Instancias de estas se enlazan como `[Field]` propiedades de una interfaz parcial en una interfaz concreta cerca-by para generar una API más intuitiva, posiblemente lo que elimina las constantes de' ' interfaz por completo.|
|MethodToProperty|Un método Objective-C se enlazó como una propiedad de C# debido a una convención como que no toman parámetros y devuelve un valor (valor devuelto distinto de void). Deben estar enlazados a menudo métodos como los anteriores como propiedades para exponer una API más adecuada, pero a veces pueden producir falsos positivos y el enlace debe ser un método.|
|StronglyTypedNSArray|Nativo `NSArray*` se enlazó como `NSObject[]`. Es posible más fuertemente tipado la matriz en el enlace que se basan en las expectativas establecer a través de la documentación de la API (por ejemplo, comentarios en el archivo de encabezado) o examinando el contenido de la matriz a través de pruebas. Por ejemplo, un NSArray * que contiene solo NSNumber * limitarse instancescan `NSNumber[]` en lugar de `NSObject[]`.|

Rápidamente puede recibir documentación para una sugerencia mediante el `sharpie verify-docs` herramienta, por ejemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

