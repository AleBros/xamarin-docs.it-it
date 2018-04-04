---
title: Introduzione ai suggerimenti attiva
description: In questo articolo viene illustrato come utilizzare i suggerimenti attiva nell'app xamarin al coinvolgimento di unità, consentendo al sistema di presentare in modo proattivo automaticamente informazioni utili all'utente.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 5b06dbf0e8e108616adb4f77910267aaa1ac71f4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-proactive-suggestions"></a>Introduzione ai suggerimenti attiva

_In questo articolo viene illustrato come utilizzare i suggerimenti attiva nell'app xamarin al coinvolgimento di unità, consentendo al sistema di presentare in modo proattivo automaticamente informazioni utili all'utente._

Familiarità con iOS 10, la presente notizie suggerimenti agli utenti di contattare un'app xamarin in modo proattivo presenti informazioni utili automaticamente all'utente in momenti appropriati.

iOS 10 presenta nuovi modi di Guida impegno per l'app per consentire al sistema in modo proattivo presentare informazioni utili automaticamente all'utente in momenti appropriati. Come iOS 9 fornita la possibilità di aggiungere una ricerca completa per l'app usando Spotlight, uniforme e i suggerimenti di Siri (vedere [nuove API di ricerca](~/ios/platform/search/index.md)), con iOS 10 un'app è possibile esporre funzionalità che possono essere presentate all'utente dal sistema all'interno di percorsi seguenti:

- La selezione di App
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni di Siri
- Suggerimenti QuickType

L'app espone questa funzionalità per il sistema utilizzando una raccolta di tecnologie, ad esempio `NSUserActivity`, markup web, componenti di base Spotlight, MapKit, Media Player e UIKit. Inoltre, grazie al supporto proattivo suggerimento per l'app, ottiene una maggiore integrazione Siri gratuitamente.

## <a name="location-based-suggestions"></a>Suggerimenti basati sulla posizione

Nuovo a 10, iOS il `NSUserActivity` classe include un `MapItem` proprietà che consente agli sviluppatori di fornire informazioni sul percorso che può essere usati in altri contesti. Ad esempio, se l'applicazione visualizza recensioni di ristoranti, lo sviluppatore può impostare il `MapItem` proprietà sulla posizione del ristorante che sta visualizzando l'utente nell'app. Se l'utente passa all'app di mappe, la posizione del ristorante è automaticamente disponibile.

Se l'app supporta la ricerca di App, è possibile utilizzare i nuovi componenti di indirizzo del `CSSearchableItemAttributesSet` classe per specificare percorsi che l'utente desidera visitare. Impostando il `MapItem` proprietà, le altre proprietà sono automaticamente compilato in.

Oltre all'impostazione di `Latitude` e `Longitude` indirizzo proprietà del componente, è consigliabile che l'applicazione fornisca il `NamedLocation` e `PhoneNumbers` proprietà troppo, pertanto Siri possono avviare una chiamata al percorso.

## <a name="web-markup-based-suggestions"></a>Suggerimenti basati sulle Markup Web

iOS 9 aggiunto alla possibilità di includere markup dati strutturati nel sito Web che arricchisce il contenuto visualizzato dagli utenti nei risultati della ricerca Spotlight e Safari (vedere [ricerca con Markup Web](~/ios/platform/search/web-markup.md)). iOS 10 consente di includere codice in base alla posizione (ad esempio [PostalAddress](http://schema.org/PostalAddress) definiti da [Schema.org](http://schema.org/)) per migliorare ulteriormente l'esperienza dell'utente. Ad esempio, se una visualizzazione agli utenti un percorso contrassegnato come pagina del sito Web, il sistema può suggerire nello stesso percorso quando aprono mappe.

## <a name="text-based-suggestions"></a>Suggerimenti basati su testo

È stata estesa UIKit iOS 10 per includere il [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) proprietà del [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) classe per specificare il valore semantico del contenuto in un'area di testo. Con queste informazioni sul posto, il sistema può in genere automaticamente, selezionare il tipo di tastiera appropriato, migliorare i suggerimenti di correzione automatica e integrare attivamente le informazioni da altre applicazioni e siti Web.

Ad esempio, se l'utente immette testo in un campo di testo contrassegnato `UITextContentType.FullStreetAddress`, il sistema può suggerire automatico riempimento il campo con il percorso in cui l'utente è stato recentemente visualizzando.

## <a name="media-based-suggestions"></a>Suggerimenti basati sulla Media

Se l'app viene eseguita utilizzando supporti di [MPPlayableContentManager](https://developer.xamarin.com/api/type/MediaPlayer.MPPlayableContentManager/) API iOS 10 consente agli utenti di visualizzare le immagini e riprodurre file multimediali tramite l'app nella schermata di blocco.

## <a name="contextual-siri-reminders"></a>Siri scelta promemoria

Consente all'utente di utilizzare Siri per creare rapidamente un promemoria per visualizzare il contenuto che attualmente visualizzate nell'app in un secondo momento. Se, ad esempio, nell'app da cui sono stati visualizzando una revisione del ristorante, può richiamare Siri e pronunciare *"Visualizza informazioni quando si è arrivati a casa".* Siri genererebbe il promemoria con un collegamento per la revisione nell'app.

## <a name="contact-based-suggestions"></a>Suggerimenti basati sulle contatto

Consente all'app contatti e informazioni correlate vengono visualizzati di **contattare** app sul dispositivo iOS insieme a tutti i contatti dell'utente archiviate nel sistema.

## <a name="ride-sharing-based-suggestions"></a>Sostituire la condivisione di suggerimenti in base

Se un'app di condivisione marcia Usa il [MKDirectionsRequest](https://developer.xamarin.com/api/type/MapKit.MKDirectionsRequest/) API iOS 10 presenterà come un'opzione di selezione del tipo di app quando l'utente è presumibile che si desideri una corsa. L'app deve essere registrato come un'applicazione di condivisione marcia anche specificando il `MKDirectionsModeRideShare` per il [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) chiave nel relativo `Info.plist` file.

Se l'app supporta solo il marcia condivisione, il suggerimento di sistema si inizierebbe con *"Ottenere un interscambio per..."*, se sono supportati altri tipi di routing direzione (ad esempio Walking o biciclette), il sistema utilizzerà *"Stradali..."*

> [!IMPORTANT]
> Il [MKMapItem](https://developer.xamarin.com/api/type/MapKit.MKMapItem/) oggetto che riceve l'app non può includere informazioni longitudine e latitudine e richiederà geocodifica.

## <a name="implementing-proactive-suggestions"></a>Implementazione di suggerimenti attiva

Aggiungendo il suggerimento per un'app xamarin è in genere facile quanto l'implementazione di alcune API o l'espansione in alcune API che l'app potrebbe già essere implementato.

Suggerimenti proattivi di lavoro con le applicazioni in tre modi principali:

- **`NSUserActivity` e Schema.org**  -  `NSUserActivity` consente il sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. Schema.org aggiunge funzionalità simili a pagine web.
- **Percorso suggerimenti** : se l'app offre o utilizza informazioni di percorso di base, queste nuove procedure offerta estensione API per condividere le informazioni tra applicazioni.
- **Supporto App suggerimenti** : il sistema è possibile alzare di livello applicazione e il relativo contenuto multimediale in base al contesto dell'interazione dell'utente con il dispositivo iOS.

Ed è supportato nell'app implementando le operazioni seguenti:

- **Passaggio di consegne**  -  `NSUserActivity` è stato aggiunto in iOS 8 per supportare uniforme, che consente allo sviluppatore di avviare un'attività in un dispositivo, quindi continuare su un altro (vedere [Introduzione a Handoff](~/ios/platform/handoff.md)).
- **Riflettore ricerca** -iOS 9 aggiunta la possibilità di promuovere il contenuto applicazione dall'interno i risultati della ricerca Spotlight utilizzando `NSUserActivity` (vedere [ricerca Spotlight di Core](~/ios/platform/search/corespotlight.md)).
- **Scelta promemoria Siri** - 10, iOS `NSUserActivity` è stato espanso per consentire a Siri di creare rapidamente un promemoria per visualizzare il contenuto, l'utente è attualmente visualizzato nell'app in un secondo momento.
- **Percorso suggerimenti** -iOS 10 migliora `NSUserActivity` per acquisire i percorsi visualizzati all'interno dell'app e alzare di livello in numerose posizioni in tutto il sistema.
- **Le richieste di Siri contestuali**  -  `NSUserActivity` fornisce contesto per le informazioni presentate all'interno dell'app a Siri in modo che l'utente può ottenere le direzioni o sul posto da una chiamata Siri chiamata dall'interno dell'app.
- **Interazioni contatto** - novità di 10, iOS `NSUserActivity` consente alle app di comunicazione da innalzare di livello da una scheda contatto (nell'app contatti) come metodo alternativo di comunicazione.

Tutte queste funzionalità hanno una cosa in comune, tutte utilizzano `NSUserActivity` in un form o un altro per fornire le proprie funzionalità. 

## <a name="nsuseractivity"></a>NSUserActivity

Come descritto in precedenza, `NSUserActivity` consente il sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. `NSUserActivity` è stato un leggero la memorizzazione nella cache meccanismo per acquisire l'attività dell'utente così come si spostano l'app. Esaminare, ad esempio, un'applicazione ristorante:

[![](proactive-suggestions-images/activity02.png "Lo stato di leggera NSUserActivity meccanismo di memorizzazione nella cache")](proactive-suggestions-images/activity02.png#lightbox)

Con le interazioni seguente:

1. L'utente lavora con l'app, un `NSUserActivity` viene creato per ricreare lo stato dell'app in un secondo momento.
2. Se l'utente effettua la ricerca di un ristorante, viene seguito lo stesso modello di creazione di attività.
3. E anche quando l'utente visualizza un risultato. In quest'ultimo caso, la visualizzazione di un percorso e in iOS 10, il sistema è più conoscere alcuni concetti (ad esempio, le interazioni di posizione o le comunicazioni).

Esaminare più vicino all'ultima pagina:

[![](proactive-suggestions-images/activity03.png "I dettagli di NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Qui è la creazione di app un `NSUserActivity` ed è stato popolato con informazioni per ricreare lo stato in un secondo momento. L'app è incluso anche alcuni metadati, ad esempio nome e l'indirizzo della sede. Con questa attività di creazione, l'app informa iOS che rappresenta lo stato dell'utente corrente.

L'app decide quindi se l'attività verrà annunciato modalità wireless per la consegna, salvato come un valore temporaneo per i suggerimenti di percorso o aggiungere all'indice Spotlight sul dispositivo per la visualizzazione nei risultati della ricerca.

Per ulteriori informazioni sulla ricerca Handoff e Spotlight, consultare il nostro [Introduzione a Handoff](~/ios/platform/handoff.md) e [iOS 9 nuove API di ricerca](~/ios/platform/search/index.md) Guide.

### <a name="creating-an-activity"></a>Creazione di un'attività

Prima di creare un'attività, sarà necessario un identificatore di tipo attività da creare per facilitarne l'identificazione. L'identificatore del tipo di attività è una stringa breve aggiunta il `NSUserActivityTypes` matrice dell'app `Info.plist` file utilizzato per identificare in modo univoco un determinato tipo di attività utente. Sarà presente una voce nella matrice per ogni attività che l'app supporta ed espone alla ricerca di App. Vedere il nostro [creazione riferimento identificatori di tipo attività](~/ios/platform/search/nsuseractivity.md) per altri dettagli.

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

Una nuova attività viene creata utilizzando un identificatore di tipo attività. Successivamente, alcuni metadati che definisce l'attività viene creato in modo da questo stato può essere ripristinato in un secondo momento. Quindi, l'attività viene assegnato un titolo significativo e collegato alle informazioni utente. Infine, alcune funzionalità sono abilitate e l'attività viene inviato al sistema.

Il codice precedente può essere ulteriormente migliorato per includere i metadati che fornisce il contesto per l'attività apportando le modifiche seguenti:

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

Se lo sviluppatore dispone di un sito Web che è in grado di visualizzare le stesse informazioni dell'App, l'applicazione può includere l'URL e il contenuto possa essere visualizzato su altri dispositivi che non hanno l'app installata (tramite Handoff):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Ripristino di un'attività

Per rispondere all'utente se si tocca su un risultato di ricerca (`NSUserActivity`) per l'app, modificare il **appdelegate. cs** file ed eseguire l'override di `ContinueUserActivity` metodo. Ad esempio:

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

Lo sviluppatore dovrà verificare che questo sia lo stesso identificatore di tipo di attività (`com.xamarin.platform`) come attività creato in precedenza. L'applicazione utilizza le informazioni archiviate nel `NSUserActivity` per ripristinare lo stato al punto in cui l'utente è stata interrotta.

### <a name="benefits-of-creating-an-activity"></a>Vantaggi della creazione di un'attività

Con la quantità minima di codice presentato in precedenza, l'applicazione è ora in grado di sfruttare i vantaggi delle nuove funzionalità di iOS 10 tre:

- **Handoff**
- **Alla ricerca Spotlight**
- **Siri scelta promemoria**

Nella sezione seguente verrà analizzati attivazione due altre nuove funzionalità di iOS 10:

- **Suggerimenti di percorso**
- **Richieste di Siri contestuali**

### <a name="location-based-suggestions"></a>Suggerimenti basati sulla posizione 

Nell'esempio di app di ricerca il ristorante precedente. Se è implementato `NSUserActivity` e compilato correttamente tutti i metadati e gli attributi, l'utente sarà in grado di eseguire le operazioni seguenti:

1. Trovare un ristorante nell'app che desiderano ricevere per soddisfare un elemento friend in.
2. Quando l'utente sposta lontano l'app usando la selezione di app multitasking, il sistema visualizza automaticamente un suggerimento (nella parte inferiore dello schermo) per ottenere istruzioni per il ristorante con il proprio app preferite navigazione.
3. Se l'utente passa all'app di messaggi e inizia a digitare *"Si incontrano in"*, la tastiera QuickType suggerisce automaticamente incollare l'indirizzo del ristorante.
4. Se l'utente passa all'app di mappe, indirizzo del ristorante viene suggerito automaticamente come una destinazione.
5. Questo procedimento funziona anche per le applicazioni di terze parti 3rd (che supportano `NSUserActivity`), in modo che l'utente può passare a un'applicazione di condivisione marcia e l'indirizzo del ristorante viene suggerito automaticamente come destinazione di anche.
6. Fornisce inoltre contesto a Siri, in modo che l'utente può richiamare Siri all'interno dell'app ristorante e chiedere *"Stradali..."* e Siri fornirà indicazioni per il ristorante Visualizza l'utente.

Tutte le funzionalità descritte sopra è una cosa in comune, tutti indicano il suggerimento originariamente provenienza. Nel caso dell'esempio precedente, è l'app di revisione del ristorante fittizio.

iOS 10 è stata migliorata per abilitare questa funzionalità per un'applicazione tramite più piccole modifiche e aggiunte al Framework esistenti:

- `NSUserActivity` include campi aggiuntivi per l'acquisizione di informazioni sul percorso che viene visualizzati all'interno dell'app.
- Diverse aggiunte sono state apportate MapKit e CoreSpotlight per acquisire percorso.
- Percorso compatibile con funzionalità è stata aggiunta a Siri, mappe, tastiere, Multitasking e altre App all'interno del sistema.

Per implementare i suggerimenti di base, iniziare con lo stesso codice di attività presentato in precedenza:

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

Se l'applicazione non utilizza MapKit, può adottare l'App ricerca e specificare i seguenti nuovi attributi per il percorso:

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

Esaminare il codice sopra riportato in modo dettagliato. Il nome della posizione in primo luogo, è necessario in ogni istanza:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Quindi, la descrizione di testo in base della posizione è richiesta per le istanze basata su testo (ad esempio, la tastiera QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitudine e longitudine sono facoltativi, ma assicurarsi che l'utente viene indirizzato alla posizione esatta che dell'app è desiderano inviarli, in modo da inclusa:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Impostando i numeri di telefono, l'app può accedere a Siri pertanto l'utente può richiamare Siri dall'app pronunciando simile al seguente, *"Chiama la risorsa"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Infine, l'app può indicare se l'istanza è adatto per la navigazione e chiamate telefoniche:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementazione di interazioni contattare

In iOS 10, nuove app di comunicazione sono perfettamente integrata nell'app contatti dalla scheda contatto. Per le applicazioni che hanno implementato le interazioni di contatto, l'utente può aggiungere informazioni dell'applicazione specificata a utenti specifici dei contatti. E se, ad esempio, sono fare clic sul pulsante di azione rapida nella parte superiore di una scheda per inviare un messaggio, l'app associata verrà elencato come un'opzione per inviare il messaggio.

Se si seleziona un'app di terze parti 3rd, può essere memorizzata e presentato come la modalità predefinita per la volta successiva che l'utente desidera contattare la persona specificata del messaggio.

Interazioni di contatto vengono implementate in cui l'app mediante `NSUserActivity` e il nuovo framework di tipi introdotti in iOS 10. Per ulteriori informazioni sull'utilizzo dei tipi, vedere il nostro [informazioni sui concetti di SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) e [SiriKit implementazione](~/ios/platform/sirikit/implementing-sirikit.md) Guide.

#### <a name="donating-interactions"></a>Interazioni donatrice

Esaminare la modalità l'app può donare interazioni:

[![](proactive-suggestions-images/activity04.png "Panoramica di interazioni donatrice")](proactive-suggestions-images/activity04.png#lightbox)

L'applicazione crea un `INInteraction` oggetto che contiene un **finalità** (`INIntent`), **partecipanti** e **metadati**. Il **finalità** rappresenta un'azione dell'utente, ad esempio una chiamata di video o l'invio di un messaggio di testo. Il **partecipanti** includono le persone che ricevono la comunicazione. Il **metadati** definisce informazioni aggiuntive, ad esempio inviare correttamente il messaggio e così via.

Lo sviluppatore crea mai direttamente un'istanza di `INIntent` o `INIntentResponse`, utilizzano una delle classi figlio specifico (in base all'attività di esecuzione di app per conto dell'utente) che ereditano da queste classi padre. Ad esempio, `INSendMessageIntent` e `INSendMessageIntentResponse` per l'invio di un messaggio di testo. 

Una volta completata l'interazione è completamente popolato, chiamare il `DonateInteraction` metodo per informare il sistema che è disponibile per l'utilizzo interazione.

Quando l'utente interagisce con l'app dalla scheda contatto, l'interazione ottiene in bundle con un `NSUserActivity`, che viene quindi usato per avviare l'app:

[![](proactive-suggestions-images/activity05.png "L'interazione ottiene in bundle con un NSUserActivity utilizzato per avviare l'app")](proactive-suggestions-images/activity05.png#lightbox)

Esaminare l'esempio seguente di un scopo del messaggio di trasmissione:

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

Esaminando questo codice in modo dettagliato, crea e popola un'istanza di `NSUserActivity` (come illustrato nel [creazione di un'attività](#Creating-an-Activity) sezione precedente). Successivamente, crea un'istanza di `INSendMessageIntent` (che eredita da `INIntent`) e la popola con i dettagli del messaggio inviato:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Un `INSendMessageIntentResponse` viene creato e passato il `NSUserActivity` creato in precedenza:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Un `INInteraction` è compilato in base alle finalità di messaggi di trasmissione (`INSendMessageIntent`) e di risposta (`INSendMessageIntentResponse`) appena creato:

```csharp
var interaction = new INInteraction (intent, response);
```

Infine, il sistema è notifica dell'interazione:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

Un gestore di completamento viene passato in cui l'app può rispondere a donato esito positivo o esito negativo.

### <a name="activities-best-practices"></a>Le attività consigliate

Quando si lavora con le attività, Apple suggerisce le procedure consigliate seguenti:

- Utilizzare `NeedsSave` per gli aggiornamenti di payload lazy.
- Assicurarsi di mantenere un riferimento forte all'attività corrente.
- Solo il trasferimento payload di piccole dimensioni che includono informazioni sufficienti per ripristinare lo stato.
- Verificare che gli identificatori di tipo attività siano descrittivo e univoco utilizzando la notazione DNS inversa specificarli. 

## <a name="schemaorg"></a>Schema.org

Come illustrato in precedenza, `NSUserActivity` consente il sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. Schema.org aggiunge funzionalità simili a pagine web.

Schema.org può fornire gli stessi tipi di interazioni posizione in base al sito Web. Apple progettato nuovi suggerimenti di percorso a funzionare anche quando viene visualizzato in Safari come avviene in un'applicazione nativa.

Alcuni concetti di base Schema.org:

- Fornisce uno standard di vocabolario markup web aperta.
- Funziona con l'inclusione di metadati strutturati sulle pagine web.
- Sono disponibili oltre 500 schemi che rappresentano vari concetti disponibili.
- Da implementare nel sito Web, lo sviluppatore può acquisire alcuni dei vantaggi dell'utilizzo di `NSUserActivity` in un'applicazione nativa.

Gli schemi vengono disposte in una struttura ad albero come struttura, in cui i tipi di specifiche, ad esempio *ristorante*, ereditare da tipi più generici come *aziendale locale*. Per ulteriori informazioni, vedere [Schema.org](http://schema.org).

Ad esempio, se la pagina web inclusi i seguenti dati:

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

Se l'utente visita questa pagina in Safari e quindi passa a un'altra app, acquisite le informazioni sul percorso dalla pagina e offerto come un suggerimento di percorso in altre parti del sistema (come illustrato nelle attività precedenti).

Safari estrarrà qualsiasi elemento in una pagina web che rispetti le seguenti proprietà dello schema:

- **PostalAddress**
- **Coordinate geografiche**
- Proprietà di un telefono.

Per ulteriori informazioni, vedere il nostro [ricerca con Markup Web](~/ios/platform/search/web-markup.md) Guida.

## <a name="consuming-location-suggestions"></a>Utilizzo di suggerimenti di percorso

In questa sezione verrà illustrate suggerimento percorso provenienti da altre parti del sistema (ad esempio l'app esegue il mapping) o altre applicazioni di terze parti 3rd di utilizzo.

Esistono due modi principali, che l'applicazione può utilizzare i suggerimenti di percorso:

- Tramite la tastiera QuickType.
- Direttamente usando le informazioni sul percorso nelle app di routing.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Suggerimenti di posizione e della tastiera QuickType

Se l'applicazione gestisce gli indirizzi in formati di testo in base, l'app può sfruttare i suggerimenti di posizione attraverso la UI QuickType. iOS 10 espande la UI QuickType con le funzionalità seguenti:

- L'app è possibile aggiungere suggerimenti sull'intenzione di semantica per i campi di testo nell'interfaccia utente.
- L'app può ottenere suggerimenti attiva nell'app.
- L'app può beneficiare di correzione automatica avanzata.

Il nuovo `TextContentType` proprietà dei controlli di campo di testo in iOS 10 consente allo sviluppatore di definire l'intenzione di semantica per il valore che l'utente dovrà immettere in un determinato campo. Ad esempio:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Sarebbe indicano che l'applicazione prevede che l'utente di immettere un indirizzo completo nel campo specificato. In questo modo la tastiera QuickType fornire automaticamente i suggerimenti di posizione della tastiera quando l'utente immette un valore in questo campo.

Di seguito vengono riportati alcuni dei tipi più comuni disponibili per lo sviluppatore nel `UITextContentType` classe statica:

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Routing App e i suggerimenti di percorsi

In questa sezione verrà esaminato un utilizzo suggerimenti percorso direttamente all'interno di un'applicazione di routing. Per l'applicazione di routing aggiungere questa funzionalità, lo sviluppatore sfruttano esistente `MKDirectionsRequest` framework come indicato di seguito:

- Per alzare di livello l'app in Multitasking.
- Per registrare l'app come un'applicazione di routing.
- Per gestire l'avvio dell'app con un MapKit `MKDirectionsRequest` oggetto.
- Per consentire a iOS per suggerire l'app per l'utente nel momento appropriato, in base il coinvolgimento degli utenti.

Quando l'app viene avviato con un MapKit `MKDirectionsRequest` dell'oggetto, è necessario avviare automaticamente assegnando le direzioni di utente nel percorso richiesto, o presentare un'interfaccia utente che rende più semplice per l'utente a iniziare a ottenere le direzioni. Ad esempio:


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

Esaminare il codice in modo dettagliato. Verifica per verificare se si tratta di una richiesta di destinazione valido:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Se è, quindi, viene creato un `MKDirectionsRequest` dall'URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Nuovo in iOS 10, l'app è possibile inviare un indirizzo che non dispone di coordinate geografico, in che provocano che lo sviluppatore deve codificare l'indirizzo:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>Supporto App suggerimenti

Se l'applicazione gestisce qualsiasi tipo di supporto come un'app di podcast o un contenuti multimediali, ad esempio audio o video, con 10, iOS possa usufruire di suggerimenti, supporto del sistema.

Per le applicazioni che gestiscono i supporti, iOS supporta i comportamenti seguenti:

- iOS Alza di livello le app che l'utente è possibile utilizzare in base al relativo comportamento precedente.
- Suggerimenti relativi all'app verranno visualizzati in primo piano e la visualizzazione oggi.
- Se l'app soddisfa uno dei seguenti trigger, potrebbe essere elevato per un suggerimento di schermata di blocco:
    - Dopo aver collegato cuffie o un dispositivo Bluetooth stabilisce una connessione.
    - Dopo il recupero di un'automobile.
    - Dopo che arrivano a casa o lavoro. 

Includendo una semplice chiamata all'API di iOS 10, lo sviluppatore può creare un'esperienza di schermata di blocco più coinvolgente per gli utenti dell'app media. Tramite il `MPPlayableContentManager` classe per gestire la riproduzione di contenuti multimediali, i controlli di supporto completo (ad esempio quelli presentati dall'app musica) verrà visualizzato nella schermata di blocco per l'app.


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

In questo articolo ha interessato attiva i suggerimenti è stato illustrato come lo sviluppatore può utilizzarli per il traffico di unità per l'app xamarin Incluso il passaggio per implementare i suggerimenti proattiva e presentate le linee guida.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Guida per programmatori SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
