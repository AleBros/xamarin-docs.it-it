---
title: Caricamento in Mac App Store
description: Questo documento descrive come usare iTunes Connect per caricare un'app Xamarin.Mac in Mac App Store. Vengono esaminate le informazioni richieste da iTunes Connect per completare il processo.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e2b25468255ff84a3fe79ed4fea913e04bf88687
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489368"
---
# <a name="upload-to-mac-app-store"></a>Caricamento in Mac App Store

_Questa guida descrive come caricare un'app Xamarin.Mac per la pubblicazione in Mac App Store._

Le applicazioni vengono inviate per l'approvazione in Mac App Store tramite [iTunes Connect](https://itunesconnect.apple.com/). Sarà inoltre necessario lo strumento [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) dall'App Store.

1. Scegliere un'**app macOS** da creare: 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Immettere il nome dell'applicazione e altri dettagli. Lo sviluppatore può scegliere solo da un **Bundle ID** (ID bundle) esistente creato in precedenza: 

    [![](uploading-images/image66.png "Selecting the bundle ID")](uploading-images/image66.png#lightbox)

3. Selezionare la data di disponibilità e il prezzo. Indipendentemente dalla data di disponibilità selezionata dallo sviluppatore, l'app sarà disponibile per la vendita solo dopo l'approvazione. Se lo sviluppatore desidera maggiore controllo sulla data di disponibilità effettiva, è possibile impostare questo valore su una data futura: 

    [![](uploading-images/image67.png "Setting the available date and price")](uploading-images/image67.png#lightbox)

4. Immettere le informazioni relative all'app, inclusa la categoria a cui appartiene nell'App Store: 

    [![](uploading-images/image68.png "Entering the app information")](uploading-images/image68.png#lightbox) 

    Selezionare le classificazioni applicabili: 

    [![](uploading-images/image69.png "Setting the app ratings")](uploading-images/image69.png#lightbox) 

    Descrizione, parole chiave e URL di contatto: 

    [![](uploading-images/image70.png "Editing the Description, keywords and contact URLs")](uploading-images/image70.png#lightbox) 

    Informazioni di contatto e consigli per i revisori dell'App Store: 

    [![](uploading-images/image71.png "Editing the contact information and advice for the App Store reviewers")](uploading-images/image71.png#lightbox) 

    E infine gli screenshot: 

    [![](uploading-images/image72.png "Adding the required screenshots")](uploading-images/image72.png#lightbox) 

    Gli screenshot devono essere in formato JPG, TIF o PNG con dimensioni di 1280x800, 1440x900, 2880x1800 o 2560x1600 pixel. Premere **Save** (Salva) per completare la procedura.

5. Le informazioni relative all'app vengono presentate per la revisione. Fare clic su **View Details** (Visualizza dettagli) per modificare lo stato: 

    [![](uploading-images/image73.png "Viewing the app details")](uploading-images/image73.png#lightbox)

6. Nella visualizzazione dei dettagli, fare clic su Ready to Upload Binary (Pronto per caricare il file binario) per inviare il file del pacchetto dell'applicazione: 

    [![](uploading-images/image74.png "Selecting Ready to Upload Binary")](uploading-images/image74.png#lightbox)

7. Rispondere alla domanda sulla crittografia: 

    [![](uploading-images/image75.png "Answering the cryptography question")](uploading-images/image75.png#lightbox)

8. Quando il sito è pronto ad accettare il file del pacchetto dell'applicazione viene visualizzato un avviso: 

    [![](uploading-images/image76.png "The acceptance notification")](uploading-images/image76.png#lightbox)

9. Avviare il **trasporto** e accedere con l'ID Apple, quindi scegliere **Aggiungi app**:

    [![](uploading-images/transporter01-sml.png "The Application Loader interface")](uploading-images/transporter01.png#lightbox)

    Seguire le istruzioni per caricare il pacchetto dell'app in iTunes Connect.

    > [!NOTE]
    > [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) sostituisce lo strumento di **Caricamento applicazioni** usato con Xcode 10 e versioni precedenti.
    > Il caricatore di applicazioni non è più disponibile in Xcode 11 o versione successiva.

Dopo l'approvazione, l'applicazione sarà disponibile per il download o l'acquisto da Mac App Store.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](~//mac/get-started/installation.md)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guida degli strumenti: firma del codice dell'app](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/resources/developer-id/)
