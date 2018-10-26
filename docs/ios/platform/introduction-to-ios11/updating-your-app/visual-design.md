---
title: Aggiornamenti di progettazione visivi in iOS 11
description: Questo documento descrive la progettazione visiva aggiornamenti introdotto in iOS 11. Vengono illustrate le modifiche apportate a barre di navigazione, ricerca controller, i margini, contenuto a schermo intero e le visualizzazioni di tabelle.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c6351f2c25f8e31181c761aea1b471315a8a05e8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114515"
---
# <a name="visual-design-updates-in-ios-11"></a>Aggiornamenti di progettazione visivi in iOS 11

In iOS 11, Apple ha introdotto nuove modifiche visive tra cui gli aggiornamenti per la barra di spostamento, barra di ricerca e le visualizzazioni di tabelle. Inoltre, sono stati apportati miglioramenti per consentire una maggiore flessibilità su margini e il contenuto a schermo intero. Queste modifiche sono descritte in questa Guida. 

Per informazioni in particolare sulla progettazione per iPhone X, Apple, guardare [progetta per iPhone X](https://developer.apple.com/videos/play/fall2017/801/) video.

## <a name="uikit"></a>UIKit

Le barre UIKit, sono state modificate in iOS 11 per renderli più accessibili per gli utenti finali.

Tale modifica è una nuova visualizzazione HUD che viene visualizzata quando un utente prolungata-pressioni su una barra elemento. A tale scopo, impostare il `largeContentSizeImage` proprietà sul `UIBarItem` e aggiungere un'immagine di dimensioni maggiori tramite un' [catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra di spostamento
iOS 11 ha introdotto nuove funzionalità per rendere più facile da leggere i titoli di barra di navigazione. Le app possono visualizzare questo titolo più grande mediante l'assegnazione di `PrefersLargeTitles` proprietà su true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

I titoli più grandi di impostazione nell'app rende _tutti_ titoli di barre di navigazione tra le app visualizzati più grandi, come illustrato nello screenshot seguente:

![Titolo di spostamento di grandi dimensioni](visual-design-images/image7.png)

Per controllare quando viene visualizzato un grande titolo in una barra di spostamento, impostare il `LargeTitleDisplayMode` nell'elemento di navigazione per `Always`, `Never`, o `Automatic`.

### <a name="search-controller"></a>Controller di ricerca

iOS 11 ha semplificato aggiungere un controller di ricerca direttamente alla barra di navigazione. Dopo aver creato un controller di ricerca, aggiungerlo alla barra di spostamento con la `SearchController` proprietà:

```csharp
NavigationItem.SearchController = searchController;
```

[![Titolo di spostamento di grandi dimensioni con barra di ricerca](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

A seconda della funzionalità dell'app, è possibile o non opportuno la barra di ricerca deve essere nascosto quando un utente scorre un elenco. È possibile apportare delle modifiche usando il `HidesSearchBarWhenScrolling` proprietà.

## <a name="margins"></a>Margini

Apple ha creato una nuova proprietà, ovvero `directionalLayoutMargins` – che può essere utilizzato per impostare lo spazio tra le visualizzazioni e le visualizzazioni secondarie. Uso `directionalLayoutMargins` con `leading` o `trailing` Inset. Indipendentemente dal fatto che il sistema è un linguaggio da sinistra a destra o da destra a sinistra, è impostata in modo appropriato la spaziatura nell'app per iOS.

In iOS 10 e prima di, tutte le visualizzazioni avevano una dimensione del margine minimo in cui allinearli. iOS 11 introdotta la possibilità di eseguire l'override utilizzando `ViewRespectsSystemMinimumLayoutMargins`. Impostando questa proprietà su false consente, ad esempio, è possibile modificare i margini di edge su zero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![Margine dell'immagine che mostra con zero inset in ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Contenuto a schermo intero

iOS 7 [introdotti](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` e `bottomLayoutGuide` allo scopo di vincolare le visualizzazioni in modo che possano non sono nascoste da UIKit barre e in un'area visibile dello schermo. Queste sono state deprecate in iOS 11 in favore del _area sicura_.

Area di sicurezza è un nuovo modo di concepire il spazio visibile dell'applicazione e come si aggiungono vincoli tra una vista e una visualizzazione con privilegi avanzata. Ad esempio, prendere in considerazione l'immagine seguente:

[![Visual Studio area sicura Top e la Guida layout in basso](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

In precedenza, se è stata aggiunta una visualizzazione e desiderava che siano visibili nell'area di colore verde precedente, è consigliabile vincolare per il _inferiore_ del `TopLayoutGuide` e il _superiore_ del `BottomLayoutGuide`. In iOS 11, è invece consigliabile vincolare per il _superiore_ e il _inferiore_ dell'Area di sicurezza. Vedere l'esempio seguente:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Visualizzazione tabella

Il UITableView ha un numero di modifiche, piccole ma significative, in iOS 11.

Per impostazione predefinita, le intestazioni, piè di pagina e le celle vengono ora automaticamente ridimensionate in base al contenuto. Per rifiutare esplicitamente questo set di comportamento di ridimensionamento automatico la `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`, o `EstimatedSectionFooterHeight` su zero.

Tuttavia, in alcuni casi (ad esempio quando aggiunta UITableViewController in iOS Designer o quando si usa Storboards esistente in Interface Builder) potrebbe essere necessario abilitare manualmente le celle il ridimensionamento automatico. A tale scopo, verificare di avere impostato le proprietà seguenti nella visualizzazione della tabella per le celle delle intestazioni e piè di pagina, rispettivamente:

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 ha esteso le funzionalità delle azioni di riga. `UISwipeActionsConfiguration` è stato introdotto per definire un set di azioni che dovrà essere eseguita quando l'utente passa in entrambe le direzioni di una riga in una visualizzazione tabella. Questo comportamento è simile a quella del Mail.app nativo. Per altre informazioni, vedere la [azioni riga](~/ios/user-interface/controls/tables/row-action.md) Guida.

Viste delle tabelle dispongono del supporto per trascinamento della selezione in iOS 11. Per altre informazioni, vedere la [trascinare e rilasciare](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) Guida.


## <a name="related-links"></a>Collegamenti correlati

- [What ' s New in iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Progettazione per iPhone X (Apple) (video)](https://developer.apple.com/videos/play/fall2017/801/)
- [Aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)

