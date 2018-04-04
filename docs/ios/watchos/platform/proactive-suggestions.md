---
title: Suggerimenti attiva
description: In questo articolo viene illustrato come utilizzare i suggerimenti attiva in un'app watchOS 3 al coinvolgimento di unità, consentendo al sistema di presentare in modo proattivo automaticamente informazioni utili all'utente.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: f22be43f814865c3c14e12aa2aec3a8dbce09b7a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="proactive-suggestions"></a>Suggerimenti attiva

_In questo articolo viene illustrato come utilizzare i suggerimenti attiva in un'app watchOS 3 al coinvolgimento di unità, consentendo al sistema di presentare in modo proattivo automaticamente informazioni utili all'utente._


Familiarità con watchOS 3, la presente notizie suggerimenti per gli utenti di stabilire con app xamarin in modo proattivo presenti informazioni utili automaticamente all'utente in momenti appropriati.


## <a name="about-proactive-suggestions"></a>Sui suggerimenti attiva

Novità di watchOS 3, `NSUserActivity` include un `MapItem` proprietà che consente all'app di fornire informazioni sul percorso che può essere usati in altri contesti. Ad esempio, se hotel app visualizzato revisioni e fornisce un `MapItem` percorso, se è attivata l'app esegue il mapping, il percorso dell'hotel appena visualizzata sarà disponibile.

L'app espone questa funzionalità per il sistema utilizzando una raccolta di tecnologie, ad esempio `NSUserActivity`, MapKit, Media Player e UIKit. Inoltre, grazie al supporto proattivo suggerimento per l'app, ottiene una maggiore integrazione Siri gratuitamente.

## <a name="location-based-suggestions"></a>Suggerimenti basati sulla posizione

Novità di watchOS 3, il `NSUserActivity` classe include un `MapItem` proprietà che consente agli sviluppatori di fornire informazioni sul percorso che può essere usati in altri contesti. Ad esempio, se l'applicazione visualizza recensioni di ristoranti, lo sviluppatore può impostare il `MapItem` proprietà sulla posizione del ristorante che sta visualizzando l'utente nell'app. Se l'utente passa all'app di mappe, la posizione del ristorante è automaticamente disponibile.

Se l'app supporta la ricerca di App, è possibile utilizzare i nuovi componenti di indirizzo del `CSSearchableItemAttributesSet` classe per specificare percorsi che l'utente desidera visitare. Impostando il `MapItem` proprietà, le altre proprietà sono automaticamente compilato in.

Oltre all'impostazione di `Latitude` e `Longitude` indirizzo proprietà del componente, è consigliabile che l'applicazione fornisca il `NamedLocation` e `PhoneNumbers` proprietà troppo, pertanto Siri possono avviare una chiamata al percorso.

## <a name="contextual-siri-reminders"></a>Siri scelta promemoria

Consente all'utente di utilizzare Siri per creare rapidamente un promemoria per visualizzare il contenuto che attualmente visualizzate nell'app in un secondo momento. Se, ad esempio, nell'app da cui sono stati visualizzando una revisione del ristorante, può richiamare Siri e pronunciare *"Visualizza informazioni quando si è arrivati a casa".* Siri genererebbe il promemoria con un collegamento per la revisione nell'app.

## <a name="implementing-proactive-suggestions"></a>Implementazione di suggerimenti attiva

Supporto per l'app xamarin aggiungendo suggerimento attiva è in genere semplice come implementazione di alcune API o l'espansione in alcune API che l'app potrebbe già essere implementato.

Suggerimenti proattivi di lavoro con le applicazioni in tre modi principali:

- **`NSUserActivity`** -Consente al sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo.
- **Percorso suggerimenti** : se l'app offre o utilizza informazioni di percorso di base, queste nuove procedure offerta estensione API per condividere le informazioni tra applicazioni.

Ed è supportato nell'app implementando le operazioni seguenti:

- **Scelta promemoria Siri** - 10, iOS `NSUserActivity` è stato espanso per consentire a Siri di creare rapidamente un promemoria per visualizzare il contenuto attualmente visualizzate nell'app in un secondo momento.
- **Percorso suggerimenti** -iOS 10 migliora `NSUserActivity` per acquisire i percorsi visualizzati all'interno dell'app e alzare di livello in numerose posizioni in tutto il sistema.
- **Le richieste di Siri contestuali**  -  `NSUserActivity` fornisce contesto per le informazioni presentate all'interno dell'app a Siri in modo che l'utente può ottenere le direzioni o sul posto da una chiamata Siri chiamata dall'interno dell'app.

Tutte queste funzionalità hanno una cosa in comune, tutte utilizzano `NSUserActivity` in un form o un altro per fornire le proprie funzionalità. 

## <a name="nsuseractivity"></a>NSUserActivity

Come descritto in precedenza, `NSUserActivity` consente il sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. `NSUserActivity` è stato un leggero la memorizzazione nella cache meccanismo per acquisire l'attività dell'utente così come si spostano l'app. Ad esempio, esaminare l'app ristorante:

[![](proactive-suggestions-images/activity02.png "L'app ristorante")](proactive-suggestions-images/activity02.png#lightbox)

Con le interazioni seguente:

1. L'utente lavora con l'app, un `NSUserActivity` viene creato per ricreare lo stato dell'app in un secondo momento.
2. Se l'utente effettua la ricerca di un ristorante, viene seguito lo stesso modello di creazione di attività.
3. E anche quando l'utente visualizza un risultato. In quest'ultimo caso, la visualizzazione di un percorso e in iOS 10, il sistema è più conoscere alcuni concetti (ad esempio, le interazioni di posizione o le comunicazioni).

Esaminare più vicino all'ultima pagina:

[![](proactive-suggestions-images/activity03.png "Il payload NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

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

Verificare che questo sia lo stesso identificatore di tipo di attività (`com.xamarin.platform`) come attività creato in precedenza. L'applicazione utilizza le informazioni archiviate nel `NSUserActivity` per ripristinare lo stato al punto in cui l'utente è stata interrotta.

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
4. Se l'utente passa all'app di mappe, indirizzo del ristorante viene suggerito automaticamente come una destinazione.
5. Questo procedimento funziona anche per le applicazioni di terze parti 3rd (che supportano `NSUserActivity`), in modo che l'utente può passare a un'applicazione di condivisione marcia e l'indirizzo del ristorante viene suggerito automaticamente come destinazione di anche.
6. Fornisce inoltre contesto a Siri, in modo che l'utente può richiamare Siri all'interno dell'app ristorante e chiedere *"Stradali..."* e Siri fornirà indicazioni per il ristorante Visualizza l'utente.

Tutte le funzionalità descritte sopra è una cosa in comune, tutti indicano il suggerimento originariamente provenienza. Nel caso dell'esempio precedente, è l'app di revisione del ristorante fittizio.

watchOS 3 è stata migliorata per abilitare questa funzionalità per un'applicazione tramite più piccole modifiche e aggiunte al Framework esistenti:

- `NSUserActivity` include campi aggiuntivi per l'acquisizione di informazioni sul percorso che viene visualizzati all'interno dell'app.
- Diverse aggiunte sono state apportate MapKit e CoreSpotlight per acquisire percorso.
- Percorso compatibile con funzionalità è stata aggiunta a Siri, mappe, il Multitasking e altre App all'interno del sistema.

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

Quindi, il testo di descrizione in base necessari per il testo basato su istanze (ad esempio, la tastiera QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitudine e longitudine sono facoltativi, ma assicurarsi che l'utente viene indirizzato alla posizione esatta a che dell'app è desiderano inviare:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Impostando i numeri di telefono, l'app può accedere a Siri pertanto l'utente può richiamare Siri dall'app pronunciando simile al seguente, * "Chiama la risorsa":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Infine, l'app può indicare se l'istanza è adatto per la navigazione e chiamate telefoniche:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>Le attività consigliate

Quando si lavora con le attività, Apple suggerisce le procedure consigliate seguenti:

- Utilizzare `NeedsSave` per gli aggiornamenti di payload lazy.
- Assicurarsi di mantenere un riferimento forte all'attività corrente.
- Solo il trasferimento payload di piccole dimensioni che includono informazioni sufficienti per ripristinare lo stato.
- Verificare che gli identificatori di tipo attività siano descrittivo e univoco utilizzando la notazione DNS inversa specificarli. 

## <a name="consuming-location-suggestions"></a>Utilizzo di suggerimenti di percorso

In questa sezione verrà illustrate suggerimento percorso provenienti da altre parti del sistema (ad esempio l'app esegue il mapping) o altre applicazioni di terze parti 3rd di utilizzo.

## <a name="routing-apps-and-locations-suggestions"></a>Routing App e i suggerimenti di percorsi

In questa sezione verrà esaminato un utilizzo suggerimenti percorso direttamente all'interno di un'applicazione di routing. Per l'applicazione di routing aggiungere questa funzionalità, lo sviluppatore sfruttano esistente `MKDirectionsRequest` framework come indicato di seguito:

- Per alzare di livello l'app in Multitasking.
- Per registrare l'app come un'applicazione di routing.
- Per gestire l'avvio dell'app con un MapKit `MKDirectionsRequest` oggetto.
- Consentire a watchOS per suggerire l'app in base il coinvolgimento degli utenti.

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

Nuovo in watchOS 3, l'app è possibile inviare un indirizzo che non dispone di coordinate geografico, in che provocano che lo sviluppatore deve codificare l'indirizzo:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>Riepilogo

In questo articolo è coperto suggerimenti attiva e la visualizzazione come lo sviluppatore può utilizzarli per il traffico di unità a un'app xamarin per watchOS. Incluso il passaggio per implementare i suggerimenti proattiva e presentate le linee guida.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Guida per programmatori SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
