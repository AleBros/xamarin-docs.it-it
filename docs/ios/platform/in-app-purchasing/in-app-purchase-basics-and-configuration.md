---
title: Nozioni fondamentali di acquisto in-App e la configurazione in xamarin. IOS
description: Questo documento descrive gli acquisti in-app in xamarin. IOS, che illustrano le informazioni pertinenti sulle regole, la configurazione e iTunes Connect.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 9ded160ad4b31346c400e63d739a3dc21f6304d3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787243"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Nozioni fondamentali di acquisto in-App e la configurazione in xamarin. IOS

Implementazione di acquisti in-app richiede che l'applicazione di utilizzare l'API StoreKit nel dispositivo. StoreKit gestisce tutte le comunicazioni con iTunes i server Apple per ottenere informazioni sul prodotto ed eseguire le transazioni. Il profilo di provisioning deve essere configurato per l'acquisto nell'applicazione e informazioni sul prodotto devono essere immessi in iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit gestisce tutte le comunicazioni con Apple, come illustrato in questo grafico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

Uso dell'archivio di App per fornire l'acquisto in-app richiede la configurazione seguente:

-  **iTunes Connect** : i prodotti in vendita e la configurazione di account utente di sandbox per testare l'acquisto. Utente deve inoltre aver fornito le informazioni di servizi bancari e fiscali a Apple in modo possibile rimettere i fondi raccolti per conto dell'utente.
-   **Portale di Provisioning iOS** : creazione di un identificatore di raggruppamento e l'abilitazione dell'accesso App Store per l'app.
-  **Archiviare Kit** : aggiunta di codice per l'app per la visualizzazione di prodotti, acquisto di prodotti e il ripristino delle transazioni.
-  **Codice personalizzato** : per rilevare gli acquisti effettuati dai clienti e fornire i prodotti o servizi vengono acquistate. È possibile inoltre necessario implementare un processo sul lato server per convalidare conferme di recapito, se i prodotti sono costituiti da contenuto scaricato da un server (ad esempio i libri e problemi di magazine).


Esistono due Store Kit "ambienti server":

-  **Produzione** : le transazioni con money reale. Accessibile solo tramite applicazioni che sono state inviate e approvate da Apple. I prodotti di acquisto in-app devono inoltre rivisto e approvati prima che siano disponibili nell'ambiente di produzione.
-  **Sandbox** : in cui avviene il test. I prodotti sono disponibili qui subito dopo la creazione (il processo di approvazione si applica solo all'ambiente di produzione). Le transazioni nella sandbox richiedono agli utenti di test (ID Apple non reale) eseguire le transazioni.

## <a name="in-app-purchase-rules"></a>Regole di acquisto in-App

Non è possibile accettare altre forme di pagamento per digitale prodotti o servizi all'interno di app, né trattarle o fare riferimento agli utenti a essi all'interno di un'app. Ciò significa che non è possibile accettare le carte di credito o PayPal quando acquisto in-app è il meccanismo di pagamento più appropriato. È un caso speciale per l'acquisto di prodotti digitali all'esterno dell'app, ma per usare l'App, ad esempio l'acquisto di libri in un sito Web che sono associati a uno specifico "login" e l'utilizzo che consente all'utente l'accesso di "accesso" nell'app i libri acquistati.
Le applicazioni che funzionano in questo modo non è consentite menzionano o collegare le funzionalità di acquisto esterna: gli sviluppatori devono comunicare questa funzionalità agli utenti in altri modi (ad esempio tramite marketing di posta elettronica o altri canali di diretto).

Tuttavia, poiché non è possibile utilizzare acquisti in-app per le merci fisiche, in tale caso è consentito usare un meccanismo alternativo di pagamento (ad es. carta di credito, PayPal) all'interno dell'app.

Apple è necessario approvare tutti i prodotti prima passa in vendita: il nome, descrizione e una schermata di 'product' è obbligatorio per la revisione. Tempi di revisione di prodotto sono le stesse revisioni di applicazione.

Non è possibile scegliere qualsiasi prezzo del prodotto – è possibile selezionare solo un 'livello di prezzo' che ha un valore specifico in ogni paese/valuta che supporta Apple. È possibile avere un livello di prezzo diversi nei diversi mercati.

## <a name="configuration"></a>Configurazione

Prima di scrivere alcun codice di acquisto in-app è necessario eseguire alcune operazioni di configurazione in iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) e il portale di Provisioning iOS ( [developer.apple.com/iOS](http://developer.apple.com/iOS)).

Questi tre passaggi devono essere completi prima di scrivere codice:

-  **Account per sviluppatori Apple** : inviare le informazioni di servizi bancari e fiscali ad Apple.
-  **Portale di Provisioning iOS** : verificare che l'applicazione ha un ID valido dell'App (non un carattere jolly con un asterisco * in essa contenuti) e In App acquisto abilitato.
-  **iTunes Connect Application Management** – aggiungere prodotti all'applicazione.


### <a name="apple-developer-account"></a>Account per sviluppatore Apple

Compilazione e distribuzione di App gratuite richiede una configurazione minima in [iTunes Connect](https://itunesconnect.apple.com), tuttavia per vendere a pagamento App o acquisti in-app è necessario fornire Apple con informazioni di servizi bancari e fiscali. Fare clic su **contratti, imposte e servizi bancari** dal menu principale illustrato di seguito:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Fare clic su contratti, imposte e servizi bancari dal menu principale")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

L'Account sviluppatore deve avere un **applicazioni a pagamento iOS** in effetti, come illustrato in questa schermata del contratto:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "L'Account sviluppatore deve avere un contratto attivo di applicazioni a pagamento di iOS")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

Non sarà in grado di verificare le funzionalità di StoreKit fino a quando non si dispone un **applicazioni a pagamento iOS** contratto: StoreKit chiamate nel codice avrà esito negativo fino a quando non Apple ha elaborato il **contratti, imposte e servizi bancari** informazioni.

### <a name="ios-provisioning-portal"></a>Portale di provisioning iOS

Nuove applicazioni sono configurate nel **ID App** sezione la **portale di Provisioning iOS**. Per creare un nuovo ID di App, visitare il [Member Center del portale di Provisioning iOS](https://developer.apple.com/membercenter/index.action), passare a **certificati, gli identificatori e i profili** sezione del portale, fare clic su **identificatori** in *delle App iOS*. Quindi, fare clic su "+" in alto a destra per generare un nuovo ID di App.


Il form per la creazione di nuovi **ID App**

 è simile al seguente:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "Il modulo per la creazione di nuovi ID App")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Immettere un nome appropriato per il *descrizione*, pertanto è possibile identificare facilmente questo ID di App in un elenco. Per il *prefisso ID App*, selezionare l'ID del Team.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Formato di suffisso dell'ID bundle identificatore/App

È possibile utilizzare qualsiasi stringa da utilizzare per il **identificatore Bundle** (purché sia univoco nell'account), ma si consiglia di Apple è conforme al formato inversa DNS anziché utilizzare qualsiasi stringa arbitraria. L'applicazione di esempio che accompagna questo articolo utilizza com.xamarin.storekit.testing per l'identificatore Bundle, ma sarebbe ugualmente valido per l'utilizzo di un identificatore come my_store_example (sebbene non consigliabile Apple).

> [!IMPORTANT]
> Apple consente inoltre con caratteri jolly asterisco da aggiungere alla fine di un **identificatore Bundle** in modo che un singolo ID di App può essere utilizzato per più applicazioni, tuttavia _ID dell'App con caratteri jolly non può essere utilizzato perAppPurchase_. Un esempio identificatore Bundle con caratteri jolly potrebbe essere com.xamarin.*

#### <a name="enabling-app-services"></a>Abilitazione di servizi App

Si noti che **acquisto In-App** verrà attivata automaticamente nell'elenco dei servizi:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "Acquisto in-App verrà attivata automaticamente nell'elenco dei servizi")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Profili di provisioning

Creare profili di Provisioning di produzione e sviluppo è normalmente verrebbe, quindi selezionare l'ID di App da impostare per l'acquisto In-App. Consultare la [Provisioning dei dispositivi iOS](~/ios/get-started/installation/device-provisioning/index.md) e [pubblicazione nell'archivio di App](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) Guide per ulteriori informazioni.

## <a name="itunes-connect"></a>iTunes Connect

Fare clic su **My Apps** in iTunes Connect per creare o modificare una voce di applicazione di iOS. La pagina Panoramica dell'applicazione è illustrata di seguito:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "La pagina Panoramica dell'applicazione")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Fare clic su **acquisti In-App** per creare o modificare i prodotti in vendita. Questa schermata mostra l'app di esempio con diversi prodotti già aggiunti:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "L'esempio di app con diversi prodotti già aggiunto")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

Processo di aggiunta di nuovi prodotti prevede due passaggi:

1.   Scegliere il tipo di prodotto: [![](in-app-purchase-basics-and-configuration-images/image8.png "scegliere il tipo di prodotto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   Immettere gli attributi dei prodotti, tra cui l'Id prodotto, piano tariffario e descrizioni localizzate: [![](in-app-purchase-basics-and-configuration-images/image9.png "immettere gli attributi di prodotti")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

I campi necessari per ogni prodotto di acquisto in-app sono descritti di seguito:


### <a name="reference-name"></a>Nome riferimento

Il nome di riferimento non viene visualizzato agli utenti; è per uso interno e viene visualizzato solo in iTunes Connect.

### <a name="product-id-format"></a>Formato ID prodotto

Un identificatore del prodotto può contenere solo caratteri alfanumerici (A-Z, a-z, 0-9), un carattere di sottolineatura (_) e caratteri punto (.). Sebbene sia possibile utilizzare qualsiasi stringa per il tipo di identificatori, Apple consiglia il formato DNS inversa. Ad esempio, l'applicazione di esempio utilizza questo identificatore Bundle:

 `com.xamarin.storekit.testing`

Di conseguenza la convenzione per identificare i prodotti di acquisto in-app sarà come segue:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Non viene applicata questa convenzione di denominazione, semplicemente un'indicazione per la gestione dei prodotti. Inoltre, nonostante seguendo la stessa convenzione inversa DNS, gli identificatori di prodotto sono *non correlati* per l'identificatore Bundle e sono non deve per iniziare con la stessa stringa. Sarebbe comunque valido per l'utilizzo di identificatori come photo_product_greyscale (sebbene non consigliabile Apple).

ID prodotto non viene visualizzato agli utenti, ma viene utilizzato per fare riferimento del prodotto nel codice dell'applicazione.

### <a name="product-type"></a>Tipo di prodotto

Esistono cinque tipi di prodotti di acquisto in-app che è possibile offrire:

1.  **Consumo** – operazioni "usati di", ad esempio valuta di gioco che può dedicare il lettore. Se si esegue un backup e ripristino o in caso contrario il dispositivo è aggiornato, una transazione consumo non ottenere il ripristino (che in modo efficace avrebbe il giocatore gli stessi vantaggi nuovo). Codice dell'applicazione è necessario assicurarsi di fornire l'elemento consumo non appena la transazione viene completata.
1.  **Non riproducibile** – prodotti che l'utente "proprietario" una volta acquistati, ad esempio un problema di magazine digitale o un livello di gioco.
1.  **Sottoscrizioni automaticamente rinnovabile** : semplicemente come una sottoscrizione di magazine del mondo reale, alla fine del periodo di sottoscrizione Apple automaticamente addebita nuovamente al cliente ed estende il periodo di sottoscrizione, per sempre o finché il cliente in modo esplicito Annulla. Si tratta del metodo di pagamento preferito per le app Newsstand (in effetti, le applicazioni devono supportare questo metodo di pagamento per essere approvato per la distribuzione Newsstand).
1.  **Iscrizione gratuita** : possono essere forniti solo nelle App basate su Newsstand e consente agli utenti di accedere al contenuto sottoscrizione su tutti i dispositivi. Le sottoscrizioni gratuite senza scadono.
1.  **Sottoscrizione di rinnovo non** : deve essere utilizzato per vendere tempo limitato l'accesso a contenuto statico, ad esempio l'accesso di un mese in un archivio di foto.


 *Attualmente, questo documento descrive solo i primo due tipi di prodotti (che può essere utilizzato e Non riproducibile).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Livelli di prezzo

Apple fornisce i livelli di prezzo fisso che è possibile scegliere tra l'archivio di App non è possibile scegliere un prezzo arbitrario per i prodotti. I prezzi sono modificabili in ciascuna valuta e Apple riserva il diritto di modificare i prezzi relativi (ad esempio, dopo una modifica prolungata del tasso di cambio relativo tra una determinata valuta e il dollaro Americano).

Apple fornisce una matrice di prezzo che consentono di selezionare il livello corretto per il prezzo valuta/desiderati. In seguito è riportato un estratto della matrice di prezzo (agosto 2012):

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Un estratto della matrice di prezzo agosto 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

Al momento della scrittura (giugno 2013) sono presenti 87 livelli da USD 0,99 per 999,99 USD. La matrice di prezzi Mostra il prezzo che pagano dei clienti e anche la quantità che viene visualizzato da Apple – questo è meno i costi del 30% e anche eventuali imposte locale sono necessari per raccogliere (l'esempio che i venditori di Stati Uniti e Canada ricevano 70c per un esempio di c 99 p roduct, mentre i venditori australiani ricevono solo 63 c a causa di ' merci &amp; Services Tax' sul prezzo di vendita).

Prezzi del prodotto possono essere aggiornati in qualsiasi momento, incluse le modifiche di prezzo programmato che diventano effettivi in una data futura. Questa schermata illustrata l'aggiunta di una variazione di prezzo con date future – il prezzo viene temporaneamente modificato da livello 1 a 3 livello per il mese di settembre solo:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Modifica di un prezzo con date future in cui il prezzo viene temporaneamente modificato da livello 1 a 3 livello per il mese di settembre solo")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Prodotti gratuiti non è supportati

Sebbene Apple ha fornito un'opzione di abbonamento gratuito speciale per le app Newsstand, non è possibile impostare un prezzo zero (gratuito) per qualsiasi altro tipo di acquisto in-app. Mentre è possibile modificare (ie. inferiore) dei prezzi di promozioni di vendita, è possibile apportare acquisti in-app 'gratuiti' tramite iTunes Connect.

### <a name="localization"></a>Localizzazione

In iTunes Connect è possibile immettere testo diverso di nome e una descrizione per un numero qualsiasi di lingue supportate. Ogni lingua può essere aggiunto/modificato in un popup:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Ogni lingua può essere aggiunto/modificato in un popup")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 Quando si visualizzano informazioni sui prodotti nell'app, il testo localizzato è disponibile per poter visualizzare tramite StoreKit. La visualizzazione di valuta deve anche essere localizzata per mostrare il simbolo corretto e la formattazione dei decimali – questa formattazione viene descritta più avanti nel documento.

### <a name="app-store-review"></a>Revisione di App Store

Come le app: ogni prodotto viene esaminato da Apple prima di poter passare in vendita. Prodotti potrebbero essere rifiutati per il contenuto appropriato nel nome o descrizione, o Apple potrebbe decidere che si è scelto il tipo di prodotto non corretto (ad es. è stato creato un libro o un problema rivista ma utilizzato il tipo di prodotto utilizzabile). Le revisioni dei prodotti può richiedere fino a una revisione di app.

La prima volta che un'app viene inviata insieme all'interno dell'applicazione acquisto abilitati, sia che si tratta di una nuova app, la funzionalità è stata aggiunta a una esistente, è necessario scegliere alcuni prodotti per l'invio con esso. Il portale di iTunes Connect verrà chiesto di eseguire questa operazione, come illustrato in questa schermata:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "Il portale di iTunes Connect verrà chiesto di inviare alcuni dei prodotti")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 In modo che tutti ottenere approvati in una sola volta (in modo che app non entra in archivio senza eventuali prodotti approvati!), verranno esaminati insieme, l'applicazione e gli acquisti in-app.

Dopo la prima versione con funzionalità di acquisto in-app è stata approvata, è possibile aggiungere altri prodotti e le invia per esaminare in qualsiasi momento. È possibile anche scegliere di inviare una nuova versione insieme ai prodotti specifici di acquisto in-app, utilizzando il **dettagli della versione** pagina come suggerisce il prompt dei comandi.

Consultare la [App Store Review Guidelines](https://developer.apple.com/appstore/guidelines.html) per ulteriori informazioni.

 [Parte 2 - archivio Kit panoramica e informazioni sui prodotti di recupero](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
