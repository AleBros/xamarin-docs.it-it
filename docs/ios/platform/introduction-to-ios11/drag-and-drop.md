---
title: Trascinare e rilasciare in xamarin. IOS
description: Questo documento descrive come implementare il trascinamento della selezione nelle App xamarin. IOS usando le API introdotte in iOS 11. In particolare, vengono illustrati abilitazione di trascinamento della selezione in UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/05/2017
ms.openlocfilehash: aa93e015a399e733a2bb52f087a1e482bc23a00a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112078"
---
# <a name="drag-and-drop-in-xamarinios"></a>Trascinare e rilasciare in xamarin. IOS

_Implementazione di trascinamento della selezione per iOS 11_

iOS 11 include trascinare e rilasciare il supporto per copiare dati tra le applicazioni in un iPad. Gli utenti possono selezionare e trascinare tutti i tipi di contenuto da app posizionata side-by-side, o mediante il trascinamento sull'icona di un'app che verrà attivata l'app per aprire e consentire i dati che si desidera eliminare:

![Esempio di trascinamento della selezione da app personalizzata in app di Lotus Notes](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Trascinamento della selezione è disponibile solo all'interno dell'app stessa su iPhone.

Prendere in considerazione che supporta trascinamento e rilascio operazioni in un punto qualsiasi del contenuto possono essere create o modificate:

- I controlli di testo supportano trascinamento e rilascio per tutte le app basate su iOS 11, senza operazioni aggiuntive.
- Le visualizzazioni di tabelle e viste di raccolta includono miglioramenti in iOS 11 che semplificano l'aggiunta di trascinare e rilasciare il comportamento.
- Qualsiasi altra visualizzazione può essere reso supporta trascinamento e rilascio con un'ulteriore personalizzazione.

Quando l'aggiunta di trascinamento e rilascio supporta alle tue App, è possibile fornire livelli diversi di fedeltà del contenuto; ad esempio, è possibile fornire il testo formattato sia una versione in testo normale dei dati in modo che l'app ricevente può scegliere che si integra meglio la destinazione del trascinamento. È anche possibile personalizzare la visualizzazione di trascinamento e anche per abilitare il trascinamento di più elementi in una sola volta.

## <a name="drag-and-drop-with-text-controls"></a>Trascinare e rilasciare i controlli di testo

`UITextView` e `UITextField` supportano automaticamente il testo selezionato out, trascinamento della selezione di contenuto in.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Trascinamento della selezione con UITableView

`UITableView` supporta la gestione incorporata per trascinare e rilasciare le interazioni con righe della tabella, che richiede solo pochi metodi per abilitare il comportamento predefinito.

Sono disponibili due interfacce:

- `IUITableViewDragDelegate` : Le informazioni di pacchetti quando viene avviato un trascinamento nella visualizzazione della tabella.
- `IUITableViewDropDelegate` – Elabora le informazioni quando un rilascio è in corso tentativi e completato.

Nel [esempio DragAndDropTableView](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/) queste due interfacce vengono entrambi implementate nel `UITableViewController` (classe), con il delegato e l'origine dati. Sono state assegnate nel `ViewDidLoad` metodo:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

La quantità minima di codice necessaria per queste due interfacce è illustrata di seguito.

### <a name="table-view-drag-delegate"></a>Delegato di trascinamento di visualizzazione tabella

L'unico metodo _obbligatorio_ per supportare il trascinamento di una riga da una visualizzazione tabella è `GetItemsForBeginningDragSession`. Se l'utente inizia a trascinare una riga, questo metodo verrà chiamato.

Seguito è riportata un'implementazione. Recupera i dati associati con la riga trascinata, viene eseguita la codifica e configura un `NSItemProvider` che determina il modo in cui le applicazioni gestirà la parte "drop" dell'operazione (ad esempio, se può gestire il tipo di dati `PlainText`, nell'esempio):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

Esistono molti metodi facoltativi sul delegato trascinamento che può essere implementato per personalizzare il comportamento di trascinamento, ad esempio il più rappresentazioni di dati che possono avvantaggiarsi nelle app di destinazione (ad esempio testo formattato come anche come testo normale o un vettore e mappa di bit le versioni di un disegno). È anche possibile fornire le rappresentazioni di dati personalizzate da usare durante il trascinamento della selezione all'interno dell'app stessa.

### <a name="table-view-drop-delegate"></a>Delegato di rilascio di visualizzazione tabella

I metodi sul delegato rilascio vengono chiamati quando un'operazione di trascinamento viene eseguita tramite una visualizzazione tabella, o completato sopra di esso. I metodi richiesti determinano se i dati sono consentiti da eliminare e le azioni da eseguire se il trascinamento è stato completato:

- `CanHandleDropSession` : Durante un trascinamento è in corso, e potenzialmente da eliminare nell'applicazione, questo metodo determina se i dati trascinati può essere eliminato.
- `DropSessionDidUpdate` – Mentre l'operazione di trascinamento è in corso, questo metodo viene chiamato per determinare quale azione è destinato. Informazioni di visualizzazione della tabella che viene trascinato sul, la sessione di trascinamento e il percorso di indice possibili tutte utilizzabili per determinare il comportamento e fornito all'utente indicazioni visive.
- `PerformDrop` : Quando l'utente completa il trascinamento (sollevare il dito), questo metodo consente di estrarre i dati trascinati e modifica la visualizzazione di tabella per aggiungere i dati in una nuova riga (o righe).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` indica se la visualizzazione di tabella può accettare dati trascinati. In questo frammento di codice `CanLoadObjects` viene usato per confermare che la visualizzazione di tabella può accettare dati di tipo stringa.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

Il `DropSessionDidUpdate` metodo viene chiamato ripetutamente mentre l'operazione di trascinamento è in corso, per fornire indicazioni visive per l'utente.

Nel codice seguente, `HasActiveDrag` viene usato per determinare se l'operazione ha avuto origine nella visualizzazione della tabella corrente. In questo caso, sono consentite solo singole righe da spostare.
Se l'operazione di trascinamento proviene da un'altra origine, verrà indicata un'operazione di copia:

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

L'operazione di trascinamento può essere uno dei `Cancel`, `Move`, o `Copy`.

L'obiettivo di rilascio è possibile inserire una nuova riga o aggiungere/aggiunta dati in una riga esistente.

#### <a name="performdrop"></a>PerformDrop

Il `PerformDrop` metodo viene chiamato quando l'utente completa l'operazione e modifica l'origine dati e visualizzazione della tabella in modo da riflettere i dati rilasciati.

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

Aggiungere il codice aggiuntivo per caricare in modo asincrono gli oggetti di grandi quantità di dati.

### <a name="testing-drag-and-drop"></a>Selezione e trascinamento test

È necessario usare un iPad per testare la [esempio](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/).
Aprire l'esempio insieme a un'altra app (ad esempio, le note) e trascinare il testo e righe tra di essi:

![screenshot di operazione di trascinamento in corso](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Collegamenti correlati

- [Trascinare e rilasciare Human Interface Guidelines (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Trascinamento della selezione di esempio di visualizzazione tabella](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [Trascinare e rilasciare il campione di visualizzazione raccolta](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [Introduzione a Drag and Drop (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Trascinamento della selezione con raccolta e visualizzazione di tabella (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/223/)
