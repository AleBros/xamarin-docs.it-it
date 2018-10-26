---
title: La registrazione di un'impronta digitale
description: Come impostare backup di un blocco dello schermo e si registra un'impronta digitale in un emulatore o dispositivo Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 18903a7d8f6c4033dc3ac7c4c0187a247d023bc1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115913"
---
# <a name="enrolling-a-fingerprint"></a>La registrazione di un'impronta digitale

## <a name="enrolling-a-fingerprint-overview"></a>La registrazione di una panoramica di impronta digitale

È possibile solo per un'applicazione Android di sfruttare l'autenticazione tramite impronta digitale se il dispositivo è già stato configurato con l'autenticazione tramite impronta digitale. Questa guida illustra come registrare un'impronta digitale in un emulatore o dispositivo Android. Emulatori non dispone di hardware effettivo per eseguire un'analisi di impronta digitale, ma è possibile simulare un'analisi di impronte digitali con l'aiuto di Android Debug Bridge (descritti di seguito).  Questa guida illustra come abilitare il blocco dello schermo in un dispositivo Android e si registra un'impronta digitale per l'autenticazione.

## <a name="requirements"></a>Requisiti

Per registrare un'impronta digitale, è necessario disporre di un dispositivo Android o un emulatore in esecuzione il livello API 23 (Android 6.0).

L'uso di Android Debug Bridge (ADB) è necessario avere dimestichezza con il prompt dei comandi e il **adb** eseguibile deve essere nel percorso di Bash, PowerShell, o ambiente di Prompt di comando.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurazione di un blocco dello schermo e la registrazione di un'impronta digitale 

Per configurare un blocco dello schermo, eseguire la procedura seguente:

1. Passare a **Impostazioni > sicurezza**e selezionare **blocco schermo**:

    ![Percorso di selezione blocco dello schermo nella schermata di sicurezza](enrolling-fingerprint-images/testing-01.png)

2. La schermata successiva visualizzata consentirà selezionare e configurare uno dei metodi di protezione di blocco dello schermo: 

    ![Selezionare scorrere rapidamente, modello, PIN o Password](enrolling-fingerprint-images/testing-02.png)

   Selezionare e completare uno dei metodi di blocco disponibile sullo schermo.

3. Dopo aver configurato il screenlock, tornare al **Impostazioni > sicurezza** pagina e selezionare **impronta digitale**:

    ![Posizione della selezione tramite impronta digitale nella schermata di sicurezza](enrolling-fingerprint-images/testing-03.png)

4. Da qui, seguire la sequenza per aggiungere un'impronta digitale per il dispositivo:

    [![Sequenza di schermate per l'aggiunta di un'impronta digitale per il dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Nella schermata finale viene chiesto di inserire il dito sullo scanner di impronta digitale: 

    ![Schermata che richiede di inserire il dito sul sensore](enrolling-fingerprint-images/testing-05.png)

    Se si usa un dispositivo Android, completare il processo toccando un dito allo scanner. 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulazione di un'analisi di impronte digitali nell'emulatore

In un emulatore Android, è possibile simulare un'analisi di impronte digitali usando Android Debug Bridge. Nel menu start di OS X una sessione Terminal mentre in Windows avviare un prompt dei comandi o una sessione di Powershell ed eseguire `adb`:

```shell
$ adb -e emu finger touch 1
```

Il valore di **1** è la _dito\_id_ per il dito "analizzato". È un numero intero univoco assegnato per ogni ID digitale virtuale. In futuro se l'app è in esecuzione, è possibile eseguire questo stesso ADB comando ogni volta che l'emulatore richiede per un'impronta digitale, è possibile eseguire la `adb` comandi e passargli il _dito\_id_ per simulare l'analisi di impronta digitale .

Al termine dell'analisi di impronta digitale Android avviserà l'utente è stato aggiunto l'impronta digitale:  

![Schermata visualizzazione impronte digitali aggiunta.](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Riepilogo 

Questa guida viene descritto come configurare un blocco dello schermo e si registra un'impronta digitale in un dispositivo Android o in un emulatore Android. 

