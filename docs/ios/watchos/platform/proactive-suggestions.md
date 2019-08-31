---
title: Suggerimenti proattivi di watchos in Novell
description: Questo articolo illustra come usare i suggerimenti proattivi in un'app watchos 3 per guidare l'engagement consentendo al sistema di presentare in modo proattivo le informazioni utili automaticamente all'utente.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 95e0eb77719a9bcc642dfb1385d7371652943155
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198813"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>Suggerimenti proattivi di watchos in Novell

_Questo articolo illustra come usare i suggerimenti proattivi in un'app watchos 3 per guidare l'engagement consentendo al sistema di presentare in modo proattivo le informazioni utili automaticamente all'utente._


Una novità di watchos 3, i suggerimenti proattivi presentano nuove modalità per consentire agli utenti di interagire con un'app Novell. iOS inviando in modo proattivo informazioni utili automaticamente all'utente in momenti appropriati.


## <a name="about-proactive-suggestions"></a>Informazioni sui suggerimenti proattivi

Una novità di watchos 3 `NSUserActivity` include una `MapItem` proprietà che consente all'app di fornire informazioni sulla posizione che possono essere usate in altri contesti. Se, ad esempio, l'app Visualizza recensioni e fornisce un `MapItem` percorso, se l'utente passa all'app Maps, la posizione dell'hotel che era sufficiente visualizzare sarebbe disponibile.

L'app espone questa funzionalità al sistema usando una raccolta di tecnologie come `NSUserActivity`, MapKit, Media Player e UIKit. Inoltre, grazie al supporto proattivo dei suggerimenti per l'app, l'integrazione di Siri è più approfondita gratuitamente.

## <a name="location-based-suggestions"></a>Suggerimenti sulla posizione

Una novità di watchos 3, `NSUserActivity` la classe include `MapItem` una proprietà che consente allo sviluppatore di fornire informazioni sulla posizione che possono essere usate in altri contesti. Ad esempio, se l'app Visualizza le recensioni dei ristoranti, lo sviluppatore può `MapItem` impostare la proprietà sulla posizione del ristorante che l'utente sta visualizzando nell'app. Se l'utente passa all'app Maps, la località del ristorante è automaticamente disponibile.

Se l'app supporta la ricerca nell'app, può usare i nuovi componenti degli indirizzi `CSSearchableItemAttributesSet` della classe per specificare le posizioni che l'utente può desiderare di visitare. Impostando la `MapItem` proprietà, le altre proprietà vengono compilate automaticamente.

Oltre a impostare `Latitude` e `Longitude` delle proprietà del componente Address, è consigliabile che l'app fornisca anche le `NamedLocation` proprietà e `PhoneNumbers` , quindi Siri può avviare una chiamata al percorso.

## <a name="contextual-siri-reminders"></a>Promemoria Siri contestuali

Consente all'utente di usare Siri per creare rapidamente un promemoria per visualizzare il contenuto attualmente visualizzato nell'app in un secondo momento. Se, ad esempio, veniva visualizzata una recensione del ristorante nell'app, era possibile richiamare Siri e pronunciare l' *avviso quando si arriva a casa.* Siri genera il promemoria con un collegamento alla revisione nell'app.

## <a name="implementing-proactive-suggestions"></a>Implementazione di suggerimenti proattivi

L'aggiunta del supporto proattivo per i suggerimenti all'app Novell. iOS è in genere semplice quanto l'implementazione di alcune API o l'espansione in alcune API che l'app potrebbe già implementare.

I suggerimenti proattivi funzionano con le app in tre modi principali:

- **`NSUserActivity`** -Consente al sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo.
- **Suggerimenti sulla posizione** : se l'app offre o utilizza informazioni basate sulla posizione, queste estensioni API offrono nuovi modi per condividere queste informazioni tra le app.

E sono supportati nell'app implementando gli elementi seguenti:

- Promemoria **Siri contestuali** : in iOS 10 `NSUserActivity` è stato ampliato per consentire a Siri di creare rapidamente un promemoria per visualizzare il contenuto attualmente visualizzato nell'app in un secondo momento.
- **Suggerimenti** `NSUserActivity` per la posizione: iOS 10 migliora l'acquisizione delle posizioni visualizzate all'interno dell'app e le promuove in molte posizioni in tutto il sistema.
- **Le richieste**  -  Siricontestualifornisconoilcontestoalleinformazionipresentateall'internodell'appaSiri,inmodochel'utentepossaottenereindicazionioinserireunachiamataperrichiamareSiridall'internodell'app.`NSUserActivity`

Tutte queste funzionalità hanno un'unica cosa in comune, usano `NSUserActivity` tutte in un form o in un'altra per fornire la loro funzionalità. 

## <a name="nsuseractivity"></a>NSUserActivity

Come indicato in precedenza `NSUserActivity` , consente al sistema di comprendere le informazioni che l'utente sta attualmente utilizzando sullo schermo. `NSUserActivity`è un meccanismo di memorizzazione nella cache dello stato leggero per acquisire l'attività dell'utente durante la navigazione nell'app. Ad esempio, guardando l'app del ristorante:

[![](proactive-suggestions-images/activity02.png "App del ristorante")](proactive-suggestions-images/activity02.png#lightbox)

Con le interazioni seguenti:

1. Quando l'utente lavora con l'app, viene `NSUserActivity` creato un oggetto per ricreare lo stato dell'app in un secondo momento.
2. Se l'utente cerca un ristorante, viene seguito lo stesso modello di creazione delle attività.
3. Anche in questo caso, quando l'utente visualizza un risultato. In quest'ultimo caso, l'utente sta visualizzando una località e in iOS 10, il sistema è più consapevole di alcuni concetti, ad esempio la posizione o le interazioni di comunicazione.

Esaminare più da vicino l'ultima schermata:

[![](proactive-suggestions-images/activity03.png "Payload NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Qui l'app crea un oggetto `NSUserActivity` ed è stato popolato con le informazioni per ricreare lo stato in un secondo momento. L'app include anche alcuni metadati, ad esempio il nome e l'indirizzo della località. Quando questa attività è stata creata, l'app informa iOS che rappresenta lo stato corrente dell'utente.

L'app decide quindi se l'attività verrà annunciata in modo condizionale per la consegna, salvata come valore temporaneo per i suggerimenti sulla posizione o aggiunta all'indice Spotlight su dispositivo per la visualizzazione nei risultati della ricerca.

Per altre informazioni sulla consegna e la ricerca in evidenza, vedere le [guide introduttive](~/ios/platform/handoff.md) e le [nuove API di ricerca per iOS 9](~/ios/platform/search/index.md).

### <a name="creating-an-activity"></a>Creazione di un'attività

Prima di creare un'attività, sarà necessario creare un identificatore del tipo di attività per identificarlo. L'identificatore del tipo di attività è una stringa breve aggiunta `NSUserActivityTypes` alla matrice del `Info.plist` file dell'app usata per identificare in modo univoco un determinato tipo di attività utente. Nella matrice sarà presente una voce per ogni attività supportata dall'app ed esposta alla ricerca di app. Per ulteriori informazioni, vedere il riferimento per la creazione di identificatori del [tipo di attività](~/ios/platform/search/nsuseractivity.md) .

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

Verificare che si tratta dello stesso identificatore del tipo`com.xamarin.platform`di attività () dell'attività creata in precedenza. L'app usa le informazioni archiviate in `NSUserActivity` per ripristinare lo stato in cui l'utente ha interrotto l'esecuzione.

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
2. Se l'utente passa all'app Maps, l'indirizzo del ristorante viene automaticamente suggerito come destinazione.
3. Questo funziona anche per le app di terze parti `NSUserActivity`(che supportano), in modo che l'utente possa passare a un'app di condivisione di corsa e l'indirizzo del ristorante viene automaticamente suggerito come destinazione.
4. Fornisce anche il contesto a Siri, in modo che l'utente possa richiamare Siri nell'app del ristorante e chiedere *"Get directions..."* e Siri fornirà le istruzioni per il ristorante visualizzato dall'utente.

Tutte le funzionalità sopra elencate hanno una cosa in comune, ognuna delle quali indica la provenienza iniziale del suggerimento. Nel caso dell'esempio precedente, si tratta dell'app di revisione del ristorante fittizio.

watchos 3 è stato migliorato in modo da abilitare questa funzionalità per un'app attraverso diverse modifiche e aggiunte a Framework esistenti:

- `NSUserActivity`include campi aggiuntivi per l'acquisizione delle informazioni sul percorso visualizzate all'interno dell'app.
- Sono state apportate diverse aggiunte a MapKit e CoreSpotlight per acquisire il percorso.
- La funzionalità di rilevamento della posizione è stata aggiunta a Siri, Maps, multitasking e altre app all'interno del sistema.

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

Quindi, la descrizione basata su testo in richiesta per le istanze basate su testo (ad esempio la tastiera QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitudine e la longitudine sono facoltative, ma assicurano che l'utente venga indirizzato al percorso esatto a cui l'app vuole inviarli:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Impostando i numeri di telefono, l'app può accedere a Siri, in modo che l'utente possa richiamare Siri dall'app, ad esempio, * "Call This Place":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Infine, l'app può indicare se l'istanza è adatta per la navigazione e le chiamate telefoniche:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

## <a name="activities-best-practices"></a>Procedure consigliate per le attività

Apple suggerisce le seguenti procedure consigliate per l'uso delle attività:

- Usare `NeedsSave` per gli aggiornamenti del payload Lazy.
- Assicurarsi di mantenere un riferimento sicuro all'attività corrente.
- Trasferire solo payload piccoli che includono informazioni sufficienti per ripristinare lo stato.
- Verificare che gli identificatori del tipo di attività siano univoci e descrittivi utilizzando la notazione DNS inverso per specificarli. 

## <a name="consuming-location-suggestions"></a>Utilizzo di suggerimenti sulla posizione

Questa sezione seguente coprirà il suggerimento relativo all'utilizzo del percorso che provenga da altre parti del sistema, ad esempio l'app Maps, o altre app di terze parti.

## <a name="routing-apps-and-locations-suggestions"></a>Suggerimenti per il routing di app e percorsi

In questa sezione verranno esaminati i suggerimenti per la posizione di utilizzo direttamente dall'interno di un'app di routing. Per aggiungere questa funzionalità all'app di routing, lo sviluppatore utilizzerà il Framework esistente `MKDirectionsRequest` come indicato di seguito:

- Per innalzare di livello l'app in multitasking.
- Per registrare l'app come app di routing.
- Per gestire l'avvio dell'app con un `MKDirectionsRequest` oggetto MapKit.
- Dare al watchos la possibilità di apprendere come suggerire l'app in base al coinvolgimento degli utenti.

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

Novità di watchos 3, l'app può inviare un indirizzo che non ha coordinate geografiche, in quanto lo sviluppatore deve codificare l'indirizzo:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address

});

```

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato suggerimenti proattivi e ha illustrato come lo sviluppatore può usarle per indirizzare il traffico a un'app Novell. iOS per watchos. È stato illustrato il passaggio per implementare i suggerimenti proattivi e le linee guida per l'utilizzo.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Guida alla programmazione di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
