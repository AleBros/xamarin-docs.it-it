---
title: Aggiornamenti della progettazione visiva in iOS 11
description: Questo documento descrive gli aggiornamenti della progettazione visiva introdotti in iOS 11. Viene illustrato come modificare le barre di navigazione, i controller di ricerca, i margini, il contenuto a schermo intero e le visualizzazioni di tabella.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: e5a61af4cd8a09df3ffddb74658f646aa8edfa1f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304758"
---
# <a name="visual-design-updates-in-ios-11"></a>Aggiornamenti della progettazione visiva in iOS 11

In iOS 11 Apple ha introdotto nuove modifiche visive, inclusi gli aggiornamenti alla barra di spostamento, alla barra di ricerca e alle visualizzazioni di tabella. Sono stati inoltre apportati miglioramenti per consentire una maggiore flessibilità rispetto ai margini e al contenuto a schermo intero. Queste modifiche sono descritte in questa guida. 

Per informazioni specifiche sulla progettazione di iPhone X, guardare il video [relativo alla progettazione di Apple per iPhone x](https://developer.apple.com/videos/play/fall2017/801/) .

## <a name="uikit"></a>UIKit

Le barre UIKit sono state adattate in iOS 11 per renderle più accessibili per gli utenti finali.

Una modifica di questo tipo è una nuova visualizzazione HUD visualizzata quando un utente preme lungo un elemento a barre. Per abilitare questa impostazione, impostare la proprietà `largeContentSizeImage` in `UIBarItem` e aggiungere un'immagine più grande tramite un [Catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra di spostamento
iOS 11 introduce nuove funzionalità per semplificare la lettura dei titoli della barra di navigazione. Le app possono visualizzare questo titolo più grande assegnando la proprietà `PrefersLargeTitles` a true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

L'impostazione di titoli più grandi nell'app consente di visualizzare _tutti i_ titoli delle barre di spostamento nell'app, come illustrato nello screenshot seguente:

![Titolo di navigazione grande](visual-design-images/image7.png)

Per controllare quando viene visualizzato un titolo di grandi dimensioni su una barra di navigazione, impostare il `LargeTitleDisplayMode` sull'elemento di navigazione su `Always`, `Never`o `Automatic`.

### <a name="search-controller"></a>Controller di ricerca

iOS 11 ha semplificato l'aggiunta di un controller di ricerca direttamente alla barra di spostamento. Dopo aver creato un controller di ricerca, aggiungerlo alla barra di navigazione con la proprietà `SearchController`:

```csharp
NavigationItem.SearchController = searchController;
```

[![titolo di spostamento grande con la barra di ricerca](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

A seconda della funzionalità dell'app, è possibile che la barra di ricerca non venga nascosta quando un utente scorre un elenco. È possibile modificare questa impostazione usando la proprietà `HidesSearchBarWhenScrolling`.

## <a name="margins"></a>Margini

Apple ha creato una nuova proprietà, `directionalLayoutMargins`, che può essere usata per impostare lo spazio tra le viste e le visualizzazioni. Usare `directionalLayoutMargins` con le impostazioni di `leading` o `trailing`. Indipendentemente dal fatto che il sistema sia una lingua da sinistra a destra o da destra a sinistra, la spaziatura nell'app viene impostata in modo appropriato da iOS.

In iOS 10 e precedenti, tutte le visualizzazioni hanno una dimensione del margine minima a cui si allineano. iOS 11 ha introdotto la possibilità di eseguire l'override di questo usando `ViewRespectsSystemMinimumLayoutMargins`. Se, ad esempio, si imposta questa proprietà su false, è possibile modificare gli indici di bordo su zero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```

![Immagine che mostra il margine con incasso zero in iOS 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Contenuto a schermo intero

iOS 7 ha [introdotto](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` e `bottomLayoutGuide` come metodo per vincolare le visualizzazioni in modo che non siano nascoste dalle barre UIKit e si trovino in un'area visibile dello schermo. Questi elementi sono stati deprecati in iOS 11 a favore dell' _area sicura_.

L'area Safe è un nuovo modo per considerare lo spazio visibile dell'applicazione e la modalità di aggiunta dei vincoli tra una vista e una visualizzazione Super. Si consideri, ad esempio, l'immagine seguente:

[Guida al layout ![area sicura rispetto alla parte superiore e inferiore](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

In precedenza, se è stata aggiunta una vista e si desidera che sia visibile nell'area verde precedente, è necessario vincolarla alla parte _inferiore_ del `TopLayoutGuide` e alla _parte superiore_ del `BottomLayoutGuide`. In iOS 11 è invece necessario vincolarlo alla _parte superiore_ e _inferiore_ dell'area sicura. Di seguito è riportato un esempio:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Visualizzazione tabella

Il UITableView ha avuto una serie di modifiche ridotte, ma significative, in iOS 11.

Per impostazione predefinita, le intestazioni, i piè di pagina e le celle vengono ora ridimensionati automaticamente in base al relativo contenuto. Per rifiutare esplicitamente questo comportamento di ridimensionamento automatico, impostare la `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`o `EstimatedSectionFooterHeight` su zero.

Tuttavia, in alcune circostanze, ad esempio quando si aggiungono UITableViewController in iOS designer o quando si usa Storboards esistenti in Interface Builder, potrebbe essere necessario abilitare manualmente le celle a ridimensionamento automatico. A tale scopo, assicurarsi di avere impostato le proprietà seguenti nella visualizzazione tabella per le celle, le intestazioni e i piè di pagina, rispettivamente:

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

iOS 11 ha espanso la funzionalità delle azioni di riga. `UISwipeActionsConfiguration` stato introdotto per definire un set di azioni che devono essere eseguite quando l'utente scorre in una delle due direzioni su una riga di una vista tabella. Questo comportamento è simile a quello di Native mail. app. Per ulteriori informazioni, vedere la Guida alle [azioni sulle righe](~/ios/user-interface/controls/tables/row-action.md) .

Le visualizzazioni di tabella dispongono del supporto per il trascinamento della selezione in iOS 11. Per ulteriori informazioni, vedere la Guida di [trascinamento della selezione](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) .

## <a name="related-links"></a>Collegamenti correlati

- [Novità di iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto App Store aggiornata (Apple)](https://developer.apple.com/app-store/product-page/)
- [Progettazione per iPhone X (Apple) (video)](https://developer.apple.com/videos/play/fall2017/801/)
- [Aggiornamento dell'app per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
