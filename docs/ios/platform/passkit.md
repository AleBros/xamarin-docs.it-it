---
title: PassKit in xamarin. IOS
description: Portafoglio è un'app iOS di sistema che consente di archiviare e visualizza i codici a barre e altre informazioni per collegare le transazioni cliente sul telefono con il mondo reale.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 0a4fd39e312cf96ac59eae97b1212f001c4ef799
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788348"
---
# <a name="passkit-in-xamarinios"></a>PassKit in xamarin. IOS

_Portafoglio è un'app iOS di sistema che consente di archiviare e visualizza i codici a barre e altre informazioni per collegare le transazioni cliente sul telefono con il mondo reale._

Portafoglio è un'app per iPhone e iPod tocca con iOS 6. Archivia e visualizza i codici a barre e altre informazioni per collegare le transazioni cliente sul telefono con il mondo reale. Passa è generati da aziende e inviata al cliente tramite posta elettronica, URL o da un'app di iOS dell'azienda. Portafoglio archivia organizza tutti i passaggi su un telefono e consente di visualizzare i promemoria passata nella schermata di blocco a seconda della data/ora o la posizione del dispositivo.

Questo documento introduce Wallet, tramite l'API di Kit passare con xamarin. IOS e viene illustrato come implementare i passaggi nel server.

 [![](passkit-images/image1.png "Il portafoglio archivia e organizza tutti i passaggi su un telefono")](passkit-images/image1.png#lightbox)


## <a name="requirements"></a>Requisiti

Le funzionalità di archivio Kit illustrate in questo documento richiedono iOS 6 e 4.5 Xcode e xamarin. IOS 6.0.


## <a name="introduction"></a>Introduzione

Problema della chiave che è stato risolto Kit passare è la distribuzione e la gestione di codici a barre. Alcuni esempi reali di come i codici a barre sono attualmente in uso:

-   **Acquisto online il ticket film** : i clienti sono in genere inviati tramite posta elettronica un codice a barre che rappresenta i ticket. Questo codice a barre viene stampato e visualizzata al cinema da analizzare per la voce.
-   **Carte fedeltà** : i clienti di eseguire un numero di schede specifiche dell'archivio diverse portafoglio o reti, per la visualizzazione e l'analisi quando l'acquisto di prodotti.
-   **Coupon** – coupon vengono distribuiti tramite posta elettronica, come printable pagine web, tramite consegna e come i codici a barre in giornali e riviste. I clienti assegnarli a un archivio per l'analisi, per la ricezione di beni, servizi o gli sconti restituito.
-   **Salgono a bordo passa** – simile all'acquisto di un ticket di film.


Passaggio di Kit offre un'alternativa per ognuno di questi scenari:

-   **I ticket film** – dopo l'acquisto, il cliente viene aggiunto un passaggio di Ticket di evento (tramite posta elettronica o un collegamento di sito Web). Come il tempo per gli approcci di film, il passaggio verrà visualizzati automaticamente nella schermata di blocco come promemoria, e in arrivo di cinema il passaggio viene facilmente recuperato e visualizzato in portafoglio per l'analisi.
-   **Carte fedeltà** – anziché (o in aggiunta a) fornendo una scheda fisica, archivi possono rilasciare (tramite posta elettronica o dopo un account di accesso del sito Web) un passaggio di carta di archivio. L'archivio può fornire funzionalità aggiuntive, come l'aggiornamento il saldo del conto al passaggio tramite le notifiche push e servizi geolocation il passaggio di stato vengono visualizzati automaticamente nella schermata di blocco quando il cliente si avvicina un percorso dell'archivio.
-   **Coupon** – Coupon passa può facilmente essere generato con caratteristiche univoche per agevolare il rilevamento e distribuiti tramite i collegamenti di sito Web o di posta elettronica. Coupon scaricato può essere visualizzato automaticamente nella schermata di blocco quando l'utente è vicino a una posizione specifica e/o in una determinata data (ad esempio, quando si avvicina la data di scadenza). Poiché i buoni sono archiviate nel telefono dell'utente, vengono sempre pratica e non venire smarriti. Coupon potrebbe incoraggiare i clienti di scaricare l'App complementare poiché i collegamenti di App Store possono essere incorporati nel passaggio, l'aumento di engagement con il cliente.
-   **Salgono a bordo passa** : dopo un online-processo di archiviazione, il cliente riceve il passaggio di caricamento tramite posta elettronica o un collegamento. Un'App complementare fornita dal provider di trasporto Impossibile includere il processo di archiviazione e anche consentire al cliente di eseguire funzioni aggiuntive come scegliere le postazioni o un pasto. Il provider del trasporto è possibile utilizzare le notifiche push per aggiornare il passaggio se il trasporto viene posticipato o annullato. Come promemoria e per fornire l'accesso rapido per il passaggio come ora di salita a bordo approcci il passaggio verranno visualizzato nella schermata di blocco.


In sostanza, passare Kit fornisce un modo semplice e pratico per archiviare e visualizzare i codici a barre nel dispositivo iPhone o iPod touch. Con il tempo aggiuntivo e l'integrazione di schermata di blocco di percorso, le notifiche push e applicazione complementare integrarlo offre una base per le vendite sofisticate, emissione e la fatturazione di servizi.


## <a name="pass-kit-ecosystem"></a>Passare l'ecosistema Kit

Kit di passaggio non è semplicemente un'API in CocoaTouch, ma fa parte di un ecosistema di App, dati e servizi che semplificano la condivisione protetta e la gestione di codici a barre e altri dati più grande. Questo diagramma di alto livello mostra le diverse entità che possono essere coinvolti nella creazione e utilizzo di passaggi:

 [![](passkit-images/image2.png "Questo diagramma di alto livello mostra le entità coinvolte nella creazione e utilizzo di sessioni")](passkit-images/image2.png#lightbox)

Ogni parte dell'ecosistema dispone di un ruolo definito chiaramente:

-   **Portafoglio** – app predefinite per iOS di Apple (per iPhone e iPod touch) che archivia e Visualizza passate. Questo è l'unico punto passa esegue il rendering per l'utilizzo nel mondo reale (ad esempio il codice a barre viene visualizzato, insieme a tutti i dati localizzati nel passaggio).
-   **App complementare** : app iOS 6 compilate dai provider di passaggio per estendere la funzionalità delle passate rilasciati, ad esempio l'aggiunta di valore a una scheda di archivio, modificare la sede in un passaggio di salita a bordo o altre funzioni di business specifici. App complementare non sono necessari per un passaggio essere utile.
-   **Il server** : un server protetto, in cui i passaggi possono essere generati e firmati per la distribuzione. L'App complementare può connettersi al server per generare nuovi passaggi o richiedono aggiornamenti a sessioni esistenti. Facoltativamente, è possibile implementare le API del servizio web per chiama portafoglio aggiornare passate.
-   **Server APN** : il server è in grado di notificare portafoglio di aggiornamenti da un passaggio in un determinato dispositivo con APNS. Eseguire una notifica push portafoglio che verrà quindi contattare il server per i dettagli della modifica. Non è necessario implementare APNS per questa funzionalità App complementare (può restare in ascolto per il `PKPassLibraryDidChangeNotification` ).
-   **Canale app** – che non modificano direttamente passate (ad esempio, eseguire l'App complementare), ma che consente di migliorare le utilità riconoscendo passate e consentendo loro da aggiungere al portafoglio di applicazioni. I client di posta elettronica, i browser di social network e altre app di aggregazione di dati tutti riscontrati allegati o collegamenti a passate.


L'intero ecosistema aspetto complesso, quindi vale la pena notare che alcuni componenti sono facoltativi e sono possibili implementazioni Kit passare molto più semplice.

## <a name="what-is-a-pass"></a>Che cos'è un passaggio?

Una sessione è una raccolta di dati che rappresentano un ticket, coupon o scheda. Possono essere destinati ad un singolo utilizzo individuale (e pertanto contenere dettagli, ad esempio un'allocazione di numero e postazioni volo) o potrebbe essere un più usare il token che può essere condivisa da un numero qualsiasi di utenti (ad esempio un coupon di sconto). Una descrizione dettagliata è disponibile in Apple [file passare](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) documento.


### <a name="types"></a>Tipi

Attualmente cinque tipi supportati, che possono essere distinti nell'app portafoglio per il layout e il margine superiore del passaggio di:

-  **Ticket eventi** : piccolo ritaglio semicerchio.
-   **Passaggio di caricamento** : è possibile specificare i punti nell'icona lato, specifico del trasporto (ad es. bus, Training, aereo).
-   **Archiviare Card** : arrotondato superiore, ad esempio una carta di credito.
-  **Buono** : perforata lungo il bordo superiore.
-  **Generico** : stesso come scheda di archivio, arrotondato superiore.


In questa schermata vengono visualizzati i tipi di cinque passaggio (nell'ordine: buono, generico, archiviare card, il passaggio di salita a bordo e il ticket eventi):

 [![](passkit-images/image3.png "In questa schermata vengono visualizzati i tipi di cinque passaggio")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Struttura dei file

Un file di sessione è effettivamente un archivio ZIP con un **.pkpass** estensione, contenente alcuni specifici file JSON (obbligatori), un'ampia gamma di immagine del file (facoltativo) e le stringhe localizzate (operazione facoltativa).

-   **pass.JSON** : richiesta. Contiene tutte le informazioni per il passaggio.
-   **manifest.JSON** : richiesta. Contiene gli hash SHA1 per ogni file durante il passaggio ad eccezione del file di firma e questo file (manifest.json).
-   **firma** : richiesta. Creato tramite la firma di `manifest.json` file con il certificato generato nel portale di Provisioning iOS.
-  **logo.PNG** : parametro facoltativo.
-  **background.PNG** : parametro facoltativo.
-  **icona Icon.PNG** : parametro facoltativo.
-  **File di stringhe localizzabili** : parametro facoltativo.


Struttura di directory di un file di passaggio è illustrato di seguito (si tratta del contenuto dell'archivio ZIP):

 [![](passkit-images/image4.png "Qui viene mostrata la struttura di directory di un file di sessione")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON è il formato poiché passa in genere viene creati in un server, significa che il codice di generazione è indipendente dalla piattaforma sul server. I tre componenti chiave di informazioni in ogni passaggio sono:

-   **teamIdentifier** – in questo modo tutte le sessioni è generare al proprio account di App Store. Questo valore è visibile nel portale di Provisioning iOS.
-   **passTypeIdentifier** -registrazione nel portale di Provisioning al gruppo passa insieme (se si produce più di un tipo). Ad esempio, un bar potrebbe creare un tipo di passare archivio scheda per consentire di ottenere i crediti di fedeltà dei clienti, ma anche un tipo distinto di passare Coupon per creare e distribuire coupon di sconto. Tale bar stesso potrebbe anche contenere eventi di musica dal vivo e rilasciare evento Ticket passate per quelli.
-   **serialNumber** : una stringa univoca all'interno di questa `passTypeidentifier` . Il valore è opaco Wallet, ma è importante per tenere traccia delle sessioni specifiche durante la comunicazione con il server.


È un numero elevato di altre chiavi JSON in ogni passaggio, di seguito è riportato un esempio di cui:

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

Sono supportati i formati solo 2D: PDF417, Aztechi, a matrice. Apple le attestazioni che sono inadeguati all'analisi in una schermata di phone retroilluminazione codici a barre 1D.

Testo alternativo visualizzato sotto il codice a barre è facoltativo, alcune aziende desidera essere in grado di lettura/tipo manualmente.

La codifica ISO-8859-1 è il controllo più comune, la codifica utilizzata per i sistemi di analisi che verranno letti i passaggi.

### <a name="relevancy-lock-screen"></a>Rilevanza (schermata di blocco)

Esistono due tipi di dati che possono causare un passaggio da visualizzare nella schermata di blocco:

 **Posizione**

Fino a 10 posizioni può essere specificato in un passaggio, ad esempio, gli archivi che un cliente visita spesso o il percorso di un cinema o aeroporto. Un cliente è stato possibile impostare questi percorsi tramite un'App complementare o il provider è stato possibile determinare le dai dati di utilizzo (se raccolte con l'autorizzazione del cliente).

Quando il passaggio viene visualizzato nella schermata di blocco, viene calcolato un intervallo in modo che quando l'utente esce dall'area il passaggio è nascosta dalla schermata di blocco. Il raggio è associato per il passaggio di stile per evitare possibili abusi.

 **Data e ora**

In un passaggio, è possibile specificare solo una data e ora. Data e ora è utile per l'attivazione di promemoria di schermata di blocco per passaggi di salita a bordo e il ticket di evento.

Può essere aggiornato tramite push o tramite l'API PassKit, in modo che nel caso di un ticket multiuso (ad esempio un ticket stagione un teatro o complesso sportivo) è stato possibile aggiornare la data/ora.

### <a name="localization"></a>Localizzazione

Conversione di un passaggio in più lingue è simile alla localizzazione di un'applicazione iOS: creazione di linguaggio specifica directory con il `.lproj` estensione e inserire gli elementi all'interno di localizzata. Traduzioni di testo devono essere inserite un `pass.strings` file, mentre immagini localizzate hanno lo stesso nome dell'immagine sostituiscono nella radice di passaggio.

## <a name="security"></a>Sicurezza

Passa viene firmati con un certificato privato generate nel portale di Provisioning iOS. I passaggi per la sessione di accesso sono:

1.  Calcolare un hash SHA1 per ogni file nella directory di sessione (non includono il `manifest.json` o `signature` file, nessuno dei quali deve essere presente in questa fase comunque).
1.  Scrivere `manifest.json` come un elenco di chiave/valore JSON di ogni nome di file con il relativo hash.
1.  Utilizzare il certificato per firmare il `manifest.json` file e scrivere il risultato in un file denominato `signature` .
1.  COMPRIMERE il tutto e assegnare al file risulta un `.pkpass` estensione di file.


Poiché la chiave privata è richiesto per firmare il passaggio di, questo processo deve solo su un server protetto che è possibile controllare. NON distribuire le chiavi per tentare di generare passate in un'applicazione.

 
## <a name="configuration-and-setup"></a>Installazione e configurazione

In questa sezione contiene istruzioni per configurare i dettagli di provisioning e creare il primo passaggio.

### <a name="provisioning-passkit"></a>Provisioning PassKit

Affinché un passaggio di immettere l'archivio di App, devono essere collegato a un account sviluppatore. Questa operazione richiede due passaggi:

1.  Il passaggio deve essere registrato utilizzando un identificatore univoco, denominato identificatore del tipo passato.
1.  Per firmare il passaggio con firma digitale dello sviluppatore, è necessario generare un certificato valido.

Per creare le operazioni seguenti a non passare tipo ID.


<a name="create-passid"/>

#### <a name="create-a-pass-type-id"></a>Creare un ID di tipo Pass

Il primo passaggio consiste nell'impostare un ID di tipo passato per ogni diversi _tipo_ della sessione devono essere supportati. L'ID passa (o identificatore di tipo passato) crea un identificatore univoco per il passaggio. Si utilizzerà questo ID per il passaggio di collegamento con l'account sviluppatore utilizza un certificato.

1. Nel [sezione certificati, gli identificatori e i profili del portale di Provisioning iOS](https://developer.apple.com/account/overview.action), passare a **identificatori** e selezionare **passare gli ID di tipo** . Selezionare quindi il **+** pulsante per creare un nuovo tipo di passaggio: [ ![ ] (passkit-images/passid.png "creare un nuovo tipo di passaggio")](passkit-images/passid.png#lightbox)

2.   Fornire un **descrizione** (nome) e **identificatore** (stringa univoca) per il passaggio. Si noti che tutti gli ID tipo passato deve iniziare con la stringa `pass.` In questo esempio viene utilizzata `pass.com.xamarin.coupon.banana` : [ ![ ] (passkit-images/register.png "fornire una descrizione e un identificatore")](passkit-images/register.png#lightbox)


3.   Confermare l'ID passaggio premendo il **registrare** pulsante.


<a name="generate" />

#### <a name="generate-a-certificate"></a>Generare un certificato

Per creare un nuovo certificato per l'ID di tipo passato, eseguire le operazioni seguenti:

1.  Selezionare l'ID appena creato di passare dall'elenco e fare clic su **modifica** : [ ![ ] (passkit-images/pass-done.png "selezionare il nuovo ID di passare dall'elenco")](passkit-images/pass-done.png#lightbox)

    Selezionare quindi **Create Certificate...** :

    [![](passkit-images/cert-dist.png "Selezionare Crea certificato")](passkit-images/cert-dist.png#lightbox)


2.  Seguire i passaggi per creare una firma richiesta certificato (CSR).
  
3. Premere il **continua** pulsante il portale per sviluppatori e caricare la richiesta CSR per generare il certificato.

4. Scaricare il certificato e fare doppio clic su di essa per installarlo nel portachiavi.


Ora che è stato creato un certificato per l'ID di tipo passato, la sezione successiva viene descritto come creare manualmente una sessione.

Per ulteriori informazioni sul Provisioning per Wallet, consultare il [utilizzo con funzionalità](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) Guida.

 <a name="Create_a_Pass_Manually" />

### <a name="create-a-pass-manually"></a>Creare un passaggio manuale

Ora che abbiamo creato il tipo passato è possibile creare manualmente un passaggio per eseguire il test in un dispositivo o simulatore. I passaggi per creare un passaggio sono:

-  Creare una directory per i file di sessione.
-  Creare un file pass.json che contiene tutti i dati richiesti.
-  Includere immagini nella cartella (se richiesto).
-  Calcolare l'hash SHA1 per ogni file nella cartella e scrivere manifest.json.
-  Firmare il file di certificato p12 manifest.json.
-  COMPRIMERE il contenuto della directory e rinominare con estensione .pkpass.


Esistono alcuni file di origine nel codice di esempio per l'articolo che può essere utilizzato per generare un passaggio. Utilizzare i file nel `CouponBanana.raw` directory della directory CreateAPassManually. I seguenti file sono presenti:

 [![](passkit-images/image18.png "Questi file sono presenti")](passkit-images/image18.png#lightbox)

Aprire pass.json e modificare la stringa JSON. È necessario aggiornare almeno il `passTypeIdentifier` e `teamIdentifer` in modo che corrisponda all'account per sviluppatori di Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

È quindi necessario calcolare l'hash per ogni file e creare il `manifest.json` file. Avrà un aspetto simile al seguente al termine:

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

Successivamente è necessario generare una firma per il file utilizzando il certificato (. p12 file) che è stato generato per l'ID di tipo Pass

 <a name="Signing_On_a_Mac" />


#### <a name="signing-on-a-mac"></a>La firma su un Mac

Scaricare il **materiale di supporto di valore di inizializzazione portafoglio** dal [Scarica Apple](https://developer.apple.com/downloads/index.action?name=Passbook) sito. Utilizzare il `signpass` strumento per trasformare la cartella in un passaggio (questa verrà calcolato anche un hash SHA1 e ZIP l'output in un file .pkpass).

 <a name="Signing_On_a_PC" />


#### <a name="signing-on-a-pc"></a>La firma in un computer

Nell'esempio di codice per questo articolo si è un progetto denominato `signpassnet` che in Windows viene eseguito in .NET. Tenta di riprodurre lo strumento Apple tuttavia include molto meno codice di convalida.

 <a name="Testing" />


#### <a name="testing"></a>Test

Se fosse necessario esaminare l'output di questi strumenti (per impostare il nome del file con estensione zip e quindi aprirlo), si vedrà i file seguenti (si noti l'aggiunta del `manifest.json` e `signature` file):

 [![](passkit-images/image19.png "Esaminare l'output di questi strumenti")](passkit-images/image19.png#lightbox)

Dopo aver effettuato l'accesso, ZIP e rinominare il file (ad es. per `BananaCoupon.pkpass`) è possibile trascinarlo nel simulatore per testare o inviare tramite posta elettronica all'utente di recuperare in un dispositivo reale. Verrà visualizzata una schermata per **Aggiungi** il passaggio, simile al seguente:

 [![](passkit-images/image20.png "Aggiungere la schermata di passaggio")](passkit-images/image20.png#lightbox)

In genere tale processo potrebbe essere automatizzato in un server, creazione di passaggio manuale, tuttavia potrebbe essere un'opzione per le piccole aziende che creano solo coupon che non richiedono il supporto di un server back-end.

 <a name="Wallet" />

## <a name="wallet"></a>Wallet

Portafoglio è la parte centrale dell'ecosistema di Kit passare. Questa schermata è riportato il portafoglio vuoto e aspetto l'elenco di passaggio e i singoli passaggi:

 [![](passkit-images/image21.png "Questa schermata è riportato il portafoglio vuoto e aspetto l'elenco di passaggio e i singoli passaggi")](passkit-images/image21.png#lightbox)

Le funzionalità di portafoglio includono:

-  È l'unico punto che viene eseguito il rendering passa con il codice a barre per l'analisi.
-  Utente può modificare le impostazioni di aggiornamenti. Se abilitata, le notifiche push possono attivare gli aggiornamenti dei dati durante il passaggio.
-  Utente può abilitare o disabilitare l'integrazione di schermata di blocco. Se abilitata, questo consente il passaggio da vengono automaticamente visualizzate sullo schermo del blocco, basato sul tempo e la posizione i dati rilevanti incorporati nel passaggio.
-  Il lato opposto del passaggio di supporta pull per l'aggiornamento, se viene fornito un URL di server web in JSON passare.
-  Può essere aperta complementare App (o scaricato) se viene specificato l'ID dell'app nel file JSON passare.
-  Passa può essere eliminata (con un'animazione frantumazione mentre).


 <a name="Getting_Passes_into_Wallet" />

## <a name="adding-passes-into-wallet"></a>Aggiunta di passate in portafoglio

È possibile aggiungere passaggi al portafoglio nei modi seguenti:

* **Canale app** – passa questi non vengano modificati direttamente, ma si limitano a caricare i file di sessione e presentare all'utente con l'opzione di aggiungerli al portafoglio. 

* **App complementare** – questi vengono scritti dai provider per distribuire passate e offrono funzionalità aggiuntive per individuare o modificarli. Xamarin. IOS applicazioni dispongono dell'accesso completo all'API di Kit passare per creare e modificare passaggi. Passa quindi può essere aggiunti al portafoglio utilizzando il `PKAddPassesViewController`. Questo processo è descritto più dettagliatamente il **applicazioni complementare** sezione di questo documento.

### <a name="conduit-applications"></a>Applicazioni di canale

Le applicazioni di canale sono App intermedia che potrebbe essere visualizzato passate per conto dell'utente e deve essere programmata per riconoscere il tipo di contenuto e fornisce funzionalità per aggiungere il portafoglio. Esempi di canale App:

-   **Posta elettronica** – riconosce allegato come un passaggio.
-   **Safari** – riconosce il tipo di contenuto passare quando si fa clic sul collegamento URL passare.
-   **Altre applicazioni personalizzate** : qualsiasi app che ricevono gli allegati o aprire i collegamenti (client di social networking, lettori di posta elettronica e così via).


Questa schermata è riportato come **posta** in iOS 6 riconosce un allegato di passaggio e (quando interessate) offre ai **Aggiungi** a portafoglio.

 [![](passkit-images/image22.png "Questa schermata è riportato come posta elettronica in iOS 6 riconosce un allegato di passaggio")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "Questa schermata è riportato come posta elettronica sono disponibili aggiungere un allegato di passare a portafoglio")](passkit-images/image23.png#lightbox)

Se si compila un'applicazione che può essere un canale per le sessioni, possono essere riconosciuti da:

-  **Estensione di file** -.pkpass
-  **Tipo MIME** -application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


Il funzionamento di base di un'applicazione di canale consiste nel recuperare il file di sessione e chiamare passare Kit `PKAddPassesViewController` per consentire all'utente la possibilità di aggiungere il passaggio per il portafoglio. L'implementazione di questo controller di visualizzazione viene descritta nella sezione successiva in **complementare applicazioni**.

Applicazioni di canale non è necessario eseguire il provisioning per un ID di tipo specifico passare nello stesso modo che le applicazioni complementare.

## <a name="companion-applications"></a>Applicazioni complementare

Un'applicazione complementare fornisce funzionalità aggiuntive per l'utilizzo di sessioni, inclusa la creazione di un passaggio, l'aggiornamento delle informazioni associate a un passaggio e la gestione delle sessioni associate all'applicazione.

Applicazioni complementare non tentare di duplicare le funzionalità di portafoglio. Non sono progettati per visualizzare i passaggi per l'analisi.

Il resto di questa sezione viene descritto come compilare un' complementare App di base che interagisce con il Kit di passare.

### <a name="provisioning"></a>Provisioning

Poiché portafoglio è una tecnologia di archivio, l'applicazione è necessario eseguire il provisioning separatamente e non è possibile utilizzare il profilo di Provisioning Team o Wildcard App ID. Fare riferimento al [utilizzo con funzionalità](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) Guida per creare un profilo di Provisioning e un ID App univoci per l'applicazione portafoglio.

### <a name="entitlements"></a>Diritti

Il **Entitlements.plist** file deve essere incluso nel progetto xamarin tutti recente. Per aggiungere un nuovo file Entitlements.plist, seguire i passaggi di [funziona con i diritti](~/ios/deploy-test/provisioning/entitlements.md) Guida.

Per impostare i diritti di eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Fare doppio clic su di **Entitlements.plist** file nel blocco note per aprire l'editor Entitlements.plist soluzione:

![](passkit-images/image31.png "Editor Entitlements.plst")

Nella sezione Wallet, selezionare il **abilitare portafoglio** opzione

![](passkit-images/image32.png "Abilitare il diritto portafoglio")


L'opzione predefinita è per l'app consentire a che tutti i passi i tipi. Tuttavia, è possibile limitare l'app e consente solo un subset dei tipi di passaggio del team. Per abilitare questo, selezionare il **Consenti subset del team passare tipi** e immettere l'identificatore del tipo di passaggio del subset che si desidera consentire.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Fare doppio clic su di **Entitlements.plist** file da aprire il file di origine XML.

Per aggiungere il diritto di Wallet, impostare il **proprietà** a `Passbook Identifiers` nella casella di riepilogo, che verrà impostato automaticamente il **tipo** `Array`. Quindi, impostare la stringa **valore** a `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Abilitare il diritto portafoglio")

Permetterà all'app di consentire tutti i tipi di pass. Per limitare l'app e consente solo un subset di tipi di passaggio di team Foundation, impostare il valore di stringa:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Dove `pass.$(CFBundleIdentifier)` è l'ID passaggio che è stato creato [sopra](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Debug

Se si verificano problemi di distribuzione dell'applicazione, verificare che si sta utilizzando il corretto **profilo di Provisioning** e che il `Entitlements.plist` sia selezionato come il **i diritti personalizzati** file il **iPhone firma Bundle** opzioni.

Se questo errore si verifica durante la distribuzione:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

il `pass-type-identifiers` matrice diritti non è corretto (o non corrisponde la **profilo di Provisioning**). Verificare che gli ID di tipo passato e l'ID del Team siano corrette.

 <a name="Classes" />

## <a name="classes"></a>Classi

Le classi di passare Kit seguenti sono disponibili per App accedere ai passaggi:

-  **PKPass** : un'istanza di un passaggio.
-  **PKPassLibrary** : fornisce l'API per accedere a passate nel dispositivo.
-  **PKAddPassesViewController** : consente di visualizzare un passaggio per l'utente di salvare nella loro portafoglio.
-  **PKAddPassesViewControllerDelegate** : consente agli sviluppatori di xamarin


## <a name="example"></a>Esempio

Fare riferimento al progetto PassLibrary nell'esempio di codice per questo articolo. Vengono illustrate le seguenti funzioni comuni necessari in un'applicazione complementare portafoglio:

### <a name="check-that-wallet-is-available"></a>Verificare che portafoglio è disponibile

Portafoglio non è disponibile in un iPad, pertanto le applicazioni devono controllare prima di tentare di accedere alle funzionalità di Kit passare.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Creazione di un'istanza di libreria del passaggio

La libreria passare Kit non è un singleton, le applicazioni è necessario creare e archiviare e istanza per accedere all'API di Kit passare.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Ottenere un elenco di passaggi

Le applicazioni possono richiedere un elenco di passaggi dalla libreria. Questo elenco viene filtrato automaticamente da Kit, passare in modo che sia possibile visualizzare solo i passaggi che sono stati creati con l'ID Team e che sono elencate nei diritti.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Si noti che il simulatore non filtrare l'elenco di passa restituito, pertanto questo metodo deve sempre essere testato nei dispositivi reali. Dopo essere stati aggiunti due coupon, questo elenco può essere visualizzato in un UITableView, l'esempio app un aspetto simile al seguente:

 [![](passkit-images/image29.png "L'aspetto di app di esempio come segue dopo essere stati aggiunti due coupon")](passkit-images/image29.png#lightbox)


### <a name="displaying-passes"></a>La visualizzazione passa

Un set limitato di informazioni è disponibile per il rendering di passaggi all'interno di App complementare.

Scegliere il set di proprietà standard per visualizzare gli elenchi di passate, come nell'esempio di codice.

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

Questa stringa viene visualizzata come un avviso nell'esempio:

 [![](passkit-images/image30.png "Nell'esempio di avviso Coupon selezionato")](passkit-images/image30.png#lightbox)

È inoltre possibile utilizzare il `LocalizedValueForFieldKey()` metodo per recuperare dati da campi nei passaggi è stata progettata (poiché si conosceranno i campi devono essere presenti). Nell'esempio di codice non viene visualizzato questo.

### <a name="loading-a-pass-from-a-file"></a>Caricamento di un passaggio da un File

Poiché un passaggio può essere aggiunti solo a portafoglio con l'autorizzazione dell'utente, un controller di visualizzazione deve essere presentato per consentire di decidere. Questo codice viene utilizzato nel **Aggiungi** pulsante nell'esempio, per caricare un passaggio predefinito che è incorporato nell'app (è necessario sostituirlo con uno che si accede):

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

Viene visualizzato il passaggio di **Aggiungi** e **Annulla** opzioni:

 [![](passkit-images/image20.png "Il passaggio di visualizzate le opzioni Aggiungi e Annulla")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Sostituire un passaggio esistente

Sostituzione di un passaggio esistente non richiede l'autorizzazione dell'utente, tuttavia non riuscirà se la sessione non esiste già.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>La modifica di un passaggio

PKPass non modificabile, quindi non è possibile aggiornare gli oggetti di sessione nel codice. Per modificare i dati in un passaggio di un'applicazione deve avere accesso a un server web che è possono mantenere un record di passaggi e generare un nuovo file di sessione con i valori aggiornati di cui è possibile scaricare l'applicazione.

Passaggio di creazione di file deve essere eseguita in un server in quanto passa deve essere firmate con un certificato che deve essere mantenuto private e protette.

Una volta è stato generato un file aggiornato di passaggio, utilizzare il `Replace` metodo per sovrascrivere i vecchi dati nel dispositivo.

### <a name="display-a-pass-for-scanning"></a>Visualizzare un passaggio per l'analisi

Come evidenziato in precedenza, solo portafoglio consente di visualizzare un passaggio per l'analisi. Un passaggio può essere visualizzato utilizzando il `OpenUrl` metodo come illustrato:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Ricezione di notifiche di modifiche

Le applicazioni possono restare in ascolto per le modifiche apportate alla libreria passare utilizzando il `PKPassLibraryDidChangeNotification`. Le modifiche potrebbero essere causate dalle notifiche attivare gli aggiornamenti in background, pertanto è consigliabile attendere nell'app.

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

Una descrizione dettagliata della creazione di un'applicazione server per supportare Kit passare non rientra nell'ambito di questo articolo introduttivo.

Il codice .NET fornito nel *signpassnet* esempio può essere utilizzato come base per un metodo sul lato server che può generare passate.

Visualizzazione [WWDC Video: introduzione Passbook, parte 2](https://developer.apple.com/videos/wwdc/2012/?include=309#309) da 27:00 minuti per altre informazioni.

### <a name="other-resources"></a>Altre risorse

Vedere [dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook) aprire codice lato server di origine c#.

## <a name="push-notifications"></a>Notifiche push

Una descrizione dettagliata dell'utilizzo di notifiche push per aggiornare passate non rientra nell'ambito di questo articolo introduttivo.

È necessario per implementare l'API REST simile definita da Apple per rispondere alle richieste web da portafoglio quando sono necessari aggiornamenti. Il codice .NET fornito nel *signpassnet* esempio può essere utilizzato come base per la generazione di passa di nuovo in seguito a tali richieste.

Visualizzazione [WWDC Video: introduzione Passbook, parte 2](https://developer.apple.com/videos/wwdc/2012/?include=309#309) da 27:00 minuti per altre informazioni.

## <a name="summary"></a>Riepilogo

In questo articolo introdotto passare Kit, descritti alcuni dei motivi per cui è utile e descritte le diverse parti che devono essere implementate per una soluzione completa di Kit passare. Descritti i passaggi necessari per configurare l'account per sviluppatori di Apple per creare viene superato, il processo per un passaggio manuale e anche come accedere alle API di Kit passare da un'applicazione di xamarin. IOS.

## <a name="related-links"></a>Collegamenti correlati

- [CreateAPassManually (esempio)](https://developer.xamarin.com/samples/PassKit/)
- [Esempio PassKit](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [Introduzione a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Guida per programmatori Passbook](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Conceptual/PassKit_PG/Chapters/Introduction.html)
- [Passbook per gli sviluppatori](https://developer.apple.com/passbook/)
- [Informazioni sui file di sessione](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [Passare il riferimento Framework Kit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passare il riferimento Framework Kit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Riferimento al servizio Web Passbook](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
