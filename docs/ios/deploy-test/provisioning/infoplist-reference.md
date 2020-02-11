---
title: Riferimenti Info.plist per Xamarin.iOS
description: Questo documento descrive le varie coppie chiave/valore che possono essere configurate nel file Info.plist di un'applicazione Xamarin.iOS. Queste chiavi sono necessarie se l'app esegue attività specifiche, come l'accesso a percorsi, foto, microfono o fotocamera.
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: d94a647583539ac6af603a9074e1966c1c41d587
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028457"
---
# <a name="infoplist-reference-for-xamarinios"></a>Riferimenti Info.plist per Xamarin.iOS

Per altre informazioni sull'uso delle chiavi Info.plist, vedere la guida [Working with Security and Privacy](~/ios/app-fundamentals/security-privacy.md) (Sicurezza e privacy). 

## <a name="location"></a>Percorso 

Anche l'accesso al percorso dell'utente richiede modifiche a Info.plist. È necessario impostare le chiavi seguenti relative ai dati del percorso: 

- **NSLocationWhenInUseUsageDescription**: per l'accesso al percorso dell'utente quando interagisce con l'app. 
- **NSLocationAlwaysUsageDescription**: per l'accesso dell'app al percorso dell'utente in background.

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
