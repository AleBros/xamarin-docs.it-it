---
title: Panoramica di API unificata
description: Il API unificata di Novell consente di condividere il codice tra Mac e iOS e di supportare le applicazioni 32 e 64 bit con lo stesso file binario.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 0fff049c48a613937513d9826647f3fc37753684
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887442"
---
# <a name="unified-api-overview"></a>Panoramica di API unificata

Il API unificata di Novell consente di condividere il codice tra Mac e iOS e di supportare le applicazioni 32 e 64 bit con lo stesso file binario. Il API unificata viene usato per impostazione predefinita nei nuovi progetti Novell. iOS e Novell. Mac.

> [!IMPORTANT]
> Il API classica Novell, che precedeva il API unificata, è stato deprecato. 
> - L'ultima versione di Novell. iOS per supportare il API classica (MonoTouch. dll) era Novell. iOS 9,10.
> - Novell. Mac supporta ancora la API classica, ma non viene più aggiornata. Poiché è deprecato, gli sviluppatori devono spostare le applicazioni nel API unificata.

## <a name="updating-classic-api-based-apps"></a>Aggiornamento di app basate su API classica

Seguire le istruzioni pertinenti per la piattaforma:

- [Aggiornare app esistenti](updating-apps.md)
- [Aggiornare app iOS esistenti](updating-ios-apps.md)
- [Aggiornare app Mac esistenti](updating-mac-apps.md)
- [Aggiornare app Xamarin.Forms esistenti](updating-xamarin-forms-apps.md)
- [Eseguire la migrazione di un binding all'API unificata](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Suggerimenti per l'aggiornamento del codice per l'API unificata](updating-tips.md)

Indipendentemente dalle applicazioni di cui si esegue la migrazione, consultare [questi suggerimenti](updating-tips.md) per eseguire correttamente l'aggiornamento al API unificata.

## <a name="library-split"></a>Suddivisione libreria

Da questo punto in poi, le nostre API verranno riportate in due modi:

- **API classica:** Limitato a 32 bit (solo) ed esposto negli `monotouch.dll` assembly e. `XamMac.dll`
- **API unificata:** Supporta lo sviluppo di bit 32 e 64 con una singola API disponibile negli `Xamarin.iOS.dll` assembly `Xamarin.Mac.dll` e.

Ciò significa che per gli sviluppatori aziendali (non destinazione l'App Store), è possibile continuare a usare le API classiche esistenti, perché continueranno a essere mantenute per sempre, oppure è possibile eseguire l'aggiornamento alle nuove API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Modifiche dello spazio dei nomi

Per ridurre la frizione per condividere il codice tra i prodotti Mac e iOS, si modificano gli spazi dei nomi per le API nei prodotti.

Il prefisso "MonoTouch" viene eliminato dal prodotto iOS e da "MonoMac" dal prodotto Mac sui tipi di dati.

In questo modo è più semplice condividere il codice tra le piattaforme Mac e iOS senza ricorrere alla compilazione condizionale e ridurrà il rumore all'inizio dei file di codice sorgente.

- **API classica:** Gli spazi dei `MonoTouch.` nomi `MonoMac.` usano il prefisso o.
- **API unificata:** Nessun prefisso dello spazio dei nomi

## <a name="runtime-defaults"></a>Impostazioni predefinite runtime

Per impostazione predefinita, il API unificata utilizza l'Garbage Collector **SGen** e il nuovo sistema di [conteggio dei riferimenti](~/ios/internals/newrefcount.md) per tenere traccia della proprietà dell'oggetto. Questa stessa funzionalità è stata portata in Novell. Mac.

Questo consente di risolvere una serie di problemi che gli sviluppatori affrontano con il vecchio sistema e semplificano anche la [gestione della memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Si noti che è possibile abilitare nuovi refcount anche per il API classica, ma il valore predefinito è conservativo e non richiede che gli utenti effettuino alcuna modifica. Grazie alla API unificata, abbiamo avuto la possibilità di modificare l'impostazione predefinita e offrire agli sviluppatori tutti i miglioramenti che hanno fatto per effettuare il refactoring e testare nuovamente il codice.

## <a name="api-changes"></a>Modifiche API

Il API unificata rimuove i metodi deprecati e sono presenti alcuni casi in cui si sono verificati errori di ortografia nei nomi delle API quando sono stati associati agli spazi dei nomi MonoTouch e MonoMac originali nelle API classiche. Queste istanze sono state corrette nelle nuove API unificate e devono essere aggiornate nelle applicazioni del componente, iOS e Mac. Di seguito è riportato un elenco di quelli più comuni che è possibile eseguire:

|Nome del metodo API classica|Nome del metodo API unificata|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Per un elenco completo delle modifiche apportate quando si passa dal modello classico al API unificata, vedere la documentazione relativa alle [differenze tra API (MonoTouch. dll) e unificata (Novell. iOS. dll)](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) .

## <a name="updating-to-unified"></a>Aggiornamento a Unified

Diverse API obsolete/obsolete o deprecate nel **modello classico** non sono disponibili nell'API unificata. Può essere più facile correggere gli `CS0616` avvisi prima di avviare l'aggiornamento (manuale o automatizzato), perché il `[Obsolete]` messaggio di attributo (parte dell'avviso) verrà visualizzato come guida all'API corretta.

Si noti che si sta pubblicando una [*differenza*](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) tra le modifiche alle API di Visual Studio classico e quelle unificate che possono essere usate prima o dopo gli aggiornamenti del progetto. Tuttavia, la correzione delle chiamate obsolete nel modello di distribuzione classica sarà spesso un risparmio di tempo (meno ricerche nella documentazione).

Seguire queste istruzioni per [aggiornare le app iOS](~/cross-platform/macios/unified/updating-ios-apps.md)o [Mac](~/cross-platform/macios/unified/updating-mac-apps.md) esistenti al API unificata.
Esaminare il resto della pagina e [questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) per ulteriori informazioni sulla migrazione del codice.

### <a name="nuget"></a>NuGet

I pacchetti NuGet che in precedenza supportano Novell. iOS tramite il API classica pubblicato i relativi assembly usando il moniker della piattaforma **Monotouch10** .

Il API unificata introduce un nuovo identificatore di piattaforma per i pacchetti compatibili, **Novell. iOS10**. I pacchetti NuGet esistenti dovranno essere aggiornati per aggiungere il supporto per questa piattaforma, compilando il API unificata.

> [!IMPORTANT]
> Se è presente un errore nel formato _"errore 3: non è possibile includere sia ' MonoTouch. dll ' che ' Novell. iOS. dll ' nello stesso progetto Novell. iOS. viene fatto riferimento in modo esplicito a' Novell. iOS. dll ', mentre a' MonoTouch. dll ' fa riferimento ' xxx, Version = 0.0.000, culture = neutral, PublicKeyToken = null ' "_ dopo la conversione dell'applicazione nelle API unificate, è in genere dovuto alla presenza di un componente o di un pacchetto NuGet nel progetto che non è stato aggiornato al API unificata. È necessario rimuovere il componente/NuGet esistente, eseguire l'aggiornamento a una versione che supporta le API unificate ed eseguire una compilazione pulita.

### <a name="the-road-to-64-bits"></a>La strada a 64 bit

Per informazioni di base su come supportare le applicazioni di bit 32 e 64 e informazioni sui Framework, vedere le [considerazioni sulla piattaforma di bit 32 e 64](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Nuovi tipi di dati

Alla base della differenza, le API Mac e iOS utilizzano i tipi di dati specifici dell'architettura sempre 32 bit sulle piattaforme a 32 bit e 64 bit su piattaforme a 64 bit.

In Objective-C, ad esempio, `NSInteger` viene eseguito il `int32_t` mapping del tipo di dati a `int64_t` nei sistemi a 32 bit e nei sistemi a 64 bit.

Per trovare la corrispondenza con questo comportamento, nel API unificata si sostituiscono gli utilizzi precedenti `int` di (che in .NET è definito come `System.Int32`sempre) in un nuovo tipo di dati `System.nint`:.  È possibile pensare a "n" come significato "native", quindi al tipo Integer nativo della piattaforma.

Stiamo introducendo `nint`e `nuint` `nfloat` fornendo i tipi di dati basati su di essi, ove necessario.

Per ulteriori informazioni su queste modifiche ai tipi di dati, vedere il documento [tipi nativi](~/cross-platform/macios/nativetypes.md) .

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Come rilevare l'architettura delle app iOS

Potrebbero essere presenti situazioni in cui l'applicazione deve essere in esecuzione su un sistema iOS a 32 bit o a 64 bit. Il codice seguente può essere usato per controllare l'architettura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Matrici e System. Collections. Generic

Poiché C# gli indicizzatori prevedono `int`un tipo di, sarà necessario eseguire il `nint` cast esplicito dei valori a `int` per accedere agli elementi in una raccolta o in una matrice. Ad esempio:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Si tratta di un comportamento previsto, perché il `int` Cast `nint` da a ha un valore Losso di 64 bit, non viene eseguita una conversione implicita.

### <a name="converting-datetime-to-nsdate"></a>Conversione di DateTime in NSDate

Quando si usano le API unificate, la conversione implicita `NSDate` di `DateTime` in valori non viene più eseguita. Questi valori dovranno essere convertiti in modo esplicito da un tipo a un altro. Per automatizzare il processo, è possibile usare i metodi di estensione seguenti:

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>API e digitazioni deprecate

All'interno dell'API classica Novell. iOS (MonoTouch. dll `[Obsolete]` ) l'attributo è stato usato in due modi diversi:

- **API iOS deprecata:** Questo è il momento in cui Apple suggerisce di smettere di usare un'API perché viene sostituita da una versione più recente. Il API classica è ancora corretto e spesso obbligatorio (se si supporta la versione precedente di iOS).
 Tali API (e l' `[Obsolete]` attributo) sono incluse nei nuovi assembly Novell. iOS.
- **API non corretta** In alcune API erano presenti errori di digitazione sui rispettivi nomi.

Per gli assembly originali (MonoTouch. dll e XamMac. dll) abbiamo mantenuto il vecchio codice disponibile per la compatibilità, ma sono stati rimossi dagli assembly API unificata (Novell. iOS. dll e Novell. Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>Sottoclassi NSObject. ctor (IntPtr)

Ogni `NSObject` sottoclasse dispone di un costruttore che `IntPtr`accetta un oggetto. Questo è il modo in cui è possibile creare un'istanza di una nuova istanza gestita da un handle ObjC nativo.

Nel modello classico era un `public` costruttore. Tuttavia è facile usare questa funzionalità nel codice utente, ad esempio creando diverse istanze gestite per una singola istanza di ObjC *o* creando un'istanza gestita che non avrebbe lo stato gestito previsto (per le sottoclassi).

Per evitare questi tipi di problemi, `IntPtr` i costruttori sono ora `protected` in un'API unificata, da usare solo per la sottoclasse. In questo modo verrà utilizzata l'API corretta/sicura per creare un'istanza gestita dagli handle, ad esempio

```csharp
var label = Runtime.GetNSObject<UILabel> (handle);
```

Questa API restituirà un'istanza gestita esistente (se già esistente) o ne creerà una nuova (quando necessario). È già disponibile nell'API classica e unificata.

Si noti che `.ctor(NSObjectFlag)` è ora anche `protected` , ma questo è stato usato raramente al di fuori della sottoclasse.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction sostituito con azione

Con le API unificate, `NSAction` è stato rimosso a favore di .NET `Action`standard. Si tratta di un grande miglioramento `Action` perché è un tipo .NET comune, `NSAction` mentre era specifico di Novell. iOS. Entrambi eseguono esattamente la stessa operazione, ma sono tipi distinti e incompatibili e hanno comportato la scrittura di più codice per ottenere lo stesso risultato.

Ad esempio, se l'applicazione Novell esistente include il codice seguente:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```

Ora può essere sostituito con una semplice espressione lambda:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

In precedenza sarebbe stato un errore del compilatore perché `Action` non è possibile assegnare `NSAction`un oggetto a `UITapGestureRecognizer` , ma poiché `Action` ora accetta un `NSAction` anziché un è valido nelle API unificate.

### <a name="custom-delegates-replaced-with-actiont"></a>Delegati personalizzati sostituiti con azione\<T >

In **Unified** alcuni delegati .NET semplici (ad esempio, un parametro) sono `Action<T>`stati sostituiti con. ad esempio

```csharp
public delegate void NSNotificationHandler (NSNotification notification);
```

ora può essere usato come `Action<NSNotification>`. Questa operazione promuove il riutilizzo del codice e riduce la duplicazione del codice all'interno di Novell. iOS e delle proprie applicazioni.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Attività\<bool > sostituita con attività < booleana, nserror > >

Nel **modello classico** erano presenti alcune API asincrone `Task<bool>`che restituivano. Tuttavia, in alcuni casi è necessario utilizzare quando un `NSError` oggetto era parte della firma, ovvero `bool` era già `true` ed era necessario intercettare un'eccezione per ottenere `NSError`.

Poiché alcuni errori sono molto comuni e il valore restituito non è stato utile, questo modello è stato modificato in **Unified** per `Task<Tuple<Boolean,NSError>>`restituire un oggetto. In questo modo è possibile verificare l'esito positivo e gli eventuali errori che si sono verificati durante la chiamata asincrona.

### <a name="nsstring-vs-string"></a>Confronto tra NSString e stringa

In alcuni casi alcune costanti devono essere modificate da `string` a `NSString`, ad esempio`UITableViewCell`

**Classico**

```csharp
public virtual string ReuseIdentifier { get; }
```

**Unificata**

```csharp
public virtual NSString ReuseIdentifier { get; }
```

In generale è preferibile `System.String` il tipo .NET. Tuttavia, nonostante le linee guida di Apple, alcune API native confrontano i puntatori costanti (non la stringa stessa) e questo può funzionare solo quando si `NSString`espongono le costanti come.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolli Objective-C

Il MonoTouch originale non dispone del supporto completo per i protocolli ObjC e alcune API non ottimali sono state aggiunte per supportare lo scenario più comune. Questa limitazione non esiste più ma, per compatibilità con le versioni precedenti, diverse API vengono mantenute `XamMac.dll`all'interno `monotouch.dll` di e.

Queste limitazioni sono state rimosse e pulite sulle API unificate. La maggior parte delle modifiche apparirà come segue:

**Classico**

```csharp
public virtual AVAssetResourceLoaderDelegate Delegate { get; }
```

**Unificata**

```csharp
public virtual IAVAssetResourceLoaderDelegate Delegate { get; }
```

Il `I` prefisso significa che **Unified** espone un'interfaccia, anziché un tipo specifico, per il protocollo objc. In questo modo si semplificano i casi in cui non si vuole creare una sottoclasse del tipo specifico fornito da Novell. iOS.

È anche possibile che alcune API siano più precise e facili da usare, ad esempio:

**Classico**

```csharp
public virtual void SelectionDidChange (NSObject uiTextInput);
```

**Unificata**

```csharp
public virtual void SelectionDidChange (IUITextInput uiTextInput);
```

Questa API è ora più semplice, senza fare riferimento alla documentazione e il completamento del codice IDE fornirà suggerimenti più utili basati sul protocollo/interfaccia.

#### <a name="nscoding-protocol"></a>Protocollo NSCoding

L'associazione originale includeva un. ctor (NSCoder) per ogni tipo, anche se non supportava il `NSCoding` protocollo.  Un singolo `Encode(NSCoder)` metodo era presente `NSObject` in per codificare l'oggetto.
Questo metodo, tuttavia, funziona solo se l'istanza è conforme al protocollo NSCoding.

Il API unificata è stato risolto.  I nuovi assembly avranno solo `.ctor(NSCoder)` se il tipo è conforme a. `NSCoding` Inoltre, tali tipi dispongono ora `Encode(NSCoder)` `INSCoding` di un metodo che è conforme all'interfaccia.

Basso effetto: Nella maggior parte dei casi questa modifica non influisce sulle applicazioni come il vecchio, rimosso, non è possibile usare costruttori.

## <a name="further-tips"></a>Ulteriori suggerimenti

Le ulteriori modifiche da tenere presenti sono elencate nei [Suggerimenti per l'aggiornamento delle app al API unificata](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Codice di esempio

A partire dal 31 luglio sono state pubblicate le porte degli esempi iOS per questa nuova API nel `magic-types` ramo in MonoTouch [-Samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Per Mac, è possibile controllare gli esempi sia nel repository [Mac-Samples](https://github.com/xamarin/mac-samples) (che mostra le nuove API in Mavericks/Yosemite), sia negli esempi di 32/64 bit nel ramo "Magic-types" [Mac-Samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Collegamenti correlati

- [Aggiornamento di app iOS](updating-ios-apps.md)
- [Aggiornamento di app Mac](updating-mac-apps.md)
- [Aggiornamento delle app Novell. Forms](updating-xamarin-forms-apps.md)
- [Aggiornamento di binding](update-binding.md)
- [Aggiornamento suggerimenti](updating-tips.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
