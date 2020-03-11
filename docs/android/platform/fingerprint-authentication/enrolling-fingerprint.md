---
title: Registrazione di un'impronta digitale
description: Come configurare un blocco dello schermo e registrare un'impronta digitale su un dispositivo o un emulatore Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027586"
---
# <a name="enrolling-a-fingerprint"></a>Registrazione di un'impronta digitale

## <a name="enrolling-a-fingerprint-overview"></a>Panoramica sulla registrazione di un'impronta digitale

È possibile che un'applicazione Android sfrutti l'autenticazione con impronta digitale solo se il dispositivo è già stato configurato con l'autenticazione con impronta digitale. Questa guida illustra come registrare un'impronta digitale in un dispositivo o un emulatore Android. Gli emulatori non dispongono dell'hardware effettivo per eseguire un'analisi delle impronte digitali, ma è possibile simulare un'analisi dell'impronta digitale con l'aiuto del Android Debug Bridge (descritto di seguito).  Questa guida illustra come abilitare il blocco dello schermo in un dispositivo Android e registrare un'impronta digitale per l'autenticazione.

## <a name="requirements"></a>Requisiti

Per registrare un'impronta digitale, è necessario avere un dispositivo Android o un emulatore che esegue il livello API 23 (Android 6,0).

L'uso del Android Debug Bridge (ADB) richiede una certa familiarità con il prompt dei comandi e il file eseguibile **ADB** deve trovarsi nel percorso dell'ambiente bash, PowerShell o del prompt dei comandi.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurazione di un blocco dello schermo e registrazione di un'impronta digitale 

Per configurare un blocco schermo, seguire questa procedura:

1. Passare a **impostazioni > sicurezza**e selezionare **blocco schermo**:

    ![Posizione della selezione del blocco schermo nella schermata sicurezza](enrolling-fingerprint-images/testing-01.png)

2. La schermata successiva visualizzata consentirà di selezionare e configurare uno dei metodi di sicurezza del blocco dello schermo: 

    ![Selezionare swipe, pattern, PIN o password](enrolling-fingerprint-images/testing-02.png)

   Selezionare e completare uno dei metodi di blocco dello schermo disponibili.

3. Una volta configurato il ScreenLock, tornare alla pagina **impostazioni > sicurezza** e selezionare **impronta digitale**:

    ![Posizione della selezione dell'impronta digitale nella schermata sicurezza](enrolling-fingerprint-images/testing-03.png)

4. Da qui, seguire la sequenza per aggiungere un'impronta digitale al dispositivo:

    [![sequenza di screenshot per l'aggiunta di un'impronta digitale al dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Nella schermata finale viene richiesto di inserire il dito nello scanner di impronta digitale: 

    ![Schermata che richiede di inserire il dito sul sensore](enrolling-fingerprint-images/testing-05.png)

    Se si usa un dispositivo Android, completare il processo toccando un dito per lo scanner. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulazione di un'analisi delle impronte digitali nell'emulatore

In un emulatore Android è possibile simulare un'analisi delle impronte digitali usando il Android Debug Bridge. In OS X avviare una sessione terminal mentre in Windows avviare un prompt dei comandi o una sessione di PowerShell ed eseguire `adb`:

```shell
$ adb -e emu finger touch 1
```

Il valore **1** è l'ID del dito _\__ per il dito che è stato "scansionato". Si tratta di un numero intero univoco assegnato per ogni impronta digitale virtuale. In futuro, quando l'app è in esecuzione, è possibile eseguire lo stesso comando ADB ogni volta che l'emulatore richiede un'impronta digitale, è possibile eseguire il comando `adb` e passare l' _ID dito\__ per simulare l'analisi delle impronte digitali.

Una volta completata l'analisi delle impronte digitali, Android invierà una notifica all'utente che l'impronta digitale è stata aggiunta:  

![Schermata che Visualizza l'impronta digitale aggiunta.](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Riepilogo 

Questa guida ha illustrato come configurare un blocco schermo e registrare un'impronta digitale in un dispositivo Android o in un emulatore Android. 
