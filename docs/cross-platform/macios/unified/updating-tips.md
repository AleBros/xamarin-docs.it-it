---
title: Suggerimenti per l'aggiornamento del codice per l'API unificata
description: Questo documento illustra gli errori comuni e i suggerimenti utili quando si aggiorna un'applicazione per usare la API unificata di Novell.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: davidortinau
ms.author: daortin
ms.openlocfilehash: ee207ffc83f887e9c86c650b6f93fc2ff9f5b69c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014985"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Suggerimenti per l'aggiornamento del codice per l'API unificata

Quando si aggiornano soluzioni Novell obsolete alla API unificata, potrebbero verificarsi i seguenti errori.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException non è riuscito a trovare l'errore dello storyboard

È presente un [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) nella versione corrente di Visual Studio per Mac che può verificarsi dopo l'uso dello strumento di migrazione automatica per convertire il progetto in API unificate. Dopo l'aggiornamento, se viene ricevuto un messaggio di errore nel formato:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Per risolvere questo problema, è possibile eseguire le operazioni seguenti per individuare il file di destinazione di compilazione seguente:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

In questo file è necessario trovare la dichiarazione di destinazione seguente:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

E aggiungere l'attributo `DependsOnTargets="_CollectBundleResources"`. analogamente a quanto segue:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Salvare il file, riavviare Visual Studio per Mac ed eseguire una ricompilazione pulita & del progetto. Una correzione per questo problema dovrebbe essere rilasciata da Novell a breve.

## <a name="useful-tips"></a>Suggerimenti utili

Dopo aver utilizzato lo strumento di migrazione, è possibile che vengano visualizzati alcuni errori del compilatore che richiedono un intervento manuale.
Alcuni elementi che potrebbero dover essere corretti manualmente includono:

- Il confronto di `enum`s potrebbe richiedere un cast di `(int)`.

- `NSDictionary.IntValue` ora restituisce un `nint`, è possibile usare un `Int32Value`.

- non è possibile contrassegnare i tipi `nfloat` e `nint` `const`; `static readonly nint` è un'alternativa ragionevole.

- Gli elementi che venivano usati direttamente nello spazio dei nomi `MonoTouch.` ora sono generalmente nello spazio dei nomi `ObjCRuntime.`, ad esempio `MonoTouch.Constants.Version` è ora `ObjCRuntime.Constants.Version`.

- Il codice che serializza gli oggetti può interrompersi durante il tentativo di serializzare i tipi `nint` e `nfloat`. Assicurarsi di controllare che il codice di serializzazione funzioni come previsto dopo la migrazione.

- In alcuni casi lo strumento automatizzato non è in `#if #else` direttive del compilatore condizionale. In questo caso sarà necessario apportare le correzioni manualmente (vedere gli errori comuni riportati di seguito).

- I metodi esportati manualmente usando `[Export]` potrebbero non essere corretti automaticamente dallo strumento di migrazione, ad esempio in questo codice Snippert è necessario aggiornare manualmente il tipo restituito in `nfloat`:

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- Il API unificata non fornisce una conversione implicita tra NSDate e .NET DateTime perché non si tratta di una conversione senza perdita di perdite. Per evitare errori correlati a `DateTimeKind.Unspecified` convertire il `DateTime` .NET in locale o UTC prima di eseguire il cast in `NSDate`.

- I metodi di categoria Objective-C vengono ora generati come metodi di estensione nel API unificata. Il codice usato in precedenza `UIView.DrawString` ora, ad esempio, fa riferimento a `NSString.DrawString` nel API unificata.

- Il codice che usa le classi AVFoundation con `VideoSettings` deve essere modificato in modo da usare la proprietà `WeakVideoSettings`. Questa operazione richiede un `Dictionary`, disponibile come proprietà nelle classi di impostazioni, ad esempio:

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- Il costruttore `.ctor(IntPtr)` NSObject è stato modificato da pubblico a protetto ([per evitare un uso non corretto](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

- `NSAction` è stato [sostituito](~/cross-platform/macios/unified/overview.md#NSAction) con .NET `Action`standard. Alcuni delegati semplici (singoli parametri) sono stati sostituiti anche con `Action<T>`.

Infine, fare riferimento alle [differenze della versione classica v API unificata](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) per cercare le modifiche apportate alle API nel codice. La ricerca in [Questa pagina](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) consente di trovare le API classiche e gli elementi a cui sono stati aggiornati.

> [!NOTE]
> Lo spazio dei nomi `MonoTouch.Dialog` rimane invariato dopo la migrazione. Se il codice usa **MonoTouch. Dialog** è necessario continuare a usare tale spazio dei nomi. *non* modificare `MonoTouch.Dialog` in `Dialog`.

## <a name="common-compiler-errors"></a>Errori comuni del compilatore

Di seguito sono riportati altri esempi di errori comuni, insieme alla soluzione:

**Errore errore CS0012: il tipo ' MonoTouch. UIKit. UIView ' è definito in un assembly a cui non si fa riferimento.**

Correzione: in genere significa che il progetto fa riferimento a un componente o a un pacchetto NuGet che non è stato compilato con l'API unificata. È necessario eliminare e aggiungere nuovamente tutti i componenti e i pacchetti NuGet. Se l'errore non viene risolto, è possibile che la libreria esterna non supporti ancora il API unificata.

**Errore MT0034: non è possibile includere sia ' MonoTouch. dll ' che ' Xamarin.iOS. dll ' nello stesso progetto Xamarin.iOS. viene fatto riferimento in modo esplicito a' Xamarin.iOS. dll ', mentre ' MonoTouch. dll ' fa riferimento a' Xamarin.mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null '.**

Correzione: eliminare il componente che causa questo errore e aggiungerlo di nuovo al progetto.

**Errore CS0234: il tipo o il nome dello spazio dei nomi "Foundation" non esiste nello spazio dei nomi "MonoTouch". Manca un riferimento a un assembly?**

Correzione: lo strumento di migrazione automatizzata in Visual Studio per Mac *dovrebbe* aggiornare tutti i riferimenti `MonoTouch.Foundation` a `Foundation`, tuttavia in alcuni casi questi dovranno essere aggiornati manualmente. È possibile che vengano visualizzati errori simili per gli altri spazi dei nomi contenuti in precedenza in `MonoTouch`, ad esempio `UIKit`.

**Errore CS0266: non è possibile convertire in modo implicito il tipo ' Double ' in ' System. float '**

Correzione: modificare il tipo e il cast in `nfloat`. Questo errore può verificarsi anche per gli altri tipi con equivalenti a 64 bit (ad esempio `nint`)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Errore CS0266: non è possibile convertire in modo implicito il tipo ' CoreGraphics. CGRect ' in ' System. Drawing. RectangleF '. Esiste una conversione esplicita (manca un cast?)**

Correzione: modificare le istanze in `RectangleF` `CGRect`, `SizeF` `CGSize`e `PointF` `CGPoint`. Il `using System.Drawing;` dello spazio dei nomi deve essere sostituito con `using CoreGraphics;` (se non è già presente).

**errore CS1502: la corrispondenza migliore del metodo di overload per ' CoreGraphics. CGContext. SetLineDash (System. nFloat, System. nFloat [])' presenta alcuni argomenti non validi**

Correzione: modificare il tipo di matrice in `nfloat[]` ed eseguire il cast esplicito di `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Errore CS0115:' WordsTableSource. RowsInSection (UIKit. UITableView, int)' è contrassegnato come override, ma non è stato trovato alcun metodo adatto per eseguire l'override**

Correzione: modificare il valore restituito e i tipi di parametro in `nint`. Questa situazione si verifica in genere negli override dei metodi, ad esempio in `UITableViewSource`, tra cui `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`e così via.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Errore CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`: il tipo restituito deve essere ' System. nint ' in modo che corrisponda al membro sottoposto a override `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Correzione: quando il tipo restituito viene modificato in `nint`, eseguire il cast del valore restituito a `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Errore CS1061: il tipo ' CoreGraphics. CGPath ' non contiene una definizione per ' AddElipseInRect '**

Correzione: correggere l'ortografia in `AddEllipseInRect`. Altre modifiche al nome includono:

- Modificare ' color. Black ' in `NSColor.Black`.
- Modificare MapKit ' AddAnnotation ' in `AddAnnotations`.
- Modificare AVFoundation ' DataUsingEncoding ' in `Encode`.
- Modificare AVFoundation ' AVMetadataObject. TypeQRCode ' in `AVMetadataObjectType.QRCode`.
- Modificare AVFoundation ' VideoSettings ' in `WeakVideoSettings`.
- Modificare PopViewControllerAnimated in `PopViewController`.
- Modificare CoreGraphics ' CGBitmapContext. SetRGBFillColor ' in `SetFillColor`.

**Errore CS0546: non è possibile eseguire l'override perché' MapKit. MKAnnotation. coordinate ' non ha una funzione di accesso set sottoponibile a override (CS0546)**

Quando si crea un'annotazione personalizzata mediante la sottoclasse MKAnnotation, il campo delle coordinate non ha alcun setter, bensì solo un getter.

[Correzione](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

- Aggiungere un campo per tenere traccia della coordinata
- restituisce questo campo nel getter della proprietà della coordinata
- Eseguire l'override del metodo secoordinate e impostare il campo
- Chiamare la coordinata nel ctor con il parametro della coordinata passato

Il file dovrebbe essere simile al seguente:

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [Aggiornamento di app](~/cross-platform/macios/unified/updating-apps.md)
- [Aggiornamento di app iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Aggiornamento di app Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Aggiornamento delle app Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento di binding](~/cross-platform/macios/unified/update-binding.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
