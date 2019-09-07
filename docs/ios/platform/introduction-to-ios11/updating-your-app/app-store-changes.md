---
title: Modifiche all'App Store in iOS 11
description: Questo documento illustra le modifiche apportate all'App Store in iOS 11. Viene illustrata l'icona dello Store di un'applicazione, gli acquisti in-app promossi, la pagina di prodotto riprogettata, la comunicazione dei clienti e le versioni a fasi.
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 0ac9b486defb74cac7ccd946d2b35b283e6aeca5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752320"
---
# <a name="app-store-changes-in-ios-11"></a>Modifiche all'App Store in iOS 11

IOS App Store ha avuto una riprogettazione completa, che non solo consente agli utenti di spostarsi in modo efficiente nello Store, ma consente anche agli sviluppatori di promuovere l'app agli utenti. Queste promozioni includono aggiornamenti per gli acquisti in-app e gli aggiornamenti della pagina del prodotto. iOS 11 aggiunge anche aggiornamenti per la comunicazione con gli utenti, come aggiungere l'icona dell'app e come rilasciare l'app al pubblico.

![Nuovo layout dell'app Store](app-store-changes-images/image3.jpg)

L'App Store riprogettata include le sezioni seguenti:

- **Oggi** : questa scheda include le app che sono un'app in primo piano o un "Editor ' s pick". Per innalzare di livello l'app, inserire le informazioni nella pagina [Promote](https://developer.apple.com//contact/app-store/promote/) .
- **Giochi** : tutte le app impostate per la categoria **Game** in iTunes Connect sono disponibili in questa scheda.
- **App** : questa scheda presenta tutte le altre app. Gli utenti possono sfogliare le app in primo piano, le categorie, le prime a pagamento/gratuite.
- **Aggiornamenti** : questa app Visualizza gli aggiornamenti per le app. Anche se si rilascia un'app tramite il rilascio in più [fasi](#Phased_Release), gli utenti possono comunque accedere a questa scheda e scaricare la versione più recente.
- **Cerca** : questa scheda consente agli utenti di cercare l'app.

## <a name="store-icon"></a>Icona archivio

Le icone dello Store (o le icone di marketing) non sono più gestite in iTunes Connect e devono invece essere contenute come [Catalogo asset](~/ios/app-fundamentals/images-icons/app-icons.md) nel file binario dell'app, in modo analogo alle icone dell'app. Per inviare correttamente le app iOS 11, è necessario includere in un catalogo asset un'icona di archivio 1024 x 1024 in formato PNG.

Il DILUENTE delle app assicura che questo catalogo asset aggiuntivo non aumenti le dimensioni dell'app.

## <a name="in-app-purchases-promoted-in-the-app-store"></a>Acquisti in-app promossi nell'App Store

Apple ha reso più individuabili gli acquisti in-app nell'App Store. È ora possibile aggiungere fino a 20 acquisti in-app _promossi di App Store_ che ora possono essere trovati nella pagina app, nella pagina del prodotto dell'app o eseguendo una ricerca.

Per visualizzare gli acquisti in-app nell'App Store, è necessario includere i dati seguenti:

- **Image (immagine** ): è necessario fornire un'immagine appositamente progettata per l'icona che descrive l'operazione di acquisto in-app. Questa immagine deve essere diversa dall'icona dell'app e non può essere una schermata.
- **Nome** : il nome può essere costituito solo da un massimo di 30 caratteri.
- **Descrizione** : la descrizione può contenere un massimo di 45 caratteri.

Eventuali promozioni di acquisto in-app sono soggette a una revisione dell'app Store prima di poterla pubblicare.

Per rendere disponibili gli acquisti in-app per alzare di livello, aprire l'app e passare a **funzionalità > acquisti in-app**. Passare alla sezione **Promozione App Store (facoltativo)** e aggiungere un'immagine 1024 x 1024 e **salvare**, come illustrato nell'immagine seguente:

![Sezione Promozione App Store in iTune Connect](app-store-changes-images/image4.png)

È anche necessario aggiungere il `ShouldAddStorePayment` metodo `SKPaymentTransactionObserver` al protocollo nell'app.

Per altre informazioni sulle promozioni di acquisto in-app, vedere la pagina relativa alla promozione della pagina [acquisti in-app](https://developer.apple.com/app-store/promoting-in-app-purchases/) di Apple.

## <a name="redesigned-product-page"></a>Pagina del prodotto riprogettata

Sono state apportate le modifiche seguenti alla pagina del prodotto:

- I titoli sono ora impostati su un massimo di 30 caratteri
- È stato aggiunto un sottotitolo
  - Si tratta di un breve riepilogo che complimenta il titolo.
  - Il sottotitolo deve avere un massimo di 30 caratteri
- Anteprime delle app
  - È ora possibile avere tre video o schermate.
  - Video AutoPlay quando un utente visita la pagina.
  - Per altre informazioni, vedere la pagina di [anteprima dell'app](https://developer.apple.com/app-store/app-previews/) Apple.
- Testo promozionale
  - Questa nuova funzionalità fornisce 170 caratteri di testo che consentono di descrivere le informazioni che cambiano di frequente sull'app.
  - Può essere aggiornato in qualsiasi momento senza dover inviare una nuova versione dell'app.

## <a name="customer-communication"></a>Comunicazione con i clienti

In 10,3, Apple ha lanciato un nuovo modo per consentire agli sviluppatori di comunicare direttamente con gli utenti delle app attraverso la possibilità di rispondere alle revisioni. È possibile accedere a questa nuova funzionalità in iTunes Connect passando ad **App > Activity > ratings and Revisios**, come illustrato nell'immagine seguente:

![Finestra di dialogo che mostra l'area in cui immettere la risposta al commento](app-store-changes-images/image5.png)

Ci sono alcuni aspetti da tenere presenti quando si risponde agli utenti:

- È possibile rispondere solo una volta, ma entrambe le parti possono modificare i commenti nel modo desiderato.
- Gli utenti ricevono una notifica quando si risponde a un commento.
- In iTunes Connect è stato creato un nuovo ruolo di supporto clienti. Gli utenti con questo ruolo o un ruolo di amministratore possono rispondere ai commenti.

Per altre informazioni, vedere la pagina relativa [alle revisioni](https://developer.apple.com/app-store/responding-to-reviews/) di Apple.

<a name="Phased_Release"/>

## <a name="phased-release"></a>Versione graduale

Con iOS 11, Apple ha implementato l'opzione delle versioni in più fasi per gli aggiornamenti dell'app. È possibile abilitare le versioni in più fasi per consentire il rilascio graduale dell'aggiornamento dell'app ai clienti, assicurando che la richiesta non sovraccarichi l'ambiente di produzione.

Le versioni in più fasi sono abilitate in iTunes Connect. Fare clic sull'app nella barra laterale, scorrere fino al **rilascio in più fasi per aggiornamenti automatici** sezione nella parte inferiore e selezionare **versione aggiornamento per un periodo di 7 giorni usando la versione in più fasi**:

![Opzione che mostra la versione in più fasi per gli aggiornamenti automatici](app-store-changes-images/image6.png)

L'aggiornamento è immediatamente disponibile per il download nella scheda aggiornamenti dell'app Store. Le versioni in più fasi sono disponibili solo per gli utenti che hanno selezionato download automatici.

## <a name="related-links"></a>Collegamenti correlati

- [Novità di iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto App Store aggiornata (Apple)](https://developer.apple.com/app-store/product-page/)
- [Aggiornamento dell'app per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
