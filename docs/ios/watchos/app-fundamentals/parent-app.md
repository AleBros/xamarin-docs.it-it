---
title: Utilizzo di watchOS padre Application in Xamarin
description: Questo documento descrive come usare un'applicazione padre watchOS in Xamarin. Illustra le estensioni delle app watchos, le app per iOS, l'archiviazione condivisa e altro ancora.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 3e11b163d16be9711bf09102e3ab8604d98299d7
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304961"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Utilizzo di watchOS padre Application in Xamarin

Esistono diversi modi per la comunicazione tra l'app e l'app per iOS che viene abbinato a:

- Le app Watch possono [eseguire codice](#run-code) sull'app padre nell'iPhone.

- Le estensioni Watch possono [condividere un percorso di archiviazione](#shared-storage) con l'app per iPhone padre.

- Usare la consegna per passare i dati da una notifica all'app Watch, inviando l'utente a uno specifico controller di interfaccia nell'app.

L'App padre viene talvolta anche detto l'App contenitore.

## <a name="run-code"></a>Eseguire il codice

Questi due esempi illustrano come usare `WCSession` per eseguire codice e inviare messaggi tra un'app Watch e l'iPhone associato:

- [Controlla connettività](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>Archiviazione condivisa

Se si configura un [gruppo di app](~/ios/watchos/app-fundamentals/app-groups.md) , le estensioni iOS 8 (incluse le estensioni Watch) possono condividere dati con l'app padre.

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

### <a name="files"></a>Files

L'estensione di app e guardare iOS è anche possibile condividere i file usando un percorso di file comuni.

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

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento WKInterfaceController di Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple condivide i dati con l'app che lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
