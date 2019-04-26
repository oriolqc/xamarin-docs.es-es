---
title: Modificadores de campo XAML en Xamarin.Forms
description: 'El atributo de espacio de nombres x: FieldModifier especifica el nivel de acceso para los campos generados para los elementos XAML con nombre.'
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075303"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificadores de campo XAML en Xamarin.Forms

_El `x:FieldModifier` namespace (atributo) especifica el nivel de acceso para los campos generados para los elementos XAML con nombre._

## <a name="overview"></a>Información general

Los valores válidos del atributo son:

- `Public` : Especifica que el campo generado para el elemento XAML es `public`.
- `NotPublic` : Especifica que el campo generado para el elemento XAML es `internal` al ensamblado.

Si no se establece el valor del atributo, el campo generado para el elemento será `private`.

Las siguientes condiciones deben cumplirse para una `x:FieldModifier` atributo procesarse:

- El elemento XAML debe ser válido `x:Class`.
- El elemento actual de XAML tiene un `x:Name` especificado.

El XAML siguiente muestra ejemplos de establecer el atributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> El `x:FieldModifier` atributo no puede utilizarse para especificar el nivel de acceso de una clase XAML.
