---
title: Ricerca con NSUserActivity in xamarin. IOS
description: Questo documento viene descritto come indice un NSUserActivity, rende possibile eseguire ricerche in Spotlight e Safari. Illustra come rispondere alla selezione di un NSUserActivity nei risultati della ricerca.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 4b053f66e9b6b7715cbe52c4e43d9db32db48f4c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788208"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Ricerca con NSUserActivity in xamarin. IOS

`NSUserActivity` è stata introdotta in iOS 8 e viene utilizzata per fornire i dati per la consegna.
Consente di creare le attività in parti specifiche dell'applicazione che può essere passato in un'altra istanza dell'app in esecuzione in un dispositivo iOS diversi. Il dispositivo di ricezione è quindi possibile continuare l'attività avviato sul dispositivo precedente, il prelievo in alto a destra in cui l'utente è stata interrotta. Per ulteriori informazioni sull'utilizzo di continuità, consultare il nostro [Introduzione a Handoff](~/ios/platform/handoff.md) documentazione.

Nuovi utenti di iOS 9, `NSUserActivity` possono essere indicizzate (sia pubblico e privato) e la ricerca alla ricerca Spotlight e Safari. Contrassegna un `NSUserActivity` come metadati indicizzabili disponibile per la ricerca e l'aggiunta, l'attività può essere elencato nei risultati della ricerca nel dispositivo iOS.

[![](nsuseractivity-images/apphistory01.png "Panoramica della cronologia di App")](nsuseractivity-images/apphistory01.png#lightbox)

Se l'utente seleziona un risultato di ricerca che appartiene a un'attività dall'app, verrà avviata l'app e l'attività descritta dal `NSUserActivity` verrà riavviato e presentate all'utente.

Le proprietà seguenti di `NSUserActivity` vengono utilizzati per supportare la ricerca di App:

 - `EligibleForHandoff` -Se `true`, questa attività può essere utilizzata in un'operazione di passaggio di consegne.
 - `EligibleForSearch` -Se `true`, questa attività verrà aggiunta all'indice sul dispositivo e presentata nei risultati della ricerca.
 - `EligibleForPublicIndexing` -Se `true`, questa attività verrà aggiunti all'indice basato sul cloud di Apple e presentata agli utenti (tramite ricerca) che non è installato l'app sul proprio dispositivo iOS. Vedere il [l'indicizzazione di ricerca pubblica](#Public-Search-Indexing) sezione riportata di seguito per ulteriori dettagli.
 - `Title` : Fornisce un titolo per l'attività e viene visualizzato nei risultati della ricerca. Gli utenti possono inoltre cercare il testo del titolo della stessa.
 - `Keywords` : È una matrice di stringhe utilizzate per descrivere l'attività che verrà indicizzato e reso disponibile per la ricerca dall'utente finale.
 - `ContentAttributeSet` : È un `CSSearchableItemAttributeSet` utilizzato per descrivere in dettaglio le attività e fornire contenuto RTF nei risultati della ricerca ulteriormente.
 - `ExpirationDate` : Se si desidera un'attività da visualizzare solo per una determinata data, è possibile fornire tale data.
 - `WebpageURL` : Se l'attività può essere visualizzata sul web o se l'app supporta collegamenti diretti del Safari, è possibile impostare il collegamento per visitare questa pagina.

## <a name="nsuseractivity-quickstart"></a>Guida introduttiva NSUserActivity

Seguire queste istruzioni per implementare una ricerca `NSUserActivity` nell'app:

- [Creazione di identificatori di tipo attività](#creatingtypeid)
- [Creazione di un'attività](#createactivity)
- [Risponde a un'attività](#respondactivity)
- [L'indicizzazione di ricerca pubblici](#indexing)

Esistono tuttavia alcuni [vantaggi aggiuntivi](#benefits) all'utilizzo `NSUserActivity` eseguire la ricerca del contenuto.

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>Creazione di identificatori di tipo attività

Prima di poter creare un'attività di ricerca, è necessario creare un _identificatore del tipo di attività_ per facilitarne l'identificazione. L'identificatore del tipo di attività è una stringa breve aggiunta il `NSUserActivityTypes` matrice dell'app **Info. plist** file utilizzato per identificare in modo univoco un determinato tipo di attività utente. Sarà presente una voce nella matrice per ogni attività che l'app supporta ed espone alla ricerca di App. 

Apple suggerisce utilizzando una notazione inversa-DNS-style per l'identificatore di tipo di attività per evitare conflitti. Ad esempio: `com.company-name.appname.activity` per app specifiche attività basate sugli o `com.company-name.activity` per le attività che è possono eseguire tra più app.

L'identificatore di tipo attività viene utilizzato durante la creazione di un `NSUserActivity` istanza per identificare il tipo di attività. Quando un'attività continua come risultato dell'utente toccando un risultato della ricerca, il tipo di attività (con l'ID dell'app Team) determina quale app da avviare per continuare l'attività.

Per creare gli identificatori di tipo attività necessarie per supportare questo comportamento, modificare il **Info. plist** file e passare il **origine** visualizzazione. Aggiungere un `NSUserActivityTypes` chiave e creare gli identificatori nel formato seguente:

[![](nsuseractivity-images/type01.png "Il NSUserActivityTypes chiave e gli identificatori necessari nell'editor plist")](nsuseractivity-images/type01.png#lightbox)

Nell'esempio precedente, è stato creato un nuovo identificatore di tipo di attività per l'attività di ricerca (`com.xamarin.platform`). Quando si crea la propria App, sostituire il contenuto del `NSUserActivityTypes` matrice con gli identificatori di tipo di attività specifiche per le attività, l'app supporta.

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

È possibile aggiungere ulteriori dettagli, impostando il `ContentAttributeSet` proprietà del nostro `NSUserActivity` come indicato di seguito:

[![](nsuseractivity-images/apphistory02.png "Panoramica di aggiunta dei dettagli di ricerca")](nsuseractivity-images/apphistory02.png#lightbox)

Utilizzando un `ContentAttributeSet` è possibile creare i risultati di ricerca avanzate che convincere l'utente finale per interagire con essi.

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>Risponde a un'attività

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

Si noti che questo è l'override del metodo stesso utilizzato per rispondere alle richieste di continuità. Ora se l'utente fa clic su un collegamento dall'applicazione nei risultati della ricerca Spotlight, l'app verrà portata in primo piano (o avviata se non è già in esecuzione) e verrà visualizzato il contenuto, spostamento o la funzionalità rappresentata da questo collegamento:

[![](nsuseractivity-images/apphistory03.png "Ripristinare lo stato precedente di ricerca")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>L'indicizzazione di ricerca pubblici

Come abbiamo visto sopra, iOS 9 semplifica fornire l'accesso di ricerca di contenuto dell'app e funzionalità che l'utente ha già individuato e utilizzato in un dispositivo iOS specifico. Con l'indicizzazione pubblico, iOS 9 fornisce un modo per gli utenti che non sono stati individuati ancora contenuto o le funzionalità (o anche che non hanno installato l'app) per ottenere questi risultati troppo le ricerche.

Indicizzazione pubblico funziona nel modo seguente:

1. Quando si crea un'attività per l'app è possibile contrassegnarlo come pubblico.
2. Attività pubbliche vengono inviate in Apple e indicizzate nel cloud.
3. Quando più utenti interagiscono con l'app su un dispositivo e utilizzano la funzionalità specifica o il contenuto rappresentato dall'attività, aumenta il numero di dimensioni della.
4. Risultati pubblici comuni sarà disponibili ad altri utenti, anche se non hanno l'app installata.
5. Questi risultati pubblici appariranno nella ricerca Spotlight e Safari (se l'attività include un URL).

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

Solo perché un'attività è stata impostata per l'indicizzazione pubblica impostando `EligibleForPublicIndexing = true`, questo non significa che verrà automaticamente aggiunto all'indice di cloud pubblico di Apple. Prima di tutto devono essere soddisfatte le condizioni seguenti:

1. È necessario visualizzate nei risultati di ricerca e seleziona da molti utenti. I risultati rimangono tali finché non è stato raggiunto un limite di engagement di attività.
2. Provisioning delle App impedisce a tutti i dati specifici dell'utente vengano indicizzati e reso pubblico.

<a name="benefits" />

## <a name="additional-benefits"></a>Vantaggi aggiuntivi

Mediante l'adozione di ricerca di App tramite `NSUserActivity` nell'app, è inoltre ottenere le funzionalità seguenti:

- **Passaggio di consegne** : poiché l'App ricerca viene esposto il contenuto, navigazione e/o funzionalità utilizzando lo stesso meccanismo come Handoff (`NSUserActivity`), è possibile consentire agli utenti dell'app di avviare un'attività in un dispositivo e continuarli un altro.
- **Suggerimenti di Siri** -insieme i suggerimenti di standard che generalmente determina i suggerimenti di Siri actives dall'app possono essere suggeriti automaticamente.
- **Promemoria Smart Siri** -gli utenti saranno in grado di richiedere Siri per ricordare le informazioni sulle attività dall'app.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida per programmatori di ricerca di App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Esempio di & post di Blog](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
