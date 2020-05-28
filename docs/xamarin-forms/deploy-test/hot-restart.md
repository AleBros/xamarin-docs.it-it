---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 45b7d0d20c43aa22ebde3a17552f10ceea77a48b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139139"
---
# <a name="xamarin-hot-restart-preview"></a>Riavvio rapido Xamarin (anteprima)

![Funzionalità di anteprima](~/media/shared/preview.png)

Il riavvio rapido Xamarin consente di testare rapidamente le modifiche apportate all'app durante lo sviluppo, incluse le modifiche al codice composto da più file, le risorse e i riferimenti. Esegue il push delle nuove modifiche apportate al bundle dell'app esistente nella destinazione di debug velocizzando notevolmente il ciclo di compilazione e distribuzione.

> [!IMPORTANT]
> Novell Hot restart è attualmente disponibile in Visual Studio 2019 versione 16,5 stabile e supporta le app iOS usando Xamarin.Forms . Il supporto per Visual Studio per Mac e non per Xamarin.Forms le app è basato sulla roadmap.

## <a name="requirements"></a>Requisiti

- Visual Studio 2019 versione 16,5
- iTunes (64 bit)
- Registrazione di Apple Developer [Program e Apple Developer Program](https://developer.apple.com/programs)


## <a name="initial-setup"></a>Configurazione iniziale

> [!NOTE]
> Novell Hot restart è disabilitato per impostazione predefinita mentre è in anteprima. È possibile abilitarlo in **strumenti > opzioni > ambiente > funzionalità di anteprima > abilitare il riavvio a caldo Novell**.

1. Verificare che il progetto iOS sia impostato come progetto di avvio e che la configurazione di compilazione sia impostata su **debug | iPhone**.

   1. Se si tratta di un progetto esistente, passare a **Compilazione > Configuration Manager** e assicurarsi che l'opzione **Distribuisci** sia abilitata per il progetto iOS.

2. Selezionare e fare clic su **Dispositivo locale** sulla barra degli strumenti per avviare la configurazione guidata:

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. Se iTunes non è installato, fare clic su **Download iTunes** (Scarica iTunes) per scaricare il programma di installazione. Al termine dell'installazione di iTunes, fare clic su **Avanti**.

4. Connettere un dispositivo iOS al computer. Se un dispositivo è già collegato, scollegarlo e riconnetterlo. Il nome del dispositivo verrà visualizzato nella procedura guidata non appena rilevato. Fare clic su **Avanti**.

5. Immettere le credenziali dell'account per sviluppatore Apple e fare clic su **Avanti**.

6. Selezionare un team di sviluppo usando il menu a discesa per abilitare il [provisioning automatico](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) nel progetto. Fare clic su **Fine**.

> [!NOTE]
> È consigliabile usare il provisioning automatico in modo che i dispositivi iOS aggiuntivi possano essere facilmente configurati per la distribuzione. È tuttavia possibile disabilitarlo e continuare a usare il provisioning manuale se sono presenti i profili di provisioning corretti.

## <a name="use-xamarin-hot-restart"></a>Usare il riavvio rapido Xamarin
Dopo la configurazione iniziale, il dispositivo connesso verrà visualizzato nel menu a discesa della destinazione di debug. Per eseguire il debug dell'app, selezionare il dispositivo nel menu a discesa e fare clic sul pulsante **Esegui**. Potrebbe essere visualizzato un messaggio in Visual Studio che richiede di avviare manualmente l'app nel dispositivo per avviare la sessione di debug.

È possibile apportare modifiche ai file di codice durante il debug, quindi fare clic sul pulsante **Riavvia** sulla barra degli strumenti di debug o usare **CTRL+MAIUSC+F5** per riavviare la sessione di debug con le nuove modifiche applicate:

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

È anche possibile usare il `HOTRESTART` simbolo del preprocessore per impedire l'esecuzione di un determinato codice durante il debug con Novell Hot restart.

## <a name="limitations"></a>Limitazioni

- Attualmente sono supportate solo le app iOS compilate con Xamarin.Forms i dispositivi iOS.
- Sono supportati solo i dispositivi iOS a 64 bit. A partire da iOS 11, Apple non consente più di eseguire app iOS sull'architettura a 32 bit (dispositivi precedenti a iPhone 5s).
- I file storyboard e XIB non sono supportati e l'app potrebbe arrestarsi in modo anomalo se prova a caricarli in fase di esecuzione. Usare il `HOTRESTART` simbolo del preprocessore per impedire l'esecuzione del codice.
- Le librerie e i Framework iOS statici non sono supportati ed è possibile che vengano visualizzati errori o arresti anomali del runtime se l'app tenta di caricarli. Usare il `HOTRESTART` simbolo del preprocessore per impedire l'esecuzione del codice. Sono supportate le librerie iOS dinamiche.
- Non è possibile usare il riavvio rapido Xamarin per creare bundle dell'app per la pubblicazione. Sarà comunque necessario un computer Mac per eseguire una compilazione completa, la firma e la distribuzione per l'applicazione nell'ambiente di produzione.

## <a name="troubleshoot"></a>Risolvere problemi

- La configurazione guidata non rileverà iTunes se è stato installato tramite Microsoft Store. Sarà necessario disinstallare prima tale versione, quindi scaricare il [programma di installazione da Apple](https://go.microsoft.com/fwlink/?linkid=2101014).
- È presente un problema noto a causa del quale l'abilitazione di compilazioni specifiche del dispositivo impedisce all'app di accedere alla modalità di debug. Per risolvere il problema, è necessario disabilitare questa opzione in **Proprietà > Compilazione iOS** e ritentare il debug. Questo problema verrà risolto in una versione futura.
- Se l'app è già presente nel dispositivo, il tentativo di distribuzione con il riavvio rapido potrebbe non riuscire con un errore `AMDeviceStartHouseArrestService`. Per ovviare al problema, disinstallare l'app nel dispositivo, quindi distribuirla di nuovo.
- L'immissione di un ID Apple che non fa parte del programma Apple Developer potrebbe causare l'errore seguente: `Authentication Error. Xcode 7.3 or later is required to continue developing with your Apple ID` . È necessario avere un account Apple Developer valido per usare il riavvio a caldo di Novell nei dispositivi iOS. 

Per segnalare problemi aggiuntivi, usare lo strumento di feedback in [Guida > Invia commenti e suggerimenti > Segnala un problema](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
