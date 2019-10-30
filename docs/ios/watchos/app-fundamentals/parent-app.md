---
title: Utilizzo dell'applicazione padre watchos in Novell
description: Questo documento descrive come usare un'applicazione padre watchos in Novell. Illustra le estensioni dell'app WatchKit, le app iOS, l'archiviazione condivisa e altro ancora.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 08637fe13b28565e7c6ab1c89b291c6db3b81025
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001482"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Utilizzo dell'applicazione padre watchos in Novell

> [!IMPORTANT]
> L'accesso all'applicazione padre usando gli esempi seguenti funziona solo con le app watchos 1 Watch.

Esistono diversi modi per comunicare tra l'app Watch e l'app per iOS con cui viene fornito:

- Le estensioni Watch possono [chiamare un metodo](#code) per l'app padre eseguita in background nell'iPhone.

- Le estensioni Watch possono [condividere un percorso di archiviazione](#storage) con l'app per iPhone padre.

- Uso della consegna per passare i dati da un'occhiata o una notifica all'app Watch, inviando l'utente a uno specifico controller di interfaccia nell'app.

L'app padre viene a volte definita anche app contenitore.

<a name="code" />

## <a name="run-code"></a>Esegui codice

La comunicazione tra un'estensione dell'espressione di controllo e l'app per iPhone padre è illustrata nell' [esempio GpsWatch](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch).
L'estensione Watch può richiedere all'app iOS padre di eseguire alcune operazioni di elaborazione per suo conto usando il metodo `OpenParentApplication`.

Questa operazione è particolarmente utile per le attività a esecuzione prolungata (incluse le richieste di rete). solo l'app per iOS padre può sfruttare l'elaborazione in background per completare queste attività e salvare i dati recuperati in un percorso accessibile all'estensione Watch.

### <a name="watch-kit-app-extension"></a>Estensione dell'app Kit Watch

Chiamare il `WKInterfaceController.OpenParentApplication` nell'estensione dell'app Watch. Restituisce un `bool` che indica se la richiesta del metodo è stata inviata correttamente o meno. Controllare il `error` parametro nella risposta `Action` per determinare se si è verificato un errore durante il metodo in esecuzione nell'app iPhone.

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

Tutte le chiamate da un'estensione dell'app Watch vengono instradate tramite il metodo `HandleWatchKitExtensionRequest` dell'app iPhone.
Se si eseguono richieste diverse nell'app Watch, questo metodo dovrà eseguire una query sul dizionario `userInfo` per determinare come elaborare la richiesta.

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

Se si configura un [gruppo di app](~/ios/watchos/app-fundamentals/app-groups.md) , le estensioni iOS 8 (incluse le estensioni Watch) possono condividere dati con l'app padre.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

Il codice seguente può essere scritto sia nell'estensione dell'app Watch che nell'app per iPhone padre, in modo che possano fare riferimento a un set comune di `NSUserDefaults`:

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

L'app iOS e l'estensione Watch possono inoltre condividere file usando un percorso di file comune.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Nota: se il percorso è `null`, controllare la [configurazione del gruppo di app](~/ios/watchos/app-fundamentals/app-groups.md) per assicurarsi che i profili di provisioning siano stati configurati correttamente e che siano stati scaricati o installati nel computer di sviluppo.

Per ulteriori informazioni, vedere la documentazione relativa alle [funzionalità del gruppo di app](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="wormholesharp"></a>WormHoleSharp

Un diffuso meccanismo open source per watchos 1 (basato su [MMWormHole](https://github.com/mutualmobile/MMWormhole)) per passare dati o comandi tra l'app padre e l'app Watch.

È possibile configurare il WormHole usando un gruppo di app come questo nell'app iOS e guardare l'estensione:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Scaricare la C# versione [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).

## <a name="related-links"></a>Collegamenti correlati

- [GpsWatch (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp (esempio)](https://github.com/Clancey/WormHoleSharp)
- [Riferimento WKInterfaceController di Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple condivide i dati con l'app che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
