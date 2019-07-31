---
title: Personalizzazione dell'aspetto di una tabella in Novell. iOS
description: In questo documento viene descritto come personalizzare l'aspetto di una tabella in Novell. iOS. Vengono illustrati gli stili delle celle, gli accessori, i separatori di celle e i layout delle celle personalizzati.
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: cad7378e3d569454606a9d472cf30c42956553e4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644939"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>Personalizzazione dell'aspetto di una tabella in Novell. iOS

Il modo più semplice per modificare l'aspetto di una tabella consiste nell'usare uno stile di cella diverso. È possibile modificare lo stile di cella usato durante la `UITableViewSource`creazione di ogni cella nel metodo di. `GetCell`

## <a name="cell-styles"></a>Stili di cella

Sono disponibili quattro stili predefiniti:

-  **Predefinito** : supporta `UIImageView`.
-  **Sottotitolo** : supporta un `UIImageView` sottotitolo e.
-  **Value1** : sottotitolo allineato a destra `UIImageView`, supporta un.
-  **Value2** : il titolo è allineato a destra e il sottotitolo è allineato a sinistra (ma nessuna immagine).


Queste schermate mostrano come viene visualizzato ogni stile:

 [![](customizing-table-appearance-images/image7.png "Queste schermate mostrano come viene visualizzato ogni stile")](customizing-table-appearance-images/image7.png#lightbox)

Il **CellDefaultTable** di esempio contiene il codice per produrre queste schermate. Lo stile della cella viene impostato nel `UITableViewCell` costruttore, come segue:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

È possibile impostare le [proprietà supportate](xref:UIKit.UITableViewCell) dello stile della cella:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Accessori

Alle celle possono essere aggiunti gli accessori seguenti a destra della visualizzazione:

-   Segno di **spunta** : può essere usato per indicare la selezione multipla in una tabella.
-   **DetailButton** : risponde al tocco indipendentemente dal resto della cella, consentendo di eseguire una funzione diversa per toccare la cella stessa, ad esempio l'apertura di un popup o di una nuova finestra che non fa parte di `UINavigationController` uno stack.
-   **DisclosureIndicator** : normalmente usato per indicare che toccare la cella consente di aprire un'altra visualizzazione.
-   **DetailDisclosureButton** : combinazione di `DetailButton` e `DisclosureIndicator`.


Si tratta di un aspetto simile al seguente:

 [![](customizing-table-appearance-images/image8.png "Accessori di esempio")](customizing-table-appearance-images/image8.png#lightbox)

Per visualizzare uno di questi accessori, è possibile impostare `Accessory` la proprietà `GetCell` nel metodo:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

Quando si visualizzano `DetailDisclosureButton` `AccessoryButtonTapped` o, è necessario eseguire l'override di anche per eseguire un'azione quando viene toccato. `DetailButton`

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

Il **CellAccessoryTable** di esempio mostra un esempio di uso degli accessori.

## <a name="cell-separators"></a>Separatori di celle

I separatori di celle sono celle di tabella utilizzate per separare la tabella. Le proprietà vengono impostate nella tabella.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

È anche possibile aggiungere un effetto sfocatura o brillantezza al separatore:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

Il separatore può includere anche un oggetto Insert:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>Creazione di layout di celle personalizzati

Per modificare lo stile di visualizzazione di una tabella, è necessario fornire le celle personalizzate da visualizzare. Per la cella personalizzata possono essere presenti colori e layout di controllo diversi.

L'esempio CellCustomTable implementa una `UITableViewCell` sottoclasse che definisce un layout personalizzato `UILabel`di e un `UIImage` con tipi di carattere e colori diversi. Le celle risultanti hanno un aspetto simile al seguente:

 [![](customizing-table-appearance-images/image9.png "Layout di celle personalizzati")](customizing-table-appearance-images/image9.png#lightbox)

La classe di celle personalizzata è costituita solo da tre metodi:

-   **Costruttore** : crea i controlli dell'interfaccia utente e imposta le proprietà di stile personalizzate, ad esempio tipo di carattere, dimensioni e colori.
-   **UpdateCell** : metodo `UITableView.GetCell` utilizzato da per impostare le proprietà della cella.
-   **LayoutSubviews** : consente di impostare il percorso dei controlli dell'interfaccia utente. Nell'esempio ogni cella presenta lo stesso layout, ma una cella più complessa, in particolare quelle con dimensioni variabili, potrebbe richiedere posizioni di layout diverse a seconda del contenuto visualizzato.


Il codice di esempio completo in **CellCustomTable > CustomVegeCell.cs** segue:

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

Per `GetCell` creare la cella `UITableViewSource` personalizzata è necessario modificare il metodo dell'oggetto:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```



## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
