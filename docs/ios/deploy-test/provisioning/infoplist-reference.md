---
title: Riferimenti Info.plist
ms.topic: article
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/18/2017
ms.openlocfilehash: 7732419af22ab8bd37cbfbf828dc59e48841217f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="infoplist-reference"></a>Riferimenti Info.plist

Per altre informazioni sull'uso delle chiavi Info.plist, vedere la guida [Working with Security and Privacy](~/ios/app-fundamentals/security-privacy.md) (Sicurezza e privacy). 

## <a name="location"></a>Percorso 

Anche l'accesso al percorso dell'utente richiede modifiche a Info.plist. È necessario impostare le chiavi seguenti relative ai dati del percorso: 

* **NSLocationWhenInUseUsageDescription**: per l'accesso al percorso dell'utente quando interagisce con l'app. 
* **NSLocationAlwaysUsageDescription**: per l'accesso dell'app al percorso dell'utente in background.

## <a name="photos"></a>Foto 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>Contatti 

NSContactsUsageDescription 

## <a name="calendar-data"></a>Giorni di calendario 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>Dati di promemoria 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>Periferiche Bluetooth 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>Microfono 

NSMicrophoneUsageDescription 

## <a name="camera"></a>Fotocamera 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>Lettura di HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>Modalità in background 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 


## <a name="related-links"></a>Collegamenti correlati

- [Apple Reference guide](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10) (Guida di riferimento Apple).
