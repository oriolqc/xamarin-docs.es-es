---
title: Referencia de Info.plist
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/18/2017
ms.openlocfilehash: 8ad2c5e8137dede71d9858aa144e440d984c1a75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="infoplist-reference"></a>Referencia de Info.plist

Para obtener más información sobre cómo trabajar con claves de Info.Plist, lea la guía [Trabajar con seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md). 

## <a name="location"></a>Ubicación 

Para obtener acceso a la ubicación del usuario, también se deben efectuar modificaciones en Info.plist. Se deben establecer las siguientes claves relacionadas con los datos de ubicación: 

* **NSLocationWhenInUseUsageDescription**: Al obtener acceso a la ubicación del usuario mientras interactúa con la aplicación. 
* **NSLocationAlwaysUsageDescription**: Al obtener acceso la aplicación a la ubicación del usuario en segundo plano.

## <a name="photos"></a>Fotos 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>Contactos 

NSContactsUsageDescription 

## <a name="calendar-data"></a>Datos de calendario 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>Datos de aviso 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>Periféricos Bluetooth 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>Micrófono 

NSMicrophoneUsageDescription 

## <a name="camera"></a>Cámara 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>Leer HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>Modos en segundo plano 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 


## <a name="related-links"></a>Vínculos relacionados

- [Guía de referencia de Apple.](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10)
