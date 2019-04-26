---
title: Objetivo Sharpie comprobar los atributos
description: Este documento describe el atributo [Verify] generado por objetivo Sharpie. El atributo [Verify] se resalta para desarrolladores donde deben comprobar manualmente los resultados del objetivo Sharpie.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 4bca896afb4dfc96fd6c1d7cdf489feb6a879e31
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261233"
---
# <a name="objective-sharpie-verify-attributes"></a>Objetivo Sharpie comprobar los atributos

A menudo encontrará que se anotará con enlaces producidos por objetivo Sharpie el `[Verify]` atributo. Estos atributos indican que debe _comprobar_ que objetivo Sharpie ha hecho lo correcto comparando el enlace con la declaración de C, Objective-C original (que se proporcionará en un comentario sobre la declaración de enlace).

Comprobación se recomienda para _todas_ enlazado declaraciones, pero es más probable _requiere_ para declaraciones anotan con el `[Verify]` atributo. Esto es porque en muchas situaciones, no hay suficientes metadatos en el código nativo de origen original para deducir cómo generar mejor un enlace. Es posible que deba hacer referencia a documentación o los comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Una vez haya comprobado que el enlace es corregir o resueltos para que sea correcto, _quitar_ el `[Verify]` atributo desde el enlace.

> [!IMPORTANT]
> `[Verify]` atributos de forma intencionada hacen que C# errores de compilación para que se debe comprobar el enlace. Debe quitar el `[Verify]` cuando ha revisado (y posiblemente corregido) el código de atributo.

## <a name="verify-hints-reference"></a>Comprobar la referencia de sugerencias

El argumento de sugerencia proporcionado para el atributo puede ser cruzado hace referencia con la documentación siguiente. Documentación para cualquier producidos `[Verify]` atributos se proporcionará en la consola también una vez completado el enlace.

|`[Verify]` Sugerencia|Descripción|
|---|---|
|InferredFromPreceedingTypedef|El nombre de esta declaración se dedujo mediante convención común desde el que precede inmediatamente `typedef` en el código nativo de origen original. Compruebe que el nombre deducido es correcto, como esta convención es ambigua.|
|ConstantsInterfaceAssociation|No hay ninguna manera de prueba de errores para determinar qué interfaz Objective-C puede asociarse una declaración de variable extern. Las instancias de estas se enlazan como `[Field]` las propiedades de una interfaz parcial en una interfaz concreta cerca de la forma para producir una API más intuitiva, posiblemente lo que elimina las constantes de' ' interfaz por completo.|
|MethodToProperty|Un método de Objective-C estaba enlazado como un C# propiedad debido a una convención como no tomar ningún parámetro y devuelve un valor (distinto de void devolución). A menudo métodos como estas deben estar enlazados como propiedades para exponer una API más bonito, pero a veces pueden producirse falsos positivos y el enlace debe ser realmente un método.|
|StronglyTypedNSArray|Nativo `NSArray*` han vinculado como `NSObject[]`. Es posible más rigurosamente la matriz en el enlace de acuerdo con las expectativas que se establecen a través de la documentación de API (por ejemplo, los comentarios en el archivo de encabezado) o al examinar el contenido de la matriz a través de pruebas. Por ejemplo, un nsarray frente * que contiene solo NSNumber * instancescan enlazarse como `NSNumber[]` en lugar de `NSObject[]`.|

Rápidamente puede recibir documentación para el uso de una sugerencia el `sharpie verify-docs` herramienta, por ejemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

## <a name="related-links"></a>Vínculos relacionados

- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Generar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
