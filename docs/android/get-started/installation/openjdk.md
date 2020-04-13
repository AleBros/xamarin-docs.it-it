---
title: Distribuzione di Microsoft Mobile OpenJDK
description: Guida dettagliata alla configurazione e alla risoluzione dei problemi della distribuzione di Microsoft OpenJDK per lo sviluppo per dispositivi mobili.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: a24edbc10d529878092b474df7f186d14049d5e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "60955082"
---
# <a name="microsofts-mobile-openjdk-distribution"></a>Distribuzione di Microsoft Mobile OpenJDK

_In questa guida vengono descritti i passaggi per passare a una distribuzione interna di OpenJDK. Questa distribuzione è destinata allo sviluppo mobile._

## <a name="overview"></a>Panoramica

A partire da Visual Studio 15.9 e Visual Studio per Mac 7.7, Strumenti di Visual Studio per Xamarin è passato da Oracle JDK a una **versione leggera di OpenJDK destinata unicamente allo sviluppo per Android**. Si tratta di una migrazione obbligatoria poiché Oracle terminerà il supporto per la distribuzione commerciale di JDK 8 nel 2019 e JDK 8 è una dipendenza richiesta per lo sviluppo per Android.

I vantaggi di questo passaggio sono:

- Sarà sempre disponibile una versione di OpenJDK che funziona per lo sviluppo Android.

- Il download di Oracle JDK 9 o versioni successive non influisce sull'esperienza di sviluppo.

- Riduzione delle dimensioni e del footprint del download.

- Nessun problema con i server e i programmi di installazione di terze parti.

Se si vuole passare prima all'esperienza migliorata, sono disponibili build della distribuzione di Microsoft Mobile OpenJDK per il testing, sia per Windows che per Mac. Il processo di installazione è descritto di seguito ed è possibile tornare a Oracle JDK in qualsiasi momento.

## <a name="download"></a>Download

La distribuzione di OpenJDK per dispositivi mobili è installata automaticamente se si selezionano i pacchetti di Android SDK nel programma di installazione di Visual Studio in Windows.

In Mac OpenJDK per dispositivi mobili verrà installato automaticamente come parte del carico di lavoro Android per le nuove installazioni. Agli utenti esistenti di Visual Studio per Mac verrà richiesta l'installazione come parte dell'aggiornamento. L'IDE richiederà di passare al nuovo JDK che verrà usato al successivo riavvio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi di installazione in Mac o Windows, è possibile eseguire la procedura seguente per l'installazione manuale:

Controllare se OpenJDK è installato nel computer nel percorso corretto:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.x**
- **Windows** &ndash; **C:\\Programmi\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.x**

Puntare l'IDE al nuovo JDK:

- **Mac** &ndash; Fare clic su **Strumenti > SDK Manager > Percorsi** e impostare **Percorso di Java SDK (JDK)** sul percorso completo dell'installazione di OpenJDK. Nell'esempio seguente questo percorso è impostato su **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**, ma la versione in uso potrebbe essere più recente.

![Impostazione del percorso di JDK per la distribuzione di Microsoft Mobile OpenJDK in Mac](openjdk-images/vsm.png)

- **Windows** &ndash; fare clic su **Strumenti > Opzioni > Xamarin > Impostazioni Android** e impostare **Percorso di Java Development Kit** sul percorso completo dell'installazione di OpenJDK. Nell'esempio seguente questo percorso è impostato su **C:\\Programmi\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**, ma la versione in uso potrebbe essere più recente:

![Impostazione del percorso di JDK per la distribuzione di Microsoft Mobile OpenJDK in Windows](openjdk-images/vs.png)

## <a name="known-issues"></a>Problemi noti

### <a name="package-openjdkv1regkeyversion18025chipx64-failed-to-install"></a>Non è stato possibile installare il pacchetto 'OpenJDKV1.RegKey,version=1.8.0.25,chip=x64'

Questo problema potrebbe verificarsi in alcuni ambienti aziendali. OpenJDK è già presente nel computer. Seguire la [procedura di risoluzione dei problemi precedente](#troubleshooting) per indirizzare l'IDE al percorso corretto. È possibile seguire lo stato dei problemi [qui](https://developercommunity.visualstudio.com/content/problem/382549/packageidopenjdkv1regkeypackageactioninstallreturn.html).

## <a name="summary"></a>Riepilogo

In questo articolo è stato descritto come configurare l'IDE per l'uso della distribuzione di Microsoft Mobile OpenJDK e come risolvere eventuali problemi.
