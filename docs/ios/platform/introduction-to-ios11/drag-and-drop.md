---
title: Trascinamento della selezione in Novell. iOS
description: Questo documento descrive come implementare il trascinamento della selezione nelle app Novell. iOS usando le API introdotte in iOS 11. In particolare, viene illustrato come abilitare il trascinamento della selezione in UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/05/2017
ms.openlocfilehash: 928936815c89dd74d0ad3775f59ea210702c8857
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304849"
---
# <a name="drag-and-drop-in-xamarinios"></a>Trascinamento della selezione in Novell. iOS

_Implementazione del trascinamento della selezione per iOS 11_

iOS 11 include il supporto per il trascinamento della selezione per copiare i dati tra le applicazioni nell'iPad. Gli utenti possono selezionare e trascinare tutti i tipi di contenuto dalle app posizionate side-by-side oppure trascinando l'icona di un'app che attiverà l'apertura dell'app e consentirà l'eliminazione dei dati:

![Esempio di trascinamento della selezione dall'app personalizzata all'app note](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Il trascinamento della selezione è disponibile solo all'interno della stessa app in iPhone.

Provare a supportare le operazioni di trascinamento della selezione ovunque sia possibile creare o modificare il contenuto:

- I controlli di testo supportano il trascinamento della selezione per tutte le app compilate con iOS 11, senza alcuna attività aggiuntiva.
- Le visualizzazioni delle tabelle e delle raccolte includono miglioramenti in iOS 11 che semplificano l'aggiunta del comportamento di trascinamento della selezione.
- Qualsiasi altra visualizzazione può essere eseguita per supportare il trascinamento della selezione con personalizzazione aggiuntiva.

Quando si aggiunge il supporto per il trascinamento della selezione alle app, è possibile fornire diversi livelli di fedeltà del contenuto; ad esempio, è possibile specificare una versione di testo formattata e testo normale dei dati, in modo che l'app ricevente possa scegliere la soluzione più adatta alla destinazione di trascinamento. È anche possibile personalizzare la visualizzazione del trascinamento e anche per abilitare il trascinamento di più elementi contemporaneamente.

## <a name="drag-and-drop-with-text-controls"></a>Trascinamento della selezione con controlli testo

`UITextView` e `UITextField` supportano automaticamente il trascinamento del testo selezionato e l'eliminazione del contenuto di testo in.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Trascinamento della selezione con UITableView

`UITableView` dispone di gestione incorporata per le interazioni di trascinamento della selezione con righe di tabella, richiedendo solo alcuni metodi per abilitare il comportamento predefinito.

Sono necessarie due interfacce:

- `IUITableViewDragDelegate`: crea un pacchetto di informazioni quando viene avviato un trascinamento nella visualizzazione tabella.
- `IUITableViewDropDelegate`: elabora le informazioni durante il tentativo e il completamento di un rilascio.

Nell' [esempio DragAndDropTableView](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview) queste due interfacce sono entrambe implementate nella classe `UITableViewController`, insieme al delegato e all'origine dati. Sono assegnati nel metodo `ViewDidLoad`:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

Il codice minimo necessario per queste due interfacce è illustrato di seguito.

### <a name="table-view-drag-delegate"></a>Delega di trascinamento vista tabella

L'unico metodo _necessario_ per supportare il trascinamento di una riga da una vista tabella è `GetItemsForBeginningDragSession`. Se l'utente inizia a trascinare una riga, questo metodo verrà chiamato.

Di seguito è riportata un'implementazione di. Recupera i dati associati alla riga trascinata, li codifica e configura un `NSItemProvider` che determina il modo in cui le applicazioni gestiranno la parte "drop" dell'operazione (ad esempio, se possono gestire il tipo di dati, `PlainText`nell'esempio):

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

Sono disponibili molti metodi facoltativi sul delegato di trascinamento che possono essere implementati per personalizzare il comportamento di trascinamento, ad esempio fornire più rappresentazioni di dati che possono essere sfruttate nelle app di destinazione (ad esempio testo formattato, testo normale o vettore e versioni bitmap di un disegno). È anche possibile fornire rappresentazioni di dati personalizzate da usare durante il trascinamento e l'eliminazione all'interno della stessa app.

### <a name="table-view-drop-delegate"></a>Delega visualizzazione tabella

I metodi sul delegato Drop vengono chiamati quando si verifica un'operazione di trascinamento in una vista tabella o viene completata al di sopra di esso. I metodi obbligatori determinano se i dati possono essere eliminati e quali azioni vengono eseguite se il rilascio è completato:

- `CanHandleDropSession`: mentre è in corso un trascinamento e potenzialmente rilasciata sull'applicazione, questo metodo determina se i dati trascinati possono essere eliminati.
- `DropSessionDidUpdate`: mentre è in corso il trascinamento, questo metodo viene chiamato per determinare l'azione desiderata. Le informazioni della vista tabella trascinate, la sessione di trascinamento e il percorso di indice possibile possono essere utilizzate per determinare il comportamento e il feedback visivo forniti all'utente.
- `PerformDrop`: quando l'utente completa il rilascio (sollevando il dito), questo metodo estrae i dati trascinati e modifica la visualizzazione tabella per aggiungere i dati in una nuova riga o righe.

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` indica se la visualizzazione tabella può accettare i dati trascinati. In questo frammento di codice, `CanLoadObjects` viene usato per confermare che questa visualizzazione tabella può accettare dati di stringa.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

Il metodo `DropSessionDidUpdate` viene chiamato ripetutamente mentre è in corso l'operazione di trascinamento per fornire segnali visivi all'utente.

Nel codice riportato di seguito, `HasActiveDrag` viene utilizzato per determinare se l'operazione è stata originata nella visualizzazione tabella corrente. In tal caso, è consentito spostare solo righe singole.
Se il trascinamento è da un'altra origine, verrà indicata un'operazione di copia:

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

L'operazione DROP può essere una delle `Cancel`, `Move`o `Copy`.

L'obiettivo di rilascio può essere l'inserimento di una nuova riga o l'aggiunta o l'aggiunta di dati a una riga esistente.

#### <a name="performdrop"></a>PerformDrop

Il metodo `PerformDrop` viene chiamato quando l'utente completa l'operazione e modifica la visualizzazione della tabella e l'origine dati in modo da riflettere i dati eliminati.

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

È possibile aggiungere codice aggiuntivo per caricare in modo asincrono oggetti dati di grandi dimensioni.

### <a name="testing-drag-and-drop"></a>Test del trascinamento della selezione

Per testare l' [esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview), è necessario usare un iPad.
Aprire l'esempio insieme a un'altra app, ad esempio note, e trascinare le righe e il testo tra di essi:

![screenshot dell'operazione di trascinamento in corso](drag-and-drop-images/01-sml.png)

## <a name="related-links"></a>Collegamenti correlati

- [Trascinare e rilasciare le linee guida per l'interfaccia umana (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Esempio di visualizzazione della tabella di trascinamento della selezione](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [Esempio di trascinamento della selezione della vista raccolta](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [Introduzione a trascinamento della selezione (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Trascinamento della selezione con la raccolta e la visualizzazione tabella (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/223/)
