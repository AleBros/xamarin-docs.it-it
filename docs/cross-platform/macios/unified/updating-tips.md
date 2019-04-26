---
title: Suggerimenti per l'aggiornamento del codice per l'API unificata
description: Questo documento illustra gli errori comuni e vari suggerimenti utili quando si aggiorna un'applicazione di usare API unificata di Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: a5083e1d31377caece1b8fb4faf33b6e3ff88202
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61211820"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Suggerimenti per l'aggiornamento del codice per l'API unificata

Quando si aggiorna meno recenti soluzioni Xamarin all'API unificata, potrebbero essere rilevati gli errori seguenti.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>Non è riuscito a trovare NSInvalidArgumentException storyboard errore

È presente una [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) nella versione corrente di Visual Studio per Mac che possono verificarsi dopo aver usato lo strumento di migrazione automatica per convertire il progetto per le API unificata. Dopo l'aggiornamento, se viene visualizzato un messaggio di errore nel formato:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

È possibile eseguire il comando seguente per risolvere questo problema, individuare il file di destinazione di compilazione seguenti:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

In questo file è necessario trovare la seguente dichiarazione di destinazione:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

E aggiungere il `DependsOnTargets="_CollectBundleResources"` attributo ad esso. analogamente a quanto segue:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Salvare il file, riavviare Visual Studio per Mac, eseguire una pulizia e ricompilazione del progetto. Una correzione per risolvere questo problema deve essere rilasciata a breve da Xamarin.

## <a name="useful-tips"></a>Suggerimenti utili

Dopo aver usato lo strumento di migrazione, è comunque possibile usare alcuni errori del compilatore che richiedono un intervento manuale.
Le operazioni che potrebbero dover essere risolti manualmente includono:

* Confronto tra `enum`s potrebbero richiedere un `(int)` cast.

* `NSDictionary.IntValue` ora restituisce un `nint`, è presente un `Int32Value` che può essere usato invece.

* `nfloat` e `nint` tipi non possono essere contrassegnati `const`;   `static readonly nint` è un'alternativa ragionevole.

* Cose che erano direttamente nella `MonoTouch.` dello spazio dei nomi sono ora in genere inclusi il `ObjCRuntime.` dello spazio dei nomi (ad esempio: `MonoTouch.Constants.Version` è ora `ObjCRuntime.Constants.Version`).

* Il codice che serializza gli oggetti può comportare l'interruzione quando si prova a serializzare `nint` e `nfloat` tipi. Assicurarsi di controllare che il codice di serializzazione funziona come previsto dopo la migrazione.

* In alcuni casi lo strumento automatico mancati riscontri nel codice all'interno di `#if #else` direttive del compilatore condizionale. In questo caso è necessario apportare le correzioni manualmente (vedere gli errori comuni riportato di seguito).

* I metodi esportati manualmente usando `[Export]` potrebbe non essere corrette automaticamente dallo strumento di migrazione, ad esempio in snippert questo codice è necessario aggiornare manualmente il tipo restituito per `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * L'API unificata non fornisce una conversione implicita tra NSDate e data/ora .NET perché non è una conversione senza perdita di dati. Per evitare errori relativi al `DateTimeKind.Unspecified` convertire .NET `DateTime` locali o l'ora UTC prima di eseguire il cast su `NSDate`.

 * I metodi di Objective-C categoria vengono ora generati come metodi di estensione nell'API unificata. Ad esempio, il codice usato in precedenza `UIView.DrawString` fa riferimento a questo punto `NSString.DrawString` nell'API unificata.

 * Codice che usa classi AVFoundation con `VideoSettings` devono passare all'utilizzo di `WeakVideoSettings` proprietà. Questa operazione richiede un `Dictionary`, che è disponibile come una proprietà sulle classi di impostazioni, ad esempio:

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * Di NSObject `.ctor(IntPtr)` costruttore è stato modificato da pubblica a protetto ([per evitare un utilizzo improprio](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

 * `NSAction` è stata [sostituiti](~/cross-platform/macios/unified/overview.md#NSAction) con il .NET starndard `Action`. Alcuni delegati semplice (singolo parametro) sono anche stati sostituiti con `Action<T>`.

Infine, fare riferimento al [differenze tra API unificata classica v](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) per cercare le modifiche alle API nel codice. La ricerca [questa pagina](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) aiuterà a trovare le API classiche e ciò che è stato aggiornati a.

**Nota:** il `MonoTouch.Dialog` rimane invariato dopo la migrazione. Se il codice Usa **monotouch. Dialog** è necessario continuare a usare tale spazio dei nomi, eseguire *non* modificare `MonoTouch.Dialog` a `Dialog`!

## <a name="common-compiler-errors"></a>Errori comuni del compilatore

Altri esempi di errori comuni sono elencati di seguito, insieme alla soluzione:

**Errore CS0012: Il tipo 'MonoTouch.UIKit.UIView' è definito in un assembly che non viene fatto riferimento.**

Fix: Ciò significa in genere il progetto fa riferimento a un componente o pacchetto NuGet che non è stato compilato con l'API unificata. È necessario eliminare e aggiungere di nuovo tutti i componenti e NuGet pacchetti. Se non viene risolto l'errore, la libreria esterna potrebbe non supportare ancora l'API unificata.

**Errore MT0034: Non può includere sia 'monotouch' e 'Dll' nello stesso progetto xamarin. IOS - 'Dll' viene fatto riferimento in modo esplicito, mentre 'monotouch' viene fatto riferimento da ' xamarin, versione = 0.6.3.0, Culture = neutral, PublicKeyToken = null'.**

Fix: Eliminare il componente che ha causato l'errore e aggiungere di nuovo al progetto.

**Errore CS0234: Il nome del tipo o spazio dei nomi 'Base' non esiste nello spazio dei nomi 'MonoTouch'. Un riferimento all'assembly mancante?**

Fix: Lo strumento di migrazione automatica in Visual Studio per Mac *dovrebbe* Aggiorna tutto `MonoTouch.Foundation` fa riferimento a `Foundation`, ma in alcuni casi questi dovranno essere aggiornate manualmente. È possibile visualizzare errori simili per altri spazi dei nomi precedentemente contenute nel `MonoTouch`, ad esempio `UIKit`.

**Errore CS0266: Impossibile convertire implicitamente il tipo 'double' a 'System.float'**

Correzione: cambiare tipo ed eseguire il cast a `nfloat`. Questo errore può verificarsi anche per gli altri tipi con gli equivalenti a 64 bit (ad esempio `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Errore CS0266: Impossibile convertire implicitamente il tipo 'CoreGraphics.CGRect' a 'System.Drawing.RectangleF'. Esiste una conversione esplicita (probabilmente manca un cast)**

Fix: Modificare le istanze di `RectangleF` al `CGRect`, `SizeF` a `CGSize`, e `PointF` a `CGPoint`. Lo spazio dei nomi `using System.Drawing;` deve essere sostituito con `using CoreGraphics;` (se non è già presenta).

**errore CS1502: La migliore corrispondenza del metodo per l'overload ' CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])' presenta alcuni argomenti non validi**

Fix: Modifica tipo di matrice `nfloat[]` e assegnato in modo esplicito `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Errore CS0115: 'WordsTableSource.RowsInSection (UIKit.UITableView, int)' è contrassegnato come override, ma nessun metodo appropriato trovato per eseguire l'override**

Fix: Modificare i tipi di parametro e valore restituiti a `nint`. Questa eventualità si verifica nell'override dei metodi, ad esempio quelli sul `UITableViewSource`, tra cui `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`e così via.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Errore CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**

Fix: Quando il tipo restituito viene impostato su `nint`, eseguire il cast del valore restituito in `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Errore CS1061: Tipo 'CoreGraphics.CGPath' non contiene una definizione per 'AddElipseInRect'**

Fix: Correggere l'ortografia di `AddEllipseInRect`. Altre modifiche di nome includono:

* Modificare 'Black' `NSColor.Black`.
* Utilizzare MapKit 'AddAnnotation' `AddAnnotations`.
* Modificare AVFoundation 'DataUsingEncoding' `Encode`.
* Modificare AVFoundation 'AVMetadataObject.TypeQRCode' `AVMetadataObjectType.QRCode`.
* Modificare AVFoundation 'VideoSettings' `WeakVideoSettings`.
* Modificare PopViewControllerAnimated a `PopViewController`.
* Modificare CoreGraphics 'CGBitmapContext.SetRGBFillColor' `SetFillColor`.

**Errore CS0546: Impossibile eseguire l'override perché 'MapKit.MKAnnotation.Coordinate' non dispone di una funzione di accesso set sottoponibile a override (CS0546)**

Durante la creazione di un'annotazione personalizzata tramite sottoclassi MKAnnotation il campo delle Coordinate non dispone di alcun setter, solo un getter.

[Fix](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Aggiungere un campo per tenere traccia della coordinata
* restituire questo campo nel getter della proprietà Coordinate
* L'override del metodo SetCoordinate e impostare il campo
* Chiamare SetCoordinate nei ctor con il parametro passato in coordinate

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

- [L'aggiornamento di App](~/cross-platform/macios/unified/updating-apps.md)
- [L'aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [L'aggiornamento di App Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [L'aggiornamento di App xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento delle associazioni](~/cross-platform/macios/unified/update-binding.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
