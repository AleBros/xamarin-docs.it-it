---
title: Registrazione di un'impronta digitale
description: Come configurare un blocco schermo e registrare un'impronta digitale su un dispositivo Android o un emulatore.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027586"
---
# <a name="enrolling-a-fingerprint"></a>Registrazione di un'impronta digitale

## <a name="enrolling-a-fingerprint-overview"></a>Panoramica della registrazione di un'impronta digitale

È possibile che un'applicazione Android sfrutti l'autenticazione delle impronte digitali solo se il dispositivo è già stato configurato con l'autenticazione delle impronte digitali. Questa guida verrà illustrato come registrare un'impronta digitale su un dispositivo Android o un emulatore. Gli emulatori non hanno l'hardware effettivo per eseguire una scansione delle impronte digitali, ma è possibile simulare una scansione delle impronte digitali con l'aiuto di Android Debug Bridge (descritto di seguito).  Questa guida verrà illustrato come abilitare il blocco schermo su un dispositivo Android e registrare un'impronta digitale per l'autenticazione.

## <a name="requirements"></a>Requisiti

Per registrare un'impronta digitale, è necessario disporre di un dispositivo Android o di un emulatore che esegue il livello API 23 (Android 6.0).

L'uso di Android Debug Bridge (ADB) richiede familiarità con il prompt dei comandi e l'eseguibile **adb** deve essere nel percorso dell'ambiente Bash, PowerShell o Prompt dei comandi.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurazione di un blocco schermo e registrazione di un'impronta digitale 

Per impostare un blocco schermo, attenersi alla seguente procedura:

1. Vai a **Impostazioni > Sicurezza**e seleziona Blocco **schermo**:

    ![Posizione della selezione del blocco schermo nella schermata Sicurezza](enrolling-fingerprint-images/testing-01.png)

2. La schermata successiva che viene visualizzata vi permetterà di selezionare e configurare uno dei metodi di sicurezza del blocco schermo: 

    ![Selezionare Scorrimento rapido, Motivo, PIN o Password](enrolling-fingerprint-images/testing-02.png)

   Selezionare e completare uno dei metodi di blocco schermo disponibili.

3. Una volta configurato lo screenlock, tornare alla pagina **Impostazioni > Sicurezza** e selezionare Impronta **digitale**:

    ![Posizione della selezione delle impronte digitali nella schermata Sicurezza](enrolling-fingerprint-images/testing-03.png)

4. Da lì, seguire la sequenza per aggiungere un'impronta digitale al dispositivo:

    [![Sequenza di screenshot per l'aggiunta di un'impronta digitale al dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Nella schermata finale viene richiesto di posizionare il dito sullo scanner di impronte digitali: 

    ![Schermo che richiede di mettere il dito sul sensore](enrolling-fingerprint-images/testing-05.png)

    Se si utilizza un dispositivo Android, completare il processo toccando un dito sullo scanner. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulazione di una scansione delle impronte digitali sull'emulatore

In un emulatore Android, è possibile simulare una scansione delle impronte digitali utilizzando il bridge di debug Android.On an Android emulator, it is possible to simulate a fingerprint scan by using the Android Debug Bridge. Su OS X avviare una sessione Terminale mentre su Windows `adb`avviare un prompt dei comandi o una sessione di PowerShell ed eseguire:

```shell
$ adb -e emu finger touch 1
```

Il valore **1** è l'ID _del dito\__ che è stato "scansionato". Si tratta di un numero intero univoco assegnato per ogni impronta digitale virtuale. In futuro, quando l'app è in esecuzione è possibile eseguire questo stesso comando ADB `adb` ogni volta che l'emulatore richiede un'impronta digitale, è possibile eseguire il comando e passarlo l'ID _del dito\__ per simulare la scansione delle impronte digitali.

Al termine della scansione delle impronte digitali, Android ti avviserà che l'impronta digitale è stata aggiunta:  

![Schermo che visualizza impronta digitale aggiunto!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Riepilogo 

Questa guida illustra come configurare un blocco schermo e registrare un'impronta digitale in un dispositivo Android o in un emulatore Android.This guide covered how to setup a screen lock and enroll a fingerprint on an Android device or in an Android emulator. 
