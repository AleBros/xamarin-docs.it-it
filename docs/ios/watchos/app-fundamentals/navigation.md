---
title: Uso della navigazione watchos in Novell
description: Questo documento descrive come usare la navigazione in un'applicazione watchos. Vengono illustrate le interfacce modali, la navigazione gerarchica e le interfacce basate su pagine.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: d3565e359ccbad9f7b779969f4273a8cbae4d438
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021739"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Uso della navigazione watchos in Novell

L'opzione di navigazione più semplice disponibile nell'orologio è un semplice [popup modale](#modal) che viene visualizzato nella parte superiore della scena corrente.

Per le app Watch a più scene sono disponibili due paradigmi di navigazione:

- [Navigazione gerarchica](#Hierarchical_Navigation)
- [Interfacce basate su pagina](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfacce modali

Usare il metodo `PresentController` per aprire un controller di interfaccia modale. Il controller di interfaccia deve essere già definito nell' **interfaccia. Storyboard**.

```csharp
PresentController ("pageController","some context info");
```

I controller presentati in modalità modale usano l'intera schermata (coprendo la scena precedente). Per impostazione predefinita, il titolo è impostato su **Annulla** e toccando il controller verrà ignorata.

Per chiudere a livello di codice il controller presentato in modo modale, chiamare `DismissController`.

```csharp
DismissController();
```

Le schermate modali possono essere una singola scena o usare un layout basato su pagine.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navigazione gerarchica

Presenta scene come uno stack di cui è possibile eseguire lo spostamento, in modo analogo al modo in cui `UINavigationController` funziona in iOS. È possibile eseguire il push delle scene nello stack di navigazione ed estrarre (a livello di codice o in base alla selezione dell'utente).

![](navigation-images/hierarchy-1.png "È possibile effettuare il push delle scene nello stack di navigazione") ![](navigation-images/hierarchy-2.png "Gli scenari possono essere estratti dallo stack di navigazione")

Come con iOS, un scorrimento a sinistra viene spostato di nuovo sul controller padre in uno stack di navigazione gerarchico.

Entrambi gli esempi di [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) e [WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) includono la navigazione gerarchica.

### <a name="pushing-and-popping-in-code"></a>Push e schiocco nel codice

Il kit di controllo non richiede la creazione di un "controller di spostamento" over-arching come iOS: esegue semplicemente il push di un controller usando il metodo `PushController` e viene creato automaticamente uno stack di navigazione.

```csharp
PushController("secondPageController","some context info");
```

La schermata dell'orologio includerà un pulsante **indietro** in alto a sinistra, ma è anche possibile rimuovere a livello di codice una scena dallo stack di navigazione usando `PopController`.

```csharp
PopController();
```

Come con iOS, è anche possibile tornare alla radice dello stack di navigazione usando `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Uso di gli elementi segue

È possibile creare gli elementi segue tra le scene nello storyboard per definire la navigazione gerarchica. Per ottenere il contesto per la scena di destinazione, il sistema operativo chiama `GetContextForSegue` per inizializzare il nuovo controller di interfaccia.

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

Le interfacce basate su pagina scorreno da sinistra a destra, in modo analogo al modo in cui `UIPageViewController` funziona in iOS. I punti indicatore vengono visualizzati nella parte inferiore della schermata per visualizzare la pagina attualmente visualizzata.

![](navigation-images/paged-1.png "Prima pagina di esempio")![](navigation-images/paged-2.png "Seconda pagina di esempio")![](navigation-images/paged-5.png "Quinta pagina di esempio")

Per rendere un'interfaccia basata su pagina l'interfaccia utente principale per l'app Watch, usare `ReloadRootControllers` con una matrice di controller di interfaccia e contesti:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

È anche possibile presentare un controller basato su pagina che non è la radice usando `PresentController` da una delle altre scene in un'app.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```

## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (esempio)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
