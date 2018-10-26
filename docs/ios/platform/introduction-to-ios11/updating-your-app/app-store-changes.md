---
title: Novità di App Store iOS 11
description: Questo documento illustra le modifiche per l'App Store di iOS 11. Illustra icona store di un'applicazione, innalzata di livello gli acquisti in-app, la pagina prodotto riprogettato con un', comunicazione con il cliente e versioni in più fasi.
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 022d6b5c3f85863352dd1343752e934240b357aa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113690"
---
# <a name="app-store-changes-in-ios-11"></a>Novità di App Store iOS 11

L'App Store iOS ha avuto una riprogettazione completa, che non solo consente agli utenti di passare in modo efficiente l'archivio, ma consente inoltre, gli sviluppatori per promuovere l'app agli utenti. Le promozioni includono gli aggiornamenti per gli acquisti in-app e aggiornamenti per la pagina del prodotto. iOS 11 aggiunge anche gli aggiornamenti relativi come comunicare con gli utenti, come aggiungere all'icona dell'app e come rilasciare l'app al pubblico.

![Nuovo layout di app store](app-store-changes-images/image3.jpg)

L'archivio app riprogettata include le sezioni seguenti:

- **Oggi** : questa scheda include le App in primo piano app o un "pick redattore". Per alzare di livello l'app in questo caso immettere informazioni sul [alzare di livello](https://developer.apple.com//contact/app-store/promote/) pagina.
- **Giochi** : qualsiasi App che è impostato sul **gioco** categoria in iTunes Connect sono disponibili in questa scheda.
- **Le app** : questa scheda include tutte le altre app. Gli utenti possono cercare di App in primo piano, categorie, superiore a pagamento o gratuita.
- **Gli aggiornamenti** : questa app consente di visualizzare gli aggiornamenti alle app. Anche se si rilascia un'app tramite [in più fasi di rilascio](#Phased_Release), gli utenti possono comunque accedere a questa scheda e scaricare la versione più recente.
- **Ricerca** : questa scheda consente agli utenti di cercare l'app.

## <a name="store-icon"></a>Icona Store

Le icone Store (o marketing le icone) non vengono più gestite in iTunes Connect e invece devono essere contenute sotto un' [catalogo di asset](~/ios/app-fundamentals/images-icons/app-icons.md) nel file binario app, simile alle icone dell'app. Un'icona di 1024 x 1024 archivio nel formato PNG deve essere incluso in un catalogo asset per l'invio ha esito positivo delle App iOS 11.

App assottigliamento assicura che il catalogo di asset aggiuntivi non aumenta le dimensioni dell'app.


## <a name="in-app-purchases-promoted-in-the-app-store"></a>Acquisti in-App alzare di livello l'App Store

Apple ha introdotto acquisti in-app più facilmente individuabile in Store l'App. È ora possibile aggiungere fino a 20 _Store App alzate di livello_ acquisti in-app sono ora disponibili nella pagina App, nella pagina del prodotto dell'app oppure eseguendo una ricerca.

Affinché gli acquisti in-app visualizzati in Store l'App è necessario includere i dati seguenti:

- **Immagine** : È necessario fornire un'immagine progettata appositamente per l'icona che descrive le operazioni eseguite l'acquisto in-app. Questa immagine deve essere diversa dall'icona dell'app e non può essere una schermata.
- **Nome** : il nome può contenere solo un massimo di 30 caratteri.
- **Descrizione** : la descrizione può contenere solo un massimo di 45 caratteri.

Le promozioni di acquisto in-app sono soggetti a una verifica dell'app store prima di poter essere pubblicata.

Per effettuare gli acquisti in-app disponibili per alzare di livello, aprire l'app e passare a **funzioni > acquisti In-App**. Andare alla **innalzamento di livello di App Store (facoltativo)** della sezione e aggiungere un'immagine di 1024 x 1024 e **salvare**, come illustrato nell'immagine seguente:

![Sezione di innalzamento di livello App Store di iTunes Connect](app-store-changes-images/image4.png)

È inoltre necessario aggiungere il `ShouldAddStorePayment` metodo per il `SKPaymentTransactionObserver` protocollo nell'app.

Per ulteriori informazioni sulla promozione di acquisto in-app, vedere di Apple [innalzamento di livello acquisti In-App Your](https://developer.apple.com/app-store/promoting-in-app-purchases/) pagina.

## <a name="redesigned-product-page"></a>Pagina prodotto riprogettato con

Le modifiche seguenti sono state apportate alla pagina del prodotto:

- I titoli vengono ora impostati su un massimo di 30 caratteri
- È stato aggiunto un sottotitolo
    - Deve essere un breve riepilogo che integra il titolo.
    - Sottotitoli in altre lingue dovrebbe avere un massimo di 30 caratteri
- Anteprime di App
    - È ora possibile avere tre schermate o video.
    - Video su autoplay quando un utente visita la pagina.
    - Per altre informazioni, vedere di Apple [anteprima dell'App](https://developer.apple.com/app-store/app-previews/) pagina.
- Testo promozionale
    - Questa nuova funzionalità fornisce 170 caratteri del testo consente di descrivere le informazioni sul soggetto a modifiche frequenti sull'app.
    - Può essere aggiornata in qualsiasi momento senza dover inviare una nuova versione dell'app.

## <a name="customer-communication"></a>Comunicazione con il cliente

Nel 10.3, Apple lanciato un nuovo modo per gli sviluppatori di comunicare direttamente con gli utenti dell'app grazie alla possibilità di rispondere alle revisioni. È possibile accedere a questa nuova funzionalità in iTunes connect passando al **App > attività > classificazioni e recensioni**, come illustrato nell'immagine seguente:

![Finestra di dialogo con l'area per l'inserimento di risposta per impostare come commento](app-store-changes-images/image5.png)

Esistono alcuni aspetti da tenere presenti quando si risponde agli utenti:

- È possibile rispondere solo una volta, ma entrambe le parti possono modificare i propri commenti come desiderato.
- Gli utenti ricevere una notifica quando l'utente risponde a un commento.
- Un nuovo supporto dei clienti ruolo è stato creato in iTunes connect. Gli utenti con questo ruolo o un ruolo di amministratore possono rispondere ai commenti.

Per altre informazioni, vedere di Apple [rispondere alle revisioni](https://developer.apple.com/app-store/responding-to-reviews/) pagina.

<a name="Phased_Release"/>

## <a name="phased-release"></a>Versione in più fasi

Con iOS 11, Apple ha implementato l'opzione delle versioni in più fasi per gli aggiornamenti all'app. È possibile abilitare le versioni in più fasi consentire l'aggiornamento dell'app essere rilasciate gradualmente per i clienti, assicurando che la richiesta non esegue l'overload dell'ambiente di produzione.

Le versioni in più fasi sono abilitate in iTunes Connect. Fare clic sull'app nell'intestazione laterale, scorrere fino al **in più fasi di rilascio per gli aggiornamenti automatici** sezione nella parte inferiore, quindi selezionare **versione aggiornamento rispetto all'uso periodo di 7 giorni in più fasi di rilascio**:

![Visualizzazione di opzione in più fasi di rilascio per gli aggiornamenti automatici](app-store-changes-images/image6.png)

L'aggiornamento è disponibile immediatamente per il download nella scheda degli aggiornamenti di Store l'App. Sono disponibili per gli utenti che dispongono di download automatici selezionate solo le versioni in più fasi.


## <a name="related-links"></a>Collegamenti correlati

- [What ' s New in iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
