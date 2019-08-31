---
title: Utilizzo di watchOS padre Application in Xamarin
description: Questo documento descrive come usare un'applicazione padre watchOS in Xamarin. Illustra le estensioni dell'app WatchKit, le app iOS, spazio di archiviazione condiviso e altro ancora.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 05dfb419834c2eee94f98d023df3a3fe8d6eee90
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198124"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Utilizzo di watchOS padre Application in Xamarin

> [!IMPORTANT]
> L'accesso all'applicazione padre usando solo gli esempi seguenti funziona nelle App watch watchOS 1.


Esistono diversi modi per la comunicazione tra l'app e l'app per iOS che viene abbinato a:

- Le estensioni Watch possono [chiamare un metodo](#code) per l'app padre eseguita in background nell'iPhone.

- Espressioni di controllo delle estensioni can [condividere un percorso di archiviazione](#storage) con l'app iPhone padre.

- Uso della consegna per passare i dati da un'occhiata o una notifica all'app Watch, inviando l'utente a uno specifico controller di interfaccia nell'app.

L'App padre viene talvolta anche detto l'App contenitore.


<a name="code" />

## <a name="run-code"></a>Eseguire il codice

La comunicazione tra un'estensione di espressioni di controllo e l'app iPhone padre viene illustrata la [GpsWatch esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch).
L'estensione di espressioni di controllo può richiedere l'app per iOS padre per eseguire alcune operazioni di elaborazione nel relativo per conto dell'utente mediante il `OpenParentApplication` (metodo).

Ciò è particolarmente utile per le attività in corso (tra cui le richieste di rete) - solo l'elemento padre app iOS possono sfruttare i vantaggi dell'elaborazione in background per completare queste attività e salvare i dati recuperati in una posizione accessibile per l'estensione di espressioni di controllo.



### <a name="watch-kit-app-extension"></a>Estensione dell'App Watch Kit

Chiamare il `WKInterfaceController.OpenParentApplication` nell'estensione dell'app watch. Restituisce un `bool` che indica se la richiesta del metodo è stata inviata correttamente o meno. Verificare i `error` parametro nella risposta `Action` per determinare se si è verificato durante il metodo in esecuzione nell'app iPhone.

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```


### <a name="ios-app"></a>App iOS

Tutte le chiamate da un'estensione dell'app watch vengono indirizzate tramite l'app iPhone `HandleWatchKitExtensionRequest` (metodo).
Se si stanno apportando diverse richieste in app watch, questo metodo sarà necessario eseguire una query di `userInfo` dizionario utilizzato per determinare la modalità di elaborazione della richiesta.


```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```


<a name="storage" />

## <a name="shared-storage"></a>Archiviazione condivisa

Se si configura un [gruppo di app](~/ios/watchos/app-fundamentals/app-groups.md) quindi estensioni iOS 8 (incluse le estensioni di espressioni di controllo) possono condividere dati con l'app padre.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

Il codice seguente può essere scritto l'estensione dell'app watch sia l'app iPhone padre in modo che si può fare riferimento a un set comune di `NSUserDefaults`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files" />

### <a name="files"></a>File

L'estensione di app e guardare iOS è anche possibile condividere i file usando un percorso di file comuni.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Nota: se il percorso `null` quindi selezionare il [configurazione del gruppo di app](~/ios/watchos/app-fundamentals/app-groups.md) per assicurarsi che i profili di provisioning siano stati configurati correttamente e sono stati scaricati o installati nel computer di sviluppo.

Per altre informazioni, vedere la [le funzionalità di gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.

## <a name="wormholesharp"></a>WormHoleSharp

Un meccanismo di open source più diffusi per watchOS 1 (base [MMWormHole](https://github.com/mutualmobile/MMWormhole)) per passare i dati o i comandi tra l'app padre e l'app watch.

È possibile configurare WormHole usando un gruppo di app simile al seguente nell'app per iOS e guarda estensione:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Scaricare il C# versione [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).



## <a name="related-links"></a>Collegamenti correlati

- [GpsWatch (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp (esempio)](https://github.com/Clancey/WormHoleSharp)
- [Riferimento WKInterfaceController di Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple condivisione dei dati con l'App che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
