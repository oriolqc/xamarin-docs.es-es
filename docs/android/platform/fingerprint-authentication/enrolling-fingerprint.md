---
title: Inscribir una huella digital
description: Cómo establecer una copia de seguridad un bloqueo de pantalla e inscribir una huella digital en un emulador o dispositivo Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 4faee5decb102d17d9a270b96cef4a12fc9dbef4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762339"
---
# <a name="enrolling-a-fingerprint"></a>Inscribir una huella digital

## <a name="enrolling-a-fingerprint-overview"></a>Inscribir una introducción de huellas digitales

Solo es posible que una aplicación Android aprovechar la autenticación de huellas dactilares si el dispositivo ya se ha configurado con la autenticación de huellas digitales. Esta guía describe cómo inscribir una huella digital en un emulador o dispositivo Android. Los emuladores no tiene el hardware real para realizar un examen de huellas digitales, pero es posible simular un examen de huellas digitales con la Ayuda de Android Debug Bridge (descritos a continuación).  Esta guía describe cómo habilitar el bloqueo de pantalla en un dispositivo Android e inscribir una huella digital para la autenticación.

## <a name="requirements"></a>Requisitos

Para inscribir una huella digital, debe tener un dispositivo Android o un emulador en ejecución de nivel de API 23 (Android 6.0).

El uso de la Android depuración Bridge (ADB) es necesario estar familiarizado con el símbolo del sistema y el **adb** ejecutable debe estar en la ruta de acceso de la fiesta, PowerShell, o comando entorno de símbolo del sistema.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configuración de un bloqueo de pantalla e inscripción de huella digital 

Para configurar un bloqueo de pantalla, haga lo siguiente:

1. Vaya a **configuración > seguridad**y seleccione **bloqueo de pantalla**:

    ![Ubicación de selección de bloqueo de pantalla en la pantalla de seguridad](enrolling-fingerprint-images/testing-01.png)

2. La siguiente pantalla que aparece le permitirá seleccionar y configurar uno de los métodos de seguridad de bloqueo de pantalla: 

    ![Seleccione deslice el dedo, el patrón, el PIN o contraseña](enrolling-fingerprint-images/testing-02.png)

   Seleccione y complete uno de los métodos de bloqueo de pantalla disponibles.

3. Una vez configurado el screenlock, volver a la **configuración > seguridad** página y seleccione **huella digital**:

    ![Ubicación de la selección de huella digital en la pantalla de seguridad](enrolling-fingerprint-images/testing-03.png)

4. Desde allí, siga la secuencia para agregar una huella digital para el dispositivo:

    [![Secuencia de capturas de pantalla para agregar una huella digital en el dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. En la pantalla final le pedirá que coloque el dedo en el escáner de huella digital: 

    ![Pantalla que le pregunta si desea colocar el dedo en el sensor](enrolling-fingerprint-images/testing-05.png)

    Si está utilizando un dispositivo Android, si se toca un dedo en el escáner para completar el proceso. 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simular un examen de huella digital en el emulador

En un emulador de Android, es posible simular un examen de la huella digital mediante el uso de Android Debug Bridge. En Inicio de OS X una sesión de Terminal mientras que en Windows, inicie un símbolo del sistema o una sesión de Powershell y ejecute `adb`:

```shell
$ adb -e emu finger touch 1
```

El valor de **1** es el _dedo\_identificador_ para el dedo "analizado". Es un entero único que asigne a cada huella virtual. En el futuro, cuando ejecuta la aplicación se puede ejecutar este mismo ADB comando cada vez que el emulador le pide que para una huella digital, puede ejecutar el `adb` comando y pasarle el _dedo\_Id. de_ para simular el examen de huellas digitales .

Una vez finalizado el examen de huellas digitales, Android le notificará que se ha agregado la huella digital:  

![¡Pantalla mostrando la huella digital agrega!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumen 

Esta guía trata cómo instalar un bloqueo de pantalla e inscribir una huella digital en un dispositivo Android o en un emulador de Android. 

