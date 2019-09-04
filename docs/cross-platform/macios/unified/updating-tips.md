---
title: Suggerimenti per l'aggiornamento del codice per l'API unificata
description: Questo documento illustra gli errori comuni e i suggerimenti utili quando si aggiorna un'applicazione per usare la API unificata di Novell.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: 844730d2ace717b951df2d80b2add6d1094fe997
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226106"
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

E aggiungervi `DependsOnTargets="_CollectBundleResources"` l'attributo. analogamente a quanto segue:

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

- Il `enum`confronto di s potrebbe `(int)` richiedere un cast.

- `NSDictionary.IntValue`ora restituisce un `nint`oggetto, `Int32Value` che può essere usato in alternativa.

- `nfloat`i `nint` tipi e non possono `const`essere contrassegnati. `static readonly nint` è un'alternativa ragionevole.

- Gli `MonoTouch.` `MonoTouch.Constants.Version` `ObjCRuntime.Constants.Version`elementi che erano utilizzati direttamente nello spazio dei nomi sono ora generalmente nello spazio dei nomi (ad esempio, è ora). `ObjCRuntime.`

- Il codice che serializza gli oggetti può interrompersi durante il `nint` tentativo `nfloat` di serializzare i tipi e. Assicurarsi di controllare che il codice di serializzazione funzioni come previsto dopo la migrazione.

- In alcuni casi, lo strumento automatizzato non dispone di codice all'interno `#if #else` delle direttive del compilatore In questo caso sarà necessario apportare le correzioni manualmente (vedere gli errori comuni riportati di seguito).

- I metodi esportati manualmente usando `[Export]` potrebbero non essere corretti automaticamente dallo strumento di migrazione, ad esempio in questo codice Snippert è necessario aggiornare manualmente il tipo restituito a: `nfloat`

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- Il API unificata non fornisce una conversione implicita tra NSDate e .NET DateTime perché non si tratta di una conversione senza perdita di perdite. Per evitare errori correlati alla `DateTimeKind.Unspecified` conversione di .NET `DateTime` in locale o UTC prima del cast `NSDate`a.

- I metodi di categoria Objective-C vengono ora generati come metodi di estensione nel API unificata. Ad esempio, il codice usato `UIView.DrawString` in precedenza ora fa riferimento `NSString.DrawString` all'API unificata.

- Il codice che usa le `VideoSettings` classi AVFoundation con deve cambiare `WeakVideoSettings` per usare la proprietà. Questa operazione richiede `Dictionary`un oggetto, disponibile come proprietà nelle classi di impostazioni, ad esempio:

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- Il costruttore `.ctor(IntPtr)` NSObject è stato modificato da pubblico a protetto ([per evitare un uso non corretto](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

- `NSAction`è stato [sostituito](~/cross-platform/macios/unified/overview.md#NSAction) con .NET `Action`standard. Sono stati sostituiti `Action<T>`anche alcuni delegati semplici (singoli parametri).

Infine, fare riferimento alle [differenze della versione classica v API unificata](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) per cercare le modifiche apportate alle API nel codice. La ricerca in [Questa pagina](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) consente di trovare le API classiche e gli elementi a cui sono stati aggiornati.

> [!NOTE]
> Lo `MonoTouch.Dialog` spazio dei nomi rimane invariato dopo la migrazione. Se il codice usa **MonoTouch. Dialog,** è necessario continuare a usare tale spazio dei nomi. `MonoTouch.Dialog` non `Dialog`modificare in.

## <a name="common-compiler-errors"></a>Errori comuni del compilatore

Di seguito sono riportati altri esempi di errori comuni, insieme alla soluzione:

**Errore errore CS0012: Il tipo ' MonoTouch. UIKit. UIView ' è definito in un assembly a cui non si fa riferimento.**

Difficoltà Ciò significa in genere che il progetto fa riferimento a un componente o a un pacchetto NuGet che non è stato compilato con l'API unificata. È necessario eliminare e aggiungere nuovamente tutti i componenti e i pacchetti NuGet. Se l'errore non viene risolto, è possibile che la libreria esterna non supporti ancora il API unificata.

**Errore MT0034: Non è possibile includere sia ' MonoTouch. dll ' che ' Novell. iOS. dll ' nello stesso progetto Novell. iOS. viene fatto riferimento in modo esplicito a' Novell. iOS. dll ', mentre ' Novell. mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null '.**

Difficoltà Eliminare il componente che causa questo errore e aggiungerlo di nuovo al progetto.

**Errore CS0234: Il tipo o il nome dello spazio dei nomi "Foundation" non esiste nello spazio dei nomi "MonoTouch". Manca un riferimento a un assembly?**

Difficoltà Lo strumento di migrazione automatizzata in Visual Studio per Mac dovrebbe `MonoTouch.Foundation` aggiornare tutti `Foundation`i riferimenti a, tuttavia in alcuni casi tali riferimenti dovranno essere aggiornati manualmente. È possibile che `UIKit`vengano visualizzati errori simili per gli altri spazi dei `MonoTouch`nomi contenuti in precedenza in, ad esempio.

**Errore CS0266: Non è possibile convertire in modo implicito il tipo ' Double ' in ' System. float '**

Correzione: modificare il tipo e eseguire `nfloat`il cast in. Questo errore può verificarsi anche per gli altri tipi con equivalenti a 64 bit (ad esempio `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Errore CS0266: Non è possibile convertire in modo implicito il tipo ' CoreGraphics. CGRect ' in ' System. Drawing. RectangleF '. Esiste una conversione esplicita (manca un cast?)**

Difficoltà Modificare le istanze `RectangleF` di `CGRect`in `SizeF` , `CGSize`in e `PointF` in .`CGPoint` Lo spazio `using System.Drawing;` dei nomi deve essere `using CoreGraphics;` sostituito con (se non è già presente).

**errore CS1502: La corrispondenza migliore del metodo di overload per ' CoreGraphics. CGContext. SetLineDash (System. nFloat, System. nFloat [])' presenta alcuni argomenti non validi**

Difficoltà Modificare il tipo di `nfloat[]` matrice in ed eseguire `Math.PI`il cast esplicito.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Errore CS0115:' WordsTableSource. RowsInSection (UIKit. UITableView, int)' è contrassegnato come override, ma non è stato trovato alcun metodo adatto per eseguire l'override**

Difficoltà Modificare il valore restituito e i tipi di `nint`parametro in. Questa situazione si verifica in genere negli override dei metodi `UITableViewSource`, ad esempio `NumberOfSections`in, tra `GetViewForHeader`cui `RowsInSection`, `GetHeightForRow` `TitleForHeader`,,, e così via.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Errore CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`: il tipo restituito deve essere ' System. nint ' in modo che corrisponda al membro sottoposto a override`UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Difficoltà Quando il tipo restituito viene modificato in `nint`, eseguire il cast del valore `nint`restituito a.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Errore CS1061: Il tipo ' CoreGraphics. CGPath ' non contiene una definizione per ' AddElipseInRect '**

Difficoltà Correggere l'ortografia in `AddEllipseInRect`. Altre modifiche al nome includono:

- Modificare ' color. Black ' in `NSColor.Black`.
- Modificare MapKit ' AddAnnotation ' in `AddAnnotations`.
- Modificare AVFoundation ' DataUsingEncoding ' in `Encode`.
- Modificare AVFoundation ' AVMetadataObject. TypeQRCode ' in `AVMetadataObjectType.QRCode`.
- Modificare AVFoundation ' VideoSettings ' in `WeakVideoSettings`.
- Modificare PopViewControllerAnimated in `PopViewController`.
- Modificare CoreGraphics ' CGBitmapContext. SetRGBFillColor ' in `SetFillColor`.

**Errore CS0546: non è possibile eseguire l'override perché' MapKit. MKAnnotation. coordinate ' non ha una funzione di accesso set sottoponibile a override (CS0546)**

Quando si crea un'annotazione personalizzata mediante la sottoclasse MKAnnotation, il campo delle coordinate non ha alcun setter, bensì solo un getter.

[Fix](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

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
- [Aggiornamento delle app Novell. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento di binding](~/cross-platform/macios/unified/update-binding.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
