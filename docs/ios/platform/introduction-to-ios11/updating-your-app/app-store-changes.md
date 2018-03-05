---
title: Modifiche di App Store
description: "Esplorare le nuove funzionalità di iOS 11"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: d75c1393f2b5701226433235010a41da9c1aeb03
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="app-store-changes"></a>Modifiche di App Store

_Esplorare le nuove funzionalità di iOS 11_

App Store iOS ha avuto una riprogettazione completa, che non solo consente agli utenti di esplorare in modo efficiente store, ma consente inoltre, gli sviluppatori, per promuovere l'app agli utenti. Queste promozioni includono aggiornamenti di acquisti in-app e gli aggiornamenti a pagina del prodotto. iOS 11 aggiunge anche gli aggiornamenti relativi a come rilasciare l'app al pubblico, come aggiungere l'icona dell'app e come comunicare con gli utenti.

![Nuovo layout di archivio di app](app-store-changes-images/image3.jpg)

L'archivio di app riprogettato include le sezioni seguenti:

- **Oggi** : questa scheda include le App in primo piano app o un "pick dell'editor". Per alzare di livello app qui inserire informazioni sul [alzare di livello](https://developer.apple.com//contact/app-store/promote/) pagina.
- **Giochi** : qualsiasi App che è stata impostata la **gioco** categoria in iTunes Connect è reperibile in questa scheda.
- **App** : questa scheda offre tutte le altre app. Gli utenti possono cercare di App in evidenza, le categorie, superiore a pagamento o gratuito.
- **Aggiornamenti** : questa applicazione consente di visualizzare gli aggiornamenti alle app. Anche se si rilascia un'app tramite [fasi versione](#Phased_Release), gli utenti possono comunque accedere a questa scheda e scaricare la versione più recente.
- **Ricerca** : questa scheda consente agli utenti di cercare l'app.

## <a name="store-icon"></a>Icona di archivio

Icone dell'archivio (o marketing le icone) non vengono gestite in iTunes Connect e invece deve essere contenute come un [catalogo di asset](~/ios/app-fundamentals/images-icons/app-icons.md) in formato binario l'app, simile a icone di app. Un'icona di 1024 x 1024 archivio in formato PNG deve essere incluso in un catalogo di asset per l'invio ha esito positivo delle App iOS 11.

App assottigliamento assicura che il catalogo di asset aggiuntive non aumenta le dimensioni dell'app.


## <a name="in-app-purchases-promoted-in-the-app-store"></a>Acquisti in-App alzate di livello nell'App Store

Apple ha reso acquisti in-app risulta più facilmente individuabile nell'App Store. È ora possibile aggiungere fino a 20 _App Store alzate di livello_ acquisti in-app che si trova nella pagina App, nella pagina del prodotto dell'app oppure eseguendo una ricerca.

Per gli acquisti in-app visualizzati nell'App Store è necessario includere i dati seguenti:

- **Immagine** : È necessario fornire un'immagine appositamente progettata per l'icona che descrive lo scopo di acquisto in-app. Questa immagine deve essere diversa dall'icona dell'app e non può essere una schermata.
- **Nome** : il nome può essere solo un massimo di 30 caratteri.
- **Descrizione** : la descrizione può contenere solo un massimo di 45 caratteri.

Le promozioni di acquisto in-app sono soggetti a una revisione di app store prima di poter essere pubblicato.

Per rendere gli acquisti in-app disponibili per alzare di livello, aprire l'app e passare a **funzionalità > acquisti In-App**. Passare al **promozione di archivio di App (facoltativo)** sezione e aggiungere un'immagine di 1024 x 1024 e **salvare**, come illustrato nella figura seguente:

![Sezione di innalzamento di livello App Store in iTune Connect](app-store-changes-images/image4.png)

È inoltre necessario aggiungere il `ShouldAddStorePayment` metodo il `SKPaymentTransactionObserver` protocollo nell'app.

Per ulteriori informazioni sulle promozioni di acquisto in-app, vedere Apple [promozione di acquisti In-App](https://developer.apple.com/app-store/promoting-in-app-purchases/) pagina.

## <a name="redesigned-product-page"></a>Pagina prodotto riprogettato con

Le seguenti modifiche sono state apportate alla pagina del prodotto:

- I titoli sono ora impostati per un massimo di 30 caratteri
- È stato aggiunto un sottotitolo.
    - Deve essere un breve riepilogo che integra il titolo.
    - Sottotitolo deve avere un massimo di 30 caratteri
- Anteprime di App
    - È ora possibile avere tre schermate o video.
    - Video autoplay quando un utente visita la pagina.
    - Per ulteriori informazioni, vedere Apple [anteprima App](https://developer.apple.com/app-store/app-previews/) pagina.
- Testo promozionale
    - Questa nuova funzionalità offre 170 caratteri di testo che consente di descrivere le informazioni sul soggetto a modifiche frequenti sull'applicazione.
    - Può essere aggiornata in qualsiasi momento senza dover inviare una nuova versione dell'app.

## <a name="customer-communication"></a>Comunicazione cliente

In 10.3, Apple avviato un nuovo modo per gli sviluppatori di comunicare direttamente con gli utenti dell'applicazione grazie alla possibilità di rispondere alle revisioni. È possibile accedere a questa nuova funzionalità in iTunes connect passando a **App > attività > punteggi e recensioni**, come illustrato nella figura seguente:

![Finestra di dialogo che mostra l'area di immettere una risposta al commento](app-store-changes-images/image5.png)

Esistono alcuni aspetti da tenere presenti quando si risponde agli utenti:

- È possibile rispondere solo una volta, ma entrambe le parti modificabili di commenti come desiderato.
- Gli utenti ricevono una notifica quando si risponde a un commento.
- Un nuovo supporto clienti ruolo sia stato creato in iTunes connect. Gli utenti con questo ruolo o un ruolo di amministratore possono rispondere ai commenti.

Per ulteriori informazioni, vedere Apple [rispondere alle revisioni](https://developer.apple.com/app-store/responding-to-reviews/) pagina.


## <a name="phased-release"></a>Fasi di rilascio

Con iOS 11, Apple ha implementato l'opzione delle versioni graduale per gli aggiornamenti all'app. È possibile abilitare versioni graduale consentire l'aggiornamento dell'app, gradualmente rilascio ai clienti, verificare che la richiesta non esegue l'overload dell'ambiente di produzione.

Graduale versioni sono abilitate in iTunes Connect. Fare clic su app nella barra laterale, scorrere verso il **fasi di rilascio per gli aggiornamenti automatici** sezione nella parte inferiore, quindi selezionare **versione aggiornamento rispetto all'uso periodo di 7 giorni fasi versione**:

![Con opzione fasi di rilascio per gli aggiornamenti automatici](app-store-changes-images/image6.png)

L'aggiornamento è disponibile immediatamente per il download nella scheda aggiornamenti dell'App Store. Versioni graduale sono disponibili solo per gli utenti che dispongono di download automatico selezionato.


## <a name="related-links"></a>Collegamenti correlati

- [Novità in iOS 11 (mela)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (mela)](https://developer.apple.com/app-store/product-page/)
- [L'aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
