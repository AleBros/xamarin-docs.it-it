---
title: Riavvio rapido Xamarin
description: Questo documento descrive come configurare e usare il riavvio rapido Xamarin per eseguire il debug di un'app iOS.
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/16/2020
ms.openlocfilehash: cc5efffd4c3646fbff9cdb1ad1a30ec614cb4921
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79429567"
---
# <a name="xamarin-hot-restart-preview"></a>Riavvio rapido Xamarin (anteprima)

![Funzionalità di anteprima](~/media/shared/preview.png)

Il riavvio rapido Xamarin consente di testare rapidamente le modifiche apportate all'app durante lo sviluppo, incluse le modifiche al codice composto da più file, le risorse e i riferimenti. Esegue il push delle nuove modifiche apportate al bundle dell'app esistente nella destinazione di debug velocizzando notevolmente il ciclo di compilazione e distribuzione.

> [!IMPORTANT]
> Xamarin Hot Restart è attualmente disponibile in Visual Studio 2019 versione 16.5 stabile e supporta le app iOS tramite Xamarin.Forms. Il supporto per Visual Studio per Mac e le app non Xamarin.Forms è in programma.

## <a name="requirements"></a>Requisiti

- Visual Studio 2019 versione 16.5
- iTunes (64 bit)
- Account Apple Developer e iscrizione [a Apple Developer Program](https://developer.apple.com/programs) a pagamento


## <a name="initial-setup"></a>Configurazione iniziale

> [!NOTE]
> Xamarin Hot Restart è disabilitato per impostazione predefinita mentre è in anteprima. È possibile attivarlo in Opzioni > Strumenti > Funzionalità di **anteprima > ambiente > Abilita riavvio a caldo Xamarin**.

1. Assicurarsi che il progetto iOS sia impostato come progetto di avvio e che la configurazione di compilazione sia impostata su **Debug , iPhone**.

   1. Se si tratta di un progetto esistente, passare a **Compilazione > Configuration Manager** e assicurarsi che l'opzione **Distribuisci** sia abilitata per il progetto iOS.

2. Selezionare e fare clic su **Dispositivo locale** sulla barra degli strumenti per avviare la configurazione guidata:

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. Se iTunes non è installato, fare clic su **Download iTunes** (Scarica iTunes) per scaricare il programma di installazione. Al termine dell'installazione di iTunes, fare clic su **Avanti**.

4. Connettere un dispositivo iOS al computer. Se un dispositivo è già stato collegato, scollegalo e ricollegalo. Il nome del dispositivo verrà visualizzato nella procedura guidata non appena rilevato. Fare clic su **Avanti**.

5. Immettere le credenziali dell'account per sviluppatore Apple e fare clic su **Avanti**.

6. Selezionare un team di sviluppo usando il menu a discesa per abilitare il [provisioning automatico](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) nel progetto. Fare clic su **Fine**.

> [!NOTE]
> È consigliabile usare il provisioning automatico in modo che i dispositivi iOS aggiuntivi possano essere facilmente configurati per la distribuzione. È tuttavia possibile disabilitarlo e continuare a usare il provisioning manuale se sono presenti i profili di provisioning corretti.

## <a name="use-xamarin-hot-restart"></a>Usare il riavvio rapido Xamarin
Dopo la configurazione iniziale, il dispositivo connesso verrà visualizzato nel menu a discesa della destinazione di debug. Per eseguire il debug dell'app, selezionare il dispositivo nel menu a discesa e fare clic sul pulsante **Esegui**. Potrebbe essere visualizzato un messaggio in Visual Studio che richiede di avviare manualmente l'app nel dispositivo per avviare la sessione di debug.

È possibile apportare modifiche ai file di codice durante il debug, quindi fare clic sul pulsante **Riavvia** sulla barra degli strumenti di debug o usare **CTRL+MAIUSC+F5** per riavviare la sessione di debug con le nuove modifiche applicate:

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

È inoltre possibile `HOTRESTART` utilizzare il simbolo del preprocessore per impedire l'esecuzione di determinati codici durante il debug con Xamarin Hot Restart.

## <a name="limitations"></a>Limitazioni

- Attualmente sono supportate solo le app iOS compilate con dispositivi Xamarin.Forms e iOS.
- Sono supportati solo i dispositivi iOS a 64 bit. A partire da iOS 11, Apple non consente più l'esecuzione di app iOS sull'architettura a 32 bit (dispositivi precedenti a iPhone 5s).
- I file storyboard e XIB non sono supportati e l'app potrebbe arrestarsi in modo anomalo se prova a caricarli in fase di esecuzione. Utilizzare `HOTRESTART` il simbolo del preprocessore per impedire l'esecuzione di questo codice.
- Le librerie e i framework iOS statici non sono supportati e potresti vedere errori di runtime o arresti anomali se l'app tenta di caricarli. Utilizzare `HOTRESTART` il simbolo del preprocessore per impedire l'esecuzione di questo codice. Sono supportate le librerie iOS dinamiche.
- Non è possibile usare il riavvio rapido Xamarin per creare bundle dell'app per la pubblicazione. Sarà comunque necessario un computer Mac per eseguire una compilazione completa, la firma e la distribuzione per l'applicazione nell'ambiente di produzione.

## <a name="troubleshoot"></a>Risolvere problemi

- La configurazione guidata non rileverà iTunes se è stato installato tramite Microsoft Store. Sarà necessario disinstallare prima tale versione, quindi scaricare il [programma di installazione da Apple](https://go.microsoft.com/fwlink/?linkid=2101014).
- È presente un problema noto a causa del quale l'abilitazione di compilazioni specifiche del dispositivo impedisce all'app di accedere alla modalità di debug. Per risolvere il problema, è necessario disabilitare questa opzione in **Proprietà > Compilazione iOS** e ritentare il debug. Questo problema verrà risolto in una versione futura.
- Se l'app è già presente nel dispositivo, il tentativo di distribuzione con il riavvio rapido potrebbe non riuscire con un errore `AMDeviceStartHouseArrestService`. Per ovviare al problema, disinstallare l'app nel dispositivo, quindi distribuirla di nuovo.
- L'immissione di un ID Apple che non fa parte `Authentication Error. Xcode 7.3 or later is required to continue developing with your Apple ID`del programma per sviluppatori Apple potrebbe generare il seguente errore: . Devi disporre di un account Apple Developer valido per utilizzare Xamarin Hot Restart sui dispositivi iOS. 

Per segnalare problemi aggiuntivi, usare lo strumento di feedback in [Guida > Invia commenti e suggerimenti > Segnala un problema](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
