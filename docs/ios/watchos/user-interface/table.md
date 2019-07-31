---
title: Controlli tabella watchos in Novell
description: Questo documento descrive come usare i controlli tabella watchos in Novell. Viene illustrata l'aggiunta di una tabella, l'aggiunta di un controller di riga, la creazione e il popolamento di righe, la risposta ai tocchi e altro ancora.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 079c72ee25f3b01c02b854a8a1eb258c885f2a96
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642801"
---
# <a name="watchos-table-controls-in-xamarin"></a>Controlli tabella watchos in Novell

Il `WKInterfaceTable` controllo watchos è molto più semplice rispetto alla relativa controparte iOS, ma esegue un ruolo simile. Viene creato un elenco scorrevole di righe che possono avere layout personalizzati e che rispondono agli eventi di tocco.

![](table-images/table-list-sml.png "Elenco tabella espressioni di controllo")![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Aggiunta di una tabella

Trascinare il controllo **tabella** in una scena. Per impostazione predefinita, l'aspetto sarà simile al seguente (che mostra un singolo layout di riga non specificato):

[![](table-images/add-table-sml.png "Aggiunta di una tabella")](table-images/add-table.png#lightbox)

Assegnare alla tabella un nome nella casella **nome** del riquadro **Proprietà** , in modo che sia possibile farvi riferimento nel codice.

## <a name="add-a-row-controller"></a>Aggiungere un controller di riga

La tabella include automaticamente una singola riga, rappresentata da un controller di riga che contiene un controllo **gruppo** per impostazione predefinita.

Per impostare la **classe** per il controller di riga, selezionare la riga nella **struttura del documento** e digitare il nome di una classe nel riquadro delle **proprietà** :

[![](table-images/add-row-controller-sml.png "Immissione di un nome di classe nel riquadro delle proprietà")](table-images/add-row-controller.png#lightbox)

Una volta impostata la classe per il controller della riga, l'IDE creerà un file corrispondente C# nel progetto. Trascinare i controlli (ad esempio le etichette) nella riga e assegnare loro i nomi in modo che possano essere definiti nel codice.




## <a name="create-and-populate-rows"></a>Creazione e popolamento di righe

`SetNumberOfRows`Crea le classi del controller di riga per ogni riga, `Identifier` utilizzando per selezionare quella corretta. Se è stato assegnato il controller di riga `Identifier`a un oggetto personalizzato, modificare il **valore predefinito** nel frammento di codice riportato di seguito nell'identificatore usato. Per ogni *riga* viene creato quando `SetNumberOfRows` viene chiamato il metodo e la tabella viene visualizzata. `RowController`

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> Le righe della tabella non sono virtualizzate come sono in iOS. Provare a limitare il numero di righe (Apple consiglia meno di 20).

Una volta create le righe, è necessario popolare ogni cella (ad esempio `GetCell` , in iOS). Questo frammento di codice dell' [esempio WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) aggiorna l'etichetta in ogni riga

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Se `SetNumberOfRows` si usa e quindi si esegue `GetRowController` il ciclo usando, l'intera tabella viene inviata all'espressione di controllo. Nelle visualizzazioni successive della tabella, se è necessario aggiungere o rimuovere righe specifiche, utilizzare `InsertRowsAt` e `RemoveRowsAt` per ottenere prestazioni migliori.


## <a name="respond-to-taps"></a>Rispondi ai rubinetti

È possibile rispondere alla selezione delle righe in due modi diversi:

- implementare il `DidSelectRow` metodo sul controller di interfaccia oppure
- creare un segue nello storyboard e implementare `GetContextForSegue` se si desidera che la selezione delle righe apra un'altra scena.

### <a name="didselectrow"></a>DidSelectRow

Per gestire la selezione delle righe a livello di `DidSelectRow` codice, implementare il metodo. Per aprire una nuova scena, usare `PushController` e passare l'identificatore della scena e il contesto dei dati da usare:

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

Trascinare un segue nello storyboard dalla riga della tabella in un'altra scena (tenendo premuto il tasto **CTRL** durante il trascinamento).
Assicurarsi di selezionare il segue e assegnargli un identificatore nel riquadro delle **Proprietà** , ad `secondLevel` esempio nell'esempio riportato di seguito.

Nel controller di interfaccia implementare il `GetContextForSegue` metodo e restituire il contesto dei dati che deve essere fornito alla scena presentata da segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Questi dati vengono passati alla scena dello storyboard di destinazione nel `Awake` relativo metodo.

## <a name="multiple-row-types"></a>Tipi di riga multipli

Per impostazione predefinita, il controllo tabella dispone di un solo tipo di riga che è possibile progettare. Per aggiungere più righe ' templates ', utilizzare la casella **Rows** nel riquadro delle **Proprietà** per creare più controller di riga:

![](table-images/prototype-rows1.png "Impostazione del numero di righe del prototipo")

Se si imposta la proprietà **Rows** su **3** , vengono creati segnaposto di riga aggiuntivi in cui trascinare i controlli. Per ogni riga, impostare il nome della **classe** nel riquadro delle **Proprietà** per assicurarsi che venga creata la classe del controller di riga.

![](table-images/prototype-rows2.png "Righe del prototipo nella finestra di progettazione")

Per popolare una tabella con tipi di riga diversi, `SetRowTypes` utilizzare il metodo per specificare il tipo di controller di riga da utilizzare per ogni riga della tabella. Usare gli identificatori della riga per specificare il controller di riga da usare per ogni riga.

Il numero di elementi in questa matrice deve corrispondere al numero di righe che si prevede siano nella tabella:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Quando si compila una tabella con più controller di riga, è necessario tenere traccia del tipo previsto durante la compilazione dell'interfaccia utente:

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


## <a name="vertical-detail-paging"></a>Paging dettagli verticali

watchos 3 ha introdotto una nuova funzionalità per le tabelle: la possibilità di scorrere le pagine dei dettagli relative a ogni riga, senza dover tornare alla tabella e scegliere un'altra riga. È possibile scorrere le schermate dei dettagli scorrendo verso l'alto e verso il basso o usando il Digital Crown.

![](table-images/table-scroll-sml.png "Esempio di paging verticale di dettagli")![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Questa funzionalità è attualmente disponibile solo modificando lo storyboard in Xcode Interface Builder.

Per abilitare questa funzionalità, selezionarla `WKInterfaceTable` nell'area di progettazione e selezionare l'opzione di **paging verticale dei dettagli** :

![](table-images/vertical-detail-paging-sml.png "Selezione dell'opzione di paging del dettaglio verticale")

Come [spiegato da Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) , l'esplorazione della tabella deve usare gli elementi segue per il funzionamento della funzionalità di paging. Riscrivere il codice esistente che usa `PushController` per usare gli elementi segue.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Appendice: Esempio di codice di controller di riga

L'IDE creerà automaticamente due file di codice quando viene creato un controller di riga nella finestra di progettazione. Il codice in questi file generati è riportato di seguito per riferimento.

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

L'altro file con **estensione designer.cs** è una definizione di classe parziale che contiene gli Outlet e le azioni creati nell'area di progettazione, ad esempio questo esempio con `WKInterfaceLabel` un controllo:

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

È possibile fare riferimento agli Outlet e alle azioni dichiarati nel codice. Tuttavia, il file con **estensione designer.cs** non deve essere modificato direttamente.



## <a name="related-links"></a>Collegamenti correlati

- [WatchTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento della tabella Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
