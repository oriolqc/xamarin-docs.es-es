---
title: Compruebe los atributos
ms.topic: article
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: cda523cd9d762c3a3c1570e2abd0acb8a264d5dd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="verify-attributes"></a>Compruebe los atributos


A menudo, encontrará que enlaces producidos por objetivo Sharpie se anotará con el `[Verify]` atributo. Estos atributos indican que debe _comprobar_ que objetivo Sharpie ha hecho lo correcto comparando el enlace con la declaración C/Objective-C original (que se proporciona en un comentario sobre la declaración de enlace).

Comprobación se recomienda para _todos los_ enlazado declaraciones, pero es más probable _requiere_ para declaraciones anotan con el `[Verify]` atributo. Esto es porque en muchas situaciones, no hay suficientes metadatos en el código nativo de origen original para deducir cómo generar más de un enlace. Debe hacer referencia a documentación o los comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Una vez que haya comprobado que el enlace es corregir o resueltos para que sea correcta, _quitar_ el `[Verify]` atributo desde el enlace.

> [!IMPORTANT]
> `[Verify]` atributos intencionadamente provocar errores de compilación de C#, por lo que tiene que comprobar el enlace. Debe quitar la `[Verify]` cuando se ha revisado (y posiblemente se ha corregido) el código de atributo.

## <a name="verify-hints-reference"></a>Comprobar la referencia de sugerencias

El argumento de sugerencia proporcionado al atributo puede ser cruzado hace referencia con la documentación siguiente. Documentación para cualquier generado `[Verify]` después de que el enlace se ha completado, se proporcionará en la consola también atributos.

<table>
  <thead>
  <tr>
    <th>Compruebe la sugerencia</th>
    <th>Descripción</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td>InferredFromPreceedingTypedef</td>
    <td>El nombre de esta declaración se inferida por convención común de la inmediatamente precedente <code>typedef</code> en el código nativo de origen original. Compruebe que el nombre deducido es correcto como esta convención es ambigua.</td>
  </tr>
  <tr>
    <td>ConstantsInterfaceAssociation</td>
    <td>No hay ninguna manera de prueba de errores para determinar qué interfaz Objective-C puede asociarse una declaración de variable de extern. Instancias de estas se enlazan como <code>[Field]</code> propiedades de una interfaz parcial en una interfaz concreta cerca-by para generar una API más intuitiva, posiblemente lo que elimina las constantes de' ' interfaz por completo.</td>
  </tr>
  <tr>
    <td>MethodToProperty</td>
    <td>Un método Objective-C se enlazó como una propiedad de C# debido a una convención como que no toman parámetros y devuelve un valor (valor devuelto distinto de void). Deben estar enlazados a menudo métodos como los anteriores como propiedades para exponer una API más adecuada, pero a veces pueden producir falsos positivos y el enlace debe ser un método.</td>
  </tr>
  <tr>
    <td>StronglyTypedNSArray</td>
    <td>Nativo <code>NSArray*</code> se enlazó como <code>NSObject[]</code>. Es posible más fuertemente tipado la matriz en el enlace que se basan en las expectativas establecer a través de la documentación de la API (por ejemplo, comentarios en el archivo de encabezado) o examinando el contenido de la matriz a través de pruebas. Por ejemplo, un NSArray * que contiene solo NSNumber * limitarse instancescan <code>NSNumber[]</code> en lugar de <code>NSObject[]</code>.</td>
  </tr>
  </tbody>
</table>

Rápidamente puede recibir documentación para una sugerencia mediante el `sharpie verify-docs` herramienta, por ejemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

