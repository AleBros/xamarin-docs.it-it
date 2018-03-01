---
title: "I pacchetti di Android SDK è necessario installare?"
ms.topic: article
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/02/2018
ms.openlocfilehash: e2b0736a9ccc4dde5c1dcdf2d99f527247040560
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="which-android-sdk-packages-should-i-install"></a>I pacchetti di Android SDK è necessario installare?

L'installazione di Android SDK non include automaticamente tutti i pacchetti minimi richiesti per lo sviluppo. Anche se è necessario variare singolo sviluppatore, i pacchetti seguenti in genere sono necessari per lo sviluppo con xamarin:

## <a name="tools"></a>Strumenti

Installare gli strumenti più recenti dalla cartella Strumenti di gestione SDK:

- Strumenti Android SDK
- Strumenti di Android SDK Platform
- Strumenti Android SDK Build

## <a name="android-platforms"></a>Piattaforme Android

Installare la "piattaforma SDK" per le versioni Android impostati come minimo e di destinazione. 

Esempi:

- API 23 di destinazione
- Minimo API 23

Necessario solo installare Platform SDK per API 23

- API 23 di destinazione
- API minimo 15

È necessario installare SDK piattaforme per API 15 e 23. Si noti che non è necessario installare i livelli di API tra i valori minimo e di destinazione (anche se si backporting per tali livelli API).

## <a name="system-images"></a>Immagini del sistema
Si tratta solo necessario se si desidera usare gli emulatori Android di casella da Google. 

- [Come configurare l'emulatore predefinito](~/android/get-started/installation/android-emulator/index.md)

- [Come accelerare l'emulatore predefinito](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
il SDK di funzionalità aggiuntive di Android non sono in genere necessarie; ma è utile tenere li dal momento che possono essere necessari a seconda del caso d'uso.

## <a name="further-reading"></a>Ulteriori informazioni
La seguente guida illustra queste opzioni e fornisce informazioni più dettagliate sui diversi pacchetti SDK manager è disponibile: [Guida all'installazione di Android SDK Manager](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

