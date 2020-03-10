---
title: Gestione degli account Apple
description: Questo documento descrive come usare le funzionalità di gestione degli account Apple in Visual Studio per Mac e Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: davidortinau
ms.author: daortin
ms.date: 03/05/2020
ms.openlocfilehash: 17607e09a141fd29cd81cde93d812b20e62a9af8
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946226"
---
# <a name="apple-account-management"></a>Gestione degli account Apple

L'interfaccia di gestione degli account Apple in Visual Studio fornisce un modo per visualizzare le informazioni per i team di sviluppo associati a un ID Apple. Consente di eseguire le operazioni seguenti:

- **Aggiungi account per sviluppatori Apple**
- **Visualizzare i certificati di firma e i profili di provisioning**
- **Creare nuovi certificati di firma**
- **Scaricare i profili di provisioning esistenti**

> [!IMPORTANT]
> Gli strumenti di Novell per la gestione degli account Apple visualizzano solo le informazioni sugli account per sviluppatori Apple a pagamento. Per informazioni su come testare un'app in un dispositivo senza un account per sviluppatore Apple a pagamento, vedere la Guida [relativa al provisioning gratuito per le app Novell. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

## <a name="requirements"></a>Requisiti

La gestione degli account Apple è disponibile in Visual Studio per Mac, Visual Studio 2019 e Visual Studio 2017 (versione 15,7 e successive). Per usare questa funzionalità, è necessario anche un account Apple Developer a pagamento. Altre informazioni sugli account per sviluppatori Apple sono disponibili nella Guida al [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .

> [!NOTE]
> Prima di iniziare, assicurarsi di accettare prima di tutto i contratti di licenza utente nel [portale Apple Developer](https://developer.apple.com/account/).

## <a name="add-an-apple-developer-account"></a>Aggiungere un account per sviluppatore Apple

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Passare a **Visual Studio > preferenze > account per sviluppatori Apple** e fare clic sul pulsante **+** per aprire la finestra di dialogo Accedi:

    ![AScreenshot della pagina degli account per sviluppatori Apple in Preferenze Visual Studio per Mac.](apple-account-management-images/add-account-vsm.png)

2. Immettere l'ID Apple e la password e quindi fare clic su **Accedi**. In questo modo, le credenziali vengono salvate nel Keychain sicuro del computer.

3. Selezionare **Consenti sempre** nella finestra di dialogo di avviso per consentire a Visual Studio di usare le credenziali:

    ![Consenti sempre la finestra di dialogo di avviso](apple-account-management-images/image4.png)

4. Una volta che l'account è stato aggiunto correttamente, verrà visualizzato l'ID Apple e i team di cui fa parte l'ID Apple:

    ![Finestra di dialogo account sviluppatore Apple con account aggiunti](apple-account-management-images/image5.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Se si usa Visual Studio 2017 o Visual Studio 2019 (versione 16,4 e precedenti), è necessario [associarlo a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) prima di procedere.

1. Passare a **strumenti > opzioni > novell > account Apple** e fare clic su **Aggiungi**:

    ![Screenshot della pagina degli account Apple nelle opzioni di Visual Studio.](apple-account-management-images/add-account-vsw.png)

2. Immettere l'ID Apple e la password e quindi fare clic su **login**.

3. Una volta che l'account è stato aggiunto, verranno visualizzati l'ID Apple e i team di cui fa parte l'ID Apple:

    ![Screenshot della pagina dell'account sviluppatore con gli account aggiunti.](apple-account-management-images/accounts-vsw.png)

-----

## <a name="view-signing-certificates-and-provisioning-profiles"></a>Visualizzare i certificati di firma e i profili di provisioning

Selezionare un team e fare clic su **Visualizza dettagli...** per aprire una finestra di dialogo in cui è visualizzato un elenco di identità di firma e di profili di provisioning installati nel computer.

La finestra di dialogo Dettagli team Visualizza un elenco di identità di firma, organizzate in base al tipo. La colonna **stato** consiglia se il certificato è: 

- **Valido** : l'identità di firma (sia il certificato che la chiave privata) viene installata nel computer e non è scaduta.

- **Non presente nel Keychain** : è presente un'identità di firma valida nel server Apple. Per installarlo nel computer, è necessario esportarlo da un altro computer. Non è possibile scaricare l'identità di firma dal portale Apple Developer perché non conterrà la chiave privata.

- **Chiave privata mancante** : nel Keychain viene installato un certificato senza chiave privata.

- **Scaduto** : il certificato è scaduto. È necessario rimuoverlo dal keychain.

  ![informazioni della finestra di dialogo Dettagli team](apple-account-management-images/image7.png)

## <a name="create-a-signing-certificate"></a>Creare un certificato di firma

Per creare una nuova identità di firma, fare clic su **Crea certificato** per aprire il menu a discesa e selezionare il [tipo di certificato](https://help.apple.com/xcode/mac/current/#/dev80c6204ec) che si desidera creare. Se si dispone delle autorizzazioni corrette, dopo alcuni secondi verrà visualizzata una nuova identità di firma.

Se un'opzione nell'elenco a discesa è disattivata e deselezionata, significa che non si dispone delle autorizzazioni di Team corrette per creare questo tipo di certificato.

## <a name="download-provisioning-profiles"></a>Scaricare i profili di provisioning

La finestra di dialogo Dettagli team Visualizza anche un elenco di tutti i profili di provisioning connessi all'account sviluppatore. È possibile scaricare tutti i profili di provisioning nel computer locale facendo clic su **Scarica tutti i profili**.


## <a name="troubleshoot"></a>Risoluzione dei problemi

- L'approvazione di un nuovo account per sviluppatore Apple può richiedere diverse ore. Non sarà possibile abilitare il provisioning automatico fino a quando l'account non è stato approvato.

- Se l'aggiunta di un account per sviluppatori Apple ha esito negativo con il messaggio `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`, assicurarsi che l'ID Apple usato disponga di un abbonamento a pagamento attivo al programma Apple Developer. Per usare un account Apple Developer a pagamento, vedere la Guida [relativa al provisioning gratuito per le app Novell. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

- Se il tentativo di creare un nuovo certificato di firma ha esito negativo con l'errore `You have reached the limit for certificates of this type`, è stato generato il numero massimo di certificati consentiti. Per risolvere il problema, passare al [centro per sviluppatori Apple](https://developer.apple.com/account/ios/certificate/distribution) e revocare uno dei certificati di produzione.

- Se si verificano problemi durante l'accesso all'account Visual Studio per Mac, una possibile correzione consiste nell'aprire l'applicazione keychain e in **Category** selezionare **password**. Cercare `deliver.` ed eliminare tutte le voci trovate.

## <a name="known-issues"></a>Problemi noti

- Per impostazione predefinita, i profili di provisioning di distribuzione indirizzano all'App Store. È necessario creare manualmente profili interni o ad hoc.
