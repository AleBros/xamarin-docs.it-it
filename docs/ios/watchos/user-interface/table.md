---
title: Controlli tabella watchos in Novell
description: Questo documento descrive come usare i controlli tabella watchos in Novell. Viene illustrata l'aggiunta di una tabella, l'aggiunta di un controller di riga, la creazione e il popolamento di righe, la risposta ai tocchi e altro ancora.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 2bed40c3ac2853a5f99c2b487e909164e12e676d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70766959"
---
# <a name="watchos-table-controls-in-xamarin"></a>Controlli tabella watchos in Novell

Il controllo `WKInterfaceTable` watchos è molto più semplice rispetto alla controparte iOS, ma esegue un ruolo simile. Viene creato un elenco scorrevole di righe che possono avere layout personalizzati e che rispondono agli eventi di tocco.

![](table-images/table-list-sml.png "Elenco tabella espressioni di controllo") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Aggiunta di una tabella

Trascinare il controllo **tabella** in una scena. Per impostazione predefinita, l'aspetto sarà simile al seguente (che mostra un singolo layout di riga non specificato):

[![](table-images/add-table-sml.png "Adding a table")](table-images/add-table.png#lightbox)

Assegnare alla tabella un nome nella casella **nome** del riquadro **Proprietà** , in modo che sia possibile farvi riferimento nel codice.

## <a name="add-a-row-controller"></a>Aggiungere un controller di riga

La tabella include automaticamente una singola riga, rappresentata da un controller di riga che contiene un controllo **gruppo** per impostazione predefinita.

Per impostare la **classe** per il controller di riga, selezionare la riga nella **struttura del documento** e digitare il nome di una classe nel riquadro delle **proprietà** :

[![](table-images/add-row-controller-sml.png "Entering a class name in the Properties pad")](table-images/add-row-controller.png#lightbox)

Una volta impostata la classe per il controller della riga, l'IDE creerà un file corrispondente C# nel progetto. Trascinare i controlli (ad esempio le etichette) nella riga e assegnare loro i nomi in modo che possano essere definiti nel codice.

## <a name="create-and-populate-rows"></a>Creazione e popolamento di righe

`SetNumberOfRows` crea le classi del controller di riga per ogni riga, usando il `Identifier` per selezionare quella corretta. Se il controller di riga è stato assegnato a un `Identifier` personalizzato, modificare il **valore predefinito** nel frammento di codice riportato di seguito nell'identificatore utilizzato. Il `RowController` *per ogni riga* viene creato quando viene chiamato `SetNumberOfRows` e la tabella viene visualizzata.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
    // loads row controller by identifier
```

> [!IMPORTANT]
> Le righe della tabella non sono virtualizzate come sono in iOS. Provare a limitare il numero di righe (Apple consiglia meno di 20).

Una volta create le righe, è necessario popolare ogni cella, ad esempio `GetCell` in iOS. Questo frammento di codice dell' [esempio WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) aggiorna l'etichetta in ogni riga

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Se si utilizza `SetNumberOfRows` e quindi si esegue il ciclo utilizzando `GetRowController`, l'intera tabella verrà inviata all'espressione di controllo. Nelle visualizzazioni successive della tabella, se è necessario aggiungere o rimuovere righe specifiche, utilizzare `InsertRowsAt` e `RemoveRowsAt` per ottenere prestazioni migliori.

## <a name="respond-to-taps"></a>Rispondi ai rubinetti

È possibile rispondere alla selezione delle righe in due modi diversi:

- implementare il metodo `DidSelectRow` sul controller di interfaccia oppure
- creare un segue nello storyboard e implementare `GetContextForSegue` se si desidera che la selezione delle righe apra un'altra scena.

### <a name="didselectrow"></a>DidSelectRow

Per gestire la selezione delle righe a livello di codice, implementare il metodo `DidSelectRow`. Per aprire una nuova scena, usare `PushController` e passare l'identificatore della scena e il contesto dei dati da usare:

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
Assicurarsi di selezionare il segue e assegnargli un identificatore nel riquadro delle **Proprietà** , ad esempio `secondLevel` nell'esempio riportato di seguito.

Nel controller di interfaccia implementare il metodo `GetContextForSegue` e restituire il contesto dei dati che deve essere fornito alla scena presentata da segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Questi dati vengono passati alla scena dello storyboard di destinazione nel metodo `Awake`.

## <a name="multiple-row-types"></a>Tipi di riga multipli

Per impostazione predefinita, il controllo tabella dispone di un solo tipo di riga che è possibile progettare. Per aggiungere più righe ' templates ', utilizzare la casella **Rows** nel riquadro delle **Proprietà** per creare più controller di riga:

![](table-images/prototype-rows1.png "Setting the number of Prototype rows")

Se si imposta la proprietà **Rows** su **3** , vengono creati segnaposto di riga aggiuntivi in cui trascinare i controlli. Per ogni riga, impostare il nome della **classe** nel riquadro delle **Proprietà** per assicurarsi che venga creata la classe del controller di riga.

![](table-images/prototype-rows2.png "The prototype rows in the designer")

Per popolare una tabella con tipi di riga diversi, utilizzare il metodo `SetRowTypes` per specificare il tipo di controller di riga da utilizzare per ogni riga della tabella. Usare gli identificatori della riga per specificare il controller di riga da usare per ogni riga.

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

![](table-images/table-scroll-sml.png "Esempio di paging verticale di dettagli") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Questa funzionalità è attualmente disponibile solo modificando lo storyboard in Xcode Interface Builder.

Per abilitare questa funzionalità, selezionare il `WKInterfaceTable` nell'area di progettazione e selezionare l'opzione di **paging verticale dei dettagli** :

![](table-images/vertical-detail-paging-sml.png "Selecting the Vertical Detail Paging option")

Come [spiegato da Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) , l'esplorazione della tabella deve usare gli elementi segue per il funzionamento della funzionalità di paging. Riscrivere il codice esistente che usa `PushController` per usare invece gli elementi segue.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Appendice: esempio di codice di un controller di riga

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

L'altro file con **estensione designer.cs** è una definizione di classe parziale che contiene gli Outlet e le azioni creati nell'area di progettazione, ad esempio questo esempio con un controllo `WKInterfaceLabel`:

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
