---
title: Ricerca con NSUserActivity in xamarin. IOS
description: Questo documento descrive come indicizzare un NSUserActivity, rendendo disponibili per la ricerca di Spotlight e Safari. Illustra come rispondere alla selezione di un NSUserActivity nei risultati della ricerca.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: a3a8872ae54d7b1efb55ee71286ca5ea479616e0
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830159"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Ricerca con NSUserActivity in xamarin. IOS

`NSUserActivity` è stato introdotto in iOS 8 e viene utilizzato per fornire i dati per la distribuzione.
Consente di creare le attività in parti specifiche dell'app che può quindi essere passato in un'altra istanza dell'app in esecuzione in un dispositivo iOS diverso. Il dispositivo di ricezione è quindi possibile continuare l'attività avviato sul dispositivo precedente, il prelievo in alto a destra in cui l'utente era stata interrotta. Per altre informazioni sull'uso di consegna, vedere la [Introduzione a Handoff](~/ios/platform/handoff.md) documentazione.

Novità di iOS 9, `NSUserActivity` possono essere indicizzate (sia pubblico e privato) e ricerca dalla ricerca Spotlight e Safari. Contrassegnando una `NSUserActivity` come metadati indicizzabili disponibili per la ricerca e aggiunta, l'attività può essere elencato nei risultati della ricerca nel dispositivo iOS.

[![](nsuseractivity-images/apphistory01.png "Panoramica della cronologia di App")](nsuseractivity-images/apphistory01.png#lightbox)

Se l'utente seleziona un risultato di ricerca che appartiene a un'attività dall'app, verrà avviata l'app e l'attività descritta dal `NSUserActivity` verrà riavviato e verrà visualizzata all'utente.

Le proprietà seguenti di `NSUserActivity` vengono utilizzati per supportare la ricerca di App:

- `EligibleForHandoff` -Se `true`, questa attività può essere utilizzata in un'operazione di passaggio di consegne.
- `EligibleForSearch` -Se `true`, questa attività verrà aggiunta all'indice sul dispositivo e verrà visualizzata nei risultati della ricerca.
- `EligibleForPublicIndexing` -Se `true`, questa attività verrà aggiunta all'indice basato sul cloud di Apple e verrà visualizzata agli utenti (tramite la ricerca) che non è installato l'app nel proprio dispositivo iOS. Vedere la [indicizzazione della ricerca pubblico](#public-search-indexing) sezione di seguito per altri dettagli.
- `Title` : Fornisce un titolo per l'attività e viene visualizzato nei risultati della ricerca. Gli utenti possono inoltre cercare il testo del titolo della stessa.
- `Keywords` – È una matrice di stringhe utilizzato per descrivere l'attività che verrà indicizzato e rese disponibili per la ricerca dall'utente finale.
- `ContentAttributeSet` – È un `CSSearchableItemAttributeSet` usato per un'ulteriore descrivono in dettaglio le attività e fornire contenuto avanzato nei risultati della ricerca.
- `ExpirationDate` : Se si desidera un'attività da visualizzare solo per una determinata data, è possibile fornire qui tale data.
- `WebpageURL` -Se l'attività può essere visualizzato nel web o se l'app supporta collegamenti diretti di Safari, è possibile impostare il collegamento a visitare questa pagina.

## <a name="nsuseractivity-quickstart"></a>NSUserActivity Quickstart

Seguire queste istruzioni per implementare un ricercabile `NSUserActivity` nell'app:

- [Creazione di identificatori di tipo di attività](#creatingtypeid)
- [Creazione di un'attività](#createactivity)
- [Risponde a un'attività](#respondactivity)
- [L'indicizzazione della ricerca pubblico](#indexing)

Esistono tuttavia alcuni [vantaggi aggiuntivi](#benefits) all'uso `NSUserActivity` per rendere il contenuto ricercabile.

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>Creazione di identificatori di tipo di attività

Prima di poter creare un'attività di ricerca, è necessario creare un _identificatore di tipo attività_ per facilitarne l'identificazione. L'identificatore del tipo di attività è una stringa breve aggiunta per il `NSUserActivityTypes` matrice dell'app **Info. plist** file utilizzato per identificare in modo univoco un determinato tipo di attività utente. Vi sarà una voce nella matrice per ogni attività che l'app supporta ed espone a ricerca di App. 

Apple consiglia di usare una notazione di stile di DNS inverso per l'identificatore del tipo di attività per evitare conflitti. Ad esempio: `com.company-name.appname.activity` per app specifiche attività basate sugli o `com.company-name.activity` per le attività che possono essere eseguiti in più app.

L'identificatore di tipo attività viene utilizzato durante la creazione di un `NSUserActivity` istanza per identificare il tipo di attività. Quando un'attività è continuata come risultato dell'utente se si tocca un risultato della ricerca, il tipo di attività (con l'ID dell'app Team) determina quali app devono essere avviati per continuare l'attività.

Per creare gli identificatori di tipo attività necessarie per supportare questo comportamento, modificare il **Info. plist** del file e passare al **origine** visualizzazione. Aggiungere un `NSUserActivityTypes` della chiave e creare gli identificatori nel formato seguente:

[![](nsuseractivity-images/type01.png "La chiave di NSUserActivityTypes e gli identificatori necessari nell'editor plist")](nsuseractivity-images/type01.png#lightbox)

Nell'esempio precedente, è stato creato un nuovo identificatore di tipo di attività per l'attività di ricerca (`com.xamarin.platform`). Durante la creazione di App personalizzate, sostituire il contenuto del `NSUserActivityTypes` matrice con gli identificatori di tipo attività specifiche per le attività, l'app supporta.

<a name="createactivity" />

## <a name="creating-an-activity"></a>Creazione di un'attività

Di seguito è riportato un esempio di creazione di un'attività per una ricerca nell'indice sul dispositivo ospitato:

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

È stato possibile aggiungere ulteriori dettagli, impostando il `ContentAttributeSet` proprietà del nostro `NSUserActivity` come indicato di seguito:

[![](nsuseractivity-images/apphistory02.png "Cenni preliminari sulla ricerca dettagli di aggiunta")](nsuseractivity-images/apphistory02.png#lightbox)

Usando un `ContentAttributeSet` è possibile creare i risultati di ricerca avanzata che inducono l'utente finale per interagire con esse.

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>Risponde a un'attività

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

Si noti che questo sia l'override del metodo stesso utilizzato per rispondere alle richieste di consegna. Ora se l'utente fa clic su un collegamento dalla nostra app nei risultati della ricerca di Spotlight, l'app verrà portata in primo piano (o avviata se non è già in esecuzione) e verrà visualizzato il contenuto, spostamento o la funzionalità rappresentata da questo collegamento:

[![](nsuseractivity-images/apphistory03.png "Ripristinare uno stato precedente dalla ricerca")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>L'indicizzazione della ricerca pubblico

Come descritto in precedenza, iOS 9 semplifica fornire l'accesso a ricerca di contenuto dell'app e funzionalità che l'utente ha già individuato e utilizzato in un dispositivo iOS specifico. Con l'indicizzazione pubblico, iOS 9 fornisce un modo per gli utenti che non hanno individuato ancora contenuto o le funzionalità (o che non hanno ancora installato l'app) per ottenere questi risultati nelle loro ricerche troppo.

Indicizzazione pubblico funziona nel modo seguente:

1. Quando si crea un'attività per l'app è possibile contrassegnarlo come pubblico.
2. Attività pubbliche vengono inviate ad Apple e indicizzate nel cloud.
3. Quando più utenti interagiscono con l'app in un dispositivo e utilizzano contenuto rappresentato dall'attività o le funzionalità specifiche, aumenta in classifica.
4. Risultati pubblici più diffusi sarà disponibili ad altri utenti, anche se non hanno installato l'app.
5. Questi risultati pubblici verranno visualizzata nella ricerca Spotlight e Safari (se l'attività include un URL).

È possibile eseguire l'attività di ricerca privato creati in precedenza ed espandere in modo che sia pubblico:

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

Solo perché un'attività è stata impostata per l'indicizzazione pubblica impostando `EligibleForPublicIndexing = true`, questo non significa che verrà automaticamente aggiunto all'indice di cloud pubblico di Apple. Le condizioni seguenti devono essere soddisfatti prima di tutto:

1. Devono essere visualizzati nei risultati della ricerca selezionato e da molti utenti. I risultati rimangono privati fino a quando non è stato raggiunto una soglia di engagement attività.
2. Provisioning delle App impedisce a tutti i dati specifici dell'utente vengano indicizzati e resi pubblici.

<a name="benefits" />

## <a name="additional-benefits"></a>Vantaggi aggiuntivi

Grazie a ricerca di App tramite `NSUserActivity` nell'app, otterrai anche le funzionalità seguenti:

- **Esegui handoff** : poiché ricerca di App viene esposto il contenuto, navigazione e/o le funzionalità usando lo stesso meccanismo come passaggio di consegne (`NSUserActivity`), è facilmente possibile consentire agli utenti dell'app di avviare un'attività in un dispositivo e continuarli un altro.
- **I suggerimenti di Siri** -insieme i suggerimenti standard che i suggerimenti di Siri rende in genere, actives dall'app possono essere suggerite automaticamente.
- **Promemoria Smart Siri** -gli utenti saranno in grado di chiedere di Siri per ricordare ai componenti di informazioni sulle attività dell'app.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di ricerca nelle App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Esempio di & post di Blog](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
