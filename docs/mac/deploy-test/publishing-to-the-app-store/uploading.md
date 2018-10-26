---
title: Caricamento in Mac App Store
description: Questo documento descrive come usare iTunes Connect per caricare un'app Xamarin.Mac in Mac App Store. Vengono esaminate le informazioni richieste da iTunes Connect per completare il processo.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fa5c8a72a469f1a798cbdcfc63c07860363f6608
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109848"
---
# <a name="upload-to-mac-app-store"></a>Caricamento in Mac App Store

_Questa guida descrive come caricare un'app Xamarin.Mac per la pubblicazione in Mac App Store._

Le applicazioni vengono inviate per l'approvazione in Mac App Store tramite [iTunes Connect](http://itunesconnect.apple.com/).

1. Scegliere un'**app macOS** da creare: 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Immettere il nome dell'applicazione e altri dettagli. Lo sviluppatore può scegliere solo da un **Bundle ID** (ID bundle) esistente creato in precedenza: 

    [![](uploading-images/image66.png "Selezione dell'ID bundle")](uploading-images/image66.png#lightbox)

3. Selezionare la data di disponibilità e il prezzo. Indipendentemente dalla data di disponibilità selezionata dallo sviluppatore, l'app sarà disponibile per la vendita solo dopo l'approvazione. Se lo sviluppatore desidera maggiore controllo sulla data di disponibilità effettiva, è possibile impostare questo valore su una data futura: 

    [![](uploading-images/image67.png "Impostazione della data di disponibilità e del prezzo")](uploading-images/image67.png#lightbox)

4. Immettere le informazioni relative all'app, inclusa la categoria a cui appartiene nell'App Store: 

    [![](uploading-images/image68.png "Immissione delle informazioni relative all'app")](uploading-images/image68.png#lightbox) 

    Selezionare le classificazioni applicabili: 

    [![](uploading-images/image69.png "Impostazione delle classificazioni dell'app")](uploading-images/image69.png#lightbox) 

    Descrizione, parole chiave e URL di contatto: 

    [![](uploading-images/image70.png "Modifica di descrizione, parole chiave e URL di contatto")](uploading-images/image70.png#lightbox) 

    Informazioni di contatto e consigli per i revisori dell'App Store: 

    [![](uploading-images/image71.png "Modifica delle informazioni di contatto e consigli per i revisori dell'App Store")](uploading-images/image71.png#lightbox) 

    E infine gli screenshot: 

    [![](uploading-images/image72.png "Aggiunta degli screenshot necessari")](uploading-images/image72.png#lightbox) 

    Gli screenshot devono essere in formato JPG, TIF o PNG con dimensioni di 1280x800, 1440x900, 2880x1800 o 2560x1600 pixel. Premere **Save** (Salva) per completare la procedura.

5. Le informazioni relative all'app vengono presentate per la revisione. Fare clic su **View Details** (Visualizza dettagli) per modificare lo stato: 

    [![](uploading-images/image73.png "Visualizzazione dei dettagli dell'app")](uploading-images/image73.png#lightbox)

6. Nella visualizzazione dei dettagli, fare clic su Ready to Upload Binary (Pronto per caricare il file binario) per inviare il file del pacchetto dell'applicazione: 

    [![](uploading-images/image74.png "Selezione di Ready to Upload Binary (Pronto per caricare il file binario)")](uploading-images/image74.png#lightbox)

7. Rispondere alla domanda sulla crittografia: 

    [![](uploading-images/image75.png "Risposta alla domanda sulla crittografia")](uploading-images/image75.png#lightbox)

8. Quando il sito è pronto ad accettare il file del pacchetto dell'applicazione viene visualizzato un avviso: 

    [![](uploading-images/image76.png "Notifica di accettazione")](uploading-images/image76.png#lightbox)

9. Avviare Application Loader e verificare di aver eseguito l'accesso con l'ID Apple.
Scegliere **Deliver Your App** (Invia l'app) per continuare: 

    [![](uploading-images/image77.png "Interfaccia di Application Loader")](uploading-images/image77.png#lightbox)

10. Selezionare l'applicazione dall'elenco delle applicazioni il cui stato è **Ready to Upload Binary** (Pronto per caricare il file binario) e fare clic su **Next** (Avanti): 

    [![](uploading-images/image78.png "Selezione dell'app da caricare")](uploading-images/image78.png#lightbox)

11. Esaminare i metadati dell'applicazione e fare clic su **Choose...**  (Scegli...) per individuare il file del pacchetto: 

    [![](uploading-images/image79.png "Revisione dei metadati dell'app")](uploading-images/image79.png#lightbox)

12. Individuare il file del pacchetto creato in Visual Studio per Mac usando la configurazione della build dell'App Store: 

    [![](uploading-images/image80.png "Selezione del file da caricare")](uploading-images/image80.png#lightbox)

13. Fare clic su **Send** (Invia): 

    [![](uploading-images/image81.png "Invio dell'app")](uploading-images/image81.png#lightbox)

14. Il pacchetto verrà convalidato ed eventuali errori verranno segnalati. Correggere gli errori e ricaricare il pacchetto. Al termine del caricamento, l'app verrà automaticamente inviata per la revisione da parte del team dell'App Store: 

    [![](uploading-images/image82.png "Esempio di errori di caricamento")](uploading-images/image82.png#lightbox)

Dopo l'approvazione, l'applicazione sarà disponibile per il download o l'acquisto da Mac App Store.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](~//mac/get-started/installation.md)
- [Esempio Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guida degli strumenti: firma del codice dell'app](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/resources/developer-id/)
