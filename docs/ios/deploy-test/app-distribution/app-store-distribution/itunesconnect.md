---
title: Configurazione di un'app in iTunes Connect
description: In questo articolo vengono illustrati i passaggi necessari per configurare e gestire un'applicazione Xamarin.iOS in iTunes Connect in modo che possa essere rilasciata per la distribuzione nell'App Store.
ms.prod: xamarin
ms.assetid: 74587317-4b15-4904-9582-dcd914827cbc
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ff4c72eaaf856083ab8e2e240ac8ce3eff37470c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669638"
---
# <a name="configuring-an-app-in-itunes-connect"></a>Configurazione di un'app in iTunes Connect

> [!IMPORTANT]
> Apple [ha indicato](https://developer.apple.com/ios/submit/) che a partire da marzo 2019 tutte le app e gli aggiornamenti inviati all'App Store devono essere stati compilati con iOS 12.1 SDK o versioni successive, disponibili in Xcode 10.1 o versioni successive.
> Le app devono anche supportare iPhone Xs e le dimensioni dello schermo 12,9" di iPad Pro.

iTunes Connect comprende una serie di strumenti basati sul Web, con i quali, tra le altre cose, è possibile gestire le applicazioni iOS nell'App Store. È necessario che l'applicazione Xamarin.iOS sia correttamente impostata e configurata in iTunes Connect prima di poter inviare l'app ad Apple per la revisione e, infine, essere rilasciata come app gratuita o in vendita nell'App Store.

iTunes Connect può essere usato per eseguire le operazioni seguenti:

- Impostare il nome dell'applicazione (il nome visualizzato nell'App Store).
- Definire screenshot o video dell'applicazione in azione nei dispositivi iOS che la supportano.
- Specificare una descrizione chiara e concisa dell'applicazione che comprende le funzionalità e i vantaggi offerti all'utente finale.
- Definire categorie e sottocategorie per facilitare all'utente la ricerca dell'app nell'App Store.
- Definire una parola chiave che possa ulteriormente aiutare l'utente a individuare l'app.
- Specificare gli URL di contatto e supporto al sito Web (richiesti da Apple).
- Impostare una classificazione dell'applicazione necessaria per il controllo genitori nell'App Store.
- Selezionare un prezzo di vendita o specificare che l'applicazione viene rilasciata gratuitamente.
- Configurare le tecnologie dell'App Store facoltative, ad esempio Game Center e Acquisto in-app.

La grafica dell'app deve essere in alta risoluzione e accattivante nel caso in cui Apple decidesse di offrirla come funzionalità nell'App Store. Per altre informazioni, vedere la [Guida per sviluppatori di iTunes Connect](https://developer.apple.com/support/itunes-connect/).

## <a name="managing-agreements-tax-and-banking"></a>Gestione di contratti, imposte e banche

Nella sezione **Agreements, Tax, and Banking** (Contratti, imposte e banche) vengono specificate le informazioni finanziarie necessarie relative a pagamenti e ritenute d'acconto degli sviluppatori iTunes. Qui è possibile tenere traccia dei contratti in essere con Apple. Prima di poter rilasciare un'applicazione iOS nell'App Store, sia che si tratti di app gratuita che di app in vendita, è necessario essere in possesso dei contratti appropriati e aver accettato eventuali modifiche ai contratti esistenti.

[![](itunesconnect-images/agreement01.png "Gestione di contratti, imposte e banche")](itunesconnect-images/agreement01.png#lightbox)

Da qui è possibile:

- Specificare un **agente del team** e definire altri ruoli utente per l'account di iTunes Connect come **Admin** (Amministratore) o **Finance** (Finanza).
- Registrare e gestire i **contratti** che consentono a un'organizzazione di distribuire le applicazioni nell'App Store.
- Specificare il nome della **persona giuridica** (il nome del venditore nell'App Store) usato per creare corrispondenza con le informazioni su contratti, banche e imposte associate all'organizzazione.
- Specificare le informazioni su **banche** e **imposte** se le applicazioni saranno vendute nell'App Store.

Anche queste informazioni _devono_ essere impostate correttamente e devono essere aggiornate prima di poter inoltrare un'applicazione iOS a iTunes Connect per la revisione e il rilascio. Per altre informazioni, vedere la documentazione sulla [gestione di contratti, imposte e banche](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/ManagingContractsandBanking.html#//apple_ref/doc/uid/TP40011225-CH21-SW1) di Apple.

<a name="creating" />

## <a name="creating-an-itunes-connect-record"></a>Creazione di un record iTunes Connect

Prima di poter caricare in iTunes Connect un'applicazione Xamarin.iOS che sarà distribuita tramite l'App Store, è necessario creare un record per l'applicazione in iTunes Connect. Questo record include tutte le informazioni sull'applicazione, come sarà visualizzata nell'App Store (in tutte le lingue necessarie) e tutte le informazioni necessarie per gestire l'app tramite il processo di distribuzione. Questo record viene anche usato per configurare le tecnologie dell'App Store, ad esempio iAd App Network o Game Center.

Per aggiungere un'applicazione iOS in iTunes Connect è necessario essere un **agente del team** o avere un ruolo **Amministratore** o **Tecnico**.

Eseguire le operazioni seguenti [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Fare clic su **My Apps** (App personali):

    [![](itunesconnect-images/add01.png "Fare clic su My Apps (App personali)")](itunesconnect-images/add01.png#lightbox)
2. Fare clic sul segno **+** nell'angolo superiore sinistro e selezionare **New iOS App** (Nuova app iOS):

    [![](itunesconnect-images/add02.png "Aggiunta di una nuova app iOS")](itunesconnect-images/add02.png#lightbox)
3. iTunes Connect visualizzerà la finestra di dialogo **New iOS App** (Nuova app iOS):

    [![](itunesconnect-images/add03.png "Finestra di dialogo di una nuova app iOS")](itunesconnect-images/add03.png#lightbox)
4. Immettere **Name** (Nome) e **Version Number** (Numero versione) dell'applicazione come dovranno essere visualizzati nell'App Store.
5. Selezionare una lingua in **Primary Language** (Lingua principale).
6. Immettere un numero **SKU**, vale a dire un identificatore univoco e costante che verrà usato per tenere traccia dell'applicazione. Non verrà visualizzato all'utente finale e _non può_ essere modificato dopo aver creato l'app.
7. Selezionare **Bundle ID** (ID bundle) per l'applicazione creata nel centro per sviluppatori quando è stato effettuato il provisioning dell'applicazione. Questo stesso ID Bundle servirà per la firma del pacchetto di distribuzione in Visual Studio per Mac o Visual Studio. Per altre informazioni, vedere la documentazione [Creating a Distribution Profile](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) (Creazione di un profilo di distribuzione) e [Selecting a Distribution Profile in a Xamarin.iOS Project](~/ios/get-started/installation/device-provisioning/index.md) (Selezione di un profilo di distribuzione in un progetto Xamarin.iOS).
8. Fare clic sul pulsante **Create** (Crea) per compilare il nuovo record iTunes Connect per l'applicazione.

La nuova applicazione verrà creata in iTunes Connect e sarà pronta per essere completata con le informazioni necessarie, ad esempio descrizione, prezzi, categorie, classificazioni e così via.:

[![](itunesconnect-images/add04.png "La nuova applicazione sarà creata in iTunes Connect")](itunesconnect-images/add04.png#lightbox)

<a name="managing" />

## <a name="managing-app-videos-and-screenshots"></a>Gestione di video e screenshot dell'app

Ottimi screenshot ed eventualmente video di anteprima sono tra gli elementi più importanti che assicurano il successo sul mercato dell'applicazione iOS nell'App Store. Usare visualizzazioni reali dell'applicazione in esecuzione che evidenziano l'interazione dell'utente e illustrano le straordinarie funzionalità dell'app. Usare video di anteprima dell'applicazione per dare agli utenti un'idea sull'uso dell'applicazione.

Per l'acquisizione degli screenshoot, eseguire le operazioni seguenti suggerite da Apple:

- Ottimizzare lo screenshot per una presentazione ottimale nei dispositivi iOS supportati dall'applicazione e verificare che il contenuto sia leggibile.
- Non incorniciare lo screenshot in un'immagine del dispositivo iOS.
- Acquisire la visualizzazione reale dell'applicazione usando lo schermo intero, senza usare elementi grafici o bordi che circondano lo screenshot.
- Rimuovere sempre la barra di stato dagli screenshot. Le dimensioni degli screenshot previsti da iTunes Connect escludono tale area.
- Quando possibile, acquisire screenshot nell'effettivo hardware Retina iOS ad alta risoluzione (non nel simulatore iOS).
- La prima schermata viene visualizzata come risultato della ricerca nell'App Store in iPhone e iPad, qualora non sia disponibile un video di anteprima dell'app. È quindi consigliabile usare per primo lo screenshot di migliore qualità.
- Usare tutti e cinque gli screenshot per raccontare la storia dell'applicazione evidenziando al tempo stesso i momenti che rendono l'app più interessante.

Apple richiede l'uso di schermate e video in tutte le dimensioni schermo e in tutte le risoluzioni supportate dall'applicazione. È anche necessario usare le versioni verticali e orizzontali, a seconda degli orientamenti supportati.

Attualmente sono richieste le dimensioni schermo e le risoluzioni seguenti:

[!include[](~/ios/includes/table-itunesconnect.md)]

### <a name="editing-screenshots-in-itunes-connect"></a>Modifica degli screenshot in iTunes Connect

Eseguire le operazioni seguenti [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Selezionare **My Apps** (App personali).
2. Fare clic sull'**icona** dell'applicazione.
3. Selezionare la scheda **Version** (Versioni).
4. Passare alla sezione **Screenshots** (Screenshot).
5. Selezionare **Image Size** (Dimensioni immagine) e trascinare le immagini necessarie (fino a 5 per ogni dimensione dello schermo):

    [![](itunesconnect-images/screenshot01.png "Selezionare le dimensioni dell'immagine e trascinare le immagini necessarie")](itunesconnect-images/screenshot01.png#lightbox)
6. Ripetere l'operazione per tutte le dimensioni dello schermo necessarie.
7. Fare clic sul pulsante **Save** (Salva) nella parte superiore dello schermo per salvare le modifiche.

> [!NOTE]
> Nota: Apple rifiuterà l'invio se gli screenshot o il video di anteprima dell'app non corrispondono all'effettiva funzionalità dell'applicazione.

<a name="metadata" />

## <a name="managing-name-description-whats-new-keywords-and-urls"></a>Gestione di nome, descrizione, novità, parole chiave e URL

In questa sezione del record applicazione di iTunes Connect sono raccolte le informazioni localizzate sull'applicazione, sul suo funzionamento, eventuali modifiche alle nuove versioni, le parole chiave usate nella ricerca, il supporto iAd e tutti gli URL di supporto.

### <a name="app-name"></a>Nome app

Scegliere un nome applicazione che sia descrittivo e rispecchi lo scopo dell'applicazione. È preferibile usare un nome breve e conciso. Il nome dell'applicazione gioca un ruolo importante nella ricerca utente. È quindi consigliabile che sia semplice e facile da ricordare. Controllare come viene visualizzato il nome in un dispositivo iOS (iPad, iPhone e iPod touch).

Per la scelta del nome di un'applicazione, attenersi alle linee guida seguenti suggerite da Apple:

- Usare un nome breve, semplice e facile da ricordare.
- Assicurarsi che non violi il copyright o i marchi di terze parti.
- Verificare che sia adatto alle funzionalità dell'applicazione.
- Localizzare i nomi per i mercati esteri quando opportuno.

### <a name="description"></a>Description

Scrivere una descrizione chiara, concisa e informativa dell'applicazione e delle sue funzionalità. Le prime righe sono le più importanti, devono dare una buona impressione e attirare l'utente. Descrivere ciò che rende speciale l'applicazione e la distingue da altre app simili.

Per la descrizione di un'applicazione, seguire i suggerimenti seguenti di Apple:

- Includere uno o due brevi paragrafi di apertura e un breve elenco puntato delle principali funzionalità.
- Localizzare le descrizioni per i mercati esteri quando opportuno.
- Se mai, includere recensioni, commenti positivi o testimonianze di utenti solo alla fine.
- Usare interruzioni di riga e punti elenco per facilitare la lettura.
- Controllare come viene visualizzata la descrizione dell'app nell'App Store in ogni tipo di dispositivo. Assicurarsi che le frasi più importanti della descrizione siano facilmente visibili.

### <a name="whats-new"></a>Novità

Quando si carica una versione nuova dell'applicazione, è necessario completare il campo disponibile **What’s New in this Version** (Novità della versione) in modo attento e scrupoloso.

Per l'immissione delle informazioni sulle novità, seguire queste linee guida suggerite da Apple:

- Aggiungere un messaggio per incoraggiare gli utenti a eseguire l'aggiornamento.
- Elencare gli elementi in ordine di importanza ed evidenziare le modifiche e le correzioni di bug.
- Illustrare le modifiche in un linguaggio semplice e comune anziché usare una terminologia tecnica.

### <a name="keywords"></a>Parole chiave

Usare parole chiave attente e strategiche che siano pertinenti alle funzionalità dell'applicazione, in modo che gli utenti possano individuare facilmente l'applicazione durante la ricerca nell'App Store. Se l'applicazione è impiegata per gli annunci iAd, la tecnologia iAd App Network usa le parole chiave durante la ricerca degli annunci nell'app.

Per la scelta delle parole chiave, seguire i suggerimenti seguenti di Apple:

- Non usare nomi di app concorrenti, nomi di società o di prodotti o nomi di marchi.
- Non usare parole o termini generici.
- Evitare termini inappropriati o inadeguati o parole non pertinenti, ad esempio nomi di personaggi famosi.
- Localizzare le parole chiave per i mercati esteri quando opportuno.

### <a name="urls"></a>URL

Apple richiede che lo sviluppatore indichi un collegamento al sito Web in cui possa offrire supporto per qualsiasi problema o rispondere alle domande che un utente potrebbe avere sull'applicazione. È anche necessario specificare un collegamento all'informativa sulla privacy dell'applicazione che deve essere disponibile nel sito Web.

È eventualmente possibile indicare un collegamento alle informazioni marketing disponibili nel sito Web per ottenere informazioni più dettagliate sull'applicazione rispetto a quelle accessibili nell'App Store.

### <a name="editing-name-description-whats-new-keywords-and-urls-in-itunes-connect"></a>Modifica di nome, descrizione, novità, parole chiave e URL in iTunes Connect

Eseguire le operazioni seguenti [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Selezionare **My Apps** (App personali).
2. Fare clic sull'**icona** dell'applicazione.
3. Selezionare la scheda **Version** (Versioni).
4. Passare alla sezione **Name** (Nome).
5. Immettere tutte le informazioni necessarie:

    [![](itunesconnect-images/name01.png "Modifica di nome, descrizione, novità, parole chiave e URL in iTunes Connect")](itunesconnect-images/name01.png#lightbox)
6. Fare clic sul pulsante **Save** (Salva) nella parte superiore dello schermo per salvare le modifiche.

> [!IMPORTANT]
> Nota: Apple rifiuterà l'invio se nome, descrizione, novità, parole chiave o URL non corrispondono all'effettiva funzionalità dell'applicazione.

<a name="general" />

## <a name="maintaining-general-app-information"></a>Gestione delle informazioni generali sull'app

In questa sezione del record applicazione di iTunes Connect è contenuto l'ID univoco dell'applicazione (come assegnato da Apple), le categorie di appartenenza dell'applicazione, la classificazione, il copyright e le informazioni sulla società che rilascia l'applicazione.

### <a name="app-icon"></a>Icona dell'app

> [!IMPORTANT]
>  Le icone dell'app non vengono più inviate tramite iTunes Connect. Devono essere inviate tramite il set di immagini **AppIcon** che si trova nel file **Assets.xcassets** del progetto. Per altre informazioni, vedere la guida [App Store Icon](~/ios/app-fundamentals/images-icons/app-store-icon.md) (Icona App Store).

L'icona dell'app rappresenta il volto dell'applicazione. Deve essere quindi facile da tenere in mente e ben visualizzabile in piccole dimensioni. Sono facilmente ricordabili le icone basiche, semplici e immediatamente riconoscibili.

Per la progettazione dell'icona di un'applicazione, attenersi alle linee guida seguenti suggerite da Apple:

- Creare un'icona appropriata per l'applicazione.
- Creare un'icona semplice che sia in linea con la progettazione dell'applicazione.
- Non usare parole all'interno dell'icona.
- Considerare un pubblico globale: un'unica icona dell'app viene usata in tutti i territori dello Store.

Per un'icona dell'app che sarà visualizzata nell'App Store, è necessaria un'immagine di 1024 x 1024 pixel.

Per altre informazioni, vedere il documento [iOS Human Interface Guidelines](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556) (Linee guida di iOS Human Interface) di Apple e la sezione relativa alle icone dell'app di grandi dimensioni nella documentazione [General App Information](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Appendices/Properties.html#//apple_ref/doc/uid/TP40011225-CH26-SW7) (Informazioni generali sull'app)

### <a name="app-id"></a>App ID

Si tratta di un numero di identificazione univoco assegnato all'applicazione da Apple quando viene creato il record iTunes Connect. È possibile usare questo numero per chiamare varie interfacce basate sul Web di Apple, tra cui le informazioni dell'App Store nel sito Web.

### <a name="version-number"></a>Numero versione

Si tratta della versione corrente e attiva dell'applicazione, che l'utente visualizza nell'App Store.

### <a name="category-and-sub-category"></a>Categoria e sottocategoria

Un aspetto importante per l'individuazione dell'applicazione è la categoria visualizzata nell'App Store. Le categorie consentono agli utenti di scorrere una raccolta di app per trovare quelle di loro interesse. iTunes Connect consente di assegnare fino a due diverse categorie quando si definisce l'applicazione. Scegliere con attenzione le categorie che descrivono al meglio la funzione principale dell'applicazione.

### <a name="ratings"></a>Classificazioni

Nell'App Store tutte le applicazioni devono avere una classificazione. Questa classificazione serve come informazione per il controllo genitori e limita l'accesso dei bambini a seconda del tipo e del contenuto dell'applicazione. Quando si definisce l'applicazione, iTunes Connect mette a disposizione un elenco di descrizioni del contenuto di cui è necessario indicare la frequenza di visualizzazione all'interno dell'applicazione. Queste selezioni vengono convertite in classificazione visualizzata nell'App Store.

Quando si creano applicazioni per bambini, l'App Store offre una categoria speciale per bambini sotto gli 11 anni. Classificare in modo appropriato il contenuto anche se l'applicazione non è pensata in modo specifico per i bambini in modo che i clienti possano fare la scelta giusta.

> [!IMPORTANT]
> Nota: Apple rifiuterà l'invio di applicazioni ritenute oscene, pornografiche, offensive o diffamatorie.

### <a name="copyright-and-company-information"></a>Informazioni sulla società e sul copyright

Apple consente di offrire informazioni sul copyright dell'applicazione e richiede informazioni sulla società che rilascia l'applicazione, ad esempio informazioni di contatto e l'indirizzo. Queste informazioni sono necessarie per le applicazioni rilasciate nell'App Store coreano. Queste informazioni vengono visualizzate nell'App Store come richiesto.

### <a name="editing-general-app-information-in-itunes-connect"></a>Modifica delle informazioni generali sull'app in iTunes Connect

Eseguire le operazioni seguenti [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Selezionare **My Apps** (App personali).
2. Fare clic sull'**icona** dell'applicazione.
3. Selezionare la scheda **Version** (Versioni).
4. Passare alla sezione **General App Information** (Informazioni generali sull'app).
5. Immettere tutte le informazioni necessarie:

    [![](itunesconnect-images/general01.png "Modifica delle informazioni generali sull'app in iTunes Connect")](itunesconnect-images/general01.png#lightbox)
6. Fare clic sul pulsante **Edit** (Modifica) in **Rating** (Classificazione) per impostare le informazioni sulla classificazione:

    [![](itunesconnect-images/general02.png "Modifica della classificazione")](itunesconnect-images/general02.png#lightbox)
6. Fare clic sul pulsante **Save** (Salva) nella parte superiore dello schermo per salvare le modifiche.

> [!NOTE]
> Nota: Apple rifiuterà l'invio se le categorie o le classificazioni non corrispondono all'effettiva funzionalità dell'applicazione.

<a name="game-center" />

## <a name="maintaining-game-center-information"></a>Gestione delle informazioni di Game Center

Per le app di gioco iOS che supportano Game Center di Apple, è possibile specificare informazioni quali le classifiche e gli obiettivi del gioco disponibili per l'utente. È anche possibile specificare se l'applicazione può essere usata in modalità multiplayer tramite una connessione di rete.

### <a name="editing-game-center-information-in-itunes-connect"></a>Modifica delle informazioni generali di Game Center in iTunes Connect

Eseguire le operazioni seguenti [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Selezionare **My Apps** (App personali).
2. Fare clic sull'**icona** dell'applicazione.
3. Selezionare la scheda **Version** (Versioni).
4. Passare alla sezione **Game Center**.
5. Posizionare il comando nella sezione **Game Center** nella posizione **On** (Attivato).
5. Immettere tutte le informazioni necessarie:

    [![](itunesconnect-images/gamecenter01.png "Modifica delle informazioni generali di Game Center in iTunes Connect")](itunesconnect-images/gamecenter01.png#lightbox)
6. Fare clic sul pulsante **Save** (Salva) nella parte superiore dello schermo per salvare le modifiche.

Usare la scheda **Game Center** per attivare Game Center e gestire le **classifiche** o gli **obiettivi** di questa applicazione:

[![](itunesconnect-images/gamecenter02.png "Attivare Game Center")](itunesconnect-images/gamecenter02.png#lightbox)

[![](itunesconnect-images/gamecenter03.png "Gestire classifiche oppure obiettivi di questa applicazione")](itunesconnect-images/gamecenter03.png#lightbox)

## <a name="maintaining-app-review-information"></a>Gestione delle informazioni sulle recensioni dell'app

Usare questa sezione per comunicare al personale Apple che revisionerà l'applicazione le informazioni necessarie, ad esempio le informazioni di contatto (qualora il tecnico avesse delle domande), gli account dimostrativi necessari ed eventuali note che consentono al tester di revisionare l'app correttamente.

### <a name="editing-app-review-information-in-itunes-connect"></a>Modifica delle informazioni sulla revisione dell'app in iTunes Connect

Eseguire le operazioni seguenti [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Selezionare **My Apps** (App personali).
2. Fare clic sull'**icona** dell'applicazione.
3. Selezionare la scheda **Version** (Versioni).
4. Passare alla sezione **App Review Information** (Informazioni sulla revisione delle app).
5. Immettere tutte le informazioni necessarie:

    [![](itunesconnect-images/review01.png "Modifica delle informazioni sulla revisione dell'app in iTunes Connect")](itunesconnect-images/review01.png#lightbox)
6. Selezionare la modalità di rilascio dell'applicazione nell'App Store dopo che è stata revisionata:

    [![](itunesconnect-images/review02.png "Modifica delle informazioni sul rilascio in iTunes Connect")](itunesconnect-images/review02.png#lightbox)
6. Fare clic sul pulsante **Save** (Salva) nella parte superiore dello schermo per salvare le modifiche.


## <a name="maintaining-pricing-information"></a>Gestione delle informazioni sui prezzi

Se si prevede di rilasciare l'applicazione per la vendita, è necessario impostare il prezzo di vendita selezionando uno dei piani tariffari disponibili di Apple e la data in cui il prezzo specificato verrà applicato. Ad esempio, al momento della redazione di questo documento, il **livello 1** è il seguente:

[![](itunesconnect-images/price01.png "Gestione delle informazioni sui prezzi")](itunesconnect-images/price01.png#lightbox)

### <a name="educational-discount"></a>Sconto per uso didattico

Selezionare questa casella se si vuole che l'applicazione sia offerta a un prezzo promozionale agli istituti didattici che acquistano più copie in una sola volta. I dettagli sullo sconto sono disponibili nell'ultima versione del **Paid Application Agreement** (Contratto per applicazioni a pagamento) che deve essere firmato prima che gli istituti didattici possano accedere all'applicazione.

### <a name="custom-business-to-business-application"></a>Applicazione Business to Business personalizzata

Un'applicazione configurata come **applicazione Business to Business personalizzata** sarà disponibile solo ai clienti iscritti al **Volume Purchase Program** specificati in iTunes Connect e sarà disponibile solo nei territori opportuni, ad esempio negli Stati Uniti. I clienti degli Stati Uniti iscritti al Volume Purchase Program devono essere iscritti all' App Store Volume Purchase Program for Business.

Le applicazioni Business to Business personalizzate non sono disponibili agli istituti didattici o ai comuni clienti dell'App Store. Per altre informazioni sull'*App Store Volume Purchase Program for Business*, visitare la pagina delle [domande frequenti](http://vpp.itunes.apple.com/faq) di Apple. Per altre informazioni su come i clienti possono iscriversi al **Volume Purchase Program**, visitare la pagina dei [programmi di distribuzione](http://enroll.vpp.itunes.apple.com).

### <a name="editing-pricing-information-in-itunes-connect"></a>Modifica delle informazioni sui prezzi in iTunes Connect

Eseguire le operazioni seguenti [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Selezionare **My Apps** (App personali).
2. Fare clic sull'**icona** dell'applicazione.
3. Selezionare la scheda **Pricing** (Prezzi):

    [![](itunesconnect-images/price02.png "Modifica delle informazioni sui prezzi in iTunes Connect")](itunesconnect-images/price02.png#lightbox)
4. Selezionare **Availability Date** (Data disponibilità).
5. Selezionare il prezzo desiderato dall'elenco a discesa **Price Tier** (livello di prezzo).
5. Abilitare facoltativamente l'opzione **Educational Discounts** (Sconti per uso didattico).
6. Eventualmente definire l'applicazione come **Custom Business to Business Application** (Applicazione Business to Business personalizzata).
6. Fare clic sul pulsante **Save** (Salva) per salvare le modifiche.

<a name="iap" />

## <a name="maintaining-in-app-purchase-information"></a>Gestione delle informazioni sull'acquisto in-app

Se si prevede la vendita di prodotti in-app virtuali dall'applicazione (ad esempio nuovi livelli di gioco o funzionalità dell'applicazione) usare questa sezione per creare e gestire gli articoli in acquisto.

[![](itunesconnect-images/inapp01.png "Gestione delle informazioni sull'acquisto in-app")](itunesconnect-images/inapp01.png#lightbox)

Per altre informazioni sull'uso degli acquisti nell'app in un'applicazione Xamarin.iOS, vedere la documentazione Microsoft [In-App Purchasing](~/ios/platform/in-app-purchasing/index.md) (Acquisti in-app).

## <a name="viewing-application-reviews"></a>Visualizzazione delle recensione sull'applicazione

Dopo aver rilasciato l'applicazione all'App Store, gli utenti che acquistano o scaricano gratuitamente l'applicazione possono scrivere recensioni sull'applicazione e lasciare una classificazione a stelle. Usare questa sezione per visualizzare le recensioni. Ad esempio:

[![](itunesconnect-images/reviews01.png "Visualizzazione delle recensioni sull'applicazione")](itunesconnect-images/reviews01.png#lightbox)

## <a name="summary"></a>Riepilogo

Questo articolo descrive come usare iTunes Connect per preparare un'applicazione Xamarin.iOS per il rilascio nell'App Store e come gestire tutte le informazioni visualizzate relative all'applicazione nello Store.

## <a name="related-links"></a>Collegamenti correlati

- [Uso delle immagini](~/ios/app-fundamentals/images-icons/index.md)
- [iOS App Development Workflow Guide: Distributing Applications](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html) (Guida al flusso di lavoro di sviluppo di iOS: distribuzione di applicazioni)
- [App Store Submission Tips](https://developer.apple.com/appstore/resources/submission/tips.html) (Suggerimenti per l'invio nell'App Store)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida sulle recensioni nell'App Store)
- [iTunes Connect Developer Guide](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/About.html#//apple_ref/doc/uid/TP40011225-CH1-SW1) (Guida per sviluppatori di iTunes Connect)
