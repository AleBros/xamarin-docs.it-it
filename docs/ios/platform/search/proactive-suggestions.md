---
title: Introduzione a suggerimenti proattivi in xamarin. IOS
description: Questo articolo illustra come usare i suggerimenti proattivi in all'app xamarin. IOS di coinvolgimento, consentendo al sistema in modo proattivo presentare automaticamente informazioni utili all'utente.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2ab0147f918b36dc47ef6eed7d9bf1b6295d9733
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408199"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Introduzione a suggerimenti proattivi in xamarin. IOS

_Questo articolo illustra come usare i suggerimenti proattivi in all'app xamarin. IOS di coinvolgimento, consentendo al sistema in modo proattivo presentare automaticamente informazioni utili all'utente._

Novità di iOS 10 modi notizie presenti suggerimenti proattivi agli utenti di interagire con un'app xamarin. IOS da presentare in modo proattivo le informazioni utili automaticamente all'utente in momenti appropriati.

iOS 10 offre nuove modalità di engagement trainanti per l'app, consentendo al sistema in modo proattivo presentare informazioni utili automaticamente all'utente in momenti appropriati. Proprio come iOS 9 è stato possibile aggiungere la ricerca avanzata per l'app usando Spotlight, consegne e i suggerimenti di Siri (vedere [nuove API di ricerca](~/ios/platform/search/index.md)), con iOS 10 un'app può esporre la funzionalità che può essere presentata all'utente dal sistema all'interno di percorsi seguenti:

- La selezione di App
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni tra Siri
- Suggerimenti QuickType

L'app espone la funzionalità per il sistema utilizzando una raccolta di tecnologie, ad esempio `NSUserActivity`, markup web, Core Spotlight, MapKit, Media Player e UIKit. Inoltre, fornendo supporto proattivo suggerimento per l'app, si ottiene una maggiore integrazione Siri gratuitamente.

## <a name="location-based-suggestions"></a>Suggerimenti basati sulla posizione

Novità di iOS 10, il `NSUserActivity` classe include una `MapItem` proprietà che consente allo sviluppatore di fornire informazioni sul percorso che può essere usati in altri contesti. Ad esempio, se l'app consente di visualizzare le recensioni dei ristoranti, lo sviluppatore può impostare il `MapItem` proprietà per la posizione del ristorante che l'utente visualizza nell'app. Se l'utente passa all'app mappe, posizione del ristorante è automaticamente disponibile.

Se l'app supporta la ricerca di App, è possibile utilizzare i nuovi componenti di indirizzo del `CSSearchableItemAttributesSet` classe per specificare i percorsi che l'utente desidera visitare. Impostando il `MapItem` proprietà, le altre proprietà sono automaticamente riempita aggiuntivo.

Oltre a impostare il `Latitude` e `Longitude` delle proprietà del componente indirizzo, è consigliabile che l'app specificare la `NamedLocation` e `PhoneNumbers` proprietà troppo, pertanto Siri può avviare una chiamata al percorso.

## <a name="web-markup-based-suggestions"></a>Suggerimenti basati sulle Markup Web

iOS 9 aggiunto alla possibilità di includere commenti dati strutturati nel sito Web che arricchisce il contenuto visualizzato dagli utenti nei risultati della ricerca di Spotlight e Safari (vedere [eseguire una ricerca con Markup Web](~/ios/platform/search/web-markup.md)). iOS 10 aggiunge la possibilità di includere markup basati sulla posizione (ad esempio [PostalAddress](http://schema.org/PostalAddress) base al parere [Schema.org](http://schema.org/)) per migliorare ulteriormente l'esperienza dell'utente. Ad esempio, se le viste agli utenti un percorso contrassegnato pagina sul sito Web, il sistema può suggerire nello stesso percorso quando i colleghi aprono mappe.

## <a name="text-based-suggestions"></a>Suggerimenti basati su testo

UIKit è stata estesa in iOS 10 in modo da includere il [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) proprietà delle [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) classe per specificare il significato semantico del contenuto in un'area di testo. Con queste informazioni posto, il sistema può in genere automaticamente selezionare il tipo appropriato da tastiera, migliorare i suggerimenti di correzione automatica e integrare in modo proattivo informazioni provenienti da altre App e siti Web.

Ad esempio, se l'utente sta immettendo il testo in un campo di testo contrassegnato `UITextContentType.FullStreetAddress`, il sistema può suggerire la compilazione automatica il campo con la posizione in cui l'utente è stato recentemente la visualizzazione.

## <a name="media-based-suggestions"></a>Suggerimenti basati sulla Media

Se l'app riproduce supporti tramite il [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API iOS 10 consente agli utenti di visualizzare le immagini e riprodurre file multimediali tramite l'app nella schermata di blocco.

## <a name="contextual-siri-reminders"></a>Promemoria di Siri contestuali

Consente all'utente di usare Siri per creare rapidamente un promemoria per visualizzare il contenuto che si sta visualizzando nell'app in un secondo momento. Ad esempio, se si stava visualizzando una revisione del ristorante nell'app, è stato possibile richiamare Siri e pronunciare *"Visualizza informazioni su questo quando recupera la principale".* Siri genererebbe il promemoria con un collegamento alla revisione nell'app.

## <a name="contact-based-suggestions"></a>Suggerimenti basati sulle contatto

Consente all'app contatti (e informazioni correlate contatto) venga visualizzato nel **contattare** app sul dispositivo iOS insieme a tutti i contatti degli utenti archiviati nel sistema.

## <a name="ride-sharing-based-suggestions"></a>Suggerimenti basati sulla condivisione Rani

Se un'app per la condivisione rani Usa il [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API iOS 10 presenterà come un'opzione nel cambio modalità per le app nei momenti in cui l'utente è presumibile che si desideri una corsa. L'app deve essere registrato come un'applicazione di condivisione rani anche specificando il `MKDirectionsModeRideShare` per il [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) chiave nel relativo `Info.plist` file.

Se l'app supporta solo la condivisione rani, inizierà con il suggerimento di sistema *"Ottieni una giostra per..."*, se sono supportati altri tipi di routing direzione (ad esempio, the Walking o biciclette), il sistema userà *"Indicazioni stradali per..."*

> [!IMPORTANT]
> Il [MKMapItem](xref:MapKit.MKMapItem) oggetto che riceve l'app non può includere informazioni di latitudine e longitudine e richiederà la geocodifica.

## <a name="implementing-proactive-suggestions"></a>Implementazione di suggerimenti proattivi

Il supporto per un'app xamarin. IOS aggiungendo suggerimento proattiva è in genere semplice come implementazione di alcune API o l'espansione su alcune API che l'app potrebbe già essere implementato.

Suggerimenti proattivi funzionano con le App in tre modi principali:

- **`NSUserActivity` e Schema.org**  -  `NSUserActivity` consente al sistema di comprendere quali informazioni l'utente è attualmente lavora sullo schermo. Schema.org aggiunge funzionalità simili alle pagine web.
- **I suggerimenti di percorso** : se l'app offre o utilizza informazioni basato su posizione, questi nuovi modi offerta estensione API per condividere le informazioni tra app.
- **I suggerimenti di App multimediali** : il sistema è possibile alzare di livello app e il contenuto multimediale basato sul contesto di interazione dell'utente con il dispositivo iOS.

Ed è supportato nell'app mediante l'implementazione seguente:

- **Esegui handoff**  -  `NSUserActivity` è stata aggiunta in iOS 8 per il supporto di consegna, che consente allo sviluppatore di avviare un'attività in un dispositivo, quindi continuarli un altro (vedere [Introduzione a Handoff](~/ios/platform/handoff.md)).
- **Ricerca di Spotlight** -iOS 9 aggiunta la possibilità di promuovere il contenuto di app dall'interno i risultati della ricerca di Spotlight usando `NSUserActivity` (vedere [ricerca con Spotlight Core](~/ios/platform/search/corespotlight.md)).
- **Promemoria di Siri contestuali** : In iOS 10, `NSUserActivity` è stata ampliata per consente a Siri di creare rapidamente un promemoria per visualizzare il contenuto all'utente è attualmente visualizzato nell'app in un secondo momento.
- **I suggerimenti di percorso** -migliora iOS 10 `NSUserActivity` acquisire percorsi visualizzati all'interno dell'app e alzare di livello in molte posizioni in tutto il sistema.
- **Le richieste Siri contestuali**  -  `NSUserActivity` fornisce contesto per le informazioni presentate all'interno dell'app a Siri in modo che l'utente può ottenere le direzioni o sul posto da una chiamata Siri chiamata dall'interno dell'app.
- **Contattare interazioni** - novità in iOS 10, `NSUserActivity` consente alle app di comunicazione da innalzare di livello da una scheda contatto (nell'app contatti) come metodo di comunicazione alternativi.

Tutte queste funzionalità hanno una cosa in comune, tutte usano `NSUserActivity` in un form o un'altra per fornire le relative funzionalità. 

## <a name="nsuseractivity"></a>NSUserActivity

Come indicato in precedenza, `NSUserActivity` consente al sistema di comprendere quali informazioni l'utente è attualmente lavora sullo schermo. `NSUserActivity` è uno stato più semplice la memorizzazione nella cache meccanismo per acquisire l'attività dell'utente così come si spostano le app. Ad esempio, osservando una ristorante app:

[![](proactive-suggestions-images/activity02.png "Lo stato leggero NSUserActivity meccanismo di memorizzazione nella cache")](proactive-suggestions-images/activity02.png#lightbox)

Con le interazioni seguente:

1. L'utente lavora con l'app, un `NSUserActivity` viene creato per ricreare lo stato dell'app in un secondo momento.
2. Se l'utente effettua la ricerca di un ristorante, viene seguito lo stesso modello di creazione di attività.
3. E anche in questo caso, quando l'utente visualizza un risultato. In quest'ultimo caso, la visualizzazione di una posizione e in iOS 10, il sistema è più compatibile con alcuni concetti (ad esempio, le interazioni di posizione o le comunicazioni).

Esaminiamo più da vicino l'ultima schermata:

[![](proactive-suggestions-images/activity03.png "I dettagli di NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

In questo caso consiste nel creare l'app una `NSUserActivity` ed è stato popolato con le informazioni per ricreare lo stato in un secondo momento. L'app è inclusa anche alcuni metadati, ad esempio nome e l'indirizzo del percorso. Con questa attività di creazione, l'app consente a iOS sapere che rappresenta lo stato dell'utente corrente.

L'app decide quindi se l'attività verrà annunciato modalità wireless per la distribuzione, salvato come un valore temporaneo per i suggerimenti di percorso o aggiungere all'indice Spotlight su dispositivo per la visualizzazione nei risultati della ricerca.

Per altre informazioni su ricerca di Spotlight e senza continuità, vedere la [Introduzione a Handoff](~/ios/platform/handoff.md) e [iOS 9 nuove API di ricerca](~/ios/platform/search/index.md) Guide.

### <a name="creating-an-activity"></a>Creazione di un'attività

Prima di creare un'attività, sarà necessario un identificatore di tipo attività da creare per facilitarne l'identificazione. L'identificatore del tipo di attività è una stringa breve aggiunta per il `NSUserActivityTypes` matrice dell'app `Info.plist` file utilizzato per identificare in modo univoco un determinato tipo di attività utente. Vi sarà una voce nella matrice per ogni attività che l'app supporta ed espone a ricerca di App. Vedere la [riferimento a identificatori di tipo attività creazione](~/ios/platform/search/nsuseractivity.md) per altri dettagli.

Esaminiamo un esempio di un'attività:

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

Viene creata una nuova attività usando un identificatore di tipo di attività. Successivamente, alcuni metadati che definiscono l'attività viene creata in modo che questo stato può essere ripristinato in un secondo momento. Quindi, l'attività viene assegnato un titolo significativo e collegato alla informazioni utente. Infine, alcune funzionalità sono abilitate e l'attività viene inviato al sistema.

Il codice riportato sopra può essere ulteriormente migliorato in modo da includere i metadati che fornisce il contesto all'attività apportando le modifiche seguenti:

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

Se lo sviluppatore dispone di un sito Web che è in grado di visualizzare le stesse informazioni dell'App, l'app può includere l'URL e il contenuto può essere visualizzato in altri dispositivi che non hanno l'app installata (tramite Handoff):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Il ripristino di un'attività

Per rispondere all'utente se si tocca un risultato di ricerca (`NSUserActivity`) per l'app, modificare il **AppDelegate.cs** file ed eseguire l'override di `ContinueUserActivity` (metodo). Ad esempio:

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

Lo sviluppatore dovrà assicurarsi che questo sia lo stesso identificatore di tipo di attività (`com.xamarin.platform`) come l'attività creata in precedenza. L'app Usa le informazioni archiviate nel `NSUserActivity` per ripristinare lo stato al punto in cui l'utente è stata interrotta.

### <a name="benefits-of-creating-an-activity"></a>Vantaggi della creazione di un'attività

Con la quantità minima di codice presentato in precedenza, l'app è ora possibile sfruttare i vantaggi delle tre nuove funzionalità di iOS 10:

- **Handoff**
- **Ricerca Spotlight**
- **Promemoria di Siri contestuali**

La sezione seguente verrà esaminati di attivazione di due altre nuove funzionalità di iOS 10:

- **Suggerimenti di posizione**
- **Richieste Siri contestuali**

### <a name="location-based-suggestions"></a>Suggerimenti basati sulla posizione 

Eseguire l'esempio di app di ricerca il ristorante precedente. Se è implementato `NSUserActivity` e popolato correttamente tutti i metadati e gli attributi, l'utente sarà in grado di eseguire le operazioni seguenti:

1. Trovare un ristorante nell'app che desiderano ricevere per soddisfare un amico in.
2. Quando l'utente sposta dall'app tramite la selezione di app il multitasking, il sistema visualizzerà automaticamente un suggerimento (nella parte inferiore della schermata) per ottenere le indicazioni per il ristorante usando le app Preferiti di navigazione.
3. Se l'utente passa all'app di messaggi e inizia a digitare *"Vediamoci"*, la tastiera QuickType suggerisce automaticamente incollare l'indirizzo del ristorante.
4. Se l'utente passa all'app mappe, indirizzo del ristorante viene suggerito automaticamente come una destinazione.
5. Questa opzione funziona anche per le app di terze parti 3rd (che supportano `NSUserActivity`), in modo che l'utente può passare a un'app per la condivisione rani e indirizzo del ristorante viene suggerito automaticamente come destinazione presente anche.
6. Inoltre fornisce il contesto Siri, in modo che l'utente può richiamare Siri all'interno dell'app ristorante e chiedere *"Indicazioni stradali..."* e Siri fornirà indicazioni per il ristorante Visualizza.

Tutte le funzionalità descritte sopra è una cosa in comune, tutti indicano il suggerimento originariamente provenienza. Nel caso dell'esempio precedente, è l'app di revisione del ristorante fittizia.

iOS 10 è stata migliorata per abilitare questa funzionalità per un'app tramite diverse piccole modifiche e aggiunte al Framework esistenti:

- `NSUserActivity` include campi aggiuntivi per l'acquisizione di informazioni sulla posizione che viene visualizzati all'interno dell'app.
- Diverse funzionalità sono state apportate MapKit e CoreSpotlight acquisire posizione.
- Funzionalità con riconoscimento percorso è stata aggiunta a Siri, mappe, tastiere, Multitasking e altre App all'interno del sistema.

Per implementare i suggerimenti basato su posizione, iniziare con lo stesso codice di attività illustrato in precedenza:

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

Se l'app Usa MapKit, è sufficiente aggiungere l'oggetto map corrente `MKMapItem` all'attività:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Se l'app non usa MapKit, può adottare ricerca nelle App e specificare i nuovi attributi per il percorso seguenti:

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

Esaminare il codice sopra riportato in modo dettagliato. Il nome della posizione in primo luogo, è necessario in ogni istanza di:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Quindi, la descrizione di testo in base della posizione è obbligatorio per le istanze basata su testo (ad esempio, la tastiera QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitudine e longitudine sono facoltativi, ma assicurano che l'utente viene indirizzato alla posizione esatta dell'app è che desiderano per inviarli, pertanto devono essere incluso:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Impostando i numeri di telefono, l'app può accedere a Siri in modo che l'utente può richiamare Siri dall'app da, ad esempio *"Chiama la risorsa"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Infine, l'app può indicare se l'istanza è adatto per la navigazione e le chiamate telefoniche:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementazione di interazioni contatto

Nuovo in iOS 10, App di comunicazione sono integrata nell'app contatti dalla scheda contatto. Per le app che hanno implementato le interazioni di contatto, l'utente può aggiungere le informazioni dell'app specificato a destinatari specifici dei contatti. E se, ad esempio, si raggiunge il pulsante di azione rapida nella parte superiore di una scheda per inviare un messaggio, l'app associata verrà elencata come un'opzione per inviare il messaggio.

Se si seleziona un'app di terze parti 3rd, può essere memorizzata e presentato come la modalità predefinita per la volta successiva che l'utente desidera contattarli la persona specificata del messaggio.

Interazioni di contatto vengono implementate usando l'app `NSUserActivity` e nuovi framework Intent introdotto in iOS 10. Per altre informazioni sull'uso di Intent, vedere la [informazioni sui concetti di SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) e [implementazione di SiriKit](~/ios/platform/sirikit/implementing-sirikit.md) Guide.

#### <a name="donating-interactions"></a>Donazione interazioni

Esaminiamo come l'app può donare interazioni:

[![](proactive-suggestions-images/activity04.png "Panoramica di interazioni donazione")](proactive-suggestions-images/activity04.png#lightbox)

L'app crea un' `INInteraction` oggetto che contiene un **Intent** (`INIntent`), **partecipanti** e **metadati**. Il **finalità** rappresenta un'azione dell'utente, ad esempio una telefonata video o l'invio di un messaggio di testo. Il **partecipanti** includono le persone che ricevono la comunicazione. Il **metadati** definisce informazioni aggiuntive, ad esempio inviare correttamente il messaggio e così via.

Lo sviluppatore crea mai direttamente un'istanza di `INIntent` o `INIntentResponse`, si userà una delle classi figlio specifico (in base all'attività è eseguire l'app per conto dell'utente) che ereditano da queste classi padre. Ad esempio, `INSendMessageIntent` e `INSendMessageIntentResponse` per l'invio di un messaggio di testo. 

Una volta completata l'interazione è completamente popolata, chiamare il `DonateInteraction` metodo per fornire informazioni al sistema che l'interazione può essere utilizzato.

Quando l'utente interagisce con l'app dalla scheda contatto, l'interazione Ottiene unite in bundle con una `NSUserActivity`, che viene quindi usato per avviare l'app:

[![](proactive-suggestions-images/activity05.png "L'interazione Ottiene abbinata a un NSUserActivity che viene usato per avviare l'app")](proactive-suggestions-images/activity05.png#lightbox)

Esaminiamo l'esempio seguente di un programma di messaggio di trasmissione:

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

Esaminando questo codice in modo dettagliato, crea e popola un'istanza della `NSUserActivity` (come illustrato nella [creazione di un'attività](#creating-an-activity) sezione precedente). Successivamente, crea un'istanza di `INSendMessageIntent` (che eredita da `INIntent`) e lo popola con i dettagli del messaggio inviato:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Un' `INSendMessageIntentResponse` viene creato e passato il `NSUserActivity` creato in precedenza:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Un' `INInteraction` progettato per lo scopo di messaggi di trasmissione (`INSendMessageIntent`) e di risposta (`INSendMessageIntentResponse`) appena creato:

```csharp
var interaction = new INInteraction (intent, response);
```

Infine, il sistema è la notifica dell'interazione:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

Un gestore di completamento viene passato in cui può rispondere l'app per la donazione in risorse eseguito correttamente o meno.

### <a name="activities-best-practices"></a>Attività consigliate

Le procedure consigliate seguenti suggerite da Apple quando si lavora con le attività:

- Usare `NeedsSave` per gli aggiornamenti di payload lazy.
- Assicurarsi di mantenere un riferimento sicuro all'attività corrente.
- Trasferire solo i payload di piccole dimensioni che includono informazioni sufficienti per ripristinare lo stato.
- Assicurarsi che gli identificatori di tipo attività siano descrittivo e univoco utilizzando la notazione DNS inverso per specificarle. 

## <a name="schemaorg"></a>Schema.org

Come illustrato in precedenza, `NSUserActivity` consente al sistema di comprendere quali informazioni l'utente è attualmente lavora sullo schermo. Schema.org aggiunge funzionalità simili alle pagine web.

Schema.org può fornire gli stessi tipi di interazioni di posizione in base al sito Web. Apple progettato i nuovi suggerimenti sulle località a funzionare altrettanto bene quando viene visualizzato in Safari, come avviene in un'app nativa.

Alcuni concetti di base Schema.org:

- Fornisce uno standard di vocabolario markup web aperta.
- Funziona mediante l'inclusione di metadati strutturati sulle pagine web.
- Sono presenti oltre 500 schemi che rappresentano i diversi concetti disponibili.
- Grazie all'implementazione, nel sito Web, lo sviluppatore può acquisire alcuni dei vantaggi dell'uso `NSUserActivity` in un'app nativa.

Gli schemi vengono disposte in una struttura ad albero come struttura, in cui i tipi di specifiche, ad esempio *ristorante*, ereditano da tipi più generici, ad esempio *aziendale locale*. Per altre informazioni, vedi [Schema.org](http://schema.org).

Ad esempio, se la pagina web include i dati seguenti:

```xml
<script type="application/ld+json>
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

Se l'utente visita questa pagina in Safari e quindi passato a un'altra app, le informazioni sul percorso dalla pagina verrà acquisite e offerto come un suggerimento di percorso in altre parti del sistema (come illustrato nell'attività precedente).

Safari consentirà di estrarre qualsiasi elemento in una pagina web che rispetti le proprietà dello schema seguenti:

- **PostalAddress**
- **GeoCoordinates**
- Proprietà di un telefono.

Per altre informazioni, vedere la [eseguire una ricerca con Markup Web](~/ios/platform/search/web-markup.md) Guida.

## <a name="consuming-location-suggestions"></a>I suggerimenti di località di utilizzo

In questa sezione illustra l'utilizzo di suggerimento di percorso che provengono da altre parti del sistema (ad esempio, l'app mappe) o altre app di terze parti 3rd.

Esistono due modi principali, che l'app può utilizzare i suggerimenti di percorso:

- Tramite la tastiera QuickType.
- Direttamente usando le informazioni sulla posizione nelle app di routing.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>I suggerimenti di posizione e la tastiera QuickType

Se l'app gestisce gli indirizzi in formati basata su testo, l'app può sfruttare i suggerimenti di percorso attraverso la UI QuickType. iOS 10 espande la UI QuickType con le funzionalità seguenti:

- L'app è possibile aggiungere hint sull'intento semantica per i campi di testo nell'interfaccia utente.
- L'app può ottenere suggerimenti proattivi nell'app.
- Le app possono trarre vantaggio dalla correzione automatica migliorata.

Il nuovo `TextContentType` proprietà dei controlli di campo di testo in iOS 10 consente allo sviluppatore di definire l'intento semantica per il valore che l'utente dovrà immettere in un determinato campo. Ad esempio:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Potrebbe indicare al sistema che l'app richiede che l'utente di immettere un indirizzo completo nel campo specificato. In questo modo la tastiera QuickType fornire automaticamente i suggerimenti di posizione sulla tastiera quando l'utente immette un valore in questo campo.

Di seguito sono elencati alcuni dei tipi più comuni disponibili per lo sviluppatore nelle `UITextContentType` classe statica:

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Le app di routine e i suggerimenti di percorsi

In questa sezione verrà esaminato un utilizzo posizione suggerimenti direttamente da all'interno di un'app di routing. Per l'app di routing aggiungere questa funzionalità, lo sviluppatore sfrutteranno esistente `MKDirectionsRequest` framework come indicato di seguito:

- Per alzare di livello l'app in Multitasking.
- Per registrare l'app come app di routing.
- Per gestire l'avvio dell'app con un MapKit `MKDirectionsRequest` oggetto.
- Per consentire a iOS di informazioni per i suggerimenti per l'app per l'utente nel momento appropriato, in base coinvolgimento degli utenti.

Quando l'app viene avviata con un MapKit `MKDirectionsRequest` dell'oggetto, deve avviare automaticamente fornendo le direzioni di utente per la località richiesta, o presentare un'interfaccia utente che rende più semplice per l'utente a iniziare a ottenere le direzioni. Ad esempio:


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

Esaminiamo questo codice in modo dettagliato. Controlla per verificare se si tratta di una richiesta di destinazione valido:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Se si tratta, quindi, viene creato un `MKDirectionsRequest` dall'URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Nuovi in iOS 10, l'app è possibile inviare un indirizzo che non ha le coordinate geografiche, in che provocano che lo sviluppatore deve codificare l'indirizzo:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>Suggerimenti di App multimediali

Se l'app gestisce qualsiasi tipo di supporti, ad esempio un'app podcast o un contenuto multimediale, ad esempio audio o video, con iOS 10, possa usufruire di suggerimenti di supporti nel sistema.

Per le app di file multimediali, iOS supporta i comportamenti seguenti:

- iOS Alza di livello le app che l'utente è probabile che vengano utilizzati in base al loro comportamento precedente.
- Suggerimenti relativi all'app verranno visualizzati nell'argomento in primo piano e la visualizzazione oggi stesso.
- Se l'app soddisfa uno dei seguenti trigger, potrebbe essere elevato per un suggerimento di schermata di blocco:
    - Dopo aver collegato le cuffie o un dispositivo Bluetooth stabilisce una connessione.
    - Dopo aver ottenuto in un'automobile.
    - Dopo che arrivano a casa o lavoro. 

Includendo una semplice chiamata API in iOS 10, lo sviluppatore può creare un'esperienza a schermo blocco più coinvolgente per gli utenti dell'app multimediali. Tramite il `MPPlayableContentManager` classe per gestire la riproduzione di contenuti multimediali, i controlli di supporti completo (ad esempio quelli già presentati dall'app Music) verrà visualizzata nella schermata di blocco per l'app.


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

Questo articolo è coperto suggerimenti proattivi ed è stato illustrato come lo sviluppatore può usarli per incentivare il traffico verso le app xamarin. IOS. Viene illustrato il passaggio per implementare i suggerimenti proattivi e presentate le linee guida sull'utilizzo.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Guida per programmatori di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
