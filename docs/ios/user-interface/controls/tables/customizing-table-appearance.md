---
title: Personalizzazione dell'aspetto di una tabella
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 12314e6ec5951a5a094ed8b0a5536b450853c5fb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="customizing-a-tables-appearance"></a>Personalizzazione dell'aspetto di una tabella

Il modo più semplice per modificare l'aspetto di una tabella è di utilizzare uno stile di cella diversa. È possibile modificare quali stile di cella viene utilizzato durante la creazione di ogni cella di `UITableViewSource`del `GetCell` metodo.

## <a name="cell-styles"></a>Stili della cella

Esistono quattro stili predefiniti:

-  **Predefinito** : supporta un `UIImageView`.
-  **Sottotitolo** : supporta un `UIImageView` e sottotitolo.
-  **Valore1** : destra sottotitolo allineato, supporta un `UIImageView`.
-  **Value2** : titolo è allineato a destra e sottotitolo allineato a sinistra (ma alcuna immagine).


Questi screenshot mostrano come viene visualizzato ogni stile:

 [ ![](customizing-table-appearance-images/image7.png "Questi screenshot mostrano come viene visualizzato ogni stile")](customizing-table-appearance-images/image7.png)

L'esempio **CellDefaultTable** contiene il codice per generare queste schermate vengono visualizzate. Lo stile di cella è impostato `UITableViewCell` costruttore, analogo al seguente:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

[Proprietà supportate](http://developer.xamarin.com/api/type/UIKit.UITableViewCell/) della cella è quindi possibile impostare lo stile:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Accessori

Le celle possono essere i seguenti Accessori aggiunti a destra della vista:

-   **Segno di spunta** : può essere utilizzato per indicare a selezione multipla in una tabella.
-   **DetailButton** – risponde tocco in modo indipendente dal resto della cella, in modo che possa eseguire una funzione diversa per modificare la cella (ad esempio aprendo una finestra popup o una nuova finestra in cui non fa parte di un `UINavigationController` stack).
-   **DisclosureIndicator** : in genere usato per indicare che toccano cella verrà aperta un'altra visualizzazione.
-   **DetailDisclosureButton** -una combinazione del `DetailButton` e `DisclosureIndicator`.


Questo è l'aspetto:

 [ ![](customizing-table-appearance-images/image8.png "Accessori di esempio")](customizing-table-appearance-images/image8.png)

Per visualizzare uno di questi accessori è possibile impostare il `Accessory` proprietà il `GetCell` metodo:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

Quando il `DetailButton` o `DetailDisclosureButton` vengono visualizzati, è inoltre necessario eseguire l'override di `AccessoryButtonTapped` per eseguire un'azione quando è selezionata.

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

L'esempio **CellAccessoryTable** Mostra un esempio di utilizzo di accessori.

## <a name="cell-separators"></a>Separatori di cella

I separatori di cella sono celle della tabella utilizzata per separare la tabella. Le proprietà vengono impostate per la tabella.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

È anche possibile aggiungere un effetto di sfocatura o vitalità il separatore:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

Il separatore può inoltre avere un margine:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>Creazione di layout di cella personalizzato

Per modificare lo stile di visualizzazione di una tabella, che è necessario fornire le celle personalizzate per poter visualizzare. La cella personalizzata può avere diversi colori e il controllo di layout.

Nell'esempio CellCustomTable viene implementato un `UITableViewCell` sottoclasse che definisce un layout personalizzato di `UILabel`s e un `UIImage` con diversi tipi di carattere e colori. Le celle risultante è simile al seguente:

 [ ![](customizing-table-appearance-images/image9.png "Layout di cella personalizzato")](customizing-table-appearance-images/image9.png)

La classe di cella personalizzato è costituita da tre metodi:

-   **Costruttore** : crea controlli dell'interfaccia utente e imposta le proprietà di stile personalizzato (ad es. tipo di carattere, dimensioni e colori).
-   **UpdateCell** : un metodo per `UITableView.GetCell` da utilizzare per impostare le proprietà della cella.
-   **LayoutSubviews** : impostare la posizione dei controlli dell'interfaccia utente. Nell'esempio ogni cella ha lo stesso layout, ma una cella più complessa, in particolare quelli con dimensioni variabili, potrebbe essere necessario posizioni di layout diversi a seconda del contenuto viene visualizzato.


Il codice di esempio completo **CellCustomTable > CustomVegeCell.cs** segue:

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

Il `GetCell` metodo il `UITableViewSource` deve essere modificata per creare la cella personalizzata:

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

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
