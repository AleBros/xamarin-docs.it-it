---
title: Gestione degli account Apple
description: Questo documento descrive come usare le funzionalità di gestione degli account Apple in Visual Studio per Mac e Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: conceptdev
ms.author: crdun
ms.date: 05/06/2018
ms.openlocfilehash: 65945a303375863f7b92b20405aa78e6b2edacda
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766127"
---
# <a name="apple-account-management"></a>Gestione degli account Apple

L'interfaccia di gestione degli account Apple fornisce un modo per visualizzare tutti i team di sviluppo associati a un ID Apple. Consente inoltre di visualizzare altri dettagli su ogni team visualizzando un elenco di _identità di firma_ e di profili di _provisioning_ installati nel computer.

L'autenticazione dell'ID Apple viene eseguita nella riga di comando con [Fastlane](https://fastlane.tools/). per eseguire correttamente l'autenticazione, è necessario che nel computer sia installato Fastlane. Altre informazioni su Fastlane e su come installarlo sono descritte in dettaglio nelle guide di [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) .

La finestra di dialogo account Apple consente di eseguire le operazioni seguenti:

- **Creare e gestire certificati**
- **Creare e gestire i profili di provisioning**

Informazioni su come eseguire questa operazione sono descritte in questa guida.

> [!NOTE]
> Gli strumenti di Novell per la gestione degli account Apple visualizzano solo le informazioni sugli account per sviluppatori Apple a pagamento. Per informazioni su come testare un'app in un dispositivo senza un account per sviluppatore Apple a pagamento, vedere la Guida [relativa al provisioning gratuito per le app Novell. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

È anche possibile usare gli strumenti di provisioning automatico di iOS per creare e gestire automaticamente le identità di firma, gli ID app e i profili di provisioning. Per altre informazioni sull'uso di queste funzionalità, vedere la guida al [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="requirements"></a>Requisiti

La gestione degli account Apple è disponibile in Visual Studio per Mac, Visual Studio 2019 e Visual Studio 2017 (versione 15,7 e successive).

Per usare questa funzionalità, è necessario disporre di un account per sviluppatore Apple. Altre informazioni sugli account per sviluppatori Apple sono disponibili nella Guida al [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .

- Assicurarsi di essere connessi a Internet. Questo perché Fastlane comunica direttamente con il portale Apple Developer.
- Assicurarsi che siano [installati gli strumenti di Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Assicurarsi di disporre degli strumenti Fastlane più recenti [https://download.fastlane.tools](https://download.fastlane.tools)da.
- Prima di iniziare, assicurarsi di accettare eventuali contratti di licenza con l'utente nel [portale per sviluppatori](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Aggiunta di un account Apple Developer

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Per aprire la finestra di dialogo Gestione account, passare a **Visual Studio > preferenze > account per sviluppatore Apple**:

    ![Opzioni dell'account sviluppatore Apple](apple-account-management-images/image1.png)

2. Premere il **+** pulsante per visualizzare la finestra di dialogo di accesso, come illustrato di seguito: 

    ![finestra di dialogo Fastlane.](apple-account-management-images/image2.png)

3. Immettere l'ID Apple e la password e fare clic sul pulsante **Accedi** . In questo modo, le credenziali vengono salvate nel Keychain sicuro del computer. [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) viene usato per gestire le credenziali in modo sicuro e passarle al portale per sviluppatori di Apple.

4. Selezionare **Consenti sempre** nella finestra di dialogo di avviso per consentire a Visual Studio di usare le credenziali:

    ![Consenti sempre la finestra di dialogo di avviso](apple-account-management-images/image4.png)

5. Una volta che l'account è stato aggiunto, verranno visualizzati l'ID Apple e i team di cui fa parte l'ID Apple.

    ![Finestra di dialogo account sviluppatore Apple con account aggiunti](apple-account-management-images/image5.png)

6. Selezionare un team e fare clic su **Visualizza dettagli...** immagini (...). Verrà visualizzato un elenco di tutte le identità di firma e i profili di provisioning installati nel computer:

    ![Visualizzare la schermata dei dettagli che mostra le identità di firma e i profili di provisioning nel computer](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Prima di iniziare ad aggiungere l'ID Apple a Visual Studio 2019, assicurarsi che l'ambiente di sviluppo sia associato [a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Per aprire la finestra Gestione account, passare a **strumenti > opzioni > novell > account Apple**:

    ![Schermata delle opzioni degli account Apple](apple-account-management-images/prov1.png)

1. Selezionare il pulsante **Aggiungi** e immettere l'ID Apple e la password:

    ![finestra di dialogo nome utente e password](apple-account-management-images/prov1a.png)

1. Una volta che l'account è stato aggiunto, verranno visualizzati l'ID Apple e i team di cui fa parte l'ID Apple.

1. Selezionare un team e fare clic su **Visualizza dettagli...** immagini (...). Verrà visualizzato un elenco di tutte le identità di firma e i profili di provisioning installati nel computer:

    ![finestra di dialogo nome utente e password](apple-account-management-images/prov2.png)

-----

## <a name="managing-signing-identities-and-provisioning-profiles"></a>Gestione delle identità di firma e dei profili di provisioning

La finestra di dialogo Dettagli team Visualizza un elenco di identità di firma, organizzate in base al tipo. La colonna **stato** consiglia se il certificato è: 

- **Valido** : l'identità di firma (sia il certificato che la chiave privata) viene installata nel computer e non è scaduta.

- **Non presente nel Keychain** : è presente un'identità di firma valida nel server Apple. Per installarlo nel computer, è necessario esportarlo da un altro computer. Non è possibile scaricare l'identità di firma dal portale Apple Developer perché non conterrà la chiave privata.

- **Chiave privata mancante** : nel Keychain viene installato un certificato senza chiave privata.

- **Scaduto** : il certificato è scaduto. È necessario rimuoverlo dal keychain.

  ![informazioni della finestra di dialogo Dettagli team](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Creare identità di firma

Per creare una nuova identità di firma, selezionare il pulsante a discesa **Crea certificato** e selezionare il tipo richiesto. Se si dispone delle autorizzazioni corrette, dopo alcuni secondi verrà visualizzata una nuova identità di firma.

Se un'opzione nell'elenco a discesa è disattivata e deselezionata, significa che non si dispone delle autorizzazioni di Team corrette per creare questo tipo di certificato.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![opzioni per la creazione di certificati](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![opzioni per la creazione di certificati](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Scaricare i profili di provisioning

La finestra di dialogo Dettagli team Visualizza anche un elenco di tutti i profili di provisioning connessi all'account sviluppatore. È possibile scaricare tutti i profili di provisioning nel computer locale premendo il pulsante **Scarica tutti i profili**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Sezione scaricare i profili di provisioning](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Sezione scaricare i profili di provisioning](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Firma bundle iOS

Per informazioni sulla distribuzione dell'app in un dispositivo, vedere la guida al [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="view-details-dialog-is-empty"></a>La finestra di dialogo Visualizza dettagli è vuota

Si tratta attualmente di un problema noto, relativo al [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)di bug. Assicurarsi di usare la versione stabile più recente di Visual Studio per Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Se si verificano problemi di registrazione nell'account, provare a eseguire le operazioni seguenti:

- Aprire l'applicazione keychain e in Category selezionare *password*. `deliver.`Cercare ed eliminare tutte le voci.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Errore durante l'aggiunta dell'account. Accedere con una password specifica per l'app "

Questo è dovuto al fatto che l'autenticazione a due fattori è abilitata per l'account. Assicurarsi di usare la versione stabile più recente di Visual Studio per Mac

### <a name="failed-to-create-new-certificate"></a>Non è stato possibile creare il nuovo certificato
"È stato raggiunto il limite per i certificati di questo tipo"

![finestra di dialogo limite certificati](apple-account-management-images/image10.png)

È stato generato il numero massimo di certificati consentiti. Per risolvere il problema, passare al [centro per sviluppatori Apple](https://developer.apple.com/account/ios/certificate/distribution) e revocare uno dei certificati di produzione.

## <a name="known-issues"></a>Problemi noti

- Per impostazione predefinita, i profili di provisioning di distribuzione indirizzano all'App Store. È necessario creare manualmente profili interni o ad hoc.
