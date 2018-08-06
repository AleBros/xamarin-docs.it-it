---
title: Utilizzo di watchOS navigazione in Xamarin
description: Questo documento viene descritto come utilizzare la navigazione in un'applicazione watchOS. Viene descritto interfacce modale, navigazione gerarchica e basata su pagina interfacce.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: c9bcfc388164060549ca7010d11671abfa8230ac
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790640"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Utilizzo di watchOS navigazione in Xamarin

L'opzione più semplice spostamento disponibile nelle espressioni di controllo è un semplice [popup modale](#modal) che viene visualizzata sopra la scena corrente.

Per scene più espressioni di controllo App vi sono due paradigmi di navigazione:

- [Navigazione gerarchica](#Hierarchical_Navigation)
- [Interfacce basate su pagina](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfacce modale

Utilizzare il `PresentController` per aprire un controller di interfaccia come form modale. Il controller di interfaccia deve essere già definito nel **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Visualizzato come form modale controller utilizzano l'intero schermo (su scena precedente). Per impostazione predefinita, il titolo viene impostato **Annulla** e copiandolo consente di chiudere il controller.

Per chiudere a livello di codice il controller presentati come form modale, chiamare `DismissController`.

```csharp
DismissController();
```

Schermate modale possono essere un singolo scena oppure utilizzare un layout di pagina.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navigazione gerarchica

Visualizza automaticamente come uno stack che è possibile spostarsi all'indietro, in modo analogo a quello che `UINavigationController` utilizzabile in iOS. Scene possono essere inserite nello stack di navigazione e dallo (a livello di codice o dalla selezione dell'utente).

![](navigation-images/hierarchy-1.png "Scene possono essere inserite nello stack di navigazione") ![](navigation-images/hierarchy-2.png "scene possono essere estratto dallo stack di navigazione")

Come con iOS, una scorrere bordo sinistro consente di passare al controller padre in uno stack di navigazione gerarchica.

Entrambi i [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog) e [WatchTables](https://developer.xamarin.com/samples/WatchTables) esempi includono lo spostamento gerarchico.

### <a name="pushing-and-popping-in-code"></a>Push e visualizzazione nel codice

Guardare Kit non richiede un singole "controller spostamento" deve essere creato come iOS - inseriscono semplicemente un controller utilizzando il `PushController` (metodo) e uno stack di navigazione verrà creati automaticamente.

```csharp
PushController("secondPageController","some context info");
```

Schermata dell'orologio includerà un **nuovamente** pulsante in alto a sinistra, ma è possibile anche a livello di codice rimuovere una scena dello stack di navigazione utilizzando `PopController`.

```csharp
PopController();
```

Perché con iOS, è anche possibile tornare alla radice dello stack di navigazione utilizzando `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Utilizzando Segues

Segues possono essere creati tra le scene dello storyboard per definire la navigazione gerarchica. Per ottenere il contesto per la scena di destinazione, le chiamate di sistema operativo `GetContextForSegue` per inizializzare il nuovo controller di interfaccia.

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

Interfacce basate su pagina scorrere verso sinistra a destra, simile a quello `UIPageViewController` utilizzabile in iOS. Punti di indicatore vengono visualizzati nella parte inferiore della schermata per visualizzare la pagina è attualmente visualizzata.

![](navigation-images/paged-1.png "Prima pagina di esempio") ![](navigation-images/paged-2.png "seconda pagina di esempio") ![](navigation-images/paged-5.png "quinta pagina di esempio")


Affinché l'interfaccia utente principale per l'applicazione di espressioni di controllo a un'interfaccia basata su pagina, utilizzare `ReloadRootControllers` con una matrice di controller di interfaccia e i contesti:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

È anche possibile presentare un controller basato su pagine che non è la radice utilizzando `PresentController` da una delle altre scene in un'applicazione.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WatchTables (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchTables/)
