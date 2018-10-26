---
title: i controlli di tabella in Xamarin watchOS
description: Questo documento descrive come usare i controlli tabella watchOS in Xamarin. Illustra l'aggiunta di una tabella, aggiunta di un controller di riga, la creazione e popolamento di righe, risponde alle scelte e altro ancora.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: cd5e7299874bbfb1b652315a549b9d067d58e9a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109393"
---
# <a name="watchos-table-controls-in-xamarin"></a>i controlli di tabella in Xamarin watchOS

WatchOS `WKInterfaceTable` controllo è molto più semplice rispetto alla controparte iOS, ma esegue un ruolo simile. Crea un elenco scorrevole di righe che può avere i layout personalizzati, e che rispondono a eventi di tocco.

![](table-images/table-list-sml.png "Elenco di espressioni di controllo tabella") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Aggiunta di una tabella

Trascinare il **tabella** controllo in una scena. Per impostazione predefinita sarà simile a questo (che mostra un layout di singola riga non specificato):

[![](table-images/add-table-sml.png "Aggiunta di una tabella")](table-images/add-table.png#lightbox)

Assegnare un nome di tabella **delle proprietà** pad **nome** finestra, in modo che sia possibile farvi riferimento nel codice.

## <a name="add-a-row-controller"></a>Aggiungere un Controller di riga

La tabella include automaticamente una sola riga, rappresentata da un controller di riga che contiene un **gruppo** controllo per impostazione predefinita.

Per impostare il **classe** per il controller di riga, selezionare la riga nel **struttura documento** e digitare un nome di classe nel **proprietà** riempimento:

[![](table-images/add-row-controller-sml.png "Immettere un nome di classe nel riquadro delle proprietà")](table-images/add-row-controller.png#lightbox)

Dopo aver impostata la classe per il controller della riga, l'IDE creerà un corrispondente C# file del progetto. Trascinare i controlli (ad esempio etichette) nella riga e fornire loro i nomi in modo che è possibile farvi riferimento nel codice.




## <a name="create-and-populate-rows"></a>Creare e popolare le righe

`SetNumberOfRows` Consente di creare le classi controller la riga per ogni riga, usando il `Identifier` selezionare quella corretta. Se è stato assegnato il controller di riga personalizzato `Identifier`, cambiare **predefinito** nel frammento di codice seguente per l'identificatore è stato usato. Il `RowController` *per ogni riga* viene creato quando `SetNumberOfRows` viene chiamata e la tabella visualizzata.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> Le righe della tabella non sono virtualizzate, ad esempio si trovano in iOS. Provare a limitare il numero di righe (Apple consiglia minore di 20).

Dopo avere create le righe, è necessario popolare ogni cella (ad esempio `GetCell` verrebbero impiegati in iOS). Questo frammento di codice dal [WatchTables esempio](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) Aggiorna l'etichetta in ogni riga

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Usando `SetNumberOfRows` e quindi eseguire i cicli di uso `GetRowController` fa sì che l'intera tabella da inviare per l'espressione di controllo. Per le viste successive della tabella, se è necessario aggiungere o rimuovere le righe specifiche utilizzano `InsertRowsAt` e `RemoveRowsAt` per ottenere prestazioni migliori.


## <a name="respond-to-taps"></a>Rispondere alle scelte

È possibile rispondere alla selezione di righe in due modi diversi:

- implementare il `DidSelectRow` metodo nel controller di interfaccia, o
- creare un elemento segue sullo storyboard e implementare `GetContextForSegue` se si desidera che la selezione di righe per aprire un'altra scena.

### <a name="didselectrow"></a>DidSelectRow

Per gestire a livello di codice la selezione della riga, implementare il `DidSelectRow` (metodo). Per aprire una nuova scena, utilizzare `PushController` e passare l'identificatore della scena e il contesto dati da utilizzare:

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

Trascinare un elemento segue sullo storyboard dalla riga di tabella a un'altra scena (tenere premuto il **controllo** chiave durante il trascinamento).
Assicurarsi di selezionare l'elemento segue e assegnargli un identificatore **delle proprietà** pad (ad esempio `secondLevel` nell'esempio seguente).

Nel controller di interfaccia, implementare il `GetContextForSegue` (metodo) e restituiscono il contesto dei dati da fornire alla scena che viene presentata dall'elemento segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Questi dati vengono passati nella scena di storyboard di destinazione nella relativa `Awake` (metodo).

## <a name="multiple-row-types"></a>Più tipi di riga

Per impostazione predefinita il controllo della tabella ha un tipo di riga singola che è possibile progettare. Per aggiungere ulteriori modelli riga, come' ', usare il **righe** nella casella il **proprietà** pad per creare più controller di riga:

![](table-images/prototype-rows1.png "Impostazione del numero di righe di prototipo")

Impostando il **righe** proprietà **3** creerà i segnaposti di riga aggiuntiva per è possibile trascinare i controlli in. Per ogni riga, impostare il **classe** nel nome il **proprietà** pad per assicurarsi che la classe di controller di riga viene creata.

![](table-images/prototype-rows2.png "Le righe di prototipo nella finestra di progettazione")

Per popolare una tabella con uso di riga diversi tipi di `SetRowTypes` metodo per specificare il tipo di controller di riga da utilizzare per ogni riga della tabella. Usare gli identificatori di riga per specificare quale controller riga deve essere usato per ogni riga.

Il numero di elementi in questa matrice deve corrispondere al numero di righe che si prevede sia nella tabella:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Durante il popolamento di una tabella con più controller di riga, è necessario tenere traccia di quale tipo previsti quando si popola l'interfaccia utente:

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


## <a name="vertical-detail-paging"></a>Paging dettaglio verticale

watchOS 3 introdotta una nuova funzionalità per le tabelle: la possibilità di scorrere le pagine di dettagli relativi a ogni riga, senza dover tornare indietro alla tabella e scegliere un'altra riga. È possibile scorrere scorrendo rapidamente su e giù, o tramite il Crown digitali alle schermate dei dettagli.

![](table-images/table-scroll-sml.png "Esempio dettaglio Paging verticale") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Questa funzionalità è attualmente disponibile solo modificando lo storyboard di Interface Builder di Xcode.

Per abilitare questa funzionalità, selezionare la `WKInterfaceTable` sulla superficie di progettazione e dei segni di graduazione il **Paging dettaglio verticale** opzione:

![](table-images/vertical-detail-paging-sml.png "Selezionando l'opzione verticale dettaglio Paging")

Come [illustrata da Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) la navigazione della tabella è necessario usare gli elementi segue usare la funzionalità di paging. Riscrivere il codice esistente che utilizza `PushController` usare gli elementi segue invece.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Appendice: Esempio di codice Controller riga

L'IDE creerà due file di codice automaticamente quando viene creato un controller di riga nella finestra di progettazione. Il codice in questi file generati è illustrato di seguito per riferimento.

Il primo verrà denominato per la classe, ad esempio **RowController.cs**, simile al seguente:

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

L'altra **. designer.cs** file è una definizione di classe parziale che contiene il Outlet e azioni che vengono create nell'area di progettazione, ad esempio in questo esempio con uno `WKInterfaceLabel` controllo:

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

Il Outlet e azioni dichiarate possono farvi riferimento nel codice - tuttavia **. designer.cs** file non deve essere modificato direttamente.



## <a name="related-links"></a>Collegamenti correlati

- [WatchTables (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento di tabella di Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
