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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292513"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quali pacchetti di Android SDK è necessario installare?

L'installazione di Android SDK non include automaticamente tutti i pacchetti minimi necessari per lo sviluppo. Mentre le esigenze dei singoli sviluppatori variano, i seguenti pacchetti saranno generalmente necessari per lo sviluppo con Xamarin.Android:

## <a name="tools"></a>Strumenti

Installare gli strumenti più recenti dalla cartella Strumenti in Gestione SDK:

- Strumenti SDK di Android
- Strumenti di piattaforma Android SDK
- Strumenti di compilazione di Android SDK

## <a name="android-platforms"></a>Piattaforme Android

Installa la "Piattaforma SDK" per le versioni di Android che hai impostato come minimo & destinazione.

Esempi:

- API di destinazione 23Target API 23
- API minima 23

Devi solo installare la piattaforma SDK per l'API 23

- API di destinazione 23Target API 23
- API minima 15

È necessario installare piattaforme SDK per le API 15 e 23. Si noti che non è necessario installare i livelli API tra il minimo e la destinazione (anche se si esegue il backporting a tali livelli API).

## <a name="system-images"></a>Immagini di sistema

Questi sono necessari solo se si desidera utilizzare gli emulatori Android out-of-the-box da Google. Per altre informazioni, vedere [Configurazione dell'emulatore AndroidFor](~/android/get-started/installation/android-emulator/index.md) more information, see Android Emulator Setup

## <a name="extras"></a>Dati aggiuntivi
Gli Android SDK Extra non sono in genere necessari; ma è utile essere a conoscenza di loro in quanto possono essere necessari a seconda del vostro caso d'uso.
