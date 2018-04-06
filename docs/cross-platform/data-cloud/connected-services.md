---
title: Connessi in Visual Studio per Mac
description: Aggiungere l'archiviazione dei dati di Azure, l'autenticazione e le notifiche push App per dispositivi mobili da Visual Studio per Mac
ms.prod: xamarin
ms.assetid: ADDFB3A5-DB6A-417C-8ACE-33D107FB75C2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b9aaa197ccf01c59d6e4bbb0476d10295a108f89
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="connected-services-walkthrough"></a>Procedura dettagliata di servizi connessi

Il flusso di lavoro di servizi connessi consente il flusso di lavoro del portale Azure in Visual Studio per Mac, in modo non è necessario lasciare il progetto per aggiungere servizi.

Questa procedura dettagliata viene illustrato come aggiungere un servizio back-end di Azure, che consente l'archiviazione dei dati cloud, l'autenticazione e le notifiche push a un'applicazione libreria xamarin. Forms classe portabile (PCL) multipiattaforma.


1.  Avviare facendo doppio clic su di **servizi connessi** nodo della soluzione, verrà visualizzata la **raccolta Services**.
  Questo è un elenco di tutti i servizi disponibili per questo tipo di applicazione. Selezionare un servizio (ad esempio **back-end per dispositivi mobili con Azure App Service**) facendo clic su di esso.

  [![](connected-services-images/image001-sml.png "Connesso il nodo servizi in Visual Studio per Mac")](connected-services-images/image001.png#lightbox)

2. Pagina dei dettagli del servizio contiene una descrizione del servizio e le dipendenze da installare.
  Fare clic su di **Aggiungi** pulsante per aggiungere le dipendenze per l'app:

  [![](connected-services-images/image002-sml.png "Back-end per dispositivi mobili con Azure")](connected-services-images/image002.png#lightbox)

3. Le dipendenze necessarie da aggiungere alla libreria di classi Portabile e i progetti specifici della piattaforma per funzionare.
  Selezionare le caselle di controllo per aggiungere il servizio a ogni progetto che farà riferimento (direttamente o indirettamente):

  [![](connected-services-images/image003-sml.png "Controllare tutti i progetti che devono fare riferimento al servizio")](connected-services-images/image003.png#lightbox)

4. Scegliere **Accept** sul **dell'accettazione della licenza** finestre di dialogo per i pacchetti NuGet.
  Potrebbero essere presenti due finestre di dialogo per accettare, uno per il MobileClient e le dipendenze e l'altro per SQLiteStore, che è necessario per la sincronizzazione dati non in linea:

  [![](connected-services-images/image004-sml.png "Accettare i contratti di licenza")](connected-services-images/image004.png#lightbox)

  ![](connected-services-images/image005.png "Finestra di accettazione licenza")

5. Dopo aver aggiunto le dipendenze, verrà richiesto di accedere con l'account che si desidera utilizzare per comunicare con Azure.
  Se si è già connessi con un ID Microsoft, verrà effettuato un tentativo di recuperare le sottoscrizioni di Azure e associati a tali servizi app Visual Studio per Mac. Se non si dispone di sottoscrizioni, è possibile aggiungere uno per effettuare l'iscrizione per una valutazione gratuita o acquistare un piano di sottoscrizione nel portale di Azure.

6. Selezionare un servizio app dall'elenco. Questo compilerà il codice del modello per il `MobileServiceClient` oggetto con l'URL corrispondente del servizio app in Azure:

  [![](connected-services-images/image006-sml.png "Selezionare il servizio app dall'elenco")](connected-services-images/image006.png#lightbox)

  Se non è elencato alcun servizio, scegliere il **New** pulsante (vedere il passaggio 9).

7. Copiare il codice del modello per il `MobileServiceClient` nella libreria di classi Portabile. App nel percorso file non è importante, purché sia presente solo un'istanza.
  L'approccio consigliato consiste nel creare un `AzureService` classe che gestisce tutte le interazioni di Azure e utilizza il `MobileServiceClient`:

  ![](connected-services-images/image007.png "Copiare il codice di configurazione nell'app")

8. Seguire la documentazione nel **passaggi successivi** per aggiungere dati, la sincronizzazione non in linea, l'autenticazione e le notifiche push all'App:

  [![](connected-services-images/image008-sml.png "Rivedere le istruzioni della procedura successiva")](connected-services-images/image008.png#lightbox)

10. Se non si dispone di qualsiasi servizio app esistente, è possibile creare nuovi servizi dall'interno di Visual Studio per Mac.
  Fare clic su di **New** pulsante nella parte inferiore sinistra dell'elenco di servizi per aprire la **nuovo servizio App** finestra di dialogo:

  [![](connected-services-images/image009-sml.png "Creare un nuovo servizio app in Visual Studio per Mac")](connected-services-images/image009.png#lightbox)

Un nuovo servizio richiede i seguenti parametri:

-   **Nome del servizio app** – nome/id univoco per il piano
-   **Sottoscrizione** – la sottoscrizione a cui si desidera utilizzare per pagare il servizio
-   **Gruppo di risorse** : un modo o organizzare tutte le risorse di Azure per un progetto. Opzione per utilizzare esistente o crearne uno nuovo. Se si tratta del primo servizio di Azure, crearne uno nuovo.
-   **Piano di servizio** : determina la posizione e il costo delle risorse che lo utilizzano. Opzione per utilizzare esistente o crearne uno nuovo. Se si tratta del primo servizio di Azure, utilizzare quello predefinito o crearne uno nuovo al livello gratuito (F1).

Visitare il [documentazione di Azure App Service](https://docs.microsoft.com/azure/app-service/) per ulteriori informazioni


## <a name="related-links"></a>Collegamenti correlati

- [Servizio app di Azure](https://docs.microsoft.com/en-us/azure/app-service/)
- [Note sulla versione](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Connected_Services)
