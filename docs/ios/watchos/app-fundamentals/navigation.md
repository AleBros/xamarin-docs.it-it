---
title: Utilizzo di watchOS navigazione in Xamarin
description: Questo documento viene descritto come utilizzare la navigazione in un'applicazione watchOS. Viene descritto le interfacce modali, navigazione gerarchica e basata su pagine interfacce.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: e0c70418849bdf394a00cd5f53744b0c01bc5794
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889616"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Utilizzo di watchOS navigazione in Xamarin

L'opzione più semplice navigazione disponibile nell'orologio è un semplice [popup modale](#modal) che viene visualizzata sopra nella scena corrente.

Per watch scena a più App vi sono due paradigmi di navigazione:

- [Navigazione gerarchica](#Hierarchical_Navigation)
- [Interfacce basate su pagina](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfacce modali

Usare il `PresentController` metodo per aprire un controller di interfaccia come form modale. Il controller di interfaccia debba essere già definito nel **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Visualizzato come form modale i controller usano l'intera schermata (che coprono la scena precedente). Per impostazione predefinita il titolo viene impostato sulla **annullare** e toccandolo consente di chiudere il controller.

Per chiudere a livello di codice del controller presentati come form modale, chiamare `DismissController`.

```csharp
DismissController();
```

Le schermate modale possono essere un unica scena o usare un layout per la pagina.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navigazione gerarchica

Presenta le scene come uno stack che è possibile spostarsi all'indietro, in modo analogo al modo in cui `UINavigationController` utilizzabile in iOS. In background possono essere inseriti nello stack di navigazione ed estratto (a livello di codice o dalla selezione dell'utente).

![](navigation-images/hierarchy-1.png "Scene possono essere inserite nello stack di navigazione") ![](navigation-images/hierarchy-2.png "scene possono essere estratto dallo stack di navigazione")

Come con iOS, un left-edge-passaggio del dito consente di passare nuovamente al controller in uno stack di navigazione gerarchica padre.

Entrambi i [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) e [WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) esempi includono navigazione gerarchica.

### <a name="pushing-and-popping-in-code"></a>Eseguire il push e si estraggono nel codice

Guarda Kit non richiede un singole "controller di spostamento" deve essere creato come iOS - inseriscono semplicemente un controller utilizzando il `PushController` (metodo) e uno stack di navigazione verrà creati automaticamente.

```csharp
PushController("secondPageController","some context info");
```

Schermata dell'orologio includerà un **nuovamente** pulsante in alto a sinistra, ma è possibile rimuovere anche a livello di codice una scena da dello stack di navigazione utilizzando `PopController`.

```csharp
PopController();
```

Come con iOS, è anche possibile tornare alla radice dello stack di navigazione utilizzando `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Usando gli elementi segue

Gli elementi segue possono essere creati tra le scene nello storyboard per definire la navigazione gerarchica. Ottenere il contesto per la scena di destinazione, il sistema operativo chiama `GetContextForSegue` per inizializzare il nuovo controller di interfaccia.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>Interfacce basate su pagina

Interfacce basate su pagina scorrere rapidamente verso sinistra a destra, simile a quelle fornite `UIPageViewController` utilizzabile in iOS. Punti di indicatore vengono visualizzati nella parte inferiore della schermata per visualizzare la pagina viene attualmente visualizzata.

![](navigation-images/paged-1.png "Prima pagina di esempio") ![](navigation-images/paged-2.png "seconda pagina di esempio") ![](navigation-images/paged-5.png "quinta pagina di esempio")


Per rendere un'interfaccia basata su pagina l'interfaccia utente principale per l'app watch, usare `ReloadRootControllers` con una matrice di controller di interfaccia e contesti:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

È anche possibile presentare un controller basato su pagine che non è la radice usando `PresentController` da una delle altre scene in un'app.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (esempio)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
