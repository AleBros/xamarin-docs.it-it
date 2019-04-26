---
title: PassKit in xamarin. IOS
description: L'app portafoglio consente agli utenti di archiviare passa digitali nei propri dispositivi di iOS. Il framework di PassKit consente agli sviluppatori di interagire con passa a livello di codice.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: d1c640bef41e875b3bb427d657c9c239e4c3e16d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61192674"
---
# <a name="passkit-in-xamarinios"></a>PassKit in xamarin. IOS

L'app per iOS portafoglio consente agli utenti di archiviare passa digitali nei propri dispositivi.
Questi passaggi sono generati da commercianti e inviati al cliente tramite posta elettronica, gli URL, o tramite l'app per iOS dell'azienda. Questi passaggi possono rappresentare vari aspetti, dalla vendita di biglietti film alle carte fedeltà per imbarco. Il framework di PassKit consente agli sviluppatori di interagire con passa a livello di codice.

Questo documento introduce portafoglio e usando l'API di PassKit con xamarin. IOS.

 [![](passkit-images/image1.png "Il portafoglio archivia e organizza tutti i passaggi in un telefono")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Requisiti

Le funzionalità di PassKit illustrate in questo documento richiedono iOS 6 e Xcode 4.5, insieme a xamarin. IOS 6.0.

## <a name="introduction"></a>Introduzione

Il problema chiave che è stato risolto PassKit è la distribuzione e la gestione di codici a barre. Alcuni esempi reali di come i codici a barre sono attualmente in uso:

-   **Acquisto di biglietti film online** : i clienti sono in genere inviati tramite posta elettronica un codice a barre che rappresenta dei ticket. Questo codice a barre viene visualizzato e impiegato per il cinema da analizzare per la voce.
-   **Le carte fedeltà** : i clienti di eseguono un numero di diverse schede specifiche dell'archivio nel loro portafoglio o conservarle, per la visualizzazione e l'analisi quando acquistano beni di consumo.
-   **Buoni** – buoni vengono distribuiti tramite posta elettronica, come pagine web stampabile, tramite consegna e come i codici a barre in giornali e riviste. I clienti indirizzati a un archivio per l'analisi, per la ricezione in cambio di prodotti, servizi o sconti.
-   **Imbarco** – simile all'acquisto di un ticket di film.

PassKit offre un'alternativa per ognuno di questi scenari:

-   **I ticket di film** – dopo l'acquisto, il cliente aggiunge un passaggio di ticket di evento (tramite posta elettronica o un collegamento di sito Web). Come il tempo per gli approcci di film, il passaggio di verrà visualizzati automaticamente nella schermata di blocco come promemoria, e nel luogo al cinema il passaggio viene facilmente recuperato e visualizzato nel portafoglio per l'analisi.
-   **Le carte fedeltà** – anziché (o oltre a) presentando una scheda fisica, archivi possono rilasciare (tramite posta elettronica o dopo un account di accesso sito Web) un passaggio di carta Store. L'archivio può fornire funzionalità aggiuntive, ad esempio aggiornare il saldo dell'account al passaggio tramite notifiche push e uso dei servizi di georilevazione il passaggio è stato possibile visualizzati automaticamente nella schermata di blocco quando il cliente si avvicina un percorso dell'archivio.
-   **Buoni** – passa buono può facilmente essere generato con caratteristiche univoche per facilitare il rilevamento e distribuiti tramite i collegamenti di posta elettronica o un sito Web. Buoni scaricati possono essere visualizzato automaticamente nella schermata di blocco quando l'utente è in prossimità di un percorso specifico e/o in una determinata data (ad esempio quando sta per raggiungere la data di scadenza). Poiché i buoni vengono archiviati nel telefono dell'utente, sono sempre utili e non venire smarriti. Buoni potrebbero invita i clienti di scaricare App complementare perché i collegamenti di App Store possono essere incorporati nel passaggio, l'aumento di engagement con il cliente.
-   **Imbarco** – dopo un controllo aggiuntivo processo online, il cliente riceverebbe loro d'imbarco tramite posta elettronica o un collegamento. Un'App complementare fornita dal provider di trasporto è stato possibile includere il processo di controllo e anche consentire al cliente di eseguire funzioni aggiuntive, ad esempio scegliendo le postazioni o un pasto. Il provider del trasporto è possibile usare le notifiche push per aggiornare il passaggio se il trasporto viene posticipato o annullato. Come gli approcci ora salita il passaggio verrà visualizzato nella schermata di blocco come promemoria e per fornire accesso rapido per il passaggio.

In sostanza, PassKit fornisce un modo semplice e pratico per archiviare e visualizzare i codici a barre nel dispositivo iOS. Con l'ulteriore tempo e l'integrazione di schermata di blocco di posizione, le notifiche push e applicazione complementare integrarla garantisce una base per le vendite molto sofisticate, emissione di ticket e servizi di fatturazione.

## <a name="passkit-ecosystem"></a>Ecosistema di PassKit

PassKit non è semplicemente un'API all'interno di CocoaTouch, ma fa parte di un più grande ecosistema di App, dati e servizi che semplificano la condivisione sicura e gestione di codici a barre e altri dati. Questo diagramma di alto livello illustra le diverse entità che possono essere coinvolti nella creazione e utilizzo di passaggi:

 [![](passkit-images/image2.png "Questo diagramma di alto livello mostra le entità coinvolte nella creazione e utilizzo di sessioni")](passkit-images/image2.png#lightbox)

Ogni parte dell'ecosistema dispone di un ruolo chiaramente definito:

-   **Portafoglio** – app iOS predefinita di Apple che memorizza e visualizza i passaggi. Questo è l'unica che passa viene sottoposti a rendering per l'uso nel mondo reale (ad esempio il codice a barre viene visualizzato, insieme a tutti i dati localizzati nella sessione).
-   **Companion app** : le app iOS 6 compilate per passare i provider per estendere la funzionalità delle passate rilasciati, ad esempio l'aggiunta di valore a una scheda di archivio, modificando la partecipazione a una carta d'imbarco o un'altra funzione di business specifici. Le app complementare non sono necessarie per un passaggio utile.
-   **Il server** : un server protetto in cui pass può essere generato e firmato per la distribuzione. L'App complementare può connettersi al server per generare nuovi passaggi o richiedono aggiornamenti a sessioni esistenti. È facoltativamente possibile implementare web API del servizio che chiamerebbe portafoglio aggiornare passa.
-   **I server APNS** : il server è in grado di notificare portafoglio di aggiornamenti da un passaggio in un determinato dispositivo tramite APNS. Una notifica push a Wallet che verrà quindi contattare il server per i dettagli della modifica. App complementare non è necessario implementare APNS per questa funzionalità (si può restare in ascolto per il `PKPassLibraryDidChangeNotification` ).
-   **Le app conduit** : le applicazioni che non modificano direttamente i passa (ad esempio, le app complementare farlo), ma che può migliorare le utilità riconoscendo passate e consentendo loro di essere aggiunto a portafoglio. Client di posta elettronica, i browser di rete basati su social network e altre app di aggregazione dati tutte riscontrati allegati o i collegamenti a pass.

L'intero ecosistema Cerca complessa, in modo che vale la pena notare che alcuni componenti sono facoltativi e sono possibili le implementazioni di PassKit molto più semplice.

## <a name="what-is-a-pass"></a>Che cos'è Pass?

Un passaggio è una raccolta di dati che rappresentano un ticket, buono o scheda. Può essere pensato per un singolo utilizzo individuale (e pertanto contengono informazioni dettagliate, ad esempio un'allocazione postazione e numero di volo) oppure è possibile che sia un più usare il token che può essere condiviso da un numero qualsiasi di utenti (ad esempio un coupon di sconto). Una descrizione dettagliata è disponibile in Apple [sui file passare](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) documento.

### <a name="types"></a>Tipi

Attualmente cinque tipi supportati, che possono essere distinti nell'app portafoglio dal bordo del passaggio di layout e la parte superiore:

-  **Ticket eventi** – ritaglio semicircolare piccole.
-   **Onboarding del Pass** – è possibile specificare punti nell'icona laterale, specifiche del trasporto (ad es. bus, eseguire il training, aereo).
-   **Store Card** : arrotondato superiore, ad esempio una carta di credito o di debito.
-  **Buono** : perforate lungo il bordo superiore.
-  **Generico** : uguale allo Store carta, arrotondato superiore.


In questa schermata vengono visualizzati i tipi di cinque pass (nell'ordine: buono, generico, archiviare carta d'imbarco e ticket eventi):

 [![](passkit-images/image3.png "In questa schermata vengono visualizzati i tipi di cinque pass")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Struttura di file

Un file pass è in realtà un archivio ZIP con un **.pkpass** , contenente alcuni specifici file JSON (obbligatori), un'ampia gamma di immagine i file con estensione (facoltativo), nonché le stringhe localizzate (facoltativo).

-   **pass.JSON** : obbligatorio. Contiene tutte le informazioni per il passaggio.
-   **manifest** : obbligatorio. Contiene gli hash SHA1 per ogni file nel passaggio di tranne il file della firma e questo file (manifest).
-   **firma** : obbligatorio. Creato tramite la firma di `manifest.json` file con il certificato generato nel portale di Provisioning iOS.
-  **logo. PNG** : facoltativo.
-  **background.PNG** : facoltativo.
-  **Icon. PNG** : facoltativo.
-  **I file di stringhe localizzabili** : facoltativo.

Struttura di directory di un file pass è illustrato di seguito (si tratta del contenuto dell'archivio ZIP):

 [![](passkit-images/image4.png "Struttura di directory di un file pass è illustrato di seguito")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON è il formato che passa in genere viene creato in un server – significa che il codice di generazione è indipendente dalla piattaforma sul server. Le tre parti principali di informazioni in ogni passaggio sono:

-   **teamIdentifier** – questo collega tutti i passaggi generare al proprio account di App Store. Questo valore è visibile nel portale di Provisioning iOS.
-   **passTypeIdentifier** – registrati nel portale di Provisioning per gruppo passa tra loro (se si produce più di un tipo). Ad esempio, un bar potrebbe creare un tipo di pass carta store per consentire ai clienti di guadagnare crediti fedeltà, ma anche un buono separato passare tipo per creare e distribuire buoni sconto. Quel bar stesso potrebbe anche contenere eventi di musica dal vivo ed emettere Ticket eventi passa per quelle.
-   **serialNumber** – una stringa univoca all'interno di questo `passTypeidentifier` . Il valore è opaco a Wallet, ma è importante per tenere traccia delle sessioni specifiche quando si comunica con il server.

È presente un numero elevato di altre chiavi JSON in ogni passaggio, di seguito è riportato un esempio dei quali:

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

Sono supportati solo i formati 2D: PDF417, Aztec, QR. Apple richiede che i codici a barre 1D siano inadeguati per l'analisi in una schermata di phone retroilluminazione.

Testo alternativo visualizzato sotto il codice a barre è facoltativo; alcuni merchants desidera essere in grado di leggere/tipo manualmente.

La codifica ISO-8859-1 è il controllo più comune, la codifica utilizzata per i sistemi di analisi che leggeranno i tuoi buoni.

### <a name="relevancy-lock-screen"></a>Rilevanza (schermata di blocco)

Esistono due tipi di dati che possono causare un passaggio da visualizzare nella schermata di blocco:

 **Posizione**

È possibile specificare fino a 10 posizioni in una sessione, ad esempio gli archivi che un cliente visita frequentemente o alla posizione di un aeroporto o di cinema. Un cliente è stato possibile impostare questi percorsi tramite un'App complementare o il provider è stato possibile determinare li dai dati di utilizzo (se raccolte con l'autorizzazione del cliente).

Quando il passaggio viene visualizzato nella schermata di blocco, un limite viene calcolato in modo che quando l'utente lascia l'area di pass è nascosto dalla schermata di blocco. Per il passaggio di stile in modo da impedirne l'uso improprio è associato il raggio.

 **Data e ora**

In un passaggio, è possibile specificare solo una data/ora. Data e ora è utile per i promemoria di schermata di blocco per imbarco e biglietti per eventi di trigger.

Possono essere aggiornati tramite push o API di PassKit, in modo che è stato possibile aggiornare la data/ora nel caso di un ticket multiuso (ad esempio, un ticket di stagione una sala o complesso sportivo).

### <a name="localization"></a>Localizzazione

Conversione di un passaggio in più lingue è simile alla localizzazione di un'applicazione iOS: creare le directory specifiche con linguaggio il `.lproj` estensione e posizionare gli elementi all'interno di localizzata. Traduzioni testuali devono essere inserite un `pass.strings` file, mentre le immagini localizzate hanno lo stesso nome dell'immagine sostituiscono nella radice del Pass.

## <a name="security"></a>Sicurezza

Passa viene firmato con un certificato privato generato nel portale di Provisioning iOS. La procedura di accesso del passaggio è:

1.  Calcolare un hash SHA1 per ogni file nella directory di sessione (non includere il `manifest.json` o `signature` file, nessuno dei quali devono essere presenti in questa fase comunque).
1.  Scrivere `manifest.json` come elenco di ogni nome di file con hash della relativa chiave/valore JSON.
1.  Usare il certificato per firmare il `manifest.json` file e scrivere il risultato in un file denominato `signature` .
1.  COMPRIMERE il e fornire il file risultante un `.pkpass` estensione di file.


Poiché la chiave privata è necessario effettuare il passaggio, questo processo deve essere eseguito solo su un server protetto che si controlla. NON distribuire le chiavi per tentare di generare passate in un'applicazione.

 
## <a name="configuration-and-setup"></a>Installazione e configurazione

Questa sezione contiene istruzioni per configurare i dettagli di provisioning e creare il primo passaggio.

### <a name="provisioning-passkit"></a>Il provisioning di PassKit

Affinché un passaggio di immettere l'App Store, deve essere collegato a un account per sviluppatore. Questa operazione richiede due passaggi:

1.  Il passaggio deve essere registrato utilizzando un identificatore univoco, chiamato l'ID del tipo passato.
1.  Per firmare il passaggio con firma digitale per gli sviluppatori, è necessario generare un certificato valido.

Per creare il seguente do passare ID del tipo.

#### <a name="create-a-pass-type-id"></a>Creare un ID di tipo Pass

Il primo passaggio consiste nel configurare un ID del tipo di Pass per ogni diversa _tipo_ della sessione devono essere supportati. L'ID del Pass (o identificatore di tipo passare) crea un identificatore univoco per il passaggio. Si userà questo ID per collegare il passaggio con l'account per sviluppatore usando un certificato.

1. Nel [certificati, identificatori e profili di sezione del portale di Provisioning iOS](https://developer.apple.com/account/overview.action), passare alla **identificatori** e selezionare **passare gli ID di tipo** . Quindi selezionare il **+** pulsante per creare un nuovo tipo di pass: [![](passkit-images/passid.png "Creare un nuovo tipo di pass")](passkit-images/passid.png#lightbox)

2.   Fornire una **Description** (nome) e **identificatore** (stringa univoca) per il passaggio. Si noti che tutti gli ID tipo passato deve iniziare con la stringa `pass.` In questo esempio usiamo `pass.com.xamarin.coupon.banana` : [![](passkit-images/register.png "Fornire una descrizione e un identificatore")](passkit-images/register.png#lightbox)


3.   Confermare l'ID del Pass premendo la **registrare** pulsante.

#### <a name="generate-a-certificate"></a>Generare un certificato

Per creare un nuovo certificato per questo ID del tipo di Pass, eseguire le operazioni seguenti:

1.  Selezionare l'ID appena creato passare dall'elenco e fare clic su **modifica** : [![](passkit-images/pass-done.png "Selezionare il nuovo ID di passare dall'elenco")](passkit-images/pass-done.png#lightbox)

    Selezionare quindi **Create Certificate...** :

    [![](passkit-images/cert-dist.png "Selezionare Crea certificato")](passkit-images/cert-dist.png#lightbox)


2.  Seguire i passaggi per creare una firma richiesta certificato (CSR).
  
3. Premere il **continuazione** pulsante nel portale per sviluppatori e caricare il file CSR per generare il certificato.

4. Scaricare il certificato e fare doppio clic su di esso per installarlo nel keychain.


Ora che è stato creato un certificato per questo ID del tipo di Pass, nella sezione successiva descrive come creare un passaggio manuale.

Per altre informazioni sul Provisioning per portafoglio, vedere la [uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) Guida.

### <a name="create-a-pass-manually"></a>Creare un passaggio manuale

Ora che abbiamo creato il tipo di passare manualmente è possibile creare un passaggio per eseguire il test in un dispositivo o simulatore. I passaggi per creare un passaggio sono:

-  Creare una directory per i file di sessione.
-  Creare un file pass.json che contiene tutti i dati richiesti.
-  Includere immagini nella cartella (se richiesto).
-  Calcolare l'hash SHA1 per ogni file nella cartella e scrivere in manifest.
-  Firmare manifest con il file con estensione p12 certificato scaricato.
-  COMPRIMERE il contenuto della directory e Rinomina con estensione .pkpass.


Esistono alcuni file di origine nel [esempi di codice](https://developer.xamarin.com/samples/monotouch/PassKit/) per questo articolo che può essere utilizzato per generare un passaggio. Usare i file nei `CouponBanana.raw` directory della directory CreateAPassManually. Sono presenti i file seguenti:

 [![](passkit-images/image18.png "Questi file sono presenti")](passkit-images/image18.png#lightbox)

Aprire pass.json e modificare il codice JSON. È necessario aggiornare almeno il `passTypeIdentifier` e `teamIdentifer` corrispondere l'account per sviluppatore Apple.

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

Successivamente una firma deve essere generata per questo file usando il certificato (file con estensione p12) che è stato generato per questo ID di tipo Pass.

#### <a name="signing-on-a-mac"></a>La firma in un computer Mac

Scaricare il **materiali di supporto di valore di inizializzazione portafoglio** dal [Apple Scarica](https://developer.apple.com/downloads/index.action?name=Passbook) sito. Uso di `signpass` dello strumento per trasformare la cartella in un passaggio (questa anche calcolerà un hash SHA1 e comprimere l'output in un file .pkpass).

#### <a name="testing"></a>Test

Se si intende esaminare l'output di questi strumenti (per impostare il nome del file con estensione zip e quindi aprirlo), si vedrebbe i file seguenti (si noti l'aggiunta del `manifest.json` e `signature` file):

 [![](passkit-images/image19.png "Esaminando l'output di questi strumenti")](passkit-images/image19.png#lightbox)

Dopo aver effettuato l'accesso, ZIP e rinominare il file (ad es. per `BananaCoupon.pkpass`) è possibile trascinarlo nel simulatore per testare o inviarla tramite posta elettronica a se stessi da recuperare in un dispositivo reale. Verrà visualizzata una schermata al **Add** pass, simile al seguente:

 [![](passkit-images/image20.png "Aggiungere la schermata di pass")](passkit-images/image20.png#lightbox)

In genere tale processo potrebbe essere automatizzato in un server, creazione pass tuttavia manuale può essere un'opzione per le piccole aziende che creano solo buoni che non richiedono il supporto di un server back-end.

## <a name="wallet"></a>Wallet

Portafoglio è la parte centrale dell'ecosistema di PassKit. Questa schermata mostra il portafoglio vuoto e come l'elenco di pass e passa singoli apparire:

 [![](passkit-images/image21.png "Questo screenshot Mostra il portafoglio vuoto e aspetto l'elenco di pass e passa singoli")](passkit-images/image21.png#lightbox)

Funzionalità di portafoglio includono:

-  È l'unico punto passa esegue il rendering con il codice a barre per l'analisi.
-  Utente può modificare le impostazioni per gli aggiornamenti. Se abilitata, le notifiche push possono attivare gli aggiornamenti ai dati nella sessione.
-  Utente può abilitare o disabilitare l'integrazione di schermata di blocco. Se abilitata, in questo modo il passaggio venga automaticamente visualizzato sulla schermata di blocco, basata sui dati pertinenti di località e l'ora incorporati nella sessione.
-  Il lato opposto del pass supporta pull per aggiornare, se un URL di server web viene fornito nel passaggio di JSON.
-  Può essere aperta Companion App (o scaricato) se viene specificato l'ID dell'app nel passaggio di JSON.
-  È possibile eliminare passaggi (con un'animazione con suddivisione suggestivo).

## <a name="adding-passes-into-wallet"></a>Aggiunta di passaggi nel portafoglio

È possibile aggiungere passaggi a Wallet nei modi seguenti:

* **Le app conduit** – passa questi non vengano modificati direttamente, ma si limitano a caricare i file pass e presentare all'utente la possibilità di aggiungerli al portafoglio. 

* **Companion app** , questi vengono scritti dai provider per distribuire pass e offrire funzionalità aggiuntive per individuare o modificarli. Le applicazioni xamarin. IOS hanno accesso completo all'API di PassKit per creare e modificare passaggi. Passa quindi può essere aggiunti all'uso di portafoglio di `PKAddPassesViewController`. Questo processo è descritto più dettagliatamente la **complementare applicazioni** sezione di questo documento.

### <a name="conduit-applications"></a>Applicazioni di canale

Le applicazioni conduit sono App intermedia potrebbe ricevere passa per conto di un utente e che devono essere programmata per riconoscere il tipo di contenuto e fornire funzionalità da aggiungere al portafoglio. Le app conduit sono esempi di:

-   **Posta elettronica** – riconosce allegato come una sessione.
-   **Safari** – riconosce il passaggio di Content-Type, quando si fa clic su un collegamento all'URL pass.
-   **Altre App personalizzate** – tutte le app che gli allegati di ricezione o per aprire i collegamenti (i client sui social media, i lettori di posta elettronica e così via).


In questo screenshot appare come **Mail** in iOS 6 riconosce un attacco pass e (quando toccati) offre agli **Add** a portafoglio.

 [![](passkit-images/image22.png "In questo screenshot appare come la posta in iOS 6 riconosce un allegato di pass")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "In questo screenshot appare come posta elettronica sono disponibili aggiungere un allegato di pass per portafoglio")](passkit-images/image23.png#lightbox)

Se si sta creando un'app che potrebbe essere un canale per le sessioni, possono essere riconosciuti da:

-  **Estensione di file** -.pkpass
-  **Tipo MIME** -application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


Il funzionamento di base di un'applicazione di canale consiste nel recuperare il file pass e chiamare PassKit `PKAddPassesViewController` per consentire all'utente la possibilità di aggiungere il passaggio al loro portafoglio. L'implementazione di questo controller di visualizzazione viene descritta nella sezione successiva sul **complementare applicazioni**.

Le applicazioni di canale non sono necessario eseguire il provisioning per un passaggio specifico ID del tipo nello stesso modo che le applicazioni complementari.

## <a name="companion-applications"></a>Applicazioni complementari

Un'applicazione complementare fornisce funzionalità aggiuntive per l'uso di passaggi, tra cui la creazione di un passaggio, l'aggiornamento di informazioni associate a un passaggio e la gestione delle sessioni associate all'applicazione.

Le applicazioni complementari non tentare di duplicare le funzionalità del portafoglio. Non sono progettati per la visualizzazione passa per l'analisi.

Il resto di questa sezione viene descritto come creare una base Companion App che interagisce con PassKit.

### <a name="provisioning"></a>Provisioning

Poiché Wallet è una tecnologia di archivio, l'applicazione deve eseguire il provisioning separatamente e non è possibile usare il profilo di Provisioning del Team o Wildcard App ID. Vedere le [uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) Guida alla creazione di un profilo di Provisioning e un ID App univoci per l'applicazione portafoglio.

### <a name="entitlements"></a>Diritti

Il **entitlements. plist** file deve essere incluso nel progetto xamarin. IOS tutto recente. Per aggiungere un nuovo file entitlements. plist, seguire i passaggi nel [uso degli Entitlement](~/ios/deploy-test/provisioning/entitlements.md) Guida.

Per impostare gli Entitlement eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Fare doppio clic sulla **entitlements. plist** file nel riquadro della soluzione per aprire l'editor entitlements. plist:

![](passkit-images/image31.png "Editor Entitlements.plst")

Nella sezione portafoglio, selezionare la **Abilita portafoglio** opzione

![](passkit-images/image32.png "Abilitare il diritto di portafoglio")


L'opzione predefinita è per l'app consenta che superano tutti i tipi. Tuttavia, è possibile limitare l'app e consente solo un subset di tipi di pass team. Per consentire scopo, selezionare la **Consenti subset del team di passare i tipi** e immettere l'identificatore di tipo pass del subset che si vuole consentire.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Fare doppio clic il **entitlements. plist** file per aprire il file di origine XML.

Per aggiungere il diritto di portafoglio, impostare il **proprietà** al `Passbook Identifiers` nell'elenco a discesa, che imposterà automaticamente la **tipo** `Array`. Quindi, impostare la stringa **valore** a `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Abilitare il diritto di portafoglio")

Permetterà all'app di consentire tutti i tipi di pass. Per limitare l'app e consentire solo un subset di tipi di pass per team, impostare il valore di stringa su:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

In cui `pass.$(CFBundleIdentifier)` è l'ID del pass creato [sopra](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Debug

Se hai problemi relativi alla distribuzione dell'applicazione, controllare che si stia usando i valori corretti **profilo di Provisioning** e che le `Entitlements.plist` sia selezionato come il **Entitlement personalizzati** file nei **firma del Bundle iPhone** opzioni.

Se questo errore si verifica durante la distribuzione:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

il `pass-type-identifiers` Entitlement matrice è errata (o non corrisponde il **profilo di Provisioning**). Verificare che gli ID di tipo passato e l'ID del Team siano corrette.

## <a name="classes"></a>Classi

Le classi di PassKit seguenti sono disponibili per le app accedere ai passaggi:

-  **PKPass** : un'istanza di un passaggio.
-  **PKPassLibrary** : fornisce l'API per accedere a passate nel dispositivo.
-  **PKAddPassesViewController** : consente di visualizzare un passaggio per l'utente salvare nel loro portafoglio.
-  **PKAddPassesViewControllerDelegate** : consente agli sviluppatori di xamarin. IOS

## <a name="example"></a>Esempio

Fare riferimento al progetto PassLibrary nel [esempi di codice](https://developer.xamarin.com/samples/monotouch/PassKit/) per questo articolo. Vengono illustrate le seguenti funzioni comuni che potrebbe essere necessario eseguire in un'applicazione complementare Wallet:

### <a name="check-that-wallet-is-available"></a>Verificare che la disponibilità di portafoglio

Portafoglio non è disponibile in un iPad, in modo che le applicazioni devono controllare prima di tentare di accedere alle funzionalità di PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Creazione di un'istanza di libreria Pass

La libreria di PassKit non è un singleton, le applicazioni devono creare e archiviare un'istanza per accedere all'API di PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Ottenere un elenco di passaggi

Le applicazioni possono richiedere un elenco dei passaggi dalla libreria. Questo elenco viene automaticamente filtrato da PassKit, in modo che è possibile visualizzare solo i passaggi che sono stati creati con l'ID Team e che sono elencati nei diritti.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Si noti che il simulatore non filtrare l'elenco di sessioni restituite, in modo che questo metodo deve sempre essere testato nei dispositivi reali. Questo elenco può essere visualizzato in un UITableView. Il [app di esempio](https://developer.xamarin.com/samples/monotouch/PassKit/) ha un aspetto simile al seguente dopo l'aggiunta di due buoni:

 [![](passkit-images/image29.png "L'aspetto di app di esempio come segue dopo l'aggiunta di due buoni")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>La visualizzazione passa

Un set limitato di informazioni è disponibile per il rendering di passaggi all'interno delle App complementare.

Scegliere da questo set di proprietà standard per visualizzare gli elenchi di passaggi, come avviene nell'esempio di codice.

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

Questa stringa viene visualizzata come un avviso nel [esempio](https://developer.xamarin.com/samples/monotouch/PassKit/):

 [![](passkit-images/image30.png "L'avviso selezionato buono nell'esempio")](passkit-images/image30.png#lightbox)

È anche possibile usare il `LocalizedValueForFieldKey()` metodo per recuperare i dati dai campi nei passaggi è stato progettato (poiché si saprà cosa campi deve essere presente). Il codice di esempio non viene visualizzato questo.

### <a name="loading-a-pass-from-a-file"></a>Il caricamento di un passaggio da un File

Poiché un pass possono essere aggiunti solo a Wallet con l'autorizzazione dell'utente, è necessario presentare un controller di visualizzazione per consentire loro di decidere. Questo codice viene usato nel **Add** pulsante nell'esempio, per caricare un passaggio di pre-compilato incorporato nell'app (è necessario sostituirlo con uno che aver effettuato l'accesso):

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

Viene visualizzato il passaggio **Add** e **Annulla** opzioni:

 [![](passkit-images/image20.png "Il passaggio di opzioni Aggiungi e Annulla")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Sostituire un passaggio esistente

Sostituzione di un passaggio esistente non richiede l'autorizzazione dell'utente, ma avrà esito negativo se la sessione non esiste già.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Modifica di un passaggio

PKPass non è modificabile, in modo che non è possibile aggiornare gli oggetti di sessione nel codice. Per modificare i dati in un passaggio di un'applicazione deve avere accesso a un server web che è possibile tenere traccia delle sessioni e generare un nuovo file passata con i valori aggiornati che è possibile scaricare l'applicazione.

Poiché passate devono essere firmate con un certificato che deve essere mantenuto privato e sicuro, è necessario eseguire la creazione di file passata in un server.

Una volta che un file aggiornato pass è stato generato, utilizzare il `Replace` metodo per sovrascrivere i dati precedenti nel dispositivo.

### <a name="display-a-pass-for-scanning"></a>Visualizzare un passaggio per l'analisi

Come indicato in precedenza, solo portafoglio consente di visualizzare un passaggio per l'analisi. Un passaggio può essere visualizzato utilizzando il `OpenUrl` metodo come illustrato:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Ricezione di notifiche delle modifiche

Le applicazioni possono restare in ascolto per le modifiche apportate alla libreria passare tramite il `PKPassLibraryDidChangeNotification`. Le modifiche potrebbe essere causate da notifiche attivazione degli aggiornamenti in background, pertanto è buona norma per l'ascolto per essi nell'app.

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

Informazioni dettagliate sulla creazione di un'applicazione server per supportare PassKit esula dall'ambito di questo articolo introduttivo.

Visualizzare [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook) open source C# codice lato server.

## <a name="push-notifications"></a>Notifiche push

Una descrizione dettagliata dell'uso di notifiche push per l'aggiornamento passa esula dall'ambito di questo articolo introduttivo.

Sarà necessario implementare l'API simili a REST definita da Apple per rispondere alle richieste web da portafoglio quando gli aggiornamenti sono necessari.

Vedere di Apple [l'aggiornamento di un passaggio](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) Guida per altre informazioni.

## <a name="summary"></a>Riepilogo

Questo articolo introdotto PassKit, descritti alcuni dei motivi per cui è utile e descritte le diverse parti che devono essere implementate per una soluzione completa di PassKit. Descrive i passaggi necessari per configurare l'account per sviluppatore Apple per creare viene superato, il processo per un passaggio manuale e anche come accedere a PassKit APIs da un'applicazione xamarin. IOS.

## <a name="related-links"></a>Collegamenti correlati

- [Wallet per gli sviluppatori](https://developer.apple.com/wallet/)
- [Esempio di PassKit](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [Guida per gli sviluppatori portafoglio](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [Framework: Apple Pay e Wallet (video di WWDC)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [Riferimento a Framework di PassKit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passbook Web Service Reference](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [Informazioni sui file di Pass](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook), una libreria open source per la generazione di pacchetti portafoglio di iOS
- [Introduzione a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
