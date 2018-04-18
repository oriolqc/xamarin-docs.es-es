---
title: Controlador de vista previa XAML para Xamarin.Forms
description: Vea los diseños de Xamarin.Forms representados a medida que escribe.
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 02/24/2017
ms.openlocfilehash: d23f89ed8ad7956f7a366280a14ccc12ba3dac0c
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="xaml-previewer-for-xamarinforms"></a>Controlador de vista previa XAML para Xamarin.Forms

_Vea los diseños de Xamarin.Forms representados a medida que escribe._

## <a name="requirements"></a>Requisitos

Proyectos requieren que el paquete de Xamarin.Forms NuGet más reciente para el controlador de vista previa de XAML para que funcione. Obtener una vista previa de aplicaciones Android requiere [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Hay más información en el [notas de la versión](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Introducción

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Use la **Vista > otras ventanas > controlador de vista previa de Xamarin.Forms** menú en Visual Studio para abrir la ventana de vista previa. Use la **Ventana > nuevo grupo de tabulación Vertical** menú para colocarla en paralelo.

[![Vista previa del control ListView en Visual Studio](xaml-previewer-images/xamlp-list-vs-sml.png "controlador de vista previa de formularios en Visual Studio")](xaml-previewer-images/xamlp-list-vs.png#lightbox "controlador de vista previa de formularios en Visual Studio")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El **vista previa** botón se puede mostrar en el editor haciendo clic en un archivo XAML y seleccionar **abrir con > controlador de vista previa de formularios**. El panel de vista previa, a continuación, pueden mostrar u ocultar presionando el **vista previa** situado en la esquina superior derecha de las ventanas de documento XAML:

[![Vista previa del control ListView en Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "controlador de vista previa de formularios en Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox "controlador de vista previa de formularios en Visual Studio para Mac")

-----

## <a name="xaml-preview-options"></a>Opciones de vista previa XAML

Las opciones en la parte superior del panel de vista previa son:

* **Teléfono** – se representan en una pantalla de tamaño de teléfono
* **Tablet** : representar en una pantalla de tamaño de Tablet PC (tenga en cuenta, hay controles de zoom en la parte inferior derecha del panel)
* **Android** : mostrar la versión Android de la pantalla
* **iOS** : mostrar la versión de iOS de la pantalla
* Portait (icono): se usa en orientación vertical de la vista previa
* Horizontal (icono): usa horizontal de la vista previa

## <a name="adding-design-time-data"></a>Agregar datos de tiempo de diseño

Algunos diseños pueden ser difíciles de visualizar sin datos enlazados a los controles de interfaz de usuario. Para que la vista previa más útil, asigne algunos datos estáticos a los controles por codificar un contexto de enlace (ya sea en el código subyacente o con XAML).

Consulte de James Montemagno [entrada de blog sobre cómo agregar datos en tiempo de diseño](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver cómo se enlaza a un modelo de vista estática en XAML.

## <a name="troubleshooting"></a>Solución de problemas

Compruebe los problemas siguientes y el [Xamarin foros](https://forums.xamarin.com/categories/xamarin-forms), si tiene problemas.

### <a name="xaml-preview-isnt-showing"></a>Vista previa de XAML no aparece

Compruebe lo siguiente:

* Proyecto debe compilarse (compilado) antes de intentar obtener una vista previa de los archivos XAML.
* El agente de diseñador debe ser instalación la primera vez que obtenga una vista previa de un archivo XAML, aparecerá un indicador de progreso en el controlador de vista previa, junto con los mensajes de progreso, hasta que esto está listo.
* Intente cerrar y volver a abrir el archivo XAML.

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>XAML no válido: El proyecto de Android debe generarse antes de que se puede crear la vista previa

El controlador de vista previa de XAML requiere que se compila el proyecto antes de procesar una página.
Si aparece el siguiente error en la parte superior del panel de vista previa, volver a compilar la aplicación e inténtelo de nuevo.

![Mensaje de error: primero se debe compilar proyecto](xaml-previewer-images/error-not-built-sml.png "mensaje de Error: volver a generar el proyecto")
