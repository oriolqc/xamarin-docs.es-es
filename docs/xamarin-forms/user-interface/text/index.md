---
title: Texto
description: Uso de Xamarin.Forms para escribir o mostrar texto.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 37289c4c118c3a84b8c81d3a1c1502ff80564bb9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="text"></a>Texto

_Uso de Xamarin.Forms para escribir o mostrar texto._

Xamarin.Forms tiene tres vistas principales para trabajar con texto:

- **[Etiqueta](#Label)**  &mdash; para presentar único o de varias líneas de texto. Puede mostrar texto con varias opciones de formato en la misma línea.
- **[Entrada](#Entry)**  &mdash; para escribir texto que es solo una línea. Entrada tiene un modo de contraseña.
- **[Editor de](#Editor)**  &mdash; para escribir texto que podría tardar más de una línea.

Se puede cambiar el aspecto del texto mediante integradas o personalizadas [estilos](#Styles) y algunos controles admiten personalizado [fuentes](#Fonts).

<a name="Label" />

## <a name="labellabelmd"></a>[Label](label.md)

El `Label` vista se utiliza para mostrar texto. Puede mostrar varias líneas de texto o una sola línea de texto. `Label` puede presentar el texto con varias opciones de formato utilizadas en línea. La vista de la etiqueta puede incluir o truncar texto cuando no caben en una sola línea.

![](images/label.png "Ejemplo de etiqueta")

Consulte la [etiqueta](label.md) artículo para obtener más información.

Para obtener información acerca de cómo personalizar la fuente utilizada en una etiqueta, vea [fuentes](fonts.md).

<a name="Entry" />

## <a name="entryentrymd"></a>[Entrada](entry.md)

`Entry` se usa para aceptar la entrada de texto de línea. `Entry` ofrece control sobre los colores, pero no se ha personalizado las fuentes. `Entry` tiene un modo de contraseña y puede mostrar texto de marcador de posición hasta que se escribe el texto.

![](images/entry.png "Ejemplo de entrada")

Consulte la [entrada](entry.md) artículo para obtener más información.

Tenga en cuenta que, a diferencia de `Label`, `Entry` no pueden tener valores de fuente personalizado.

<a name="Editor" />

## <a name="editoreditormd"></a>[Editor](editor.md)

`Editor` se usa para aceptar la entrada de texto de varias líneas. `Editor` puede tener un color de fondo personalizado, pero el color del texto y no se puede modificar la fuente.

![](images/editor.png "Ejemplo de editor")

Consulte la [Editor](editor.md) artículo para obtener más información.

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[Fuentes](fonts.md)

El `Label` control admite valores de fuente diferente utilizando las fuentes integradas en cada plataforma o las fuentes personalizadas que se incluye con la aplicación. Consulte la [fuentes](fonts.md) artículo para obtener más información.

<a name="Styles" />

## <a name="stylesstylesmd"></a>[Estilos](styles.md)

Hacer referencia a [trabajar con estilos](~/xamarin-forms/user-interface/styles/index.md) para obtener información sobre cómo configurar la fuente, [color](~/xamarin-forms/user-interface/colors.md)y otras propiedades de presentación que se aplican a través de varios controles.



## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
