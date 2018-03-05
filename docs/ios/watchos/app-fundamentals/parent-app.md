---
title: Utilizzare l'applicazione padre
description: Condivisione di dati tra app iOS ed espressioni di controllo in watchOS 1
ms.topic: article
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: dab1f5d571473fbbdb12ef78eed3788165c3f0a6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-the-parent-application"></a>Utilizzare l'applicazione padre

_Condivisione di dati tra app iOS ed espressioni di controllo in watchOS 1_

> [!IMPORTANT]
> **Nota:** l'accesso all'applicazione padre utilizzando solo gli esempi seguenti funziona nelle app di espressioni di controllo watchOS 1.


Esistono diversi modi per la comunicazione tra l'applicazione di espressioni di controllo e l'app iOS che in dotazione:

- Espressioni di controllo estensioni può [chiamare un metodo](#code) contro l'app padre che viene eseguito in background su iPhone.

- Espressioni di controllo estensioni può [condividono una posizione di archiviazione](#storage) con l'app iPhone padre.

- Utilizzo di uniforme per passare i dati da un riepilogo o una notifica all'app di espressioni di controllo, l'invio l'utente a un controller di interfaccia specifica nell'app.

L'App padre è anche detta anche l'applicazione contenitore.


<a name="code" />

## <a name="run-code"></a>Esecuzione di codice

La comunicazione tra un'estensione di espressioni di controllo e l'app iPhone padre viene illustrata la [GpsWatch esempio](https://developer.xamarin.com/samples/GpsWatch).
L'estensione di espressioni di controllo può richiedere l'app iOS padre per eseguire alcune operazioni di elaborazione sul relativo utilizzo per conto di `OpenParentApplication` metodo.

Ciò è particolarmente utile per le attività in corso (incluse le richieste di rete) - solo l'elemento padre app iOS possono sfruttare l'elaborazione in background per completare queste attività e salvare i dati recuperati in una posizione accessibile per l'estensione di espressioni di controllo.



### <a name="watch-kit-app-extension"></a>Espressioni di controllo dell'estensione App Kit

Chiamare il `WKInterfaceController.OpenParentApplication` nell'estensione app espressioni di controllo. Restituisce un `bool` che indica se la richiesta del metodo è stata inviata correttamente o No. Controllare il `error` parametro nella risposta `Action` per determinare se si è verificato durante il metodo in esecuzione nell'app iPhone.

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


### <a name="ios-app"></a>iOS App

Tutte le chiamate da un'estensione di app di espressioni di controllo vengono indirizzate tramite l'app iPhone `HandleWatchKitExtensionRequest` metodo.
Se si eseguono richieste diverse nell'applicazione di espressioni di controllo, questo metodo sarà necessario eseguire una query di `userInfo` dizionario per determinare come elaborare la richiesta.


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

Se si configura un [gruppo app](~/ios/watchos/app-fundamentals/app-groups.md) quindi le estensioni iOS 8 (incluse le estensioni di espressioni di controllo) possono condividere dati con l'applicazione padre.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

Il codice seguente può essere scritto nell'estensione dell'app espressioni di controllo sia l'app iPhone padre in modo che fanno riferimento a un set comune di `NSUserDefaults`:

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

L'estensione di iOS app ed espressioni di controllo è anche possibile condividere i file usando un percorso file comune.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Nota: se il percorso è `null` quindi controllare il [configurazione del gruppo di app](~/ios/watchos/app-fundamentals/app-groups.md) per verificare i profili di provisioning sono stati configurati correttamente e che sono stati scaricati o installati nel computer di sviluppo.

Per ulteriori informazioni, vedere il [le funzionalità delle App gruppo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.

## <a name="wormholesharp"></a>WormHoleSharp

Un meccanismo open source per watchOS 1 (in base a [MMWormHole](https://github.com/mutualmobile/MMWormhole)) per passare dati o i comandi tra l'app padre e l'applicazione di espressioni di controllo.

È possibile configurare spaziale usando un gruppo di app simile al seguente nell'app iOS e guardare estensione:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Scaricare la versione c# [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).



## <a name="related-links"></a>Collegamenti correlati

- [GpsWatch (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WormHoleSharp (esempio)](https://github.com/Clancey/WormHoleSharp)
- [Riferimento WKInterfaceController di Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple condivisione dei dati con l'App che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
