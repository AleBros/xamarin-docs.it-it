---
title: Quali pacchetti di Android SDK è necessario installare?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 04a07d5b7f37222515136e5592f31a4583b02fe3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157364"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quali pacchetti di Android SDK è necessario installare?

Installazione di Android SDK non include automaticamente tutti i pacchetti minimi richiesti per lo sviluppo. Mentre devono variare per singoli sviluppatori, i pacchetti seguenti a livello generale sono necessari per lo sviluppo con xamarin. Android:

## <a name="tools"></a>Strumenti

Installare gli strumenti più recenti dalla cartella strumenti in SDK manager:

- Android SDK Tools
- Android SDK Platform Tools
- Android SDK Build Tools

## <a name="android-platforms"></a>Piattaforme Android in uso

Installare "Piattaforma SDK" per le versioni di Android che è stato impostato come minimo des & tinazione. 

Esempi:

- API 23 di destinazione
- API minimo 23

Necessario solo installare Platform SDK per API 23

- API 23 di destinazione
- API minimo 15

È necessario installare le piattaforme SDK per API 15 e 23. Si noti che è necessario installare i livelli API tra i valori minimo e di destinazione (anche se si sta utilizzando backporting di questi livelli di API).

## <a name="system-images"></a>Immagini del sistema

Questi sono solo necessari se si desidera usare gli emulatori Android out-of-the-box da Google. Per altre informazioni, vedere [Setup emulatore Android](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
il SDK di funzionalità aggiuntive di Android non sono in genere necessarie; ma è utile essere a conoscenza della loro dal momento che possono essere necessari in base al caso d'uso.

## <a name="further-reading"></a>Ulteriori informazioni
La Guida seguente illustra queste opzioni e diventa più dettagliate sui diversi pacchetti SDK manager è disponibile: [Guida all'installazione di Android SDK Manager](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

