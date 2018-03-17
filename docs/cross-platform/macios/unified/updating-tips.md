---
title: Suggerimenti per l'aggiornamento del codice per l'API unificata
ms.topic: article
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 31ee94b7a463a651556472967152b98ee3061a8d
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Suggerimenti per l'aggiornamento del codice per l'API unificata

Usando lo strumento di migrazione automatizzati in Visual Studio per Mac convertirà iOS e Mac progetti da riferimento API unificata (Xamarin.iOS.dll o Xamarin.Mac.dll) e apportano numerose modifiche del codice necessario per l'utente (vedere il [versione di Xamarin Studio note sulla sezione 'Classic allo strumento di migrazione di codice Unified API'](http://developer.xamarin.com/releases/studio/xamarin.studio_5.7/xamarin.studio_5.7/) per informazioni dettagliate).

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>Non è riuscito a trovare NSInvalidArgumentException storyboard errore

È presente un [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) nella versione corrente di Visual Studio per Mac che possono verificarsi dopo aver utilizzato lo strumento di migrazione automatica di convertire il progetto per le API unificata. Dopo l'aggiornamento, se viene visualizzato un messaggio di errore nel formato:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...

```

È possibile eseguire le operazioni seguenti per risolvere questo problema, individuare il file di destinazione di compilazione seguente:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

In questo file è necessario individuare la seguente dichiarazione di destinazione:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

E aggiungere il `DependsOnTargets="_CollectBundleResources"` attributo. analogamente a quanto segue:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Salvare il file, riavviare Visual Studio per Mac, eseguire una nuova e ricompilazione del progetto. Una correzione per questo problema deve essere rilasciata da Xamarin a breve.

## <a name="useful-tips"></a>Suggerimenti utili

Dopo aver utilizzato lo strumento di migrazione, si possono comunque ricevere alcuni errori del compilatore che richiedono un intervento manuale.
Le operazioni che potrebbero dover essere risolti manualmente includono:

* Confronto tra `enum`s potrebbe richiedere un `(int)` cast.

* `NSDictionary.IntValue` Restituisce un `nint`, esiste un `Int32Value` che può essere usato invece.

* `nfloat` e `nint` tipi non possono essere contrassegnati `const`;   `static readonly nint` è un'alternativa ragionevole.

* Operazioni che consentono di essere direttamente nel `MonoTouch.` dello spazio dei nomi sono in genere inclusi ora il `ObjCRuntime.` dello spazio dei nomi (ad esempio: `MonoTouch.Constants.Version` è ora `ObjCRuntime.Constants.Version`).

* Codice che serializza gli oggetti potrebbe essere interrotto quando si tenta di serializzare `nint` e `nfloat` tipi. Assicurarsi di controllare che il codice di serializzazione funziona come previsto dopo la migrazione.

* In alcuni casi lo strumento automatizzato mancati riscontri codice all'interno di `#if #else` direttive del compilatore condizionale. In questo caso è necessario apportare manualmente le correzioni (vedere gli errori comuni seguenti).

* I metodi esportati manualmente utilizzando `[Export]` potrebbe non essere corrette automaticamente dallo strumento di migrazione, ad esempio in snippert questo codice è necessario aggiornare manualmente il tipo restituito per `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * L'API unificata non fornisce una conversione implicita tra NSDate e DateTime .NET perché non è una conversione senza perdita di dati. Per evitare gli errori correlati a `DateTimeKind.Unspecified` convertire .NET `DateTime` a locale o UTC prima di eseguire il cast a `NSDate`.

 * Metodi di categoria Objective-C vengono ora generati come metodi di estensione nell'API unificata. Esempio di codice usato in precedenza `UIView.DrawString` fa ora riferimento `NSString.DrawString` nell'API unificata.

 * Codice che usa le classi AVFoundation con `VideoSettings` deve modificare per utilizzare il `WeakVideoSettings` proprietà. Questa operazione richiede un `Dictionary`, che è disponibile come proprietà per le classi di impostazioni, ad esempio:

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * Il NSObject `.ctor(IntPtr)` costruttore è stato modificato da pubblica a protetto ([per impedire l'uso improprio](~/cross-platform/macios/unified/index.md#NSObject_ctor)).

 * `NSAction` è stata [sostituito](~/cross-platform/macios/unified/index.md#NSAction) con starndard .NET `Action`. Inoltre, alcuni delegati semplice (parametro singolo) sono stati sostituiti con `Action<T>`.

Infine, fare riferimento al [differenze tra API unificata classica v](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) per cercare le modifiche alle API nel codice. Ricerca [questa pagina](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) consentirà di trovare le API classica e ciò che è stato aggiornati a.

**Nota:** il `MonoTouch.Dialog` rimane invariato dopo la migrazione. Se il codice utilizza **MonoTouch.Dialog** è necessario continuare a utilizzare tale spazio dei nomi - *non* modificare `MonoTouch.Dialog` a `Dialog`!

## <a name="common-compiler-errors"></a>Errori comuni del compilatore

Altri esempi di errori comuni sono elencati di seguito, insieme alla soluzione:

**Errore CS0012: Il tipo 'MonoTouch.UIKit.UIView' è definito in un assembly che non viene fatto riferimento.**

Correzione: Questo in genere significa che il progetto fa riferimento a un componente o un pacchetto NuGet che non è stato compilato con l'API unificata. È necessario eliminare e aggiungere di nuovo tutti i componenti e NuGet pacchetti. Se non viene risolto l'errore, la libreria esterna potrebbe non supportare ancora l'API unificata.

**Errore MT0034: Non può includere sia 'monotouch.dll' e 'Xamarin.iOS.dll' nello stesso progetto xamarin. IOS - 'Xamarin.iOS.dll' viene fatto riferimento in modo esplicito, mentre 'monotouch.dll' fa riferimento ' Xamarin.Mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null'.**

Correzione: Eliminare il componente che ha causato l'errore e aggiungere di nuovo al progetto.

**Errore CS0234: Il nome di tipo o spazio dei nomi 'Base' non esiste nello spazio dei nomi 'MonoTouch'. Probabilmente manca un riferimento all'assembly.**

Correzione: Lo strumento di migrazione automatizzati in Visual Studio per Mac *deve* Aggiorna tutte `MonoTouch.Foundation` fa riferimento a `Foundation`, tuttavia in alcuni casi sarà necessario aggiornare manualmente. Errori simili potrebbero essere visualizzate per gli altri spazi dei nomi precedentemente contenute nel `MonoTouch`, ad esempio `UIKit`.

**Errore CS0266: Impossibile convertire in modo implicito il tipo 'double' a 'System.float'**

Correzione: modificare tipo e il cast a `nfloat`. Questo errore può verificarsi anche per altri tipi con equivalenti a 64 bit (ad esempio `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Errore CS0266: Impossibile convertire in modo implicito il tipo 'CoreGraphics.CGRect' a 'System.Drawing.RectangleF'. Esiste una conversione esplicita (probabilmente manca un cast)**

Correzione: Modificare le istanze di `RectangleF` a `CGRect`, `SizeF` a `CGSize`, e `PointF` a `CGPoint`. Lo spazio dei nomi `using System.Drawing;` deve essere sostituito con `using CoreGraphics;` (se non è già presenta).

**errore CS1502: la migliore corrispondenza metodo di overload per ' CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])' presenta alcuni argomenti non validi**

Correzione: Modificare il tipo di matrice in `nfloat[]` e sottoposto a cast in modo esplicito `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Errore CS0115: 'WordsTableSource.RowsInSection (UIKit.UITableView, int)' è contrassegnato come override, ma alcun metodo adatto trovato per eseguire l'override**

Correzione: Modificare i tipi di parametro e valore restituiti per `nint`. Questa eventualità si verifica in override dei metodi, ad esempio quelli in `UITableViewSource`, tra cui `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`e così via.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Errore CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**

Correzione: Quando il tipo restituito viene modificato per `nint`, eseguire il cast del valore restituito `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Errore CS1061: Type 'CoreGraphics.CGPath' non contiene una definizione per 'AddElipseInRect'**

Correzione: Correggere l'ortografia di `AddEllipseInRect`. Altre modifiche di nome includono:

* Impostare 'Black' `NSColor.Black`.
* Modificare MapKit 'AddAnnotation' `AddAnnotations`.
* Modificare AVFoundation 'DataUsingEncoding' `Encode`.
* Modificare AVFoundation 'AVMetadataObject.TypeQRCode' `AVMetadataObjectType.QRCode`.
* Modificare AVFoundation 'VideoSettings' `WeakVideoSettings`.
* Modificare PopViewControllerAnimated a `PopViewController`.
* Modificare CoreGraphics 'CGBitmapContext.SetRGBFillColor' `SetFillColor`.

**Errore CS0546: Impossibile eseguire l'override perché 'MapKit.MKAnnotation.Coordinate' non dispone di una funzione di accesso set (CS0546)**

Durante la creazione di un'annotazione personalizzata creando sottoclassi MKAnnotation il campo delle Coordinate non ha ci sono setter, solo un metodo Get.

[Fix](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Aggiungere un campo per tenere traccia della coordinata
* restituire questo campo nel getter della proprietà Coordinate
* Override del metodo SetCoordinate e impostare il campo
* Chiamare il costruttore con parametro passato in coordinate SetCoordinate

Dovrebbe essere simile al seguente:

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
- [Aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [L'aggiornamento di App Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [L'aggiornamento di App xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento delle associazioni](~/cross-platform/macios/unified/update-binding.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
