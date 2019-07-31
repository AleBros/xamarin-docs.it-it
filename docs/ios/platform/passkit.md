---
title: PassKit in Novell. iOS
description: L'app Wallet consente agli utenti iOS di archiviare i pass digitali nei propri dispositivi. Il Framework PassKit consente agli sviluppatori di interagire con i passaggi a livello di codice.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: 2bd694d903da9f30f8fffa5fea991c1f386752dd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656377"
---
# <a name="passkit-in-xamarinios"></a>PassKit in Novell. iOS

L'app per portafogli iOS consente agli utenti di archiviare i pass digitali nei propri dispositivi.
Queste sessioni vengono generate dai commercianti e inviate al cliente tramite posta elettronica, URL o tramite l'app iOS. Questi passaggi possono rappresentare vari elementi, dai biglietti per i film alle schede fedeltà ai pass. Il Framework PassKit consente agli sviluppatori di interagire con i passaggi a livello di codice.

Questo documento introduce il portafoglio e l'uso dell'API PassKit con Novell. iOS.

 [![](passkit-images/image1.png "Il portafoglio archivia e organizza tutti i passaggi in un telefono")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Requisiti

Le funzionalità di PassKit descritte in questo documento richiedono iOS 6 e Xcode 4,5, insieme a Novell. iOS 6,0.

## <a name="introduction"></a>Introduzione

Il problema principale che PassKit risolve è la distribuzione e la gestione dei codici a barre. Di seguito sono riportati alcuni esempi reali del modo in cui vengono attualmente usati i codici a barre:

-   **Acquisto dei biglietti online** : i clienti vengono in genere inviati tramite posta elettronica a un codice a barre che rappresenta i biglietti. Questo codice a barre viene stampato e portato al cinema per essere analizzato per l'immissione.
-   **Carte fedeltà** : i clienti contengono una serie di schede specifiche del negozio nel portafoglio o nella borsa, per la visualizzazione e l'analisi quando acquistano beni.
-   **Buoni** : i buoni vengono distribuiti tramite posta elettronica, come pagine Web stampabili, tramite letterbox e come codici a barre in giornali e riviste. I clienti li portano a uno Store per l'analisi, per ricevere beni, servizi o sconti in cambio.
-   **Passaggi di imbarco** : simili all'acquisto di un ticket di film.

PassKit offre un'alternativa per ognuno di questi scenari:

-   **Ticket di film** : dopo l'acquisto, il cliente aggiunge un ticket di ticket di evento (tramite posta elettronica o un collegamento al sito Web). Nel momento in cui si avvicina il film, il pass verrà automaticamente visualizzato nella schermata di blocco come promemoria e all'arrivo al cinema il pass viene facilmente recuperato e visualizzato in Wallet per l'analisi.
-   **Carte fedeltà** : anziché (o oltre a) fornire una scheda fisica, gli archivi possono emettere (tramite posta elettronica o dopo un accesso al sito Web) un pass per la scheda dello Store. Lo Store può fornire funzionalità aggiuntive, ad esempio l'aggiornamento del saldo dell'account nel passaggio tramite notifiche push e l'uso dei servizi di georilevazione. il passaggio potrebbe essere visualizzato automaticamente nella schermata di blocco quando il cliente si trova vicino a una posizione di negozio.
-   **Buoni** : i pass per i tagliandi possono essere facilmente generati con caratteristiche univoche per facilitare il rilevamento e la distribuzione tramite posta elettronica o collegamenti a siti Web. I tagliandi scaricati possono essere visualizzati automaticamente nella schermata di blocco quando l'utente si trova vicino a una posizione specifica e/o in una data specifica, ad esempio quando si avvicina la data di scadenza. Poiché i buoni sono archiviati sul telefono dell'utente, sono sempre pratici e non vengono posizionati in una posizione non configurata. I buoni possono incoraggiare i clienti a scaricare app complementari perché i collegamenti all'App Store possono essere incorporati nel passaggio, aumentando il coinvolgimento del cliente.
-   **Passaggi di imbarco** : dopo un processo di archiviazione online, il cliente riceverà il pass-through tramite posta elettronica o un collegamento. Un'app complementare fornita dal provider di trasporto potrebbe includere il processo di archiviazione e consentire al cliente di eseguire altre funzioni, ad esempio la scelta della loro postazione o pasto. Il provider di trasporto può utilizzare le notifiche push per aggiornare il passaggio se il trasporto viene posticipato o annullato. Quando il tempo di imbarco si avvicina, il passaggio verrà visualizzato nella schermata di blocco come promemoria e per fornire accesso rapido al passaggio.

PassKit fornisce un modo semplice e pratico per archiviare e visualizzare i codici a barre sul dispositivo iOS. Grazie all'integrazione della schermata di blocco di tempo e località, le notifiche push e le applicazioni complementari si integrano e offrono una base per servizi di vendita, ticket e fatturazione molto sofisticati.

## <a name="passkit-ecosystem"></a>Ecosistema PassKit

PassKit non è solo un'API all'interno di CocoaTouch, ma fa parte di un ecosistema più ampio di app, dati e servizi che facilitano la condivisione e la gestione sicure di codici a barre e altri dati. Questo diagramma generale Mostra le diverse entità che possono essere necessarie per la creazione e l'uso di passaggi:

 [![](passkit-images/image2.png "Questo diagramma di alto livello Mostra le entità che coinvolgono la creazione e l'uso di pass")](passkit-images/image2.png#lightbox)

Ogni parte dell'ecosistema ha un ruolo ben definito:

-   **Wallet** : app iOS incorporata di Apple che archivia e Visualizza i pass. Questa è l'unica posizione in cui viene eseguito il rendering di pass per l'uso nel mondo reale, ovvero viene visualizzato il codice a barre, insieme a tutti i dati localizzati nel passaggio.
-   **App complementari** : app iOS 6 compilate dai provider di servizi di passaggio per estendere la funzionalità dei passaggi che emettono, ad esempio l'aggiunta di un valore a una scheda dello Store, la modifica della posizione in un passaggio di imbarco o di altre funzioni specifiche dell'azienda. Non sono necessarie app complementari per un passaggio.
-   **Server** : server protetto in cui è possibile generare e firmare i passaggi per la distribuzione. L'app complementare può connettersi al server per generare nuovi passaggi o richiedere aggiornamenti ai pass esistenti. Facoltativamente, è possibile implementare l'API del servizio Web che il portafogli chiamerà per aggiornare le sessioni.
-   **Server APNs** : il server è in grado di inviare notifiche al portafogli degli aggiornamenti a un passaggio in un determinato dispositivo usando APNs. Effettuare il push di una notifica a Wallet, che contatterà il server per informazioni dettagliate sulla modifica. Le `PKPassLibraryDidChangeNotification` app complementari non devono implementare APNs per questa funzionalità (possono restare in ascolto di).
-   **App Conduit** : applicazioni che non modificano direttamente i pass (ad esempio, le app complementari), ma che possono migliorare la propria utilità riconoscendo i pass e consentendone l'aggiunta al portafogli. I client di posta, i browser di social networking e altre app per l'aggregazione di dati possono incontrare tutti gli allegati o i collegamenti da passare.

L'intero ecosistema sembra complesso, quindi vale la pena notare che alcuni componenti sono facoltativi e sono possibili implementazioni PassKit più semplici.

## <a name="what-is-a-pass"></a>Che cos'è un pass?

Un pass è una raccolta di dati che rappresentano un ticket, un buono o una scheda. Può essere destinata a un singolo utilizzo da parte di un singolo utente (e quindi contenere dettagli come un numero di volo e l'allocazione della postazione) oppure un token di utilizzo multiplo che può essere condiviso da un numero qualsiasi di utenti (ad esempio un buono sconto). Una descrizione dettagliata è disponibile nel documento [sulle informazioni sui file pass](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) di Apple.

### <a name="types"></a>Tipi

Attualmente cinque tipi supportati, che possono essere distinti nell'app Wallet dal layout e dal bordo superiore del passaggio:

-  **Ticket evento** : piccolo ritaglio semicircolare.
-   **Boarding Pass** : è possibile specificare un'icona specifica del trasporto, ad esempio bus, treno, aereo).
-   **Scheda dello Store** : arrotondato in alto, ad esempio una carta di credito o di debito.
-  **Cedole** : perforato lungo la parte superiore.
-  **Generico** : uguale alla scheda dello Store, arrotondato in alto.


In questa schermata vengono mostrati i cinque tipi di pass (in ordine: cedola, generico, scheda dello Store, carta di imbarco e ticket di evento):

 [![](passkit-images/image3.png "In questa schermata vengono mostrati i cinque tipi di pass")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Struttura di file

Un file pass è in realtà un archivio ZIP con estensione **pkpass** , che contiene alcuni file JSON specifici (obbligatorio), un'ampia gamma di file di immagine (facoltativo) e stringhe localizzate (anche facoltative).

-   **Pass. JSON** : obbligatorio. Contiene tutte le informazioni per il passaggio.
-   **manifest. JSON** : obbligatorio. Contiene gli hash SHA1 per ogni file nel passaggio, ad eccezione del file di firma e del file (manifest. Json).
-   **firma** : obbligatorio. Creato firmando il `manifest.json` file con il certificato generato nel portale di provisioning iOS.
-  **logo. png** -facoltativo.
-  **background. png** -facoltativo.
-  **Icon. png** : facoltativo.
-  **File di stringhe localizzabili** -facoltativo.

Di seguito è riportata la struttura di directory di un file di pass (questo è il contenuto dell'archivio ZIP):

 [![](passkit-images/image4.png "Di seguito è riportata la struttura di directory di un file pass")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON è il formato perché i passaggi vengono in genere creati in un server, ovvero il codice di generazione è indipendente dalla piattaforma sul server. Le tre principali informazioni in ogni passaggio sono:

-   **teamIdentifier** : tutti i collegamenti che vengono generati nell'account di App Store. Questo valore è visibile nel portale di provisioning iOS.
-   **passTypeIdentifier** : consente di eseguire la registrazione nel portale di provisioning per raggruppare i passaggi (se si produce più di un tipo). Ad esempio, un coffee shop potrebbe creare un tipo di pass per la carta dello Store per consentire ai clienti di ottenere crediti di fedeltà, ma anche un tipo di pass per i tagliandi separato per creare e distribuire i buoni sconto. Lo stesso coffee bar potrebbe anche conservare eventi Live Music e inviare ticket di ticket per gli eventi.
-   **serialNumber** : stringa univoca all'interno `passTypeidentifier` di questo oggetto. Il valore è opaco rispetto al portafoglio, ma è importante per tenere traccia dei passaggi specifici durante la comunicazione con il server.

Ogni passaggio include un numero elevato di altre chiavi JSON, un esempio di quanto illustrato di seguito:

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>Codici a barre

Sono supportati solo i formati 2D: PDF417, Aztec, QR. Apple dichiara che i codici a barre 1D non sono configurati per l'analisi su una schermata del telefono retroilluminato.

Il testo alternativo visualizzato sotto il codice a barre è facoltativo: alcuni commercianti vogliono essere in grado di leggere/digitare manualmente.

La codifica ISO-8859-1 è la più comune, verificare quale codifica viene usata dai sistemi di analisi che leggeranno i passaggi.

### <a name="relevancy-lock-screen"></a>Pertinenza (schermata di blocco)

Esistono due tipi di dati che possono comportare la visualizzazione di un passaggio sulla schermata di blocco:

 **Location**

È possibile specificare fino a 10 località in un passaggio, ad esempio archivi che un cliente visita spesso o la posizione di un cinema o di un aeroporto. Un cliente può impostare questi percorsi tramite un'app complementare o il provider può determinarli dai dati di utilizzo (se raccolti con l'autorizzazione del cliente).

Quando il passaggio viene visualizzato nella schermata di blocco, viene calcolato un limite in modo che quando l'utente lascia l'area il passaggio sia nascosto dalla schermata di blocco. Il raggio è associato allo stile pass per evitare abusi.

 **Data e ora**

In un passaggio è possibile specificare solo una data/ora. La data e l'ora sono utili per attivare i promemoria della schermata di blocco per i passaggi di imbarco e i ticket di evento.

Può essere aggiornato tramite push o tramite l'API PassKit, in modo che la data e l'ora possano essere aggiornate nel caso di un ticket a utilizzo multiplo, ad esempio un ticket di stagione a un teatro o un complesso sportivo.

### <a name="localization"></a>Localizzazione

La conversione di un passaggio in più linguaggi è simile alla localizzazione di un'applicazione iOS: creare directory specifiche del linguaggio `.lproj` con l'estensione e inserire gli elementi localizzati all'interno di. È necessario immettere le traduzioni di testo `pass.strings` in un file, mentre le immagini localizzate devono avere lo stesso nome dell'immagine che sostituisce nella radice del passaggio.

## <a name="security"></a>Security

Le sessioni sono firmate con un certificato privato generato nel portale di provisioning iOS. I passaggi per la firma del passaggio sono:

1.  Calcolare un hash SHA1 per ogni file nella directory Pass (non includere il `manifest.json` file o `signature` , nessuno dei quali deve esistere in questa fase).
1.  Scrivere `manifest.json` come un elenco di chiavi/valori JSON di ogni nome file con il relativo hash.
1.  Utilizzare il certificato per firmare il `manifest.json` file e scrivere il risultato in un file denominato `signature` .
1.  Comprimere tutti gli elementi e assegnare al file `.pkpass` risultante un'estensione di file.


Poiché la chiave privata è necessaria per firmare il passaggio, questo processo deve essere eseguito solo su un server protetto controllato dall'utente. NON distribuire le chiavi per provare a generare i passaggi in un'applicazione.

 
## <a name="configuration-and-setup"></a>Configurazione e configurazione

Questa sezione contiene istruzioni per la configurazione dei dettagli del provisioning e la creazione del primo passaggio.

### <a name="provisioning-passkit"></a>Provisioning di PassKit

Per poter accedere all'App Store, un pass deve essere collegato a un account per sviluppatore. Questa operazione richiede due passaggi:

1.  Il passaggio deve essere registrato usando un identificatore univoco, denominato ID del tipo di pass.
1.  Per firmare il pass con la firma digitale dello sviluppatore, è necessario generare un certificato valido.

Per creare un ID di tipo pass, eseguire le operazioni seguenti.

#### <a name="create-a-pass-type-id"></a>Creare un ID tipo pass

Il primo passaggio consiste nell'impostare un ID del tipo di passaggio per ogni _tipo_ di passaggio da supportare. Il pass ID (o identificatore di tipo pass) crea un identificatore univoco per il passaggio. Questo ID verrà usato per collegare il pass con l'account sviluppatore usando un certificato.

1. Nella [sezione certificati, identificatori e profili del portale di provisioning iOS](https://developer.apple.com/account/overview.action)passare a identificatori e selezionare **passa ID tipo** . Quindi selezionare il **+** pulsante per creare un nuovo tipo di pass: [![](passkit-images/passid.png "Crea un nuovo tipo di passaggio")](passkit-images/passid.png#lightbox)

2.   Specificare una **Descrizione** (nome) e un **identificatore** (stringa univoca) per il passaggio. Si noti che tutti gli ID di tipo pass devono iniziare `pass.` con la stringa in questo `pass.com.xamarin.coupon.banana` esempio: [![](passkit-images/register.png "Specificare una descrizione e un identificatore")](passkit-images/register.png#lightbox)


3.   Confermare il pass ID premendo il pulsante **Register (registra** ).

#### <a name="generate-a-certificate"></a>Generare un certificato

Per creare un nuovo certificato per questo ID tipo di pass, procedere come segue:

1.  Selezionare l'ID passato appena creato dall'elenco e fare clic su **modifica** : [![](passkit-images/pass-done.png "Selezionare il nuovo ID Pass dall'elenco")](passkit-images/pass-done.png#lightbox)

    Selezionare quindi **Crea certificato...** :

    [![](passkit-images/cert-dist.png "Selezionare Crea certificato")](passkit-images/cert-dist.png#lightbox)


2.  Seguire i passaggi per creare una richiesta di firma del certificato (CSR).
  
3. Premere il pulsante **continue (continua** ) nel portale per sviluppatori e caricare il certificato CSR per generare il certificato.

4. Scaricare il certificato e fare doppio clic su di esso per installarlo nel keychain.


Ora che è stato creato un certificato per questo ID tipo di pass, nella sezione successiva viene descritto come compilare manualmente un pass.

Per altre informazioni sul provisioning per Wallet, vedere la Guida [uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) .

### <a name="create-a-pass-manually"></a>Creazione di un pass manualmente

Ora che è stato creato il tipo di passaggio, è possibile creare manualmente una sessione di test nel simulatore o in un dispositivo. I passaggi per creare un passaggio sono:

-  Creare una directory in cui includere i file di pass.
-  Creare un file pass. JSON che contenga tutti i dati necessari.
-  Includere le immagini nella cartella (se necessario).
-  Calcolare gli hash SHA1 per ogni file nella cartella e scrivere in manifest. JSON.
-  Firmare manifest. JSON con il file Certificate. P12 scaricato.
-  Comprimere il contenuto della directory e rinominare con l'estensione PKPASS.


Nel [codice di esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) per questo articolo sono presenti alcuni file di origine che possono essere usati per generare un pass. Usare i file nella `CouponBanana.raw` directory della directory CreateAPassManually. Sono presenti i file seguenti:

 [![](passkit-images/image18.png "Questi file sono presenti")](passkit-images/image18.png#lightbox)

Aprire pass. JSON e modificare il codice JSON. È necessario aggiornare almeno il `passTypeIdentifier` e `teamIdentifer` per trovare la corrispondenza con l'account sviluppatore Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

È quindi necessario calcolare gli hash per ogni file e creare il `manifest.json` file. Al termine, il risultato sarà simile al seguente:

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

Successivamente, è necessario generare una firma per questo file usando il certificato (file con estensione P12) generato per questo ID di tipo pass.

#### <a name="signing-on-a-mac"></a>Accesso a un Mac

Scaricare i **materiali di supporto** per il seeding del portafogli dal sito di [Apple Downloads](https://developer.apple.com/downloads/index.action?name=Passbook) . Usare lo `signpass` strumento per trasformare la cartella in un passaggio (in questo modo vengono calcolati anche gli hash SHA1 e viene eseguito il CAP dell'output in un file con estensione pkpass).

#### <a name="testing"></a>Test

Se si esamina l'output di questi strumenti (impostando il nome del file su. zip e aprendolo), verranno visualizzati i file seguenti (si noti l'aggiunta dei `manifest.json` file e `signature` ):

 [![](passkit-images/image19.png "Esame dell'output di questi strumenti")](passkit-images/image19.png#lightbox)

Una volta firmato, compresso e rinominato il file (ad esempio, a `BananaCoupon.pkpass`) è possibile trascinarlo nel simulatore per eseguire il test o inviarlo tramite posta elettronica a se stessi per recuperare in un dispositivo reale. Verrà visualizzata una schermata per **aggiungere** il passaggio, come indicato di seguito:

 [![](passkit-images/image20.png "Aggiungere la schermata di passaggio")](passkit-images/image20.png#lightbox)

In genere, il processo viene automatizzato in un server, tuttavia la creazione di passaggi manuali potrebbe essere un'opzione per le piccole imprese che creano solo buoni che non richiedono il supporto di un server back-end.

## <a name="wallet"></a>Wallet

Wallet è la parte centrale dell'ecosistema PassKit. Questa schermata mostra il portafogli vuoto e il modo in cui vengono visualizzati l'elenco di pass e i singoli passaggi:

 [![](passkit-images/image21.png "Questa schermata mostra il portafogli vuoto e la modalità di visualizzazione dell'elenco di pass e dei singoli passaggi")](passkit-images/image21.png#lightbox)

Le funzionalità di Wallet includono:

-  È l'unica posizione in cui viene eseguito il rendering con il codice a barre per l'analisi.
-  L'utente può modificare le impostazioni per gli aggiornamenti. Se abilitate, le notifiche push possono attivare gli aggiornamenti ai dati nel passaggio.
-  L'utente può abilitare o disabilitare l'integrazione della schermata di blocco. Se abilitata, consente di visualizzare automaticamente il pass per la schermata di blocco, in base ai dati relativi al tempo e alla posizione incorporati nel passaggio.
-  Il lato inverso del passaggio supporta il pull a Refresh, se viene fornito un URL server Web nel passaggio JSON.
-  Le app complementari possono essere aperte (o scaricate) se l'ID dell'app viene specificato nel passaggio JSON.
-  È possibile eliminare le sessioni (con un'animazione di suddivisione).

## <a name="adding-passes-into-wallet"></a>Aggiunta di pass a Wallet

I passaggi possono essere aggiunti al portafogli nei modi seguenti:

* **App Conduit** : non modificano direttamente i pass, ma semplicemente caricano i file e presentano l'opzione per aggiungerli al portafogli. 

* **App complementari** : vengono scritte dai provider per distribuire i pass e offrire funzionalità aggiuntive per sfogliarli o modificarli. Le applicazioni Novell. iOS hanno accesso completo all'API PassKit per creare e modificare i passaggi. I `PKAddPassesViewController`passaggi possono quindi essere aggiunti al portafogli usando. Questo processo viene descritto più dettagliatamente nella sezione **applicazioni complementari** di questo documento.

### <a name="conduit-applications"></a>Applicazioni Conduit

Le applicazioni Conduit sono app intermedie che potrebbero ricevere i pass per conto di un utente e devono essere programmate per riconoscerne il tipo di contenuto e fornire funzionalità da aggiungere al portafoglio. Esempi di app Conduit includono:

-   **Posta** : riconosce l'allegato come un pass.
-   **Safari** : riconosce il passaggio Content-Type quando si fa clic su un collegamento pass URL.
-   **Altre app personalizzate** : qualsiasi app che riceve allegati o collegamenti aperti (client di social media, lettori di posta elettronica e così via).


Questo screenshot mostra come la **posta elettronica** in iOS 6 riconosca un allegato pass e, quando viene toccato, consente di aggiungerlo al portafogli.

 [![](passkit-images/image22.png "Questo screenshot mostra come la posta in iOS 6 riconosca un allegato pass")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "Questo screenshot mostra come la posta elettronica offre l'aggiunta di un pass Attachment al portafoglio")](passkit-images/image23.png#lightbox)

Se si compila un'app che potrebbe essere un canale per i passaggi, possono essere riconosciuti da:

-  **Estensione di file** :. pkpass
-  **Tipo MIME** -application/vnd. Apple. pkpass
-  **Uti** – com. Apple. pkpass


Il funzionamento di base di un'applicazione Conduit consiste nel recuperare il file pass e chiamare PassKit `PKAddPassesViewController` per fornire all'utente la possibilità di aggiungere il pass al portafogli. L'implementazione di questo controller di visualizzazione viene trattata nella sezione successiva sulle **applicazioni complementari**.

Non è necessario eseguire il provisioning delle applicazioni Conduit per un ID tipo di pass specifico nello stesso modo in cui le applicazioni complementari.

## <a name="companion-applications"></a>Applicazioni complementari

Un'applicazione complementare fornisce funzionalità aggiuntive per l'utilizzo di sessioni, tra cui la creazione di un passaggio, l'aggiornamento delle informazioni associate a un passaggio e la gestione dei passaggi associati all'applicazione.

Le applicazioni complementari non devono tentare di duplicare le funzionalità del portafoglio. Non sono progettati per visualizzare i passaggi per l'analisi.

Nella parte restante di questa sezione viene descritto come creare un'app complementare di base che interagisce con PassKit.

### <a name="provisioning"></a>Provisioning

Poiché Wallet è una tecnologia di archiviazione, è necessario eseguire il provisioning dell'applicazione separatamente e non è possibile usare il profilo di provisioning del team o l'ID app con caratteri jolly. Per creare un ID app univoco e un profilo di provisioning per l'applicazione Wallet, vedere la Guida [uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) .

### <a name="entitlements"></a>Diritti

Il file con **estensione plist dei diritti** deve essere incluso in tutti i progetti Novell. iOS recenti. Per aggiungere un nuovo file con estensione plist dei diritti, seguire i passaggi descritti nella Guida [uso dei diritti](~/ios/deploy-test/provisioning/entitlements.md) .

Per impostare i diritti, procedere come segue:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Fare doppio clic sul file **titles. plist** nel riquadro della soluzione per aprire l'editor dei diritti. plist:

![](passkit-images/image31.png "Diritti. Editor plst")

Nella sezione portafogli selezionare l'opzione **Abilita portafogli**

![](passkit-images/image32.png "Abilita diritti portafogli")


L'opzione predefinita prevede che l'app consenta tutti i tipi di pass. Tuttavia, è possibile limitare l'app e consentire solo un subset di tipi di pass del team. Per abilitare questa impostazione, selezionare **Consenti subset di tipi di pass del team** e immettere l'identificatore del tipo di pass del subset che si desidera consentire.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Fare doppio clic sul file **titles. plist** per aprire il file di origine XML.

Per aggiungere il diritto del portafoglio, impostare la proprietà `Passbook Identifiers` su nell'elenco a discesa, in modo da impostare automaticamente il **tipo** `Array`. Impostare quindi il **valore** della stringa su `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Abilita diritti portafogli")

Permetterà all'app di consentire tutti i tipi di pass. Per limitare l'app e consentire solo un subset di tipi di pass per il team, impostare il valore della stringa su:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Dove `pass.$(CFBundleIdentifier)` è l'ID di passaggio creato in [precedenza](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Debug

In caso di problemi durante la distribuzione dell'applicazione, controllare che si stia usando il **profilo** di provisioning corretto `Entitlements.plist` e che sia selezionato come file dei **diritti personalizzati** nelle opzioni di **firma del bundle iPhone** .

Se si verifica questo errore durante la distribuzione di:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

la matrice `pass-type-identifiers` dei diritti non è corretta (o non corrisponde al **profilo**di provisioning). Verificare che gli ID del tipo di passaggio e l'ID del team siano corretti.

## <a name="classes"></a>Classi

Sono disponibili le seguenti classi PassKit per le app per l'accesso ai passaggi:

-  **PKPass** : istanza di un passaggio.
-  **PKPassLibrary** : fornisce l'API per accedere ai passaggi del dispositivo.
-  **PKAddPassesViewController** : consente di visualizzare un passaggio per l'utente per il salvataggio nel portafogli.
-  **PKAddPassesViewControllerDelegate** -Novell. iOS-sviluppatori

## <a name="example"></a>Esempio

Vedere il progetto PassLibrary nel codice di [esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) per questo articolo. Vengono illustrate le seguenti funzioni comuni che sarebbero necessarie in un'applicazione di portafoglio complementare:

### <a name="check-that-wallet-is-available"></a>Verificare che il portafoglio sia disponibile

Il portafoglio non è disponibile nell'iPad, quindi le applicazioni devono controllare prima di provare ad accedere alle funzionalità di PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Creazione di un'istanza della libreria pass

La libreria PassKit non è un singleton, le applicazioni devono creare e archiviare e l'istanza per accedere all'API PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Ottenere un elenco di passaggi

Le applicazioni possono richiedere un elenco di passaggi dalla libreria. Questo elenco viene filtrato automaticamente in base a PassKit, in modo che sia possibile visualizzare solo i passaggi creati con l'ID team e che sono elencati nei diritti.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Si noti che il simulatore non filtra l'elenco di passaggi restituiti, quindi questo metodo deve essere sempre testato sui dispositivi reali. Questo elenco può essere visualizzato in un UITableView. L' [app di esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) ha un aspetto simile al seguente dopo l'aggiunta di due buoni:

 [![](passkit-images/image29.png "L'app di esempio ha un aspetto simile al seguente dopo l'aggiunta di due buoni")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>Visualizzazione di passaggi

È disponibile un set limitato di informazioni per il rendering dei passaggi nelle app complementari.

Scegliere da questo set di proprietà standard per visualizzare gli elenchi di passaggi, come avviene nel codice di esempio.

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

Questa stringa viene visualizzata come un avviso nell' [esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit):

 [![](passkit-images/image30.png "Avviso del buono selezionato nell'esempio")](passkit-images/image30.png#lightbox)

È anche possibile usare il `LocalizedValueForFieldKey()` metodo per recuperare i dati dai campi nei passaggi progettati (poiché si saprà quali campi devono essere presenti). Il codice di esempio non Mostra questa operazione.

### <a name="loading-a-pass-from-a-file"></a>Caricamento di un passaggio da un file

Poiché un passaggio può essere aggiunto solo al portafogli con l'autorizzazione dell'utente, è necessario presentare un controller di visualizzazione per consentire loro di decidere. Questo codice viene usato nel pulsante **Aggiungi** nell'esempio per caricare un passaggio predefinito incorporato nell'app. è necessario sostituirlo con uno che è stata firmata:

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

Il passaggio viene visualizzato con le opzioni **Aggiungi** e **Annulla** :

 [![](passkit-images/image20.png "Il passaggio presentato con le opzioni Aggiungi e Annulla")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Sostituisci un passaggio esistente

La sostituzione di un pass esistente non richiede l'autorizzazione dell'utente, ma non avrà esito positivo se il passaggio non esiste già.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Modifica di un passaggio

PKPass non è modificabile, pertanto non è possibile aggiornare gli oggetti pass nel codice. Per modificare i dati in un passaggio, un'applicazione deve avere accesso a un server Web che può tenere un record di passaggi e generare un nuovo file di pass con i valori aggiornati che l'applicazione può scaricare.

La creazione del file pass deve essere eseguita in un server perché il passaggio deve essere firmato con un certificato che deve essere mantenuto privato e sicuro.

Dopo la generazione di un file superato aggiornato, usare il `Replace` metodo per sovrascrivere i dati precedenti nel dispositivo.

### <a name="display-a-pass-for-scanning"></a>Visualizza un passaggio per l'analisi

Come indicato in precedenza, solo il portafogli può visualizzare un passaggio per l'analisi. Un passaggio può essere visualizzato usando il `OpenUrl` metodo, come illustrato di seguito:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Ricezione delle notifiche delle modifiche

Le applicazioni possono restare in ascolto delle modifiche apportate alla libreria `PKPassLibraryDidChangeNotification`pass usando. Le modifiche potrebbero essere causate dalle notifiche che attivano gli aggiornamenti in background, quindi è consigliabile metterle in ascolto nell'app.

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

È importante passare un'istanza di libreria durante la registrazione per la notifica perché PKPassLibrary non è un singleton.

## <a name="server-processing"></a>Elaborazione del server

Una descrizione dettagliata della creazione di un'applicazione server per il supporto di PassKit esula dall'ambito di questo articolo introduttivo.

Vedere codice sul lato server Open C# Source [DotNet-Passbook](https://github.com/tomasmcguinness/dotnet-passbook) .

## <a name="push-notifications"></a>Notifiche push

Una descrizione dettagliata dell'uso delle notifiche push per le sessioni di aggiornamento esula dall'ambito di questo articolo introduttivo.

È necessario implementare l'API REST-like definita da Apple per rispondere alle richieste Web da Wallet quando sono necessari aggiornamenti.

Per ulteriori informazioni, vedere la pagina relativa all' [aggiornamento di una guida Pass](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) di Apple.

## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto PassKit, che illustra alcuni dei motivi per cui è utile e descrive le diverse parti che devono essere implementate per una soluzione PassKit completa. Sono stati descritti i passaggi necessari per configurare l'account per sviluppatore Apple per la creazione di pass, il processo per eseguire un passaggio manualmente e anche come accedere alle API PassKit da un'applicazione Novell. iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Portafogli per sviluppatori](https://developer.apple.com/wallet/)
- [Esempio PassKit](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)
- [Guida per gli sviluppatori di Wallet](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [Framework-Apple Pay e Wallet (video WWDC)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [Guida di riferimento a PassKit Framework](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Informazioni di riferimento sul servizio Web Passbook](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [Informazioni sui file pass](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [DotNet-Passbook](https://github.com/tomasmcguinness/dotnet-passbook), una libreria open source per la generazione di pacchetti di portafogli iOS
- [Introduzione a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
