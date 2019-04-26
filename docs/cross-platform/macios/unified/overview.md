---
title: Introduzione all'API unificata
description: API unificata di Xamarin consente di condividere codice tra iOS e Mac e il supporto applicazioni a 32 e 64 bit con lo stesso binario.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1d159d280bd3b8855c32e3e437dfdefcbe0463cb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261129"
---
# <a name="unified-api-overview"></a>Introduzione all'API unificata

API unificata di Xamarin consente di condividere codice tra iOS e Mac e il supporto applicazioni a 32 e 64 bit con lo stesso binario. L'API unificata viene usata per impostazione predefinita nei nuovi progetti xamarin. IOS e xamarin. Mac.

> [!IMPORTANT]
> L'API classica Xamarin, che è preceduto l'API unificata, è stato deprecato. 
> - L'ultima versione di xamarin. IOS per supportare l'API classica (monotouch) era 9.10 di xamarin. IOS.
> - Xamarin. Mac supporta ancora l'API classica, ma non verrà più aggiornata. Poiché è deprecato, gli sviluppatori devono spostare le proprie applicazioni per l'API unificata.

## <a name="updating-classic-api-based-apps"></a>L'aggiornamento di App basate su API classica

Seguire le istruzioni rilevanti per la tua piattaforma:

- [Aggiornare app esistenti](updating-apps.md)
- [Aggiornare app iOS esistenti](updating-ios-apps.md)
- [Aggiornare app Mac esistenti](updating-mac-apps.md)
- [Aggiornare app Xamarin.Forms esistenti](updating-xamarin-forms-apps.md)
- [Eseguire la migrazione di un binding all'API unificata](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Suggerimenti per l'aggiornamento del codice per l'API unificata](updating-tips.md)

Indipendentemente dal fatto che le applicazioni si esegue la migrazione, consultare [questi suggerimenti](updating-tips.md) che consentono di aggiornare correttamente all'API unificata.

## <a name="library-split"></a>Suddivisione di libreria

Da questo momento, le nostre API verranno visualizzate in due modi:

-  **API classica:** Limitato a 32-bit (solo) ed esposti nel `monotouch.dll` e `XamMac.dll` assembly.
-  **API unificata:** Supporta lo sviluppo di sia 32 e 64 bit con una singola API disponibile nel `Xamarin.iOS.dll` e `Xamarin.Mac.dll` assembly.

Ciò significa che per Enterprise gli sviluppatori (non destinata l'App Store), puoi continuare a usare le API classiche esistenti, come abbiamo mantenere mantenendo li all'infinito, oppure è possano eseguire l'aggiornamento alle nuove API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Modifiche di Namespace

Per ridurre l'attrito per condividere codice tra i prodotti Mac e iOS, si stanno modificando gli spazi dei nomi per le API nei prodotti.

Si sta eliminando "MonoTouch" il prefisso dalla prodotto iOS e da "MonoMac" dal nostro prodotto Mac sui tipi di dati.

Questo rende più semplice condividere codice tra le piattaforme Mac e iOS senza ricorrere alla compilazione condizionale e consente di ridurre il rumore nella parte superiore del file del codice sorgente.

-  **API classica:** Usare gli spazi dei nomi `MonoTouch.` o `MonoMac.` prefisso.
-  **API unificata:** Nessun prefisso dello spazio dei nomi

## <a name="runtime-defaults"></a>Impostazioni predefinite di runtime

L'API unificata per impostazione predefinita Usa la **SGen** garbage collector e il [nuovo conteggio dei riferimenti](~/ios/internals/newrefcount.md) system per tenere traccia delle proprietà degli oggetti. Questa stessa funzionalità è stata portata a xamarin. Mac.

Ciò consente di risolvere alcuni dei problemi che gli sviluppatori di affrontare il vecchio sistema e semplificano [gestione della memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Si noti che è possibile abilitare il nuovo conteggio dei riferimenti anche per l'API classica, ma il valore predefinito è conservativo e non richiede agli utenti di apportare modifiche. Con l'API unificata, si ha avuto l'opportunità di modificare l'impostazione predefinita e offrire agli sviluppatori di tutti i miglioramenti allo stesso tempo che il refactoring e testare nuovamente il proprio codice.

## <a name="api-changes"></a>Modifiche apportate all'API

L'API unificata rimuove metodi deprecati e sono presenti poche istanze in cui sono presenti errori di digitazione nei nomi delle API quando sono stati associati per l'originale MonoTouch MonoMac spazi dei nomi e nelle API classica. Queste istanze sono state corrette le nuove API unificata e dovranno essere aggiornata nel componente, iOS e le applicazioni Mac. Ecco un elenco di quelle più comuni che riscontrabili:

|Nome del metodo API classica|Nome del metodo API unificata|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Per un elenco competo delle modifiche quando si passa dalla distribuzione classica all'API unificata di, vedere la [modello di distribuzione classica (monotouch) vs differenze tra API unificata (DLL)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) documentazione.

## <a name="updating-to-unified"></a>L'aggiornamento a unificato

Diverse API/interrotti/obsoleta in **classiche** non sono disponibili nel **unificato** API. Può essere più semplice risolvere il `CS0616` poiché è possibile eseguire l'aggiornamento avvisi prima di avviare il (manuali o automatizzati) il `[Obsolete]` attributo messaggio (incluso il messaggio di avviso) per ottenere istruzioni utili per l'API a destra.

Si noti che viene pubblicata una [ *diff* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) unificata modifiche all'API che possono essere utilizzate prima o dopo l'aggiornamento del progetto di Visual Studio classico. Ancora la correzione di obsoletes spesso chiamate faranno classico un risparmio di tempo (meno ricerche documentazione).

Per seguire queste istruzioni [aggiornare le app iOS esistenti](~/cross-platform/macios/unified/updating-ios-apps.md), o [App Mac](~/cross-platform/macios/unified/updating-mac-apps.md) all'API unificata.
Esaminare la restante parte di questa pagina, e [questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) per altre informazioni sulla migrazione del codice.

### <a name="nuget"></a>NuGet

I pacchetti NuGet che supportavano in precedenza xamarin. IOS tramite l'API classica pubblicati i relativi assembly utilizzando il **Monotouch10** moniker di piattaforma.

API unificata di introduce un nuovo identificatore di piattaforma per i pacchetti compatibili - **Xamarin.iOS10**. I pacchetti NuGet esistenti dovrà essere aggiornata per aggiungere il supporto per questa piattaforma, in base all'API unificata di.

> [!IMPORTANT]
> Se si dispone di un errore nel modulo _"errore 3 non può includere sia 'monotouch' e 'Dll' nello stesso progetto xamarin. ios: 'Dll' viene fatto riferimento in modo esplicito, mentre 'monotouch' fa riferimento ' xxx ', versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo avere convertito l'applicazione per le API unificata, si tratta in genere con un componente o pacchetto NuGet nel progetto che non è stato aggiornato all'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.

### <a name="the-road-to-64-bits"></a>Il passaggio a 64 bit

Per informazioni generali sul supporto di 32 e 64 bit delle applicazioni e informazioni sui Framework per vedere le [32 e 64 bit considerazioni relative alla piattaforma](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Nuovi tipi di dati

Alla base della differenza, API iOS e Mac Usa un tipi di dati specifici dell'architettura sono sempre a 32 bit su piattaforme a 32 bit e 64 bit su piattaforme a 64 bit.

Ad esempio, Objective-C esegue il mapping di `NSInteger` tipo di dati `int32_t` nei sistemi a 32 bit e a `int64_t` nei sistemi a 64 bit.

In modo che corrisponda a questo comportamento, nella nostra API unificata, si sta sostituendo l'utilizzi precedenti del `int` (che in .NET viene definito come devono essere sempre `System.Int32`) in un nuovo tipo di dati: `System.nint`.  È possibile pensare "n" con il significato "nativa", quindi digitare il valore integer nativo della piattaforma.

Sono state introdotte `nint`, `nuint` e `nfloat` nonché fornendo i tipi di dati basato su essi laddove necessario.

Per altre informazioni su queste modifiche ai tipi di dati, vedere la [i tipi nativi](~/cross-platform/macios/nativetypes.md) documento.

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Come rilevare l'architettura delle App iOS

Potrebbero esserci situazioni in cui l'applicazione deve sapere se è in esecuzione in un a 32 bit o un sistema iOS a 64 bit. Il codice seguente è utilizzabile per controllare l'architettura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Matrici e System.Collections.Generic

Poiché C# indicizzatori prevedono un tipo di `int`, è possibile eseguire il cast esplicito `nint` valori `int` accedere agli elementi di una matrice o raccolta. Ad esempio:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Questo comportamento è previsto, perché il cast da `int` a `nint` è perdita di dati a 64 bit, una conversione implicita non viene eseguita.

### <a name="converting-datetime-to-nsdate"></a>Conversione di DateTime a NSDate

Quando si usano le API unificata, la conversione implicita dei `DateTime` a `NSDate` valori non viene più eseguito. Questi valori dovrà essere convertito in modo esplicito da un tipo a altro. I seguenti metodi di estensione sono utilizzabile per automatizzare questo processo:

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

### <a name="deprecated-apis-and-typos"></a>Errori di digitazione e le API deprecate

API classica xamarin. IOS interna (monotouch) il `[Obsolete]` attributo è stato usato in due modi diversi:

-  **API iOS deprecate:** Questo è quando gli hint di Apple per è possibile interrompere l'uso di un'API perché si è progressivamente sostituito da una versione più recente. L'API classica sia corretto e spesso obbligatorio (se si supporta la versione precedente di iOS).
 Tali API (e `[Obsolete]` attributo) sono inclusi nei nuovi assembly xamarin. IOS.
-  **API non corretta** alcune API sono stati rilevati errori di digitazione nei relativi nomi.

Per gli assembly originali (monotouch. dll e XamMac.dll) viene mantenuto il vecchio codice disponibile per la compatibilità, ma sono state rimosse dagli assembly di API unificata (DLL e xamarin. Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>.Ctor(IntPtr) sottoclassi di NSObject

Ogni `NSObject` sottoclasse ha un costruttore che accetta un `IntPtr`. Si tratta di come è possibile creare una nuova istanza gestita da un handle nativo ObjC.

In modalità classica questo era un `public` costruttore. Tuttavia, era facile in modo improprio di questa funzionalità nel codice utente, ad esempio, creare diverse istanze gestite di per una singola istanza ObjC *o* la creazione di un'istanza gestita che potrebbe non dispongono di stato previsto gestito (per sottoclassi).

Per evitare questi tipi di problemi il `IntPtr` costruttori sono ora `protected` nelle **unificata** API, per essere usato solo per la creazione di sottoclassi. In questo modo che il correggere/safe API viene utilizzata per creare, ovvero istanza gestita dagli handle,

    var label = Runtime.GetNSObject<UILabel> (handle);

Questa API restituirà un'istanza gestita esistente (se esiste già) o ne creerà uno nuovo (se necessario). È già disponibile nell'API unificata tanto classica.

Si noti che il `.ctor(NSObjectFlag)` sono ora configurabili anche `protected` ma questo veniva raramente utilizzato di fuori di creazione di una sottoclasse.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction sostituiti con azione

Con le API unificata `NSAction` è stato rimosso a favore di .NET standard `Action`. Questo è un importante miglioramento perché `Action` è un tipo .NET comune, mentre `NSAction` era specifico per xamarin. IOS. Avviene esattamente la stessa operazione, ma erano tipi distinti e non compatibili e ha restituito il codice più la necessità di essere scritti per ottenere lo stesso risultato.

Ad esempio, se l'applicazione esistente di Xamarin inclusa nel codice seguente:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
A questo punto, può essere sostituito con una semplice lambda:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

In precedenza sarebbe un errore del compilatore perché un `Action` non può essere assegnato a `NSAction`, tuttavia, poiché `UITapGestureRecognizer` accetta ora un `Action` anziché un `NSAction` è valido nelle API unificata.

### <a name="custom-delegates-replaced-with-actiont"></a>Delegati personalizzati sostituiti con azione<T>

Nelle **unificata** alcune semplici (ad esempio, un parametro) i delegati di .net sono stati sostituiti con `Action<T>`. Ad esempio,

    public delegate void NSNotificationHandler (NSNotification notification);

è ora possibile usare come un `Action<NSNotification>`. Questo codice Alza di livello riutilizzare e ridurre la duplicazione del codice all'interno di xamarin. IOS sia le proprie applicazioni.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Attività<bool> sostituito con attività < Boolean, NSError >>

Nelle **classiche** esistevano alcune API asincrone restituzione `Task<bool>`. Tuttavia alcune di esse in cui sono da utilizzare quando un `NSError` faceva parte della firma, vale a dire la `bool` era già `true` ed era necessario intercettare un'eccezione per ottenere il `NSError`.

Poiché alcuni errori sono molto comuni e il valore restituito non è stato utile questo modello è stato modificato **unificata** per restituire un `Task<Tuple<Boolean,NSError>>`. In questo modo è possibile controllare l'esito positivo e qualsiasi errore che potrebbe essersi verificato durante la chiamata asincrona.

### <a name="nsstring-vs-string"></a>Stringa di Visual Studio NSString

In alcuni casi dovevano essere modificato da alcune costanti `string` a `NSString`, ad esempio `UITableViewCell`

**modello di distribuzione classica**

    public virtual string ReuseIdentifier { get; }

**Unified**

    public virtual NSString ReuseIdentifier { get; }

In generale è preferibile .NET `System.String` tipo. Tuttavia, nonostante le linee guida di Apple, alcune API native confrontano i puntatori costanti (non la stringa stessa) e questo approccio può funzionare solo quando si espongono le costanti come `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolli di Objective-C

API di MonoTouch originale non aveva un supporto completo per i protocolli ObjC e alcuni non ottimale, sono stati aggiunti per supportare lo scenario più comune. Questa limitazione non esiste più, ma, per compatibilità con le varie API vengono mantenute intorno all'interno `monotouch.dll` e `XamMac.dll`.

Queste limitazioni sono stati rimosse e la pulizia sulle API unificata. La maggior parte delle modifiche avrà un aspetto simile al seguente:

**modello di distribuzione classica**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unified**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

Il `I` prefisso significa **unificata** esporre un'interfaccia, anziché un tipo specifico, per il protocollo ObjC. Ciò semplificherà casi in cui non desiderate per creare una sottoclasse al tipo specifico fornito di xamarin. IOS.

È consentito anche alcune API per essere più precise e facile da usare, ad esempio:

**modello di distribuzione classica**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unified**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

Queste API sono ora più facile da noi, senza fare riferimento alla documentazione e il completamento del codice IDE fornirà suggerimenti più utili in base il protocollo o interfaccia.

#### <a name="nscoding-protocol"></a>Protocollo NSCoding

L'associazione originale incluso un .ctor(NSCoder) per ogni tipo - anche se non supportava la `NSCoding` protocollo.  Un unico `Encode(NSCoder)` era presente nel metodo il `NSObject` per codificare l'oggetto.
Ma questo metodo funziona solo se l'istanza è conforme a NSCoding protocollo.

L'API unificata è stato risolto in questo.  I nuovi assembly avrà solo il `.ctor(NSCoder)` se il tipo è conforme a `NSCoding`. Anche questi tipi ora dispongono un' `Encode(NSCoder)` metodo che è conforme al `INSCoding` interfaccia.

Impatto basso: Nella maggior parte dei casi questa modifica non influisce sulle applicazioni come i costruttori precedenti, rimossi, non può essere usati.

## <a name="further-tips"></a>Ulteriori suggerimenti

Modifiche aggiuntive da tenere presenti sono elencati nel [suggerimenti per l'aggiornamento di App all'API unificata di](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Codice di esempio

A partire da luglio 31, abbiamo pubblicato le porte degli esempi iOS per questa nuova API nel `magic-types` suddiviso [monotouch-samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Per Mac, corso un controllo negli esempi inclusi in entrambi i [mac-samples](https://github.com/xamarin/mac-samples) repository (rappresentato da nuove API in Mavericks o Yosemite), nonché esempi 32/64 bit nel ramo di magic-types [esempi di mac](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Collegamenti correlati

- [L'aggiornamento delle App iOS](updating-ios-apps.md)
- [L'aggiornamento di App Mac](updating-mac-apps.md)
- [L'aggiornamento di App xamarin. Forms](updating-xamarin-forms-apps.md)
- [Aggiornamento delle associazioni](update-binding.md)
- [L'aggiornamento di suggerimenti](updating-tips.md)
- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
