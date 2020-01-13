---
title: Eseguire una ricerca con NSUserActivity in Xamarin.iOS
description: Questo documento descrive come indicizzare un NSUserActivity, rendendolo ricercabile in Spotlight e Safari. Viene illustrato come rispondere alla selezione di un NSUserActivity nei risultati della ricerca.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: c6ceb6e10abc4dbd26bffecbb6fefa5835f3d630
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031549"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Eseguire una ricerca con NSUserActivity in Xamarin.iOS

`NSUserActivity` è stato introdotto in iOS 8 e viene usato per fornire i dati per la consegna.
Consente di creare attività in parti specifiche dell'app che possono quindi essere passate a un'altra istanza dell'app in esecuzione in un dispositivo iOS diverso. Il dispositivo ricevente può quindi continuare l'attività avviata sul dispositivo precedente, selezionando la posizione in cui l'utente ha interrotto l'esecuzione. Per altre informazioni sull'uso della consegna, vedere l' [Introduzione alla](~/ios/platform/handoff.md) documentazione.

Una novità di iOS 9, `NSUserActivity` può essere indicizzata (sia pubblica che privata) ed è stata eseguita una ricerca in Spotlight Search e Safari. Contrassegnando un `NSUserActivity` come ricercabile e aggiungendo metadati indicizzabili, l'attività può essere elencata nei risultati della ricerca nel dispositivo iOS.

[![](nsuseractivity-images/apphistory01.png "The App History overview")](nsuseractivity-images/apphistory01.png#lightbox)

Se l'utente seleziona un risultato di ricerca che appartiene a un'attività dall'app, l'app verrà avviata e l'attività descritta dal `NSUserActivity` verrà riavviata e presentata all'utente.

Per supportare la ricerca di app vengono usate le seguenti proprietà di `NSUserActivity`:

- `EligibleForHandoff`: se `true`, questa attività può essere utilizzata in un'operazione di consegna.
- `EligibleForSearch`: se `true`, questa attività verrà aggiunta all'indice sul dispositivo e visualizzato nei risultati della ricerca.
- `EligibleForPublicIndexing`: se `true`, questa attività verrà aggiunta all'indice basato sul cloud di Apple e presentata agli utenti (tramite ricerca) che non hanno ancora installato l'app nel dispositivo iOS. Per ulteriori informazioni, vedere la sezione [indicizzazione della ricerca pubblica](#public-search-indexing) riportata di seguito.
- `Title`: fornisce un titolo per l'attività e viene visualizzato nei risultati della ricerca. Gli utenti possono anche cercare il testo del titolo stesso.
- `Keywords`: è una matrice di stringhe utilizzata per descrivere l'attività che verrà indicizzata e resa ricercabile dall'utente finale.
- `ContentAttributeSet`: è un `CSSearchableItemAttributeSet` usato per descrivere ulteriormente l'attività in dettaglio e fornire contenuti avanzati nei risultati della ricerca.
- `ExpirationDate`: se si vuole che un'attività venga visualizzata solo fino a una data specifica, è possibile fornire tale data qui.
- `WebpageURL`: se l'attività può essere visualizzata sul Web o se l'app supporta i collegamenti profondi di Safari, è possibile impostare il collegamento per visitare qui.

## <a name="nsuseractivity-quickstart"></a>Guida introduttiva a NSUserActivity

Seguire queste istruzioni per implementare un `NSUserActivity` ricercabile nell'app:

- [Creazione di identificatori del tipo di attività](#creatingtypeid)
- [Creazione di un'attività](#createactivity)
- [Risposta a un'attività](#respondactivity)
- [Indicizzazione di ricerca pubblica](#indexing)

Ci sono alcuni [vantaggi aggiuntivi](#benefits) per l'uso di `NSUserActivity` per rendere il contenuto disponibile per la ricerca.

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>Creazione di identificatori del tipo di attività

Prima di poter creare un'attività di ricerca, è necessario creare un _identificatore del tipo di attività_ per identificarlo. L'identificatore del tipo di attività è una stringa breve aggiunta alla matrice `NSUserActivityTypes` del file **info. plist** dell'app usato per identificare in modo univoco un tipo di attività utente specificato. Nella matrice sarà presente una voce per ogni attività supportata dall'app ed esposta alla ricerca di app. 

Apple suggerisce di usare una notazione in stile DNS inverso per l'identificatore del tipo di attività per evitare conflitti. Ad esempio: `com.company-name.appname.activity` per attività specifiche basate su app o `com.company-name.activity` per le attività che possono essere eseguite su più app.

L'identificatore del tipo di attività viene utilizzato quando si crea un'istanza di `NSUserActivity` per identificare il tipo di attività. Quando un'attività viene continuata in seguito al tocco di un risultato di ricerca da parte dell'utente, il tipo di attività (insieme all'ID del team dell'app) determina quale app avviare per continuare l'attività.

Per creare gli identificatori del tipo di attività necessari per supportare questo comportamento, modificare il file **info. plist** e passare alla visualizzazione di **origine** . Aggiungere una chiave di `NSUserActivityTypes` e creare gli identificatori nel formato seguente:

[![](nsuseractivity-images/type01.png "The NSUserActivityTypes key and required identifiers in the plist editor")](nsuseractivity-images/type01.png#lightbox)

Nell'esempio precedente è stato creato un nuovo identificatore del tipo di attività per l'attività di ricerca (`com.xamarin.platform`). Quando si creano app personalizzate, sostituire il contenuto della matrice di `NSUserActivityTypes` con gli identificatori del tipo di attività specifici per le attività supportate dall'app.

<a name="createactivity" />

## <a name="creating-an-activity"></a>Creazione di un'attività

Di seguito è riportato un esempio di creazione di un'attività per una ricerca ospitata in un indice sul dispositivo:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

È possibile aggiungere ulteriori dettagli impostando la proprietà `ContentAttributeSet` del `NSUserActivity` come indicato di seguito:

[![](nsuseractivity-images/apphistory02.png "Addition Search Details overview")](nsuseractivity-images/apphistory02.png#lightbox)

Utilizzando un `ContentAttributeSet` è possibile creare risultati di ricerca avanzati che consentono all'utente finale di interagire con essi.

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>Risposta a un'attività

Per rispondere all'utente toccando un risultato di ricerca (`NSUserActivity`) per l'app, modificare il file **AppDelegate.cs** ed eseguire l'override del metodo `ContinueUserActivity`. Esempio:

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

Si noti che si tratta dello stesso override del metodo utilizzato per rispondere alle richieste di continuità. Ora, se l'utente fa clic su un collegamento dall'app nei risultati della ricerca in evidenza, l'app verrà portata in primo piano (o avviata se non è già in esecuzione) e verrà visualizzato il contenuto, la navigazione o la funzionalità rappresentata dal collegamento seguente:

[![](nsuseractivity-images/apphistory03.png "Restore Previous State from Search")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>Indicizzazione di ricerca pubblica

Come illustrato in precedenza, iOS 9 semplifica l'accesso di ricerca al contenuto e alle funzionalità dell'app che l'utente ha già individuato e usato in un determinato dispositivo iOS. Con l'indicizzazione pubblica, iOS 9 fornisce un modo per gli utenti che non hanno ancora individuato contenuti o funzionalità (o che non hanno installato l'app) per ottenere tali risultati nelle ricerche.

L'indicizzazione pubblica funziona nel modo seguente:

1. Quando si crea un'attività per l'app, è possibile contrassegnarla come pubblica.
2. Le attività pubbliche vengono inviate ad Apple e indicizzate nel cloud.
3. Poiché più utenti interagiscono con l'app in un dispositivo e usano la funzionalità o il contenuto specifico rappresentato dall'attività, aumenta in rango.
4. I risultati pubblici più diffusi saranno disponibili per altri utenti, anche se non hanno l'app installata.
5. Questi risultati pubblici vengono visualizzati nella ricerca Spotlight e in Safari (se l'attività include un URL).

È possibile eseguire l'attività di ricerca privata creata in precedenza ed espanderla per renderla pubblica:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

Solo perché un'attività è stata impostata per l'indicizzazione pubblica impostando `EligibleForPublicIndexing = true`, non significa che verrà aggiunta automaticamente all'indice cloud pubblico di Apple. Per prima cosa è necessario soddisfare le condizioni seguenti:

1. Deve apparire nei risultati della ricerca e essere selezionato da molti utenti. I risultati rimangono privati fino a quando non viene soddisfatta una soglia di Engagement dell'attività.
2. Il provisioning delle app impedisce che i dati specifici dell'utente vengano indicizzati e resi pubblici.

<a name="benefits" />

## <a name="additional-benefits"></a>Vantaggi aggiuntivi

Adottando la ricerca di app tramite `NSUserActivity` nell'app, si ottengono anche le funzionalità seguenti:

- Consegna **: poiché** la ricerca di app sta esponendo contenuto, navigazione e/o funzionalità con lo stesso meccanismo di consegna (`NSUserActivity`), è possibile consentire agli utenti dell'app di avviare un'attività in un dispositivo e continuare a usarla in un altro dispositivo.
- **Siri suggerimenti** : insieme ai suggerimenti standard che Siri suggerisce di solito, gli attivi dall'app possono essere suggeriti automaticamente.
- **Siri Smart promemoria** : gli utenti potranno chiedere a Siri di ricordare le attività dell'app.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di app Search](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Post di Blog & esempio](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
