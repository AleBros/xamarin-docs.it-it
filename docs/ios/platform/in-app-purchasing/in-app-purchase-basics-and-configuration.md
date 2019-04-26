---
title: Nozioni di base di acquisto in-App e la configurazione di xamarin. IOS
description: Questo documento descrive gli acquisti in-app in xamarin. IOS, che illustrano le informazioni pertinenti sulle regole, la configurazione e iTunes Connect.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 267dac5b6aec263f1d8b69d81f34f732118c1802
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61406895"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Nozioni di base di acquisto in-App e la configurazione di xamarin. IOS

Implementazione di acquisti in-app richiede che l'applicazione a usare l'API di StoreKit nel dispositivo. StoreKit gestisce tutte le comunicazioni con i server di Apple iTunes per ottenere informazioni sul prodotto ed eseguire le transazioni. Il profilo di provisioning deve essere configurato per acquisti in-app e le informazioni sul prodotto devono essere immessi in iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit gestisce tutte le comunicazioni con Apple, come illustrato in questo grafico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

Uso di App Store per offrire acquisti in-app richiede l'installazione e la configurazione seguente:

-  **iTunes Connect** : configurazione di prodotti per la vendita e impostazione degli account utente di sandbox per testare l'acquisto. È necessario anche aver fornito le informazioni bancarie e fiscali ad Apple, in modo che possibile rimettere fondi raccolti per tuo conto.
-   **Portale di Provisioning iOS** : creazione di un identificatore del Bundle e abilitazione dell'accesso di App Store per l'app.
-  **Store Kit** – aggiunta di codice all'App per la visualizzazione dei prodotti, acquisto di prodotti e il ripristino delle transazioni.
-  **Codice personalizzato** : per rilevare gli acquisti effettuati dai clienti e fornire i prodotti o servizi che hai acquistato. È possibile inoltre necessario implementare un processo lato server per convalidare le conferme se i prodotti è costituito da contenuto scaricato da un server (ad esempio libri e riviste problemi).


Esistono due Store Kit "ambienti server":

-  **Ambiente di produzione** : le transazioni con denaro reale. Accessibile solo tramite applicazioni che sono state inviate e approvate da Apple. I prodotti di acquisto in-app devono essere anche esaminati e approvati prima che siano disponibili nell'ambiente di produzione.
-  **Sandbox** cui avviene l'attività di test. I prodotti sono disponibili qui immediatamente dopo la creazione (il processo di approvazione si applica solo all'ambiente di produzione). Le transazioni nell'ambiente sandbox richiedono agli utenti di test (ID Apple non reali) eseguire le transazioni.

## <a name="in-app-purchase-rules"></a>Regole di acquisto in-App

È possibile accettare le altre forme di pagamento per i prodotti digitali o i servizi all'interno dell'app, né trattarle o vi fanno riferimento gli utenti dall'interno di un'app. Ciò significa che non è possibile accettare carte di credito o di PayPal quando acquisti in-app è il meccanismo di pagamento più appropriato. C'è un caso speciale per l'acquisto di prodotti digitali all'esterno dell'app, ma per usare l'App, ad esempio l'acquisto di libri in un sito Web che sono associati a uno specifico "login" e l'uso di "accesso" nell'app consente all'utente l'accesso i libri acquistati.
Applicazioni che funzionano in questo modo non è possibile menzionare o sul collegamento per la funzionalità di acquisizione esterna: gli sviluppatori devono comunicare questa funzionalità agli utenti in altri modi (magari tramite attività di marketing tramite posta elettronica o alcuni altri su un canale diretto).

Tuttavia, poiché non è possibile usare acquisti in-app per beni, nel cui caso è possibile usare un meccanismo alternativo di pagamento (ad es. carta di credito, PayPal) all'interno dell'app.

Apple deve approvare ogni prodotto prima entrerà in vendita: nome, descrizione e uno screenshot del prodotto' ' è obbligatorio per la revisione. Tempi di revisione di prodotto sono gli stessi delle revisioni dell'applicazione.

Non è possibile scegliere qualsiasi prezzo del prodotto: è possibile selezionare solo un 'livello di prezzo' che ha un valore specifico in ogni paese/valuta che supporta Apple. È possibile avere un livello di prezzo diverso nei diversi mercati.

## <a name="configuration"></a>Configurazione

Prima di scrivere codice acquisti in-app è necessario eseguire alcune operazioni di configurazione in iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) e il portale di Provisioning iOS ( [developer.apple.com/iOS](https://developer.apple.com/iOS)).

Questi tre passaggi devono essere completati prima di scrivere codice:

-  **Account sviluppatore Apple** : inviare le informazioni bancarie e fiscali ad Apple.
-  **Portale di Provisioning iOS** : verificare che l'app ha un ID App valido (non un carattere jolly con un asterisco * in esso) e sono state nell'acquisto di App abilitate.
-  **iTunes Connect Application Management** – aggiungere prodotti all'applicazione.


### <a name="apple-developer-account"></a>Account per sviluppatore Apple

Creazione e la distribuzione di App gratuite richiede una configurazione minima nel [iTunes Connect](https://itunesconnect.apple.com), tuttavia per vendere a pagamento App o gli acquisti in-app è necessario fornire Apple con le informazioni bancarie e fiscali. Fare clic su **contratti, imposte e banche** dal menu principale illustrato di seguito:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Fare clic su contratti, imposte e banche dal menu principale")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

L'Account per sviluppatore deve avere un **applicazioni a pagamento iOS** contratto in effetti, come illustrato in questo screenshot:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "L'Account per sviluppatore deve avere un iOS applicazioni a pagamento del contratto in vigore")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

Non sarà in grado di testare qualsiasi funzionalità di StoreKit finché non possiedi un **applicazioni iOS a pagamento** contratto: StoreKit chiamate nel codice avranno esito negativo fino a quando non è elaborato Apple le **contratti, imposte e banche** informazioni.

### <a name="ios-provisioning-portal"></a>Portale di provisioning iOS

Le nuove applicazioni vengono impostate nel **App IDs** sezione del **portale di Provisioning iOS**. Per creare un nuovo ID App, visitare il [Member Center del portale di Provisioning iOS](https://developer.apple.com/membercenter/index.action), passare alla **certificati, identificatori e profili** sezione del portale e fare clic su **identificatori** sotto *delle App iOS*. Quindi, fare clic su "+" in alto a destra per generare un nuovo ID App.


Il form per la creazione di nuove **gli ID App**

 ha un aspetto simile al seguente:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "Il modulo per la creazione di nuovi ID App")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Immettere un nome appropriato per il *descrizione*, pertanto è possibile identificare facilmente questo ID App in un elenco. Per il *prefisso ID App*, selezionare l'ID del Team.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Formato di suffisso dell'ID bundle dell'App o l'identificatore

È possibile usare qualsiasi stringa desiderato per il **identificatore del Bundle** (purché sia univoca nel proprio account), tuttavia, Apple consiglia è conforme al formato DNS inverso anziché usare una stringa arbitraria. L'applicazione di esempio che accompagna questo articolo Usa com.xamarin.storekit.testing per l'identificatore del Bundle, ma sarà ugualmente corretto da usare un identificatore, ad esempio my_store_example (sebbene non consigliabile Apple).

> [!IMPORTANT]
> Apple consente inoltre con caratteri jolly asterisco da aggiungere alla fine di una **Bundle Identifier** in modo che un singolo ID dell'App può essere usato per più applicazioni, tuttavia _gli ID delle App con caratteri jolly non può essere usati perInAppPurchase_. Un esempio di identificatore del Bundle con caratteri jolly potrebbe essere com.xamarin.*

#### <a name="enabling-app-services"></a>Abilitazione di servizi App

Si noti che **acquisto In-App** verrà abilitata automaticamente nell'elenco dei servizi:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "Acquisto in-App verrà abilitata automaticamente nell'elenco dei servizi")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Profili di provisioning

Come si farebbe, in genere selezionando l'ID di App a cui è stata configurata per l'acquisto In-App, creare lo sviluppo e i profili di Provisioning di produzione. Vedere il [iOS Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) e [pubblicazione per l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) Guide per altre informazioni.

## <a name="itunes-connect"></a>iTunes Connect

Fare clic su **My Apps** in iTunes Connect per creare o modificare una voce di applicazione iOS. La pagina di panoramica dell'applicazione è illustrata di seguito:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "La pagina di panoramica dell'applicazione")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Fare clic su **acquisti In-App** per creare o modificare i prodotti in vendita. Questa schermata mostra l'app di esempio con diversi prodotti già aggiunti:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "L'app di esempio con diversi prodotti già aggiunto")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

Processo di aggiunta di nuovi prodotti prevede due passaggi:

1.   Scegliere il tipo di prodotto: [![](in-app-purchase-basics-and-configuration-images/image8.png "Scegliere il tipo di prodotto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   Immettere gli attributi del prodotto, tra cui l'Id prodotto, piano tariffario e le descrizioni localizzate: [![](in-app-purchase-basics-and-configuration-images/image9.png "Immettere gli attributi di prodotti")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

I campi necessari per ogni prodotto di acquisto in-app sono descritti di seguito:


### <a name="reference-name"></a>Nome riferimento

Il nome di riferimento non viene visualizzato agli utenti; è per uso interno e viene visualizzato solo in iTunes Connect.

### <a name="product-id-format"></a>Formato ID prodotto

Un identificatore del prodotto può contenere solo caratteri alfanumerici (alla Z, a-z, 0-9), un carattere di sottolineatura (_) e caratteri punto (.). Sebbene sia possibile usare qualsiasi stringa per gli identificatori degli, Apple consiglia il formato DNS inverso. Ad esempio, l'applicazione di esempio Usa questo identificatore del Bundle:

 `com.xamarin.storekit.testing`

Di conseguenza la convenzione per identificare i prodotti di acquisto in-app sarà come segue:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Non viene applicata questa convenzione di denominazione, è sufficiente una raccomandazione per la gestione dei prodotti. Inoltre, malgrado seguendo la stessa convenzione di DNS inverso, gli identificatori di prodotto sono *non correlati* per l'identificatore del Bundle e sono non è necessario per iniziare con la stessa stringa. Ma rimarrebbe comunque valido per l'utilizzo di identificatori come photo_product_greyscale (sebbene non consigliabile Apple).

ID prodotto non viene visualizzato agli utenti, ma viene usato per fare riferimento del prodotto nel codice dell'applicazione.

### <a name="product-type"></a>Tipo di prodotto

Esistono cinque tipi di prodotto di acquisto in-app che è possibile offrire:

1.  **Utilizzabile** : ciò che è "usati backup", ad esempio valuta nel gioco che può impiegare Windows Media player. Se l'utente esegue un backup o ripristino o in caso contrario, il dispositivo è aggiornato, una transazione può essere utilizzata non vengono ripristinata anche (che in modo efficace produrrebbe l'assegnatario di questo vantaggio soggiacente). Il codice dell'applicazione è necessario assicurarsi di fornire l'elemento può essere utilizzato, non appena la transazione viene completata.
1.  **Non riproducibile** – prodotti che l'utente 'proprietario' una volta acquistati, ad esempio un problema di magazine digitale o un livello di gioco.
1.  **Auto-rinnovabile sottoscrizioni** – è sufficiente, ad esempio abbonamenti a riviste reali, alla fine del periodo di sottoscrizione Apple automaticamente gli addebiti per il cliente anche in questo caso ed estende il periodo di sottoscrizione all'infinito o fino a quando il cliente in modo esplicito lo Annulla. Si tratta del metodo di pagamento preferita per le app di Newsstand (in effetti, le applicazioni devono supportare questo metodo di pagamento per essere approvato per la distribuzione di Newsstand).
1.  **Sottoscrizione gratuita** : possono essere offerti solo nelle App abilitate per Newsstand e consente agli utenti di accedere al contenuto di sottoscrizione su tutti i dispositivi. Le sottoscrizioni gratuite senza scadono.
1.  **Sottoscrizione di rinnovo non** : deve essere usata per la vendita periodo di tempo limitato l'accesso a contenuto statico, ad esempio l'accesso di un mese in un archivio di foto.


 *Questo documento illustra attualmente solo i primi due tipi di prodotti (che può essere utilizzato e Non riproducibile).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Livelli di prezzo

L'App Store non è possibile scegliere un prezzo arbitrario per i prodotti, Apple offre i livelli di prezzo fisso che è possibile scegliere tra. I prezzi indicati sono stati risolti in ognuna delle valute e Apple si riserva il diritto di modificare i prezzi relativi (ad esempio, dopo un cambiamento prolungato del tasso di cambio esteri relativo tra una determinata valuta e il dollaro Statunitense).

Apple offre una matrice di prezzo che consentono di selezionare il livello corretto per il valuta/prezzo desiderato. Di seguito è riportato un estratto della matrice prezzo (agosto 2012):

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Un estratto della matrice prezzo agosto 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

Al momento della scrittura (giugno 2013) sono disponibili 87 livelli da USD 0,99 a 999.99 dollari USA. La matrice dei prezzi Mostra il costo che paghino i clienti e anche la quantità, riceverai da Apple: questo è meno il costo del 30% e anche eventuali imposte locale sono necessarie per raccogliere (si noti che nell'esempio che i venditori di Stati Uniti e Canada ricevono 70c per un esempio di c 99 p redefinita, mentre i venditori australiani ricevono solo 63 c a causa dell'errore ' beni &amp; Services Tax' colpiscono il prezzo di vendita).

Prezzi del prodotto possono essere aggiornati in qualsiasi momento, incluse le modifiche di prezzo pianificati che vengono applicate in una data futura. Questo screenshot Mostra come viene aggiunta una variazione di prezzo con date future, il prezzo viene temporaneamente modificato da livello 1 al livello 3 per il mese di settembre solo:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Una variazione di prezzo con date future in cui il prezzo viene temporaneamente modificato da livello 1 al livello 3 per il mese di settembre solo")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Prodotti gratuiti non supportati

Sebbene Apple ha fornito un'opzione di sottoscrizione gratuita speciale per le app di Newsstand, non è possibile impostare un prezzo pari a zero (gratuito) per qualsiasi altro tipo di acquisto in-app. Sebbene sia possibile modificare (ie. inferiore) i prezzi per le promozioni di vendite, è possibile effettuare acquisti in-app 'gratuiti' tramite iTunes Connect.

### <a name="localization"></a>Localizzazione

In iTunes Connect è possibile immettere testo di nome e una descrizione diversi per un numero qualsiasi delle lingue supportate. Ogni lingua può essere aggiunto/modificato in tramite una finestra popup:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Ogni lingua può essere aggiunto/modificato in tramite una finestra popup")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 Quando si visualizzano le informazioni sul prodotto nell'app, è disponibile per consentire la visualizzazione tramite StoreKit il testo localizzato. La visualizzazione di valuta deve anche essere localizzata per mostrare il simbolo corretto e la formattazione decimali: questo tipo di formattazione viene descritta più avanti nel documento.

### <a name="app-store-review"></a>Revisione di App Store

Come le app: ogni prodotto viene esaminato da Apple prima di poter passare in vendita. I prodotti possono essere rifiutati per contenuti inappropriati nel nome o descrizione, o Apple può decidere che si è scelto il tipo di prodotto sbagliato (ad es. è stato creato un libro o alla rivista ma utilizzato il tipo di prodotto può essere utilizzato). Recensioni dei prodotti possono richiedere fino a una verifica dell'app.

La prima volta che un'app viene inviata con acquisti in-app abilitata, sia che si tratta di una nuova app, la funzionalità è stata aggiunta a uno esistente, è necessario scegliere alcuni prodotti di inviare con esso. Il portale di iTunes Connect verrà richiesto di eseguire questa operazione, come illustrato in questo screenshot:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "Il portale di iTunes Connect verrà richiesto di inviare anche alcuni prodotti")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 In modo che tutti ottenere approvati in una sola volta (in modo che l'app non viene accettata nell'archivio senza eventuali prodotti approvati!), verranno esaminati tra loro, l'applicazione e gli acquisti in-app.

Dopo la prima versione con funzionalità di acquisto in-app è stata approvata, è possibile aggiungere altri prodotti e inviarle per la revisione in qualsiasi momento. È possibile anche scegliere di inviare una nuova versione insieme ai prodotti di acquisto in-app specifiche, usando il **informazioni dettagliate sulla versione** pagina come suggerisce il prompt dei comandi.

Vedere le [App Store Review Guidelines](https://developer.apple.com/appstore/guidelines.html) per altre informazioni.

 [Parte 2: Panoramica sul Kit di Store e prodotti durante il recupero delle informazioni](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
