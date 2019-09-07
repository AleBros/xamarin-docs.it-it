---
title: Nozioni di base e configurazione di acquisto in-app in Novell. iOS
description: Questo documento descrive gli acquisti in-app in Novell. iOS, illustrando le informazioni rilevanti su regole, configurazione e iTunes Connect.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: fc76a642c27b7a2c7f3483911be596f8d6cdd1ce
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752865"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Nozioni di base e configurazione di acquisto in-app in Novell. iOS

Per implementare gli acquisti in-app, è necessario che l'applicazione utilizzi l'API StoreKit sul dispositivo. StoreKit gestisce tutte le comunicazioni con i server iTunes di Apple per ottenere informazioni sui prodotti ed eseguire transazioni. Il profilo di provisioning deve essere configurato per l'acquisto in-app e le informazioni sul prodotto devono essere immesse in iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit gestisce tutte le comunicazioni con Apple, come illustrato in questo grafico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

L'uso dell'app Store per fornire l'acquisto in-app richiede l'installazione e la configurazione seguenti:

- **iTunes Connect** : configurazione dei prodotti per la vendita e la configurazione degli account utente sandbox per testare l'acquisto. È inoltre necessario fornire le informazioni bancarie e fiscali ad Apple in modo che possano ripartire i fondi raccolti per conto dell'utente.
- **portale di provisioning iOS** : creazione di un identificatore del bundle e abilitazione dell'accesso all'App Store per l'app.
- **Store Kit** -aggiunta di codice all'app per la visualizzazione di prodotti, l'acquisto di prodotti e il ripristino di transazioni.
- **Codice personalizzato** : per tenere traccia degli acquisti effettuati dai clienti e fornire i prodotti o i servizi acquistati. Potrebbe anche essere necessario implementare un processo sul lato server per convalidare le ricevute se i prodotti sono costituiti da contenuto scaricato da un server, ad esempio problemi relativi a libri e riviste.

Ci sono due "ambienti server" del kit di archiviazione:

- **Produzione** : transazioni con denaro reale. Accessibile solo tramite le applicazioni inviate e approvate da Apple. I prodotti di acquisto in-app devono anche essere rivisti e approvati prima che siano disponibili nell'ambiente di produzione.
- **Sandbox** : dove si verifica il test. I prodotti sono disponibili qui subito dopo la creazione (il processo di approvazione si applica solo all'ambiente di produzione). Per le transazioni in sandbox è necessario che gli utenti di test (non gli ID Apple reali) eseguano le transazioni.

## <a name="in-app-purchase-rules"></a>Regole di acquisto in-app

Non è possibile accettare altre forme di pagamento per i prodotti o i servizi digitali all'interno dell'app, né citarli o farvi riferimento dagli utenti all'interno di un'app. Ciò significa che non è possibile accettare carte di credito o PayPal quando l'acquisto in-app è il meccanismo di pagamento più appropriato. Un caso speciale per l'acquisto di prodotti digitali all'esterno dell'app, ma per l'uso nell'app, ad esempio l'acquisto di libri in un sito Web associato a un "account di accesso" specifico e l'uso di tale "account di accesso" nell'app, consente all'utente di accedere ai libri acquistati.
Le applicazioni che operano in questo modo non possono citare o collegarsi alla funzionalità di acquisto esterna: gli sviluppatori devono comunicare questa funzionalità ai propri utenti in altri modi, ad esempio tramite il marketing tramite posta elettronica o un altro canale diretto.

Tuttavia, poiché non è possibile usare gli acquisti in-app per i beni fisici, in tal caso è possibile usare un meccanismo di pagamento alternativo, ad esempio carta di credito, PayPal) dall'interno dell'app.

Apple deve approvare ogni prodotto prima di procedere alla vendita: il nome, la descrizione e una schermata del ' prodotto ' sono necessari per la revisione. I tempi di revisione del prodotto sono identici a quelli delle revisioni delle applicazioni.

Non è possibile scegliere alcun prezzo per il prodotto: è possibile selezionare solo un "piano tariffario" con un valore specifico in ogni paese/valuta supportato da Apple. Non è possibile avere un livello di prezzo diverso in mercati diversi.

## <a name="configuration"></a>Configurazione

Prima di scrivere il codice di acquisto in-app, è necessario eseguire alcune operazioni di configurazione in iTunes Connect ( [iTunesConnect.Apple.com](http://itunesconnect.apple.com)) e nel portale di provisioning iOS ( [Developer.Apple.com/iOS](https://developer.apple.com/iOS)).

Questi tre passaggi devono essere completati prima di scrivere il codice:

- **Account per sviluppatore Apple** : inviare le informazioni bancarie e fiscali ad Apple.
- **portale di provisioning di iOS** : assicurarsi che l'app abbia un ID app valido (non un carattere jolly con un asterisco *) e che sia abilitato per l'acquisto di app.
- **gestione delle applicazioni di iTunes Connect** : aggiungere i prodotti all'applicazione.

### <a name="apple-developer-account"></a>Account per sviluppatore Apple

Per la compilazione e la distribuzione di app gratuite è richiesta una configurazione minima in [iTunes Connect](https://itunesconnect.apple.com). Tuttavia, per vendere app a pagamento o acquisti in-app, è necessario fornire Apple con informazioni fiscali e bancarie. Fare clic su **agreements, Tax and Banking** dal menu principale riportato di seguito:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Fare clic su agreements, Tax and Banking dal menu principale")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

Per l'account sviluppatore deve essere attivo un contratto per **applicazioni a pagamento iOS** , come illustrato in questo screenshot:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "L'account sviluppatore deve avere un contratto di applicazioni a pagamento iOS attivo")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

Non sarà possibile testare le funzionalità di StoreKit fino a quando non si dispone di un contratto per **le applicazioni a pagamento iOS** : le chiamate StoreKit nel codice avranno esito negativo fino a quando Apple non avrà elaborato i **contratti, le imposte e le informazioni bancarie** .

### <a name="ios-provisioning-portal"></a>Portale di provisioning iOS

Le nuove applicazioni vengono configurate nella sezione **ID app** del **portale di provisioning iOS**. Per creare un nuovo ID app, passare al [centro membri del portale di provisioning iOS](https://developer.apple.com/membercenter/index.action), passare alla sezione **certificati, identificatori e profili** del portale e fare clic su **identificatori** in *app iOS*. Quindi, fare clic su "+" in alto a destra per generare un nuovo ID app.

Il modulo per la creazione di nuovi **ID app**

 ha un aspetto simile al seguente:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "Il modulo per la creazione di nuovi ID app")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Immettere un valore appropriato per la *Descrizione*, in modo da poter identificare facilmente questo ID app in un elenco. Per il *prefisso ID app*selezionare l'ID del team.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Formato identificatore bundle/suffisso ID app

È possibile usare qualsiasi stringa per l'identificatore del **bundle** (purché sia univoco nell'account). Tuttavia, Apple consiglia di seguire il formato DNS inverso anziché usare qualsiasi stringa arbitraria. L'applicazione di esempio che accompagna questo articolo usa com. Novell. storekit. testing per l'identificatore del bundle, ma sarebbe ugualmente valido usare un identificatore come my_store_example (anche se Apple non lo consiglia).

> [!IMPORTANT]
> Apple consente inoltre di aggiungere un asterisco jolly alla fine di un **identificatore del bundle** , in modo da poter usare un singolo ID app per più applicazioni, ma _non è possibile usare gli ID app con caratteri jolly per AppPurchase_. Un esempio di identificatore di bundle con carta jolly potrebbe essere com. Novell. *

#### <a name="enabling-app-services"></a>Abilitazione dei servizi app

Si noti che l' **acquisto in-app** verrà abilitato automaticamente nell'elenco dei servizi:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "L'acquisto in-app verrà abilitato automaticamente nell'elenco dei servizi")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Profili di provisioning

Creare i profili di provisioning di sviluppo e produzione normalmente, selezionando l'ID app configurato per l'acquisto in-app. Per ulteriori informazioni, vedere la pagina relativa al [provisioning](~/ios/get-started/installation/device-provisioning/index.md) e alla pubblicazione dei dispositivi iOS nelle guide all' [App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) .

## <a name="itunes-connect"></a>iTunes Connect

Fare clic su **app personali** in iTunes Connect per creare o modificare una voce di applicazione iOS. La pagina Panoramica dell'applicazione è illustrata di seguito:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "Pagina Panoramica dell'applicazione")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Fare clic su **acquisti in-app** per creare o modificare i prodotti per la vendita. Questa schermata mostra l'app di esempio con diversi prodotti già aggiunti:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "L'app di esempio con diversi prodotti è già stata aggiunta")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

Il processo per aggiungere nuovi prodotti è costituito da due passaggi:

1. Scegliere il tipo di prodotto:  [![](in-app-purchase-basics-and-configuration-images/image8.png "Scegliere il tipo di prodotto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2. Immettere gli attributi del prodotto, inclusi l'ID prodotto, il piano tariffario e le descrizioni localizzate:  [![](in-app-purchase-basics-and-configuration-images/image9.png "Immissione degli attributi dei prodotti")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

I campi necessari per ogni prodotto di acquisto in-app sono descritti di seguito:

### <a name="reference-name"></a>Nome riferimento

Il nome di riferimento non viene visualizzato agli utenti; è per uso interno e viene visualizzato solo in iTunes Connect.

### <a name="product-id-format"></a>Formato ID prodotto

Un identificatore di prodotto può contenere solo caratteri alfanumerici (A-Z, a-z, 0-9), caratteri di sottolineatura (_) e punto (.). Sebbene sia possibile usare qualsiasi stringa per gli identificatori, Apple consiglia il formato DNS inverso. Ad esempio, l'applicazione di esempio usa questo identificatore del bundle:

 `com.xamarin.storekit.testing`

La convenzione per identificare i prodotti di acquisto in-app, quindi, è la seguente:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Questa convenzione di denominazione non viene applicata, semplicemente una raccomandazione che consente di gestire i prodotti. Inoltre, nonostante la stessa convenzione DNS inversa, gli identificatori di prodotto *non sono correlati* all'identificatore del bundle e non devono iniziare con la stessa stringa. Sarebbe comunque valido usare gli identificatori come photo_product_greyscale (anche se Apple non lo consiglia).

L'ID prodotto non viene visualizzato agli utenti, ma viene usato per fare riferimento al prodotto nel codice dell'applicazione.

### <a name="product-type"></a>Tipo di prodotto

Sono disponibili cinque tipi di prodotto di acquisto in-app che è possibile offrire:

1. **Utilizzabile: elementi** che sono "usati", ad esempio valuta del gioco che il giocatore può spendere. Se l'utente esegue un backup o un ripristino o se il dispositivo viene aggiornato, non viene ripristinata anche una transazione utilizzabile (il che darebbe al giocatore lo stesso vantaggio). Il codice dell'applicazione deve assicurarsi di fornire l'elemento "utilizzabile" non appena la transazione viene completata.
1. **Non** utilizzabile: i prodotti che l'utente ' possiede ' ha acquistato, ad esempio un problema di rivista digitale o un livello di gioco.
1. **Sottoscrizioni rinnovate** automaticamente: Analogamente a una sottoscrizione di Magazine reale, al termine del periodo di sottoscrizione Apple addebita automaticamente il cliente ed estende il periodo di validità della sottoscrizione, per sempre o fino a quando non viene annullato esplicitamente dal cliente. Questo è il metodo di pagamento preferito per le app in edicola (infatti, le app devono supportare questo metodo di pagamento per essere approvate per la distribuzione in edicola).
1. **Sottoscrizione gratuita** : può essere offerta solo nelle app abilitate per l'edicola e consente al cliente di accedere al contenuto della sottoscrizione su tutti i dispositivi. Le sottoscrizioni gratuite non scadono mai.
1. **Sottoscrizione senza rinnovo** : è consigliabile usare per vendere un accesso limitato al tempo al contenuto statico, ad esempio l'accesso di un mese a un archivio foto.

 *Questo documento include attualmente solo i primi due tipi di prodotto (utilizzabili e non utilizzabili).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Piani tariffari

L'App Store non consente di scegliere un prezzo arbitrario per i prodotti, Apple fornisce piani tariffari fissi tra cui è possibile scegliere. I prezzi sono corretti in ogni valuta e Apple si riserva il diritto di modificare i prezzi relativi (ad esempio, dopo una modifica apportata al tasso di cambio estero corrispondente tra una determinata valuta e il dollaro USA).

Apple fornisce una tabella dei prezzi che consente di selezionare il livello corretto per la valuta e il prezzo desiderati. Di seguito è riportato un estratto della matrice dei prezzi (agosto 2012):

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Estratto della tabella dei prezzi agosto 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

Al momento della stesura (giugno 2013) sono presenti 87 livelli da 0,99 a USD 999,99. La matrice dei prezzi Mostra il prezzo che i clienti pagheranno e anche la quantità ricevuta da Apple. si tratta di un costo inferiore al 30% e di eventuali imposte locali che devono essere raccolti (si noti che nell'esempio i venditori statunitensi e canadesi ricevono 70C per un 99C p) Product, mentre i venditori australiani ricevono solo 63C a causa di &amp; "Tax Services Tax" addebitato sul prezzo di vendita.

I prezzi del prodotto possono essere aggiornati in qualsiasi momento, incluse le modifiche dei prezzi pianificate che hanno effetto su una data futura. Questo screenshot mostra come viene aggiunta una modifica del prezzo con data successiva: il prezzo viene temporaneamente modificato dal livello 1 al livello 3 solo per il mese di settembre:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Variazione del prezzo con data successiva in cui il prezzo viene temporaneamente modificato dal livello 1 al livello 3 solo per il mese di settembre")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Prodotti gratuiti non supportati

Sebbene Apple abbia fornito una speciale opzione di sottoscrizione gratuita per le app in edicola, non è possibile impostare un prezzo zero (gratuito) per qualsiasi altro tipo di acquisto in-app. Sebbene sia possibile modificare i prezzi (IE. Lower) per le promozioni di vendita, non è possibile effettuare acquisti in-app ' gratuiti ' tramite iTunes Connect.

### <a name="localization"></a>Localizzazione

In iTunes Connect è possibile immettere un nome e un testo di descrizione diversi per qualsiasi numero di lingue supportate. Ogni lingua può essere aggiunta/modificata tramite un popup:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Ogni lingua può essere aggiunta/modificata tramite un popup")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   

Quando si visualizzano le informazioni sul prodotto nell'app, il testo localizzato è disponibile per la visualizzazione tramite StoreKit. La visualizzazione della valuta deve essere localizzata anche per mostrare il simbolo e la formattazione decimali corretti. questa formattazione è illustrata più avanti nel documento.

### <a name="app-store-review"></a>Revisione dell'app Store

Uguale alle app: ogni prodotto viene rivisto da Apple prima di essere autorizzato a passare alla vendita. I prodotti possono essere rifiutati per contenuti inappropriati nel nome o nella descrizione oppure Apple potrebbe decidere di aver scelto il tipo di prodotto errato (ad esempio, è stato creato un problema relativo a un libro o una rivista ma è stato usato il tipo di prodotto utilizzabile. Le revisioni del prodotto possono richiedere fino a quando una revisione dell'app.

La prima volta che un'app viene inviata con l'acquisto in-app abilitata (se si tratta di una nuova app o se la funzionalità è stata aggiunta a una esistente), è necessario anche scegliere alcuni prodotti da inviare. Il portale di iTunes Connect richiederà di eseguire questa operazione, come illustrato in questo screenshot:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "Il portale di iTunes Connect richiederà di inviare anche alcuni prodotti")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   

L'applicazione e gli acquisti in-app verranno esaminati insieme, in modo che tutti i dati vengano approvati in una sola volta (in modo che l'app non possa entrare nello Store senza alcun prodotto approvato).

Dopo che la prima versione con la funzionalità di acquisto in-app è stata approvata, è possibile aggiungere altri prodotti e inviarli per la revisione in qualsiasi momento. È anche possibile scegliere di inviare una nuova versione insieme a specifici prodotti di acquisto in-app, usando la pagina dei **Dettagli della versione** come suggerito dal prompt.

Per ulteriori informazioni, vedere le [linee guida per la revisione dell'app Store](https://developer.apple.com/appstore/guidelines.html) .

 [Parte 2: Panoramica del kit di archiviazione e informazioni sul prodotto recupero](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
