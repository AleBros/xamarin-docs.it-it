---
title: Gestione degli account Apple
description: Questo documento descrive come usare le funzionalità di gestione di account Apple in Visual Studio per Mac e Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 28a7d299e78a7fa6837ba9285795d380760f13b7
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864640"
---
# <a name="apple-account-management"></a>Gestione degli account Apple

L'interfaccia di gestione di account Apple consente di visualizzare tutti i team di sviluppo associati con un ID Apple. Permette anche per altri dettagli su ogni team visualizzando un elenco delle _identità di firma_ e _profili di Provisioning_ che vengono installati nel computer.

Nella riga di comando con cui viene eseguita l'autenticazione dell'ID Apple [fastlane](https://fastlane.tools/). FastLane deve essere installata nel computer per poter essere autenticati. Altre informazioni su come installarlo e fastlane sono descritta in dettaglio nel [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) Guide.

La finestra di dialogo Account Apple consente di eseguire le operazioni seguenti:

* **Creare e gestire i certificati**
* **Creare e gestire i profili di Provisioning**

Informazioni su come eseguire questa operazione sono descritta in questa Guida.

> [!NOTE]
> Gli strumenti Xamarin per la gestione degli account Apple solo visualizzano le informazioni sugli account per sviluppatori di Apple a pagamento. Per informazioni su come testare un'app su un dispositivo senza un account sviluppatore Apple a pagamento, vedere la [provisioning gratuito per le app xamarin. IOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) Guida.

È anche possibile usare iOS gli strumenti di Provisioning automatico per creare e gestire le identità di firma, gli ID App e i profili di Provisioning automaticamente. Per altre informazioni sull'uso di queste funzionalità, vedere la [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) Guida.

## <a name="requirements"></a>Requisiti

Gestione degli account Apple è disponibile in Visual Studio per Mac, Visual Studio 2019 e Visual Studio 2017 (versione 15.7 e successive).

È necessario un account Apple Developer per usare questa funzionalità. Ulteriori informazioni sull'account per sviluppatore Apple sono disponibile nel [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) Guida.

- Assicurarsi di che essere connessi a internet. Questo avviene perché fastlane comunica direttamente con il portale per sviluppatori Apple.
- Assicurarsi di aver [installati gli strumenti fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Verificare che gli strumenti fastlane più recenti dal [ https://download.fastlane.tools ](https://download.fastlane.tools).
- Prima di iniziare, assicurarsi di accettare eventuali contratti di licenza utente nel [portale per sviluppatori](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Aggiunta di un account sviluppatore Apple

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Per aprire la finestra di dialogo Gestione account, andare al **Visual Studio > Preferenze > Account per sviluppatore Apple**:

    ![Opzioni di Account per sviluppatore Apple](apple-account-management-images/image1.png)

2. Premere il **+** pulsante per visualizzare l'accesso nella finestra di dialogo, come illustrato di seguito: 

    ![finestra di dialogo di FastLane.](apple-account-management-images/image2.png)

3. Immettere l'ID Apple e la Password, quindi scegliere il **Accedi** pulsante. Le credenziali verranno salvate nel Keychain sicura su questo computer. [FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) consente di gestire in modo sicuro le credenziali e passarle al portale per sviluppatori di Apple.
 
4. Selezionare **Consenti sempre** nella finestra di dialogo di avviso per consentire a Visual Studio usare le credenziali:

    ![Consenti sempre la finestra di dialogo Avviso](apple-account-management-images/image4.png)

5. Dopo l'account è stato aggiunto correttamente, verrà visualizzato il proprio ID Apple e tutti i team che il proprio ID Apple fa parte di.

    ![Finestra con gli account aggiunti account sviluppatore Apple](apple-account-management-images/image5.png)

6. Selezionare qualsiasi team e premere il **Visualizza i dettagli...** immagini (...). Verrà visualizzato un elenco di tutte le identità di firma e i profili di Provisioning installati nel computer:

    ![Visualizzazione dei dettagli nella schermata identità di firma e i profili nel computer di provisioning](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Prima di iniziare ad aggiungere il proprio ID Apple per Visual Studio 2019, assicurarsi che l'ambiente di sviluppo sia [abbinato a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Per aprire la finestra di gestione di account, passare a **strumenti > Opzioni > Xamarin > account Apple**:

    ![Schermata delle opzioni degli account Apple](apple-account-management-images/prov1.png)

1. Selezionare il **Add** pulsante e immettere l'ID Apple e la password:

    ![finestra di dialogo nome utente e password](apple-account-management-images/prov1a.png)

1. Dopo l'account è stato aggiunto correttamente, verrà visualizzato il proprio ID Apple e tutti i team che il proprio ID Apple fa parte di.
 
1. Selezionare qualsiasi team e premere il **Visualizza i dettagli...** immagini (...). Verrà visualizzato un elenco di tutte le identità di firma e i profili di Provisioning installati nel computer:

    ![finestra di dialogo nome utente e password](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>La gestione delle identità di firma e i profili di Provisioning

La finestra di dialogo Dettagli team visualizza un elenco di identità di firma, organizzati per tipo. Il **stato** colonna Avvisa se il certificato è: 

* **Valido** : l'identità di firma (il certificato e la chiave privata) viene installato nel computer e non è ancora scaduto.

* **Non presente nel Keychain** – vi sia un'identità di firma valida nel server di Apple. Per installarlo nel computer, devono essere esportato da un altro computer. È possibile scaricare l'identità di firma dal portale Apple Developer che non contiene la chiave privata.

* **Manca la chiave privata** : viene installato un certificato senza chiave privata nel keychain.

* **Scaduto** : il certificato è scaduto. È necessario rimuovere questo dal keychain.

  ![informazioni finestra di dialogo Dettagli su team](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Creare un'identità di firma

Per creare una nuova identità di firma, selezionare la **Create Certificate** pulsante a discesa e selezionare il tipo necessari. Se si dispone delle autorizzazioni corrette di una nuova firma identità verrà visualizzato dopo pochi secondi.

Se un'opzione nell'elenco a discesa è disabilitata e questa opzione è deselezionata, significa che non hai le autorizzazioni di team corretto per creare questo tipo di certificato.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![creare le opzioni di certificato](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![creare le opzioni di certificato](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Scaricare i profili di Provisioning

La finestra di dialogo Dettagli team visualizza inoltre un elenco di tutti i profili di provisioning connesso all'account per sviluppatore. È possibile scaricare tutti i profili di provisioning nel computer locale, premere il **Scarica tutti i profili** pulsante

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Scaricare sezione profili di provisioning](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Scaricare sezione profili di provisioning](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Firma bundle iOS

Per informazioni sulla distribuzione dell'app in un dispositivo, vedere la [device provisioning in](~/ios/get-started/installation/device-provisioning/index.md) Guida.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="view-details-dialog-is-empty"></a>Finestra di dialogo visualizzazione dettagli è vuoto

Attualmente si tratta di un problema noto, relativi a bug [53906 #](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Assicurarsi che si usa la versione stabile più recente di Visual Studio per Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Se si verificano problemi di accesso nell'account, provare quanto segue:

* Aprire l'applicazione keychain e Seleziona categoria *password*. Cercare `deliver.`ed eliminare tutte le voci.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Errore durante l'aggiunta di Account. Accedi con una password specifici dell'app"

Questo avviene perché l'autenticazione a 2 fattori è abilitata per l'account. Assicurarsi che si usa la versione stabile più recente di Visual Studio per Mac

### <a name="failed-to-create-new-certificate"></a>Impossibile creare il nuovo certificato
"È stato raggiunto il limite per i certificati di questo tipo"

![finestra di dialogo limite certificato](apple-account-management-images/image10.png)

Il numero massimo di certificati consentito è stato generato. Per risolvere questo problema, individuare il [Apple Developer Center](https://developer.apple.com/account/ios/certificate/distribution) e revocarne uno dei certificati di produzione.

## <a name="known-issues"></a>Problemi noti

* Per impostazione predefinita, i profili di provisioning di distribuzione indirizzano all'App Store. È necessario creare manualmente profili interni o ad hoc.
