---
title: Inscripción de una huella digital
description: Cómo establecer un bloqueo de pantalla de seguridad e inscribir una huella digital en un emulador o dispositivo Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 18903a7d8f6c4033dc3ac7c4c0187a247d023bc1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027093"
---
# <a name="enrolling-a-fingerprint"></a>Inscripción de una huella digital

## <a name="enrolling-a-fingerprint-overview"></a>Una huella digital de información general sobre la inscripción

Solo es posible que una aplicación de Android aprovechar la autenticación con huella digital si el dispositivo ya se ha configurado con autenticación con huella digital. Esta guía describirá cómo inscribir una huella digital en un emulador o dispositivo Android. Los emuladores no tiene el hardware real para realizar un examen de huellas digitales pero es posible simular un examen de huellas digitales con la Ayuda de Android Debug Bridge (descrito a continuación).  Esta guía describirá cómo habilitar el bloqueo de pantalla en un dispositivo Android e inscriba una huella digital para la autenticación.

## <a name="requirements"></a>Requisitos

Para inscribir una huella digital, debe tener un dispositivo Android o un emulador en funcionamiento el nivel de API 23 (Android 6.0).

El uso de la Android Debug Bridge (ADB) es necesario estar familiarizado con el símbolo del sistema y el **adb** ejecutable debe estar en la ruta de acceso de Bash, PowerShell, o entorno de símbolo del sistema de comandos.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configuración de un bloqueo de pantalla e inscripción de huella digital 

Para configurar un bloqueo de pantalla, realice los pasos siguientes:

1. Vaya a **configuración > seguridad**y seleccione **bloqueo de pantalla**:

    ![Ubicación de selección de bloqueo de pantalla en la pantalla de seguridad](enrolling-fingerprint-images/testing-01.png)

2. La siguiente pantalla que aparece le permitirá seleccionar y configurar uno de los métodos de seguridad de bloqueo de pantalla: 

    ![Seleccione pasar el dedo, el patrón, PIN o contraseña](enrolling-fingerprint-images/testing-02.png)

   Seleccione y complete uno de los métodos de bloqueo de pantalla disponibles.

3. Una vez configurada la screenlock, volver a la **configuración > seguridad** página y seleccione **huellas digitales**:

    ![Ubicación de la selección de huella digital en la pantalla de seguridad](enrolling-fingerprint-images/testing-03.png)

4. Desde allí, siga la secuencia para agregar una huella digital para el dispositivo:

    [![Secuencia de capturas de pantalla para agregar una huella digital en el dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. En la pantalla final se le pedirá que colocar su dedo sobre el escáner de huella digital: 

    ![Pantalla que le pide que pone el dedo en el sensor](enrolling-fingerprint-images/testing-05.png)

    Si se usa un dispositivo Android, complete el proceso si toca un dedo al escáner. 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulación de un examen de huellas digitales en el emulador

En un emulador de Android, es posible simular un examen de huellas digitales con Android Debug Bridge. En el inicio de OS X una sesión de Terminal mientras se encuentra en Windows, inicie un símbolo del sistema o una sesión de Powershell y ejecute `adb`:

```shell
$ adb -e emu finger touch 1
```

El valor de **1** es el _dedo\_id_ para el dedo que se "examinan". Es un entero único que asigne a cada huella virtual. En el futuro cuando ejecuta la aplicación se puede ejecutar este mismo ADB comando cada vez que el emulador le pide una huella digital, puede ejecutar el `adb` comando y pasarle el _dedo\_Id. de_ para simular el examen de huellas digitales .

Una vez completado el examen de huellas digitales, Android le notificará que se ha agregado la huella digital:  

![¡Pantalla que se muestra la huella digital agrega!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumen 

Esta guía trata sobre cómo configurar un bloqueo de pantalla e inscribir una huella digital en un dispositivo Android o en un emulador de Android. 

