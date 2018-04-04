---
title: Controllo della tabella
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 3f596b424292dd0f4db08429bc7c5d4a885d3954
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="table-control"></a>Controllo della tabella

Il watchOS `WKInterfaceTable` controllo è molto più semplice rispetto alla controparte iOS, ma esegue un ruolo simile. Crea un elenco di scorrimento di righe che possono essere layout personalizzati e cui rispondere a eventi di tocco.

![](table-images/table-list-sml.png "Elenco di tabelle di espressioni di controllo") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Aggiunta di una tabella

Trascinare il **tabella** controllo in una scena. Per impostazione predefinita sarà simile al seguente (con un layout di riga non specificato):

[![](table-images/add-table-sml.png "Aggiunta di una tabella")](table-images/add-table.png#lightbox)

Assegna un nome di tabella nel **proprietà** pad **nome** casella, in modo che sia possibile farvi riferimento nel codice.

## <a name="add-a-row-controller"></a>Aggiungere un Controller di riga

La tabella include automaticamente una sola riga, rappresentata da un controller di riga che contiene un **gruppo** controllo per impostazione predefinita.

Per impostare il **classe** per il controller di riga, selezionare la riga nel **struttura documento** e digitare un nome di classe nel **proprietà** riempimento:

[![](table-images/add-row-controller-sml.png "Immettere un nome di classe nel riquadro proprietà")](table-images/add-row-controller.png#lightbox)

Dopo aver impostata la classe per il controller della riga, l'IDE creerà un file c# corrispondente nel progetto. Trascinare i controlli (ad esempio etichette) nella riga e fornire loro i nomi in modo è possibile farvi riferimento nel codice.




## <a name="create-and-populate-rows"></a>Creare e popolare le righe

`SetNumberOfRows` Consente di creare classi controller la riga per ogni riga, con la `Identifier` per selezionare quello corretto. Se il controller di riga è assegnato un oggetto personalizzato `Identifier`, modificare **predefinito** nel frammento di codice seguente per l'identificatore è utilizzato. Il `RowController` *per ogni riga* viene creato quando `SetNumberOfRows` viene chiamato e la tabella.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> Le righe della tabella non siano virtualizzate, ad esempio si trovano in iOS. Provare a limitare il numero di righe (Apple consiglia inferiore a 20).

Dopo avere create le righe, è necessario popolare ogni cella (ad esempio `GetCell` farebbe in iOS). Questo frammento di codice il [WatchTables esempio](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) Aggiorna l'etichetta in ogni riga

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Utilizzo `SetNumberOfRows` e quindi ciclo viene eseguito mediante `GetRowController` fa sì che l'intera tabella da inviare per le espressioni di controllo. Le viste successive della tabella, se si desidera aggiungere o rimuovere righe specifiche utilizzano `InsertRowsAt` e `RemoveRowsAt` per ottenere prestazioni migliori.


## <a name="respond-to-taps"></a>Rispondere alle scelte

È possibile rispondere alla selezione di riga in due modi diversi:

- implementare il `DidSelectRow` metodo nel controller di interfaccia, o
- creare un segue nello storyboard e implementare `GetContextForSegue` se si desidera che la selezione di righe per aprire un'altra scena.

### <a name="didselectrow"></a>DidSelectRow

Per gestire a livello di codice la selezione della riga, implementare il `DidSelectRow` metodo. Per aprire una nuova scena, utilizzare `PushController` e passare l'identificatore della scena e il contesto dei dati da utilizzare:

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

Trascinare un segue nello storyboard la riga della tabella a un'altra scena (tenere premuto il **controllo** chiave durante il trascinamento).
Assicurarsi di selezionare il segue e assegnargli un identificatore **proprietà** riempimento (ad esempio `secondLevel` nell'esempio seguente).

Nel controller di interfaccia, implementare il `GetContextForSegue` metodo e restituire il contesto dei dati che deve essere fornito alla scena presentato dalla segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Questi dati vengono passati alla scena storyboard destinazione nella relativa `Awake` metodo.

## <a name="multiple-row-types"></a>Più tipi di riga

Per impostazione predefinita il controllo tabella include un tipo di riga singola che è possibile progettare. Per aggiungere ulteriori righe 'modelli' utilizzano il **righe** casella il **proprietà** riempimento per creare più controller di riga:

![](table-images/prototype-rows1.png "Impostazione del numero di righe di prototipo")

L'impostazione di **righe** proprietà **3** creerà segnaposto riga aggiuntiva è possibile trascinare i controlli in. Per ogni riga, impostare il **classe** nome il **proprietà** pad per verificare la classe controller riga viene creata.

![](table-images/prototype-rows2.png "Le righe di prototipo nella finestra di progettazione")

Per popolare una tabella con righe diversi tipi di `SetRowTypes` per specificare il tipo di controller di riga da utilizzare per ogni riga della tabella. Utilizzare gli identificatori di riga per specificare il controller di riga deve essere utilizzato per ogni riga.

Il numero di elementi nella matrice deve corrispondere al numero di righe che si prevede che nella tabella:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Durante il popolamento di una tabella con più controller di riga, è necessario tenere traccia di quale tipo di cui si prevede di compilare l'interfaccia utente:

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```


## <a name="vertical-detail-paging"></a>Spostamento verticale di dettaglio

watchOS 3 introdotta una nuova funzionalità per le tabelle: la possibilità di scorrere le pagine di dettaglio correlati a ogni riga, senza dover tornare alla tabella e scegliere un'altra riga. Passaggio di alto e verso il basso o utilizzando il chioma digitale, è possono scorrere le schermate di dettaglio.

![](table-images/table-scroll-sml.png "Esempio di dettaglio Paging verticale") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Questa funzionalità è attualmente disponibile solo modificando lo storyboard in Xcode interfaccia generatore.

Per abilitare questa funzionalità, selezionare il `WKInterfaceTable` sulla superficie di progettazione e segni di graduazione di **Paging dettaglio verticale** opzione:

![](table-images/vertical-detail-paging-sml.png "Selezionare l'opzione verticale dettaglio Paging")

Come [spiegati da Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) la navigazione della tabella è necessario utilizzare segues usare la funzionalità di paging. Riscrivere il codice esistente che utilizza `PushController` utilizzare segues invece.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Appendice: Esempio di codice Controller riga

L'IDE crea due file di codice automaticamente quando viene creato un controller di riga nella finestra di progettazione. In questi file generati il codice è illustrato di seguito per riferimento.

Il primo verrà denominato per la classe, ad esempio **RowController.cs**, come segue:

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

L'altro **. designer.cs** file è una definizione di classe parziale che contiene i punti vendita e le azioni che vengono create nell'area di progettazione, ad esempio in questo esempio con uno `WKInterfaceLabel` controllo:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

I punti vendita e le azioni dichiarate qui è possibile fare riferimento nel codice, ma la **. designer.cs** file non deve essere modificato direttamente.



## <a name="related-links"></a>Collegamenti correlati

- [WatchTables (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento di tabella di Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
