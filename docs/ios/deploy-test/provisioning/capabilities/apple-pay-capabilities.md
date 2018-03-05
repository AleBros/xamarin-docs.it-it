---
title: "Funzionalità di Apple Pay"
description: "L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per le funzionalità Apple Pay."
ms.topic: article
ms.prod: xamarin
ms.assetid: 735CC916-16A4-471B-87F7-0535E24288D7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 88db45135104f14ca3a4b18e466e95288853a6df
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="apple-pay-capabilities"></a>Funzionalità di Apple Pay

_L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per le funzionalità Apple Pay._

Apple Pay consente agli utenti di pagare beni tramite il dispositivo iOS. Questa sezione descrive come creare tutti i componenti necessari per Apple Pay nel centro sviluppatori Apple.

Durante il provisioning di una nuova app tramite il centro sviluppatori è necessario eseguire tre passaggi:

1.  Creare un ID esercente.
2.  Creare un ID app con la funzionalità Apple Pay e aggiungervi l'esercente.
3.  Generare un certificato per l'ID esercente.

I passaggi seguenti consentono di creare gli elementi elencati sopra:

<a name="merchantid" />

## <a name="create-merchant-id"></a>Creare l'ID esercente

Un ID esercente viene usato per comunicare ad Apple Pay che è possibile accettare pagamenti e viene passato al metodo `PaymentRequest` di PassKit e usato nell'entitlement Apple Pay:

1.  Aprire il [centro sviluppatori Apple](https://developer.apple.com/account/) e passare alla sezione Certificates, Identifiers, and Profiles (Certificati, identificatori e profili): 
 
    ![Selezione dell'ID esercente nel centro sviluppatori](apple-pay-capabilities-images/image57.png)

2.  In **Identifiers** (Identificatori) selezionare **ID esercente** e quindi selezionare **+** per creare un nuovo ID esercente:  

3.  Compilare il modulo, illustrato sotto, con una nuova descrizione e un nuovo identificatore. La descrizione consente di identificare l'ID e può essere modificata in seguito. L'identificatore deve essere univoco e iniziare con la stringa `merchant`. Apple consiglia il formato seguente per l'identificatore: `merchant.com.[Your-App-Name]`:
   
    ![Dettagli del nuovo ID esercente](apple-pay-capabilities-images/image58.png)

4.  Confermare i dettagli e **registrare** l'ID: 
    
    ![Conferma dell'ID esercente](apple-pay-capabilities-images/image59.png)

<a name="appid" />

## <a name="create-an-app-id-with-the-apple-pay-capability-that-includes-the-merchant-id"></a>Creare con la funzionalità Apple Pay un ID app che include l'ID esercente

1.  Nel [centro sviluppatori](https://developer.apple.com/account/) fare clic su **App IDs** (ID app) in **Identifiers** (Identificatori): 
    
    ![Selezionare l'ID app nel centro sviluppatori](apple-pay-capabilities-images/image6.png)

2.  Selezionare il pulsante **+** per aggiungere un nuovo ID app: 
   
    ![Pulsante per aggiungere un nuovo ID app](apple-pay-capabilities-images/image27.png)

3.  Immettere un nome per l'ID app e assegnargli un ID app esplicito:    
   
    ![Schermata dei dettagli dell'ID app ](apple-pay-capabilities-images/image35.png)

4.  In App Services (Servizi app) selezionare Apple Pay:    
  
    ![Apple Pay in App Services (Servizi app)](apple-pay-capabilities-images/image36.png)

5.  Selezionare **Continue** (Continua) e quindi **Register** (Registra). Si noti che nella schermata di conferma Apple Pay viene visualizzato con la voce Configurable (Configurabile) selezionata, con un simbolo giallo: 
   
    ![Schermata di conferma per Apple Pay](apple-pay-capabilities-images/image37.png)

6.  Tornare all'elenco di ID app e selezionare quello appena creato:  
   
    ![Modificare l'ID app](apple-pay-capabilities-images/image38.png)

7.  Scorrere fino alla fine di questa sezione espansa e fare clic su **Edit** (Modifica).
8.  Scorrere l'elenco verso il basso fino ad Apple Pay e fare clic sul pulsante **Edit** (Modifica):  
    
    
    ![Modificare i dettagli dell'ID app di Apple Pay](apple-pay-capabilities-images/image39.png)
9.  Selezionare l'ID esercente da usare con questo ID app e fare clic su **Continue** (Continua):  
    
    ![Selezionare l'ID esercente da usare per l'ID app](apple-pay-capabilities-images/image40.png)

10. Verificare le assegnazioni dell'ID esercente e fare clic su **Assign** (Assegna):  
    
    ![Schermata di conferma](apple-pay-capabilities-images/image41.png)

Questo ID app può ora essere usato per generare o rigenerare un nuovo profilo di provisioning, come descritto nella guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md). 

<a name="certificate" />

## <a name="create-a-certificate-for-your-merchant-id"></a>Creare un certificato per l'ID esercente

Apple richiede un certificato per crittografare i dati sensibili associati alla transazione. Ogni ID esercente creato deve avere il proprio certificato. 

Per creare un certificato, seguire questa procedura:

1.  Selezionare l'ID esercente creato sopra e fare clic su **Edit** (Modifica): 
    
    ![Finestra di dialogo di modifica dell'ID esercente](apple-pay-capabilities-images/image42.png)

2.  Nella schermata iOS Merchant ID Settings (Impostazioni dell'ID esercente iOS) fare clic su **Create Certificate** (Crea certificato): 
   
    ![Creare il certificato per l'elaborazione del pagamento](apple-pay-capabilities-images/image43.png)

3.  Rispondere alla domanda seguente: 

    ![Indicare se i pagamenti verranno elaborati esclusivamente in Cina](apple-pay-capabilities-images/image44.png)

4.  A questo punto verrà chiesto di creare una _richiesta di firma del certificato_: 

    ![Creazione di una richiesta di firma del certificato](apple-pay-capabilities-images/image45.png)
    
    > [!IMPORTANT]
> Se si usa un provider di servizi di pagamento per Apple Pay, ad esempio JudoPay o Stripe, questo può fornire una richiesta di firma del certificato formattata correttamente che è possibile usare adesso. Per informazioni sulla richiesta, vedere i siti di [JudoPay](https://www.judopay.com/docs/version-52/apple-pay/getting-started/#create-an-apple-pay-certificate) e [Stripe](https://stripe.com/docs/apple-pay/apps#csr). Per creare la richiesta di firma del certificato, seguire i passaggi da 5 a 8 indicati sotto. Quando la richiesta di firma del certificato è disponibile, andare al passaggio 9.

5.  Aprire l'applicazione Keychain Access (Accesso keychain) e passare a **Keychain Access > Certificate Assistant > Request a Certificate from a Certificate Authority** (Accesso keychain > Assistente certificati > Richiedi certificato da Autorità di certificazione): 

     ![Creare una richiesta di firma del certificato usando il keychain su un Mac](apple-pay-capabilities-images/image46.png)

6.  Immettere l'indirizzo di posta elettronica, immettere un nome per la chiave privata, lasciare vuoto CA Email Address (Indirizzo di posta elettronica CA), selezionare l'opzione **Save to Disk** (Salva su disco) e selezionare **Let me specify key pair information** (Consenti di specificare le informazioni sulla coppia di chiavi):

     ![Finestra di dialogo Certificate information (Informazioni certificato)](apple-pay-capabilities-images/image47.png)

7.  Salvare la richiesta di firma del certificato in una posizione facilmente raggiungibile: 

     ![Salvataggio della richiesta di firma del certificato nel computer locale](apple-pay-capabilities-images/image48.png)

8.  Nella schermata Key Pair information (Informazioni sulla coppia di chiavi) impostare **Key Size** (Dimensioni chiave) su **256 bits** (256 bit) e **Algorithm** (Algoritmo) su **ECC** e fare clic su **Continue** (Continua):

     ![Finestra di dialogo per l'immissione delle informazioni sulla coppia di chiavi](apple-pay-capabilities-images/image49.png)

9.  Nel centro sviluppatori fare clic su **Continue** (Continua) per caricare la richiesta di firma del certificato: 

     ![Preparare il caricamento della richiesta di firma del certificato nel centro sviluppatori](apple-pay-capabilities-images/image50.png)

10. Fare clic su **Scegli file** per selezionare la richiesta di firma del certificato e fare clic su **Continue** (Continua) per caricarlo nel portale per sviluppatori: 

     ![Caricare la richiesta di firma del certificato nel centro sviluppatori](apple-pay-capabilities-images/image51.png)

11. Dopo che il certificato è stato generato, scaricarlo e fare doppio clic su di esso per installarlo nel keychain.

Per altre informazioni sull'uso di Apple Pay, vedere la guida seguente:

*   [Introduction to Apple Pay](~/ios/platform/apple-pay.md) (Introduzione ad Apple Pay)

## <a name="next-steps"></a>Passaggi successivi
 
Nell'elenco seguente vengono descritti i passaggi aggiuntivi che potrebbero essere necessari:

* Usare lo spazio dei nomi del framework nell'app.
* Aggiungere all'app gli entitlement necessari. Per informazioni dettagliate sugli entitlement necessari e su come aggiungerli, vedere la guida [Uso degli entitlement](~/ios/deploy-test/provisioning/entitlements.md).
* In **Firma del bundle iOS** dell'app assicurarsi che **Entitlement personalizzati** sia impostato su **Entitlements.plist**. Questa _non_ è l'impostazione predefinita per le build di debug e del simulatore iOS.

In caso di problemi con i servizi app, vedere la sezione [Troubleshooting](~/ios/deploy-test/provisioning/capabilities/index.md) (Risoluzione dei problemi) della guida principale.