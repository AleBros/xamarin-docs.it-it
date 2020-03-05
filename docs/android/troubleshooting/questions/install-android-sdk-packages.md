---
title: Quali pacchetti di Android SDK è necessario installare?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: 24c70c2e869f59091a1519af6d1165dbea9cc467
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292513"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quali pacchetti di Android SDK è necessario installare?

L'installazione del Android SDK non include automaticamente tutti i pacchetti minimi necessari per lo sviluppo. Sebbene i singoli sviluppatori debbano variare, i pacchetti seguenti saranno in genere necessari per lo sviluppo con Novell. Android:

## <a name="tools"></a>Strumenti

Installare gli strumenti più recenti dalla cartella Tools in SDK Manager:

- Android SDK Tools
- Strumenti della piattaforma Android SDK
- Strumenti di compilazione Android SDK

## <a name="android-platforms"></a>Piattaforme Android

Installare la "piattaforma SDK" per le versioni di Android impostate come minimo & destinazione.

Esempi:

- API di destinazione 23
- API minima 23

È sufficiente installare la piattaforma SDK per l'API 23

- API di destinazione 23
- API 15 minima

È necessario installare le piattaforme SDK per le API 15 e 23. Si noti che non è necessario installare i livelli API tra il minimo e la destinazione (anche se si esegue il backporting a tali livelli API).

## <a name="system-images"></a>Immagini di sistema

Questi sono necessari solo se si vuole usare gli emulatori Android predefiniti da Google. Per ulteriori informazioni, vedere la pagina relativa all' [installazione di emulatore Android](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extra
Le Android SDK aggiuntive non sono in genere necessarie; è tuttavia utile conoscerli poiché potrebbero essere necessari a seconda del caso d'uso.
