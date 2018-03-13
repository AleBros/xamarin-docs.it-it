---
title: Aggiornamenti di progettazione visiva
description: "Esplorare le nuove funzionalità di iOS 11"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: d66d8cd722aa9a7b6fe27db3f6128ee24309a1de
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="visual-design-updates"></a>Aggiornamenti di progettazione visiva

_Esplorare le nuove funzionalità di iOS 11_

In iOS 11, Apple ha introdotto nuove modifiche visive tra cui gli aggiornamenti per la barra di spostamento, barra di ricerca e viste delle tabelle. Inoltre, sono stati apportati miglioramenti per consentire maggiore flessibilità su margini e il contenuto a schermo intero. Queste modifiche sono descritte in questa Guida.

## <a name="uikit"></a>UIKit

Barre di UIKit sono state modificate in iOS 11 per renderli più accessibile per gli utenti finali.

Una di queste modifiche è una nuova visualizzazione HUD che viene visualizzato quando un utente long-pressioni su una barra di elemento. A tale scopo, impostare il `largeContentSizeImage` proprietà `UIBarItem` e aggiungere un'immagine più grande tramite un [catalogo di asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra di spostamento
iOS 11 introdotte nuove funzionalità per rendere più facile da leggere i titoli di barra di navigazione. App è possono visualizzare il titolo più grande assegnando il `PrefersLargeTitles` proprietà su true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Rende i titoli più grandi di impostazione dell'app _tutti_ vengono visualizzati i titoli di barre di navigazione tra l'app più grandi, come illustrato nella schermata riportata di seguito:

![Titolo di navigazione di grandi dimensioni](visual-design-images/image7.png)

Per controllare quando un titolo di grandi dimensioni viene visualizzato una barra di spostamento, impostare il `LargeTitleDisplayMode` sull'elemento di navigazione per `Always`, `Never`, o `Automatic`.

### <a name="search-controller"></a>Controller di ricerca

iOS 11 ha reso più semplice aggiungere un controller di ricerca direttamente alla barra di spostamento. Dopo aver creato un controller di ricerca, aggiungerlo alla barra di spostamento con la `SearchController` proprietà:

```csharp
NavigationItem.SearchController = searchController;
```

[![Titolo di navigazione di grandi dimensioni con barra di ricerca](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

A seconda della funzionalità dell'app, è possibile o potrebbe non desidera che la barra di ricerca deve essere nascosto quando l'utente scorre un elenco. È possibile modificare questo utilizzando il `HidesSearchBarWhenScrolling` proprietà.

## <a name="margins"></a>Margini

Apple ha creato una nuova proprietà – `directionalLayoutMargins` – che può essere utilizzato per impostare lo spazio tra le visualizzazioni e sottoviste. Utilizzare `directionalLayoutMargins` con `leading` o `trailing` elementi interni. Indipendentemente dal fatto che il sistema sia una lingua da sinistra a destra o da destra a sinistra, la spaziatura dell'App è impostata in modo appropriato per iOS.

In iOS 10 e prima di tutte le visualizzazioni ha una dimensione minima del margine a cui allinearli. iOS 11 ha introdotto l'opzione per eseguire l'override di tale mediante `ViewRespectsSystemMinimumLayoutMargins`. Ad esempio, impostando questa proprietà su false consente di modificare i margini di bordo a zero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![Margine dell'immagine che mostra con zero inset in ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Contenuto a schermo intero

iOS 7 [introdotto](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` e `bottomLayoutGuide` come un modo per vincolare le visualizzazioni in modo che non sono nascosti da barre UIKit e sono in un'area visibile dello schermo. Questi sono stati deprecati in iOS 11 a favore del _area di sicurezza_.

Area di sicurezza è un nuovo modo di concepire area visibile dell'applicazione e come l'aggiunta di vincoli tra una vista e una visualizzazione con privilegi avanzata. Si consideri ad esempio l'immagine seguente:

[![Area di sicurezza vs Top e Guida layout inferiore](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

In precedenza, è stato aggiunto a una vista e desiderato non sia visibile nell'area di colore verde precedente, si limita al _inferiore_ del `TopLayoutGuide` e _top_ del `BottomLayoutGuide`. In iOS 11, è invece necessario vincolare per il _top_ e _inferiore_ dell'Area di sicurezza. Vedere l'esempio seguente:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Visualizzazione tabella

Il UITableView ha un numero di modifiche piccole ma significative, in iOS 11.

Per impostazione predefinita, le intestazioni, piè di pagina e le celle vengono ora automaticamente ridimensionate in base al relativo contenuto. Per rifiutare esplicitamente questo comportamento di ridimensionamento automatico di set di `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`, o `EstimatedSectionFooterHeight` a zero.

Tuttavia, in alcuni casi (ad esempio quando aggiunta UITableViewController nella finestra di progettazione iOS o quando si utilizza Storboards esistente in interfaccia generatore) potrebbe essere necessario abilitare manualmente le celle di ridimensionamento automatico. A tale scopo, assicurarsi di aver impostato le proprietà seguenti nella visualizzazione tabella per le celle, le intestazioni e piè di pagina, rispettivamente:

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

iOS 11 ha esteso le funzionalità delle azioni di riga. `UISwipeActionsConfiguration` è stato introdotto per definire un set di azioni che dovrà essere eseguita quando l'utente passa in entrambe le direzioni in una riga in una visualizzazione tabella. Questo comportamento è simile a quello del Mail.app nativo. Per ulteriori informazioni, vedere il [azioni riga](~/ios/user-interface/controls/tables/row-action.md) Guida.

Viste delle tabelle sono disponibile supporto per trascinamento della selezione in iOS 11. Per ulteriori informazioni, vedere il [trascinare e rilasciare](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) Guida.


## <a name="related-links"></a>Collegamenti correlati

- [Novità in iOS 11 (mela)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (mela)](https://developer.apple.com/app-store/product-page/)
- [L'aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
