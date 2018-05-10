---
title: Gestione degli Account di Apple
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 2a37f6644c66ebeb3b10a9fa0467115a21f69e75
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="apple-account-management"></a>Gestione degli Account di Apple

L'interfaccia di gestione di account Apple fornisce un modo per visualizzare tutti i team di sviluppo associati a un ID Apple. Consente inoltre di visualizzare ulteriori dettagli su ogni team visualizzando un elenco di _identità di firma_ e _i profili di Provisioning_ che vengono installati nel computer.

L'autenticazione dell'ID Apple viene eseguita nella riga di comando con [fastlane](https://fastlane.tools/). FastLane debba essere installati nel computer per poter essere autenticato. Ulteriori informazioni su fastlane e modalità di installazione dettagliate di [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) Guide.

La finestra di dialogo Account Apple consente di eseguire le operazioni seguenti:

* **Creare e gestire i certificati** 
* **Creare e gestire profili di Provisioning** 

Informazioni su come eseguire questa operazione sono descritta in questa Guida.

È inoltre possibile utilizzare iOS gli strumenti di Provisioning automatico per creare e gestire le identità di firma, ID di App e i profili di Provisioning automaticamente.

Per ulteriori informazioni sull'utilizzo di queste funzionalità, consultare il [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) Guida.
️
## <a name="requirements"></a>Requisiti

Gestione degli account Apple è disponibile in Visual Studio per Mac e Visual Studio 2017 (versione 15.7 e versioni successive)

È necessario disporre di un account per sviluppatori di Apple per usare questa funzionalità. Ulteriori informazioni sugli account per sviluppatori di Apple sono disponibile nel [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) Guida.

- Assicurarsi di che essere connessi a internet. Questo avviene perché fastlane comunica direttamente con il portale per sviluppatori di Apple.
- Verificare di aver [installati gli strumenti fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Verificare di aver gli strumenti più recenti fastlane da [ https://download.fastlane.tools ](https://download.fastlane.tools).
- Prima di iniziare, assicurarsi di eventuali contratti di licenza utente di accettare il [portale per sviluppatori](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Aggiunta di un account per sviluppatori di Apple

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Per aprire la finestra di dialogo di gestione di account, passare a **Visual Studio > Preferenze > Account per sviluppatori Apple**:

    ![Opzioni dell'Account per sviluppatori di Apple](apple-account-management-images/image1.png)

2. Premere il **+** pulsante per visualizzare il simbolo nella finestra di dialogo, come illustrato di seguito: 

    ![finestra di dialogo FastLane.](apple-account-management-images/image2.png)

4. Immettere l'ID Apple e la Password, quindi scegliere il **Accedi** pulsante. Le credenziali verranno salvate nel portachiavi protetto in questo computer. [FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) viene utilizzato per gestire in modo sicuro le credenziali e passarle al portale per sviluppatori di Apple.
 
5. Selezionare **Consenti sempre** nella finestra di dialogo di avviso per consentire a Visual Studio utilizzare le credenziali:

    ![Consenti sempre le finestra di dialogo Avviso](apple-account-management-images/image4.png)

6. Una volta che l'account è stato aggiunto correttamente, verrà visualizzato il proprio ID Apple e agli eventuali team che il proprio ID Apple.

    ![Finestra di dialogo di Apple developer account con gli account aggiunti](apple-account-management-images/image5.png)

7. Selezionare qualsiasi team e premere il **visualizzare i dettagli...** immagini (...). Questo verrà visualizzato un elenco di tutti i profili di Provisioning che vengono installati nel computer e le identità di firma:

    ![Visualizzazione dei dettagli nella schermata identità di firma e profili nel computer di provisioning](apple-account-management-images/image6.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Prima di iniziare ad aggiungere il proprio ID Apple in Visual Studio 2017, assicurarsi che l'ambiente di sviluppo sia [abbinati a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Per aprire la finestra di gestione di account, Vai a **strumenti > Opzioni > Xamarin > account Apple**:

    ![Schermata di opzioni di account di Apple](apple-account-management-images/prov1.png)

1. Selezionare il **Aggiungi** pulsante e immettere il proprio ID Apple e la password:

    ![finestra di dialogo nome utente e password](apple-account-management-images/prov1a.png)

1. Una volta che l'account è stato aggiunto correttamente, verrà visualizzato il proprio ID Apple e agli eventuali team che il proprio ID Apple.
 
1. Selezionare qualsiasi team e premere il **visualizzare i dettagli...** immagini (...). Questo verrà visualizzato un elenco di tutti i profili di Provisioning che vengono installati nel computer e le identità di firma:

    ![finestra di dialogo nome utente e password](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>La gestione delle identità di firma e profili di Provisioning

La finestra di dialogo Dettagli team visualizza un elenco di identità di firma, organizzati per tipo. Il **stato** colonna informa se il certificato è: 

* **Valido** : l'identità di firma (il certificato e la chiave privata) è installato nel computer in uso e non è scaduto.

* **Non nel portachiavi** – è un'identità di firma valida nel server di Apple. Per installarlo nel computer in uso, deve essere esportato da un altro computer. Non contiene la chiave privata non è possibile scaricare l'identità di firma da portale per sviluppatori di Apple.

* **Manca la chiave privata** – è installato un certificato senza chiave privata nel portachiavi.

* **Scaduto** : il certificato è scaduto. È necessario rimuovere questo da portachiavi.

  ![informazioni finestra di dialogo Dettagli su team](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Creare un'identità di firma

Per creare una nuova identità di firma, selezionare il **Create Certificate** pulsante di menu a discesa e selezionare il tipo che è necessario. Se si dispone delle autorizzazioni corrette di una nuova firma identità apparirà dopo alcuni secondi.

Se un'opzione nell'elenco a discesa è disattivata e deselezionata, significa che non si dispone delle autorizzazioni team corretti per creare questo tipo di certificato.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![creare le opzioni di certificati](apple-account-management-images/image8.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![creare le opzioni di certificati](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Scaricare i profili di Provisioning

La finestra di dialogo Dettagli team visualizza inoltre un elenco di tutti i profili di provisioning connessi al proprio account sviluppatore. È possibile scaricare tutti i profili di provisioning nel computer locale, premendo il **scaricare tutti i profili** pulsante

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Scaricare provisioning sezione profili](apple-account-management-images/image9.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Scaricare provisioning sezione profili](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Firma bundle iOS

Per informazioni sulla distribuzione dell'app in un dispositivo, consultare il [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) Guida.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="view-details-dialog-is-empty"></a>Finestra di dialogo visualizzazione dettagli è vuoto

È attualmente un problema noto, relativi a bug [53906 #](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Assicurarsi che si utilizza la versione stabile più recente di Visual Studio per Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Se si verificano problemi nell'account di accesso, provare le operazioni seguenti:

* Aprire l'applicazione di portachiavi e selezionare categoria *password*. Cercare `deliver.`ed eliminare tutte le voci.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Errore durante l'aggiunta di Account. Eseguire l'accesso con una password di app specifiche"

In questo modo 2 factor authentication è abilitato per l'account. Assicurarsi che si utilizza la versione stabile più recente di Visual Studio per Mac

### <a name="failed-to-create-new-certificate"></a>Impossibile creare il nuovo certificato
"È stato raggiunto il limite per i certificati di questo tipo"

![finestra di dialogo limite certificato](apple-account-management-images/image10.png)

Il numero massimo di certificati consentito è stato generato. Per risolvere questo problema, individuare il [Apple Developer Center](https://developer.apple.com/account/ios/certificate/distribution) e revocare uno dei certificati di produzione.

## <a name="known-issues"></a>Problemi noti

* Per impostazione predefinita, i profili di provisioning di distribuzione indirizzano all'App Store. È necessario creare manualmente profili interni o ad hoc.
