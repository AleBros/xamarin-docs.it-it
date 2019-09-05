---
title: Introduzione ai suggerimenti proattivi in Novell. iOS
description: Questo articolo illustra come usare i suggerimenti proattivi nell'app Novell. iOS per guidare l'engagement consentendo al sistema di presentare in modo proattivo le informazioni utili automaticamente all'utente.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: ecc31fa6aab9e2be1c33f16d2f424b397f4a9a73
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291733"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Introduzione ai suggerimenti proattivi in Novell. iOS

_Questo articolo illustra come usare i suggerimenti proattivi nell'app Novell. iOS per guidare l'engagement consentendo al sistema di presentare in modo proattivo le informazioni utili automaticamente all'utente._

Novità di iOS 10, i suggerimenti proattivi presentano le ultime novità per consentire agli utenti di interagire con un'app Novell. iOS, presentando in modo proattivo le informazioni utili automaticamente all'utente in momenti appropriati.

iOS 10 presenta nuovi modi per guidare il coinvolgimento nell'app consentendo al sistema di presentare in modo proattivo le informazioni utili automaticamente all'utente in momenti appropriati. Così come iOS 9 ha fornito la possibilità di aggiungere una ricerca completa all'app usando i suggerimenti Spotlight, Componi e Siri (vedere le [nuove API di ricerca](~/ios/platform/search/index.md)) con iOS 10 un'app può esporre le funzionalità che possono essere presentate all'utente dal sistema dall'interno delle seguenti posizioni :

- Switcher dell'app
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni Siri
- Suggerimenti QuickType

L'app espone questa funzionalità al sistema usando una raccolta di tecnologie come `NSUserActivity`, markup web, Core Spotlight, MapKit, Media Player e UIKit. Inoltre, grazie al supporto proattivo dei suggerimenti per l'app, l'integrazione di Siri è più approfondita gratuitamente.

## <a name="location-based-suggestions"></a>Suggerimenti sulla posizione

Una novità di iOS 10, `NSUserActivity` la classe include `MapItem` una proprietà che consente allo sviluppatore di fornire informazioni sulla posizione che possono essere usate in altri contesti. Ad esempio, se l'app Visualizza le recensioni dei ristoranti, lo sviluppatore può `MapItem` impostare la proprietà sulla posizione del ristorante che l'utente sta visualizzando nell'app. Se l'utente passa all'app Maps, la località del ristorante è automaticamente disponibile.

Se l'app supporta la ricerca nell'app, può usare i nuovi componenti degli indirizzi `CSSearchableItemAttributesSet` della classe per specificare le posizioni che l'utente può desiderare di visitare. Impostando la `MapItem` proprietà, le altre proprietà vengono compilate automaticamente.

Oltre a impostare `Latitude` e `Longitude` delle proprietà del componente Address, è consigliabile che l'app fornisca anche le `NamedLocation` proprietà e `PhoneNumbers` , quindi Siri può avviare una chiamata al percorso.

## <a name="web-markup-based-suggestions"></a>Suggerimenti basati sul markup Web

iOS 9 aggiunto per includere markup di dati strutturati nel sito Web che arricchisce il contenuto visualizzato dagli utenti nei risultati della ricerca in Spotlight e Safari (vedere [ricerca con markup web](~/ios/platform/search/web-markup.md)). iOS 10 aggiunge la possibilità di includere markup basato sulla posizione (ad esempio [PostalAddress](http://schema.org/PostalAddress) come definito da [schema.org](http://schema.org/)) per migliorare ulteriormente l'esperienza dell'utente. Se, ad esempio, gli utenti visualizzano una pagina contrassegnata nel sito Web, il sistema può suggerire la stessa posizione in cui si aprono le mappe.

## <a name="text-based-suggestions"></a>Suggerimenti basati su testo

UIKit è stato espanso in iOS 10 per includere la proprietà [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) della classe [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) per specificare il significato semantico del contenuto in un'area di testo. Con queste informazioni, il sistema può in genere selezionare automaticamente il tipo di tastiera appropriato, migliorare i suggerimenti di correzione automatica e integrare in modo proattivo le informazioni da altre app e siti Web.

Ad esempio, se l'utente immette testo in un campo di testo contrassegnato `UITextContentType.FullStreetAddress`, il sistema può suggerire il riempimento automatico del campo con la posizione che l'utente ha visualizzato di recente.

## <a name="media-based-suggestions"></a>Suggerimenti basati su supporti

Se l'app riproduce contenuti multimediali con l'API [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) , iOS 10 consente agli utenti di visualizzare l'immagine dell'album e riprodurre i file multimediali tramite l'app nella schermata di blocco.

## <a name="contextual-siri-reminders"></a>Promemoria Siri contestuali

Consente all'utente di usare Siri per creare rapidamente un promemoria per visualizzare il contenuto attualmente visualizzato nell'app in un secondo momento. Se, ad esempio, veniva visualizzata una recensione del ristorante nell'app, era possibile richiamare Siri e pronunciare l' *avviso quando si arriva a casa.* Siri genera il promemoria con un collegamento alla revisione nell'app.

## <a name="contact-based-suggestions"></a>Suggerimenti basati sui contatti

Consente di visualizzare i contatti dell'app (e le informazioni correlate al contatto) nell'app di **contatto** sul dispositivo iOS insieme a tutti i contatti degli utenti archiviati nel sistema.

## <a name="ride-sharing-based-suggestions"></a>Suggerimenti basati sulla condivisione di percorso

Se un'app di condivisione di corsa usa l'API [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) , iOS 10 lo presenterà come opzione nello strumento di selezione delle app in momenti in cui è probabile che l'utente voglia eseguire una corsa. L'app deve anche essere registrata come app di condivisione di corsa specificando `MKDirectionsModeRideShare` per la chiave [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) nel relativo `Info.plist` file.

Se l'app supporta solo la condivisione del percorso, il suggerimento di sistema inizierà con *"Get a ride to..."* , se sono supportati altri tipi di direzione di routing (ad esempio, walking o bike), il sistema userà *"Get directions to..."*

> [!IMPORTANT]
> L'oggetto [MKMapItem](xref:MapKit.MKMapItem) che l'app riceve non può includere le informazioni sulla longitudine e la latitudine e richiede la geocodifica.

## <a name="implementing-proactive-suggestions"></a>Implementazione di suggerimenti proattivi

L'aggiunta del supporto proattivo dei suggerimenti a un'app Novell. iOS è in genere semplice quanto l'implementazione di alcune API o l'espansione in alcune API che l'app potrebbe già implementare.

I suggerimenti proattivi funzionano con le app in tre modi principali:

- **`NSUserActivity`e**  -  schema.org`NSUserActivity` consentono al sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. Schema.org aggiunge funzionalità simili alle pagine Web.
- **Suggerimenti sulla posizione** : se l'app offre o utilizza informazioni basate sulla posizione, queste estensioni API offrono nuovi modi per condividere queste informazioni tra le app.
- **Suggerimenti per le app multimediali** : il sistema può alzare di livello l'app e il relativo contenuto multimediale in base al contesto dell'interazione dell'utente con il dispositivo iOS.

E sono supportati nell'app implementando gli elementi seguenti:

- **La consegna è stata aggiunta** in iOS 8 per supportare la consegna, che consente allo sviluppatore di avviare un'attività in un dispositivo, quindi di continuare con un'altra (vedere [Introduzione alla](~/ios/platform/handoff.md)consegna).`NSUserActivity`  - 
- **Ricerca Spotlight** : iOS 9 ha aggiunto la possibilità di innalzare di livello il contenuto delle app nei `NSUserActivity` risultati della ricerca di Spotlight usando (vedere [eseguire ricerche con core Spotlight](~/ios/platform/search/corespotlight.md)).
- **Reminders Siri contestuali** : in iOS 10 `NSUserActivity` è stato ampliato per consentire a Siri di creare rapidamente un promemoria per visualizzare il contenuto attualmente visualizzato dall'utente nell'app in un secondo momento.
- **Suggerimenti** `NSUserActivity` per la posizione: iOS 10 migliora l'acquisizione delle posizioni visualizzate all'interno dell'app e le promuove in molte posizioni in tutto il sistema.
- **Le richieste**  -  Siricontestualifornisconoilcontestoalleinformazionipresentateall'internodell'appaSiri,inmodochel'utentepossaottenereindicazionioinserireunachiamataperrichiamareSiridall'internodell'app.`NSUserActivity`
- **Interazioni contatto** : novità in iOS 10, `NSUserActivity` consente di promuovere le app di comunicazione da una scheda contatto (nell'app Contatti) come metodo di comunicazione alternativo.

Tutte queste funzionalità hanno un'unica cosa in comune, usano `NSUserActivity` tutte in un form o in un'altra per fornire la loro funzionalità. 

## <a name="nsuseractivity"></a>NSUserActivity

Come indicato in precedenza `NSUserActivity` , consente al sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. `NSUserActivity`è un meccanismo di memorizzazione nella cache dello stato leggero per acquisire l'attività dell'utente durante la navigazione nell'app. Ad esempio, esaminando un'app del ristorante:

[![](proactive-suggestions-images/activity02.png "Meccanismo di memorizzazione nella cache dello stato leggero NSUserActivity")](proactive-suggestions-images/activity02.png#lightbox)

Con le interazioni seguenti:

1. Quando l'utente lavora con l'app, viene `NSUserActivity` creato un oggetto per ricreare lo stato dell'app in un secondo momento.
2. Se l'utente cerca un ristorante, viene seguito lo stesso modello di creazione delle attività.
3. Anche in questo caso, quando l'utente visualizza un risultato. In quest'ultimo caso, l'utente sta visualizzando una località e in iOS 10, il sistema è più consapevole di alcuni concetti, ad esempio la posizione o le interazioni di comunicazione.

Esaminare più da vicino l'ultima schermata:

[![](proactive-suggestions-images/activity03.png "Dettagli NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Qui l'app crea un oggetto `NSUserActivity` ed è stato popolato con le informazioni per ricreare lo stato in un secondo momento. L'app include anche alcuni metadati, ad esempio il nome e l'indirizzo della località. Quando questa attività è stata creata, l'app informa iOS che rappresenta lo stato corrente dell'utente.

L'app decide quindi se l'attività verrà annunciata in modo condizionale per la consegna, salvata come valore temporaneo per i suggerimenti sulla posizione o aggiunta all'indice Spotlight su dispositivo per la visualizzazione nei risultati della ricerca.

Per altre informazioni sulla consegna e la ricerca in evidenza, vedere le [guide introduttive](~/ios/platform/handoff.md) e le [nuove API di ricerca per iOS 9](~/ios/platform/search/index.md).

### <a name="creating-an-activity"></a>Creazione di un'attività

Prima di creare un'attività, sarà necessario creare un identificatore del tipo di attività per identificarlo. L'identificatore del tipo di attività è una stringa breve aggiunta `NSUserActivityTypes` alla matrice del `Info.plist` file dell'app usata per identificare in modo univoco un determinato tipo di attività utente. Nella matrice sarà presente una voce per ogni attività supportata dall'app ed esposta alla ricerca di app. Per ulteriori informazioni, vedere il riferimento per la [creazione di identificatori del tipo di attività](~/ios/platform/search/nsuseractivity.md) .

Esaminare un esempio di un'attività:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

Viene creata una nuova attività usando un identificatore del tipo di attività. Successivamente, vengono creati alcuni metadati che definiscono l'attività in modo da poter ripristinare questo stato in un secondo momento. A questo punto, l'attività riceve un titolo significativo e viene collegato alle informazioni dell'utente. Infine, alcune funzionalità sono abilitate e l'attività viene inviata al sistema.

Il codice precedente può essere ulteriormente migliorato per includere i metadati che forniscono il contesto all'attività apportando le modifiche seguenti:

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

Se lo sviluppatore ha un sito Web in grado di visualizzare le stesse informazioni dell'app, l'app può includere l'URL e il contenuto può essere visualizzato in altri dispositivi in cui non è installata l'app (tramite la consegna):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Ripristino di un'attività

Per rispondere all'utente toccando un risultato di ricerca (`NSUserActivity`) per l'app, modificare il file **AppDelegate.cs** ed eseguire l'override `ContinueUserActivity` del metodo. Ad esempio:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

Lo sviluppatore dovrà assicurarsi che si tratta dello stesso identificatore del tipo di attività`com.xamarin.platform`() dell'attività creata in precedenza. L'app usa le informazioni archiviate in `NSUserActivity` per ripristinare lo stato in cui l'utente ha interrotto l'esecuzione.

### <a name="benefits-of-creating-an-activity"></a>Vantaggi della creazione di un'attività

Con la quantità minima di codice presentata in precedenza, l'app è ora in grado di sfruttare tre nuove funzionalità di iOS 10:

- **Handoff**
- **Ricerca Spotlight**
- **Promemoria Siri contestuali**

La sezione seguente consentirà di abilitare altre due nuove funzionalità di iOS 10:

- **Suggerimenti sulla posizione**
- **Richieste Siri contestuali**

### <a name="location-based-suggestions"></a>Suggerimenti sulla posizione 

Eseguire l'esempio dell'app Search per i ristoranti sopra. Se sono stati implementati `NSUserActivity` e popolati correttamente tutti i metadati e gli attributi, l'utente sarà in grado di eseguire le operazioni seguenti:

1. Trovare un ristorante nell'app a cui si desidera incontrare un amico.
2. Quando l'utente si allontana dall'app usando lo strumento di selezione delle app multitasking, il sistema visualizzerà automaticamente un suggerimento (nella parte inferiore dello schermo) per ottenere le indicazioni per il ristorante usando l'app di navigazione preferita.
3. Se l'utente passa all'app Messages e inizia a digitare *"Let ' s meet at"* , la tastiera QuickType suggerisce automaticamente di incollare l'indirizzo del ristorante.
4. Se l'utente passa all'app Maps, l'indirizzo del ristorante viene automaticamente suggerito come destinazione.
5. Questo funziona anche per le app di terze parti `NSUserActivity`(che supportano), in modo che l'utente possa passare a un'app di condivisione di corsa e l'indirizzo del ristorante viene automaticamente suggerito come destinazione.
6. Fornisce anche il contesto a Siri, in modo che l'utente possa richiamare Siri nell'app del ristorante e chiedere *"Get directions..."* e Siri fornirà le istruzioni per il ristorante visualizzato dall'utente.

Tutte le funzionalità sopra elencate hanno una cosa in comune, ognuna delle quali indica la provenienza iniziale del suggerimento. Nel caso dell'esempio precedente, si tratta dell'app di revisione del ristorante fittizio.

iOS 10 è stato migliorato per abilitare questa funzionalità per un'app con alcune piccole modifiche e aggiunte ai Framework esistenti:

- `NSUserActivity`include campi aggiuntivi per l'acquisizione delle informazioni sul percorso visualizzate all'interno dell'app.
- Sono state apportate diverse aggiunte a MapKit e CoreSpotlight per acquisire il percorso.
- La funzionalità di rilevamento della posizione è stata aggiunta a Siri, Maps, tastiere, multitasking e altre app all'interno del sistema.

Per implementare suggerimenti basati sulla posizione, iniziare con lo stesso codice di attività presentato sopra:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

Se l'app usa MapKit, è sufficiente aggiungere la mappa `MKMapItem` corrente all'attività:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Se l'app non usa MapKit, può adottare la ricerca di app e specificare i nuovi attributi seguenti per il percorso:

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

Esaminare il codice precedente in dettaglio. In primo luogo, il nome del percorso è obbligatorio in ogni istanza:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Quindi, la descrizione basata su testo della posizione è obbligatoria per le istanze basate su testo (ad esempio la tastiera QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitudine e la longitudine sono facoltative, ma assicurano che l'utente venga indirizzato al percorso esatto a cui l'app vuole inviarli, quindi deve essere incluso:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Impostando i numeri di telefono, l'app può accedere a Siri, in modo che l'utente possa richiamare Siri dall'app, definendo un *messaggio*simile al seguente:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Infine, l'app può indicare se l'istanza è adatta per la navigazione e le chiamate telefoniche:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementazione di interazioni contatto

Novità in iOS 10, le app per la comunicazione sono profondamente integrate nell'app Contatti dalla scheda contatto. Per le app che hanno implementato interazioni di contatto, l'utente può aggiungere le informazioni dell'app specificata a utenti specifici nei propri contatti. Se, ad esempio, si preme il pulsante azione rapida nella parte superiore di una scheda per inviare un messaggio, l'app collegata viene elencata come un'opzione per l'invio del messaggio da.

Se si seleziona un'app di terze parti, è possibile memorizzarla e presentarla come metodo predefinito per il messaggio alla persona specificata al successivo tentativo di contatto da parte dell'utente.

Le interazioni dei contatti vengono implementate nell' `NSUserActivity` app usando e il nuovo Framework di intenting introdotto in iOS 10. Per altre informazioni sull'uso degli Intent, vedere i [concetti relativi a SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) e l' [implementazione](~/ios/platform/sirikit/implementing-sirikit.md) delle guide SiriKit.

#### <a name="donating-interactions"></a>Donazioni di interazioni

Esaminare il modo in cui l'app può donare le interazioni:

[![](proactive-suggestions-images/activity04.png "Cenni preliminari sulle interazioni di donazione")](proactive-suggestions-images/activity04.png#lightbox)

L'app crea un `INInteraction` oggetto che contiene una **finalità** (`INIntent`), **i partecipanti e i** **metadati**. Lo **scopo** rappresenta un'azione dell'utente, ad esempio l'esecuzione di una chiamata video o l'invio di un SMS. I **partecipanti** includono gli utenti che ricevono la comunicazione. I **metadati** definiscono informazioni aggiuntive, ad esempio l'invio corretto del messaggio e così via.

Lo sviluppatore non crea mai direttamente un'istanza `INIntent` di `INIntentResponse`o, userà una delle classi figlio specifiche (in base all'attività eseguita dall'app per conto dell'utente) che ereditano da queste classi padre. Ad esempio, `INSendMessageIntent` e `INSendMessageIntentResponse` per l'invio di un SMS. 

Quando l'interazione è completamente popolata, chiamare `DonateInteraction` il metodo per informare il sistema che l'interazione è disponibile per l'utilizzo.

Quando l'utente interagisce con l'app dalla scheda contatto, l'interazione viene inclusa in un oggetto `NSUserActivity`, che viene quindi usato per avviare l'app:

[![](proactive-suggestions-images/activity05.png "L'interazione viene aggregata con un NSUserActivity usato per avviare l'app")](proactive-suggestions-images/activity05.png#lightbox)

Esaminare l'esempio seguente di finalità di invio dei messaggi:

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
  public class DonateInteraction
  {
    #region Constructors
    public DonateInteraction ()
    {
    }
    #endregion

    #region Public Methods
    public void SendMessageIntent (string text, INPerson from, INPerson[] to)
    {

      // Create App Activity
      var activity = new NSUserActivity ("com.xamarin.message");

      // Define details
      var info = new NSMutableDictionary ();
      info.Add (new NSString ("message"), new NSString (text));

      // Populate Activity
      activity.Title = "Sent MonkeyChat Message";
      activity.UserInfo = info;

      // Enable capabilities
      activity.EligibleForSearch = true;
      activity.EligibleForHandoff = true;
      activity.EligibleForPublicIndexing = true;

      // Inform system of Activity
      activity.BecomeCurrent ();

      // Create message Intent
      var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

      // Create Intent Reaction
      var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

      // Create interaction
      var interaction = new INInteraction (intent, response);

      // Donate interaction to the system
      interaction.DonateInteraction ((err) => {
        // Handle donation error
        ...
      });
    }
    #endregion
  }
}
```

Esaminando il codice in dettaglio, viene creata e popolata un'istanza di `NSUserActivity` , come illustrato nella sezione [creazione di un'attività](#creating-an-activity) precedente. Successivamente, crea un'istanza di `INSendMessageIntent` (che eredita da `INIntent`) e la popola con i dettagli del messaggio inviato:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Viene `INSendMessageIntentResponse` creato un oggetto e viene `NSUserActivity` passato il valore creato in precedenza:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Una `INInteraction` viene compilata in base alla finalità`INSendMessageIntent`del messaggio di invio`INSendMessageIntentResponse`() e alla risposta () appena creata:

```csharp
var interaction = new INInteraction (intent, response);
```

Infine, il sistema notifica l'interazione:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
  // Handle donation error
  ...
});
```

Viene passato un gestore di completamento in cui l'app può rispondere alla donazione che ha avuto esito positivo o negativo.

### <a name="activities-best-practices"></a>Procedure consigliate per le attività

Apple suggerisce le seguenti procedure consigliate per l'uso delle attività:

- Usare `NeedsSave` per gli aggiornamenti del payload Lazy.
- Assicurarsi di mantenere un riferimento sicuro all'attività corrente.
- Trasferire solo payload piccoli che includono informazioni sufficienti per ripristinare lo stato.
- Verificare che gli identificatori del tipo di attività siano univoci e descrittivi utilizzando la notazione DNS inverso per specificarli. 

## <a name="schemaorg"></a>Schema.org

Come illustrato in precedenza `NSUserActivity` , consente al sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. Schema.org aggiunge funzionalità simili alle pagine Web.

Schema.org può fornire gli stessi tipi di interazioni basate sul percorso al sito Web. Apple ha progettato i nuovi suggerimenti sulla posizione per lavorare anche quando vengono visualizzati in Safari come in un'app nativa.

Uno sfondo Schema.org:

- Fornisce uno standard del vocabolario di markup web aperto.
- Funziona includendo metadati strutturati nelle pagine Web.
- Sono disponibili più di 500 schemi che rappresentano i diversi concetti disponibili.
- Implementando il sito Web, lo sviluppatore può acquisire alcuni dei vantaggi derivanti dall' `NSUserActivity` uso di in un'app nativa.

Gli schemi sono disposti in una struttura ad albero, in cui tipi specifici, ad esempio *ristorante*, ereditano da tipi più generici, ad esempio un' *attività locale*. Per ulteriori informazioni, vedere [schema.org](http://schema.org).

Ad esempio, se la pagina Web include i dati seguenti:

```html
<script type="application/ld+json">
{
  "@context":"http://schema.org",
  "@type":"Restaurant",
  "telephone":"(415) 781-1111",
  "url":"https://www.yanksing.com",
  "address":{
    "@type":"PostalAddress",
    "streetAddress":"101 Spear St",
    "addressLocality":"San Francisco",
    "postalCode":"94105",
    "addressRegion":"CA"
  },
  "aggregateRating":{
    "@type":"AggregateRating",
    "ratingValue":"3.5",
    "reviewCount":"2022"
  }
}
</script>
```

Se l'utente visita questa pagina in Safari e quindi passa a un'altra app, le informazioni sulla posizione della pagina verranno acquisite e fornite come suggerimento per la posizione in altre parti del sistema, come illustrato nelle attività precedenti.

Safari estrae qualsiasi elemento in una pagina Web conforme a una delle proprietà dello schema seguenti:

- **PostalAddress**
- **Coordinate geografiche**
- Proprietà del telefono.

Per ulteriori informazioni, vedere la Guida [alla ricerca con markup web](~/ios/platform/search/web-markup.md) .

## <a name="consuming-location-suggestions"></a>Utilizzo di suggerimenti sulla posizione

Questa sezione seguente coprirà il suggerimento relativo all'utilizzo del percorso che provenga da altre parti del sistema, ad esempio l'app Maps, o altre app di terze parti.

L'app può utilizzare i suggerimenti sulla posizione in due modi principali:

- Tramite la tastiera QuickType.
- Direttamente tramite l'utilizzo delle informazioni sulla posizione nelle app di routing.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Suggerimenti sulla posizione e tastiera QuickType

Se l'app gestisce gli indirizzi in formati basati su testo, l'app può sfruttare i suggerimenti sulla posizione tramite l'interfaccia utente di QuickType. iOS 10 espande l'interfaccia utente di QuickType con le funzionalità seguenti:

- L'app può aggiungere suggerimenti sulla finalità semantica per i campi di testo nell'interfaccia utente.
- L'app può ottenere suggerimenti proattivi nell'app.
- L'app può trarre vantaggio dalla correzione automatica avanzata.

La nuova `TextContentType` proprietà dei controlli campo di testo in iOS 10 consente allo sviluppatore di definire la finalità semantica per il valore che l'utente dovrà immettere in un determinato campo. Ad esempio:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Indicherà al sistema che l'app prevede che l'utente immetta un indirizzo completo nel campo specificato. Questo consentirà alla tastiera di QuickType di fornire automaticamente suggerimenti sulla posizione sulla tastiera quando l'utente immette un valore in questo campo.

Di seguito sono riportati alcuni dei tipi più comuni disponibili per lo sviluppatore nella `UITextContentType` classe statica:

- `Name`
- `GivenName`
- `FamilyName`
- `Location`
- `FullStreetAddress`
- `AddressCityAndState`
- `TelephoneNumber`
- `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Suggerimenti per il routing di app e percorsi

In questa sezione verranno esaminati i suggerimenti per la posizione di utilizzo direttamente dall'interno di un'app di routing. Per aggiungere questa funzionalità all'app di routing, lo sviluppatore utilizzerà il Framework esistente `MKDirectionsRequest` come indicato di seguito:

- Per innalzare di livello l'app in multitasking.
- Per registrare l'app come app di routing.
- Per gestire l'avvio dell'app con un `MKDirectionsRequest` oggetto MapKit.
- Per consentire a iOS di apprendere come suggerire l'app all'utente in momenti appropriati, in base al coinvolgimento degli utenti.

Quando l'app viene avviata con un `MKDirectionsRequest` oggetto MapKit, deve iniziare automaticamente a fornire le istruzioni per l'utente al percorso richiesto oppure presentare un'interfaccia utente che consente all'utente di iniziare a ottenere le indicazioni in modo semplice. Ad esempio:


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
  [Register ("AppDelegate")]
  public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
  {
    ...
    
    public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
    {
      if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
        var request = new MKDirectionsRequest (url);
        var coordinate = request.Destination?.Placemark.Location?.Coordinate;
        var address = request.Destination.Placemark.AddressDictionary;
        if (coordinate.IsValid()) {
          var geocoder = new CLGeocoder ();
          geocoder.GeocodeAddress (address, (place, err) => {
            // Handle the display of the address

          });
        }
      }

      return true;
    }
  }    
}
```

Esaminare il codice in dettaglio. Esegue un test per verificare se si tratta di una richiesta di destinazione valida:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

In caso contrario, crea un oggetto `MKDirectionsRequest` dall'URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Una novità di iOS 10, all'app può essere inviato un indirizzo che non ha coordinate geografiche, in quanto lo sviluppatore deve codificare l'indirizzo:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
  // Handle the display of the address
  
});

```

## <a name="media-app-suggestions"></a>Suggerimenti per app multimediali

Se l'app gestisce qualsiasi tipo di supporto, ad esempio un'app Podcast o un contenuto multimediale di streaming, ad esempio audio o video, con iOS 10, può sfruttare i suggerimenti dei supporti nel sistema.

Per le app che gestiscono supporti, iOS supporta i comportamenti seguenti:

- iOS promuove le app che l'utente può usare in base al comportamento precedente.
- I suggerimenti relativi all'app verranno presentati in Spotlight e nella visualizzazione Today.
- Se l'app soddisfa uno dei trigger seguenti, potrebbe essere elevata a un suggerimento per la schermata di blocco:
  - Dopo aver collegato le cuffie o un dispositivo Bluetooth, effettua una connessione.
  - Dopo essere stato trovato in un'auto.
  - Dopo l'arrivo a casa o in ufficio. 

Includendo una semplice chiamata API in iOS 10, lo sviluppatore può creare un'esperienza di blocco più accattivante per gli utenti dell'app multimediale. Usando la `MPPlayableContentManager` classe per gestire la riproduzione multimediale, i controlli multimediali completi (come quelli presentati dall'app Music) verranno visualizzati nella schermata di blocco dell'app.


```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
  public class PlayableContentDelegate : MPPlayableContentDelegate
  {
    #region Constructors
    public PlayableContentDelegate ()
    {
    }
    #endregion

    #region Override methods
    public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
    {
      // Access the media item to play
      var item = LoadMediaItem (indexPath);

      // Populate the lock screen
      PopulateNowPlayingItem (item);

      // Prep item to be played
      var status = PreparePlayback (item);

      // Call completion handler
      completionHandler (null);
    }
    #endregion

    #region Public Methods
    public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
    {
      var item = new MPMediaItem ();

      // Load item from media store
      ...

      return item;
    }

    public void PopulateNowPlayingItem (MPMediaItem item)
    {
      // Get Info Center and album art
      var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
      var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

      // Populate Info Center
      infoCenter.NowPlaying.Title = item.Title;
      infoCenter.NowPlaying.Artist = item.Artist;
      infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
      infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
      infoCenter.NowPlaying.Artwork = albumArt;
    }

    public bool PreparePlayback (MPMediaItem item)
    {
      // Prepare media item for playback
      ...

      // Return results
      return true;
    }
    #endregion
  }
}
```

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato suggerimenti proattivi e ha illustrato come lo sviluppatore può usarle per indirizzare il traffico all'app Novell. iOS. È stato illustrato il passaggio per implementare i suggerimenti proattivi e le linee guida per l'utilizzo.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Guida alla programmazione di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
