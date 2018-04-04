---
title: Trascinamento e rilascio
description: Implementazione di trascinamento della selezione per iOS 11
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/05/2016
ms.openlocfilehash: fa6fcb2c4f5f17011307b31e4644889d066b71a9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="drag-and-drop"></a>Trascinamento e rilascio

_Implementazione di trascinamento della selezione per iOS 11_

iOS 11 include trascinare e rilasciare il supporto per copiare dati tra applicazioni in un iPad. Gli utenti possono selezionare e trascinare tutti i tipi di contenuto da app posizionata side-by-side o trascinandovi sopra un'icona di app che verrà attivata l'app per aprire ed eseguire i dati da eliminare:

![Esempio di trascinamento della selezione da app personalizzata in app note](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Trascinamento della selezione è disponibile solo all'interno dell'app stessa su iPhone.

Provare a supporto di trascinamento e rilascio operazioni in qualsiasi punto del contenuto possono essere create o modificate:

- Controlli di testo supportano trascinamento della selezione per tutte le app compilate iOS 11, senza operazioni aggiuntive.
- Viste delle tabelle e viste di raccolta includono miglioramenti in iOS 11 che semplificano l'aggiunta di trascinare e rilasciare il comportamento.
- Per il supporto di trascinamento e rilascio con un'ulteriore personalizzazione, è possibile eseguire qualsiasi altra visualizzazione.

Quando l'aggiunta di trascinamento e rilascio supportano alle App, è possibile fornire livelli diversi di contenuto fedeltà; ad esempio, è possibile fornire un testo formattato e versione in testo normale dei dati in modo che l'applicazione ricevente può scegliere che si integra con meglio la destinazione del trascinamento. È anche possibile personalizzare la visualizzazione di trascinamento, nonché per abilitare il trascinamento di più elementi in una sola volta.

## <a name="drag-and-drop-with-text-controls"></a>Trascinare e rilasciare i controlli di testo

`UITextView` e `UITextField` supportano automaticamente il testo selezionato out, trascinamento della selezione del contenuto in.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Trascinamento della selezione con UITableView

`UITableView` supporta la gestione incorporata per trascinare e rilasciare le interazioni con le righe di tabella, che richiede solo alcuni metodi per abilitare il comportamento predefinito.

Sono disponibili due interfacce per:

- `IUITableViewDragDelegate` : Informazioni di pacchetti quando viene avviato un trascinamento nella visualizzazione tabella.
- `IUITableViewDropDelegate` – Elabora le informazioni quando è in corso un calo tentato e completate.

Nel [DragAndDropTableView esempio](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/) queste due interfacce vengono entrambi implementate nel `UITableViewController` (classe), con il delegato e l'origine dati. Cui sono state assegnate le `ViewDidLoad` metodo:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

Il codice minimo necessario per queste due interfacce è illustrato di seguito.

### <a name="table-view-drag-delegate"></a>Tabella vista trascinare delegato

L'unico metodo _obbligatorio_ per supportare il trascinamento di una riga dalla visualizzazione di una tabella è `GetItemsForBeginningDragSession`. Se l'utente inizia a trascinare una riga, questo metodo verrà chiamato.

Di seguito è riportata un'implementazione. Recupera i dati associati alla riga trascinata, viene eseguita la codifica e configura un `NSItemProvider` che determina il modo in cui le applicazioni gestirà la parte "drop" dell'operazione (ad esempio, se possibile gestire il tipo di dati, `PlainText`, nell'esempio):

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

Sono disponibili molti metodi facoltativi sul trascinamento delegato che può essere implementato per personalizzare il comportamento di trascinamento, ad esempio fornire più rappresentazioni di dati che possono essere sfruttate nelle app di destinazione (ad esempio testo formattato come anche come testo normale o di un vettore e bitmap le versioni di un disegno). È anche possibile fornire le rappresentazioni di dati personalizzati da utilizzare durante il trascinamento della selezione all'interno dell'app stessa.

### <a name="table-view-drop-delegate"></a>Delegato di rilascio visualizzazione tabella

I metodi sul delegato rilascio vengono chiamati quando un'operazione di trascinamento viene eseguita tramite una vista tabella o completa sopra di esso. I metodi richiesti determinano se i dati sono consentiti da eliminare, e le azioni eseguite se l'eliminazione è stata completata:

- `CanHandleDropSession` : Durante il trascinamento è in corso e potenzialmente l'eliminazione dell'applicazione, questo metodo determina se i dati trascinati può essere eliminato.
- `DropSessionDidUpdate` : Durante l'operazione di trascinamento è in corso, questo metodo viene chiamato per determinare quale azione è destinato. Informazioni di visualizzazione della tabella viene trascinato sul, la sessione di trascinamento e il percorso di indice possibili tutti utilizzabile per determinare il comportamento e fornito all'utente indicazioni visive.
- `PerformDrop` : Quando l'utente completa il rilascio (sollevare il dito), questo metodo estrae i dati trascinati e modifica la vista della tabella per aggiungere i dati in una nuova riga (o righe).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` indica se la vista della tabella può accettare dati trascinati. In questo frammento di codice, `CanLoadObjects` viene usato per confermare che la tabella visualizzazione può accettare dati di tipo stringa.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

Il `DropSessionDidUpdate` metodo viene chiamato più volte durante l'operazione di trascinamento è in corso, per fornire indicazioni visive per l'utente.

Il codice riportato di seguito, `HasActiveDrag` viene utilizzato per determinare se l'operazione ha avuto origine nella visualizzazione tabella corrente. In questo caso, sono consentite solo singole righe da spostare.
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

Il `PerformDrop` metodo viene chiamato quando l'utente completa l'operazione e modifica l'origine dati e visualizzazione della tabella per riflettere i dati eliminati.

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

### <a name="testing-drag-and-drop"></a>Trascinamento test

È necessario utilizzare un iPad per testare il [esempio](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/).
Aprire il file di esempio insieme a un'altra applicazione (ad esempio le note) e trascinare le righe e il testo tra di essi:

![schermata dell'operazione di trascinamento in corso](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Collegamenti correlati

- [Trascinamento della selezione delle linee guida dell'interfaccia umana (mela)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Trascinamento della selezione anteprima tabella](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [Trascinamento della selezione raccolta visualizzare l'esempio](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [Introduzione a trascinare e rilasciare (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Trascinamento della selezione con la raccolta e di visualizzazione per la tabella (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/223/)
