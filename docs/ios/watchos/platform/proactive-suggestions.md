---
title: Suggerimenti proattivi in Xamarin watchOS
description: Questo articolo illustra come usare i suggerimenti proattivi in un'app watchOS 3 al coinvolgimento, consentendo al sistema in modo proattivo presentare automaticamente informazioni utili all'utente.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 979b103db478e3888d3a3c20df6afbd91d0c37d8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116901"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>Suggerimenti proattivi in Xamarin watchOS

_Questo articolo illustra come usare i suggerimenti proattivi in un'app watchOS 3 al coinvolgimento, consentendo al sistema in modo proattivo presentare automaticamente informazioni utili all'utente._


Nuovo a watchOS 3 modi notizie presenti suggerimenti proattivi agli utenti di interagire con un'app xamarin. IOS da presentare in modo proattivo le informazioni utili automaticamente all'utente in momenti appropriati.


## <a name="about-proactive-suggestions"></a>Sui suggerimenti proattivi

Nuovo a watchOS 3 `NSUserActivity` include un `MapItem` proprietà che consente all'app fornire informazioni di percorso che possono essere usate in altri contesti. Se, ad esempio, hotel app visualizzate verifiche e fornisce un `MapItem` percorso, se l'utente passa all'app mappe, la posizione dell'hotel si stava visualizzando appena sarà disponibile.

L'app espone la funzionalità per il sistema utilizzando una raccolta di tecnologie, ad esempio `NSUserActivity`, MapKit, Media Player e UIKit. Inoltre, fornendo supporto proattivo suggerimento per l'app, si ottiene una maggiore integrazione Siri gratuitamente.

## <a name="location-based-suggestions"></a>Suggerimenti basati sulla posizione

Novità di watchOS 3, il `NSUserActivity` classe include una `MapItem` proprietà che consente allo sviluppatore di fornire informazioni sul percorso che può essere usati in altri contesti. Ad esempio, se l'app consente di visualizzare le recensioni dei ristoranti, lo sviluppatore può impostare il `MapItem` proprietà per la posizione del ristorante che l'utente visualizza nell'app. Se l'utente passa all'app mappe, posizione del ristorante è automaticamente disponibile.

Se l'app supporta la ricerca di App, è possibile utilizzare i nuovi componenti di indirizzo del `CSSearchableItemAttributesSet` classe per specificare i percorsi che l'utente desidera visitare. Impostando il `MapItem` proprietà, le altre proprietà sono automaticamente riempita aggiuntivo.

Oltre a impostare il `Latitude` e `Longitude` delle proprietà del componente indirizzo, è consigliabile che l'app specificare la `NamedLocation` e `PhoneNumbers` proprietà troppo, pertanto Siri può avviare una chiamata al percorso.

## <a name="contextual-siri-reminders"></a>Promemoria di Siri contestuali

Consente all'utente di usare Siri per creare rapidamente un promemoria per visualizzare il contenuto che si sta visualizzando nell'app in un secondo momento. Ad esempio, se si stava visualizzando una revisione del ristorante nell'app, è stato possibile richiamare Siri e pronunciare *"Visualizza informazioni su questo quando recupera la principale".* Siri genererebbe il promemoria con un collegamento alla revisione nell'app.

## <a name="implementing-proactive-suggestions"></a>Implementazione di suggerimenti proattivi

Supporto per le app xamarin. IOS aggiungendo suggerimento proattiva è in genere semplice come implementazione di alcune API o l'espansione su alcune API che l'app potrebbe già essere implementato.

Suggerimenti proattivi funzionano con le App in tre modi principali:

- **`NSUserActivity`** -Consente al sistema di comprendere quali informazioni l'utente è attualmente lavora sullo schermo.
- **I suggerimenti di percorso** : se l'app offre o utilizza informazioni basato su posizione, questi nuovi modi offerta estensione API per condividere le informazioni tra app.

Ed è supportato nell'app mediante l'implementazione seguente:

- **Promemoria di Siri contestuali** : In iOS 10, `NSUserActivity` è stata ampliata per consente a Siri di creare rapidamente un promemoria per visualizzare il contenuto che si sta visualizzando nell'app in un secondo momento.
- **I suggerimenti di percorso** -migliora iOS 10 `NSUserActivity` acquisire percorsi visualizzati all'interno dell'app e alzare di livello in molte posizioni in tutto il sistema.
- **Le richieste Siri contestuali**  -  `NSUserActivity` fornisce contesto per le informazioni presentate all'interno dell'app a Siri in modo che l'utente può ottenere le direzioni o sul posto da una chiamata Siri chiamata dall'interno dell'app.

Tutte queste funzionalità hanno una cosa in comune, tutte usano `NSUserActivity` in un form o un'altra per fornire le relative funzionalità. 

## <a name="nsuseractivity"></a>NSUserActivity

Come indicato in precedenza, `NSUserActivity` consente al sistema di comprendere quali informazioni l'utente è attualmente lavora sullo schermo. `NSUserActivity` è uno stato più semplice la memorizzazione nella cache meccanismo per acquisire l'attività dell'utente così come si spostano le app. Ad esempio, accanto all'app di ristoranti:

[![](proactive-suggestions-images/activity02.png "L'app di ristoranti")](proactive-suggestions-images/activity02.png#lightbox)

Con le interazioni seguente:

1. L'utente lavora con l'app, un `NSUserActivity` viene creato per ricreare lo stato dell'app in un secondo momento.
2. Se l'utente effettua la ricerca di un ristorante, viene seguito lo stesso modello di creazione di attività.
3. E anche in questo caso, quando l'utente visualizza un risultato. In quest'ultimo caso, la visualizzazione di una posizione e in iOS 10, il sistema è più compatibile con alcuni concetti (ad esempio, le interazioni di posizione o le comunicazioni).

Esaminiamo più da vicino l'ultima schermata:

[![](proactive-suggestions-images/activity03.png "Il payload NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

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

Verificare che questo sia lo stesso identificatore di tipo di attività (`com.xamarin.platform`) come l'attività creata in precedenza. L'app Usa le informazioni archiviate nel `NSUserActivity` per ripristinare lo stato al punto in cui l'utente è stata interrotta.

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
4. Se l'utente passa all'app mappe, indirizzo del ristorante viene suggerito automaticamente come una destinazione.
5. Questa opzione funziona anche per le app di terze parti 3rd (che supportano `NSUserActivity`), in modo che l'utente può passare a un'app per la condivisione rani e indirizzo del ristorante viene suggerito automaticamente come destinazione presente anche.
6. Inoltre fornisce il contesto Siri, in modo che l'utente può richiamare Siri all'interno dell'app ristorante e chiedere *"Indicazioni stradali..."* e Siri fornirà indicazioni per il ristorante Visualizza.

Tutte le funzionalità descritte sopra è una cosa in comune, tutti indicano il suggerimento originariamente provenienza. Nel caso dell'esempio precedente, è l'app di revisione del ristorante fittizia.

watchOS 3 è stata migliorata per abilitare questa funzionalità per un'app tramite diverse piccole modifiche e aggiunte al Framework esistenti:

- `NSUserActivity` include campi aggiuntivi per l'acquisizione di informazioni sulla posizione che viene visualizzati all'interno dell'app.
- Diverse funzionalità sono state apportate MapKit e CoreSpotlight acquisire posizione.
- Funzionalità con riconoscimento percorso è stata aggiunta a Siri, mappe, il Multitasking e altre App all'interno del sistema.

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

Quindi, il testo descrizione basata su in richiesto per il testo basato su istanze (ad esempio, la tastiera QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitudine e longitudine sono facoltativi, ma assicurano che l'utente viene indirizzato alla posizione esatta per che dell'app è desiderano inviarli:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Impostando i numeri di telefono, l'app può accedere a Siri in modo che l'utente può richiamare Siri dall'app da, ad esempio * "Chiama la risorsa":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Infine, l'app può indicare se l'istanza è adatto per la navigazione e le chiamate telefoniche:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>Attività consigliate

Le procedure consigliate seguenti suggerite da Apple quando si lavora con le attività:

- Usare `NeedsSave` per gli aggiornamenti di payload lazy.
- Assicurarsi di mantenere un riferimento sicuro all'attività corrente.
- Trasferire solo i payload di piccole dimensioni che includono informazioni sufficienti per ripristinare lo stato.
- Assicurarsi che gli identificatori di tipo attività siano descrittivo e univoco utilizzando la notazione DNS inverso per specificarle. 

## <a name="consuming-location-suggestions"></a>I suggerimenti di località di utilizzo

In questa sezione illustra l'utilizzo di suggerimento di percorso che provengono da altre parti del sistema (ad esempio, l'app mappe) o altre app di terze parti 3rd.

## <a name="routing-apps-and-locations-suggestions"></a>Le app di routine e i suggerimenti di percorsi

In questa sezione verrà esaminato un utilizzo posizione suggerimenti direttamente da all'interno di un'app di routing. Per l'app di routing aggiungere questa funzionalità, lo sviluppatore sfrutteranno esistente `MKDirectionsRequest` framework come indicato di seguito:

- Per alzare di livello l'app in Multitasking.
- Per registrare l'app come app di routing.
- Per gestire l'avvio dell'app con un MapKit `MKDirectionsRequest` oggetto.
- Consentire a watchOS di informazioni per i suggerimenti per l'app basata sul coinvolgimento degli utenti.

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

Nuovo in watchOS 3, l'app è possibile inviare un indirizzo che non ha le coordinate geografiche, in che provocano che lo sviluppatore deve codificare l'indirizzo:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>Riepilogo

Questo articolo è coperto suggerimenti proattivi ed è stato illustrato come lo sviluppatore può usarli per incentivare il traffico a un'app xamarin. IOS per watchOS. Viene illustrato il passaggio per implementare i suggerimenti proattivi e presentate le linee guida sull'utilizzo.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Guida per programmatori di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
