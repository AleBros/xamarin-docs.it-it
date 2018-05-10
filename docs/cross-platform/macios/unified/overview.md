---
title: Panoramica dell'API unificata
description: Il nuovo stile API rende più semplice che mai per condividere il codice tra Mac e iOS nonché in modo da supportare 32 e 64 bit le applicazioni con lo stesso binario.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 0bdbf4a41ad5737603fccc7e78bc588a2f3acee3
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="unified-api-overview"></a>Panoramica dell'API unificata

_Il nuovo stile API rende più semplice che mai per condividere il codice tra Mac e iOS nonché in modo da supportare 32 e 64 bit le applicazioni con lo stesso binario._

Per migliorare la condivisione tra Mac e iOS del codice e per consentire agli sviluppatori di utilizzare una sola codebase che funzionano su 32 e 64 bit, in anticipo 2015 è stata introdotta una nuova API di prodotti sia Xamarin.Mac e xamarin. IOS, chiamata l'API unificata.

> [!IMPORTANT]
> **Deprecazione di profilo classico:** con l'aggiunta di nuove piattaforme in xamarin. IOS, si inizierà gradualmente deprecare funzionalità dal profilo classico (monotouch.dll). Ad esempio, l'opzione non NRC (nuovo-ref-count) è stato rimosso. NRC è sempre stato abilitato per unificata tutte le applicazioni (ad esempio NRC non è stato mai un'opzione) e sono presenti problemi noti. Nelle versioni future rimuoverà la possibilità di usare Boehm come il garbage collector. Questo è anche un'opzione non disponibile alle applicazioni unificate. La rimozione completa del supporto classico è pianificata per il passaggio successivo con la versione di xamarin. IOS 10.0.

## <a name="ios"></a>iOS

Il `Xamarin.iOS.dll` assembly fornito con xamarin. IOS 8.6 la **prima versione stabile e supportata** dell'API unificata per iOS.
Le versioni precedenti di anteprima dell'API unificata sono Chiudi ma non è completamente compatibile.

## <a name="mac"></a>Mac

Il `Xamarin.Mac.dll` assembly nel canale stabile Xamarin.Mac è il nostro **prima versione stabile e supportata** dell'API unificata per Mac.
Le versioni precedenti di anteprima dell'API unificata sono Chiudi ma non è completamente compatibile.

## <a name="runtime-defaults"></a>Impostazioni predefinite di runtime

Unified API per impostazione predefinita utilizzerà la **SGen** garbage collector e [il conteggio dei riferimenti nuovo](~/ios/internals/newrefcount.md) sistema per il rilevamento delle proprietà dell'oggetto. Questa stessa funzionalità è stata trasferita a Xamarin.Mac.

Questo consente di risolvere numerosi problemi che gli sviluppatori di affrontare il sistema precedente e semplificano anche [gestione della memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Si noti che è possibile abilitare di nuovo conteggio dei riferimenti anche per l'API classico, ma il valore predefinito è conservativo e non richiede agli utenti di apportare modifiche. Con l'API unificata, ha la possibilità di modificare il valore predefinito e consentire agli sviluppatori di tutti i miglioramenti apportati allo stesso tempo che il refactoring e testare nuovamente il proprio codice.

<a name="namespace-changes" />

## <a name="library-split"></a>Suddivisione di libreria

Da questo momento, le API verranno rilevate in due modi:

-  **API classica:** limitata a 32 bit (solo) ed esposto nel `monotouch.dll` e `XamMac.dll` assembly.
-  **API unificata:** supporta lo sviluppo di sia a 32 e 64 bit con una singola API disponibile nel `Xamarin.iOS.dll` e `Xamarin.Mac.dll` assembly.

Ciò significa che per Enterprise gli sviluppatori (non destinata all'App Store), puoi continuare a usare le API classico esistente, è mantenere mantenere li infinita, oppure è possono eseguire l'aggiornamento alle nuove API.

### <a name="namespace-changes"></a>Modifiche Namespace

Per ridurre ulteriormente l'attrito correlato per condividere il codice tra i prodotti Mac e iOS, si stanno modificando gli spazi dei nomi per le API dei prodotti.

Si sta eliminazione "MonoTouch" il prefisso da prodotti di iOS e "MonoMac" dal nostro prodotto Mac sui tipi di dati.

Questo rende più semplice condividere il codice tra le piattaforme Mac e iOS senza ricorrere alla compilazione condizionale e consente di ridurre il rumore nella parte superiore del file del codice sorgente.

-  **API classica:** utilizzare spazi dei nomi `MonoTouch.` o `MonoMac.` prefisso.
-  **API unificata:** alcun prefisso dello spazio dei nomi

### <a name="api-changes"></a>Modifiche all'API

L'API unificata rimuove metodi deprecati e sono presenti poche istanze in cui sono presenti errori di digitazione nei nomi delle API quando sono stati associati per l'originale MonoTouch MonoMac spazi dei nomi e nelle API classica. Queste istanze sono state corrette le nuove API unificata e dovranno essere aggiornata nel componente, iOS e le applicazioni Mac. Di seguito è riportato un elenco di quelle più comuni che possono verificarsi:

|Nome del metodo API classico|Nome del metodo API unificata|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Per un elenco completo delle modifiche dopo il passaggio dalla classica Unified API, visitare il nostro [vs classica (monotouch.dll) differenze tra API unificata (Xamarin.iOS.dll)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) documentazione.

### <a name="updating-to-unified"></a>L'aggiornamento a unificata

Diverse API/interrotto/obsoleta in **classico** non sono disponibili nel **unificato** API. Potrebbe risultare più semplice risolvere il `CS0616` avvisi prima di avviare il (manuali o automatizzati) Aggiorna poiché è necessario il `[Obsolete]` attributo messaggio (parte dell'avviso) e istruzioni per l'API di destra.

Si noti che attualmente vengono pubblicati un [ *diff* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) di Visual Studio classico unificata modifiche all'API che possono essere usate prima o dopo l'aggiornamento del progetto. Ancora la correzione di obsoletes chiamate in verrà classica spesso un risparmio di tempo (meno ricerche documentazione).

Seguire queste istruzioni per [aggiornare le app iOS esistente](~/cross-platform/macios/unified/updating-ios-apps.md), o [App Mac](~/cross-platform/macios/unified/updating-mac-apps.md) all'API unificata.
Il resto di questa pagina, esaminare e [questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) per ulteriori informazioni sulla migrazione del codice.

## <a name="components-and-nuget"></a>Componenti e NuGet

La maggior parte dei componenti esistenti e i pacchetti NuGet dovrà essere aggiornata per supportare l'API unificata.
I componenti compilati con l'API classica non è possibile fare riferimento da un progetto di API unificata.

Componenti esistenti che non disponevano di riferimenti a `monotouch.dll` (o `XamMac.dll`) non è necessario che gli aggiornamenti.

### <a name="components"></a>Componenti

componenti di iOS nel [archivio componenti Xamarin](https://components.xamarin.com/) deve essere aggiornato per funzionare con i progetti API unificata. Xamarin sta per aggiornare i componenti che si pubblica e incoraggiano altri autori per eseguire la stessa.

### <a name="nuget"></a>NuGet

Pacchetti NuGet a cui è supportato in precedenza tramite l'API classico di xamarin. IOS pubblicato i relativi assembly utilizzando il **Monotouch10** moniker di piattaforma.

L'API unificata introduce un nuovo identificatore di piattaforma per pacchetti compatibili - **Xamarin.iOS10**. Pacchetti NuGet esistenti dovrà essere aggiornata per aggiungere il supporto per questa piattaforma, compilando sull'API unificata.

> [!IMPORTANT]
> Se si dispone di un errore nel modulo _"errore 3 non può includere sia 'monotouch.dll' 'Xamarin.iOS.dll' nello stesso progetto xamarin. IOS - 'Xamarin.iOS.dll' viene fatto riferimento in modo esplicito, mentre si fa riferimento 'monotouch.dll' ' xxx, versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo la conversione dell'applicazione per le API unificata, è in genere a causa del componente o un pacchetto NuGet nel progetto che non è stato aggiornato per l'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.

<a name="deprecated-apis" />

## <a name="arrays-and-systemcollectionsgeneric"></a>Le matrici e System.Collections.Generic

Perché gli indicizzatori c# prevedono un tipo di `int`, sarà necessario eseguire il cast esplicito `nint` valori `int` accedere agli elementi di una matrice o raccolta. Ad esempio:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Tale comportamento è previsto, perché il cast da `int` a `nint` è perdita di dati a 64 bit, una conversione implicita non viene eseguita.

## <a name="converting-datetime-to-nsdate"></a>Conversione di dati DateTime NSDate

Quando si utilizzano API unificata, la conversione implicita di `DateTime` a `NSDate` valori non viene più eseguito. Questi valori dovranno essere convertiti in modo esplicito da un tipo a un altro. I seguenti metodi di estensione possono essere utilizzati per automatizzare il processo:

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

## <a name="deprecated-apis-and-typos"></a>Errori di digitazione e API deprecate

API classica all'interno di xamarin. IOS (monotouch.dll) il `[Obsolete]` attributo è stato usato in due modi diversi:

-  **IOS API deprecate:** ciò avviene Apple suggerimenti si desidera interrompere l'uso di un'API perché verranno sostituita da una versione più recente. L'API classico è ancora correttamente e spesso necessaria (se si supporta la versione precedente di iOS).
 Tale API (e `[Obsolete]` attributo) sono inclusi nei nuovi assembly xamarin. IOS.
-  **API non corretta** alcune API contiene errori di digitazione ai relativi nomi.

Per gli assembly originali (monotouch.dll e XamMac.dll) è stato conservato il vecchio codice disponibile per compatibilità ma sono stati rimossi dagli assembly Unified API (Xamarin.iOS.dll e Xamarin.Mac)

<a name="NSObject_ctor" />

## <a name="nsobject-subclasses-ctorintptr"></a>NSObject sottoclassi .ctor(IntPtr)

Ogni `NSObject` sottoclasse ha un costruttore che accetta un `IntPtr`. Si tratta di come è possibile creare un'istanza di una nuova istanza gestita da un handle ObjC nativo.

Classica questo è un `public` costruttore. Tuttavia è stato facile uso improprio di questa funzionalità nel codice utente, ad esempio, creare diverse istanze per una singola istanza ObjC gestite *o* la creazione di un'istanza gestita che potrebbe non dispongono dello stato previsto gestito (per le sottoclassi).

Per evitare questi tipi di problemi di `IntPtr` costruttori sono ora `protected` in **unificata** API, per essere utilizzato solo per la creazione di sottoclassi. In questo modo che il correggere/safe API viene utilizzata per creare istanza gestita da handle, ovvero

    var label = Runtime.GetNSObject<UILabel> (handle);

Questa API restituirà un'istanza gestita esistente (se esiste già) o verrà creata una nuova istanza (se necessario). È già disponibile nell'API unificata sia classico.

Si noti che il `.ctor(NSObjectFlag)` fa ora parte anche `protected` ma questa è stata utilizzata raramente all'esterno di creazione di una sottoclasse.

<a name="NSAction" />

## <a name="nsaction-replaced-with-action"></a>NSAction sostituito con l'azione

Con le API unificata, `NSAction` è stato rimosso a favore .NET standard `Action`. Questo miglioramento è notevole perché `Action` è un tipo .NET comune, mentre `NSAction` era specifico a xamarin. IOS. Svolgono esattamente la stessa operazione, ma sono tipi distinti e non compatibili e ha restituito il codice più la necessità di essere scritti per ottenere lo stesso risultato.

Ad esempio, se l'applicazione esistente di Xamarin è incluso il codice seguente:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
A questo punto, può essere sostituito con una semplice lambda:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

In precedenza sarebbe un errore del compilatore perché un `Action` non può essere assegnato a `NSAction`, tuttavia, poiché `UITapGestureRecognizer` ha ora un `Action` anziché un `NSAction` è valido nelle API unificata.

## <a name="custom-delegates-replaced-with-actiont"></a>Sostituito con l'azione di delegati personalizzati<T>

In **unificata** alcune semplici (ad esempio, un parametro) i delegati di .net sono stati sostituiti con `Action<T>`. Ad esempio,

    public delegate void NSNotificationHandler (NSNotification notification);

può ora essere usato come un `Action<NSNotification>`. Questo codice Alza di livello riutilizzare e ridurre la duplicazione del codice all'interno di xamarin. IOS sia le proprie applicazioni.

## <a name="taskbool-replaced-with-taskbooleannserror"></a>Attività<bool> sostituito con attività < booleano, NSError >>

In **classico** si sono verificati alcuni async API restituzione `Task<bool>`. Tuttavia alcuni di essi in cui vengono utilizzati quando un `NSError` faceva parte della firma, ad esempio il `bool` era già `true` ed era necessario intercettare un'eccezione per ottenere il `NSError`.

Poiché alcuni errori sono molto comuni e il valore restituito non è stato utile questo modello è stato modificato **unificata** per restituire un `Task<Tuple<Boolean,NSError>>`. Ciò consente di verificare l'esito positivo e qualsiasi errore che potrebbe essersi verificato durante la chiamata asincrona.

## <a name="nsstring-vs-string"></a>Stringa di vs NSString

In alcuni casi devono essere modificata da alcune costanti `string` a `NSString`, ad esempio `UITableViewCell`

**Classica**

    public virtual string ReuseIdentifier { get; }

**Unificata**

    public virtual NSString ReuseIdentifier { get; }

In genere è preferibile .NET `System.String` tipo. Tuttavia, nonostante le istruzioni di Apple, alcune API native confrontare i puntatori costanti (non la stringa stessa) e può funzionare solo quando è necessario esporre le costanti come `NSString`.

 <a name="protocols" />

## <a name="objective-c-protocols"></a>Protocolli di Objective-C

API di MonoTouch originale non conteneva il supporto completo per i protocolli ObjC e alcune, non ottimale, sono stati aggiunti per supportare lo scenario più comune. Questa limitazione non esiste più, ma, per compatibilità con le varie API vengono mantenute intorno all'interno di `monotouch.dll` e `XamMac.dll`.

Queste limitazioni sono stati rimosse e pulire sulle API unificata. La maggior parte delle modifiche avrà un aspetto simile al seguente:

**Classica**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unificata**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

Il `I` prefisso significa **unificata** esporre un'interfaccia, anziché un tipo specifico, per il protocollo ObjC. Questo semplificherà casi in cui si desidera sottoclasse il tipo specifico fornito con xamarin. IOS.

È consentito anche alcune API da più precisa e facile da usare, ad esempio:

**Classica**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unificata**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

Queste API sono ora più semplice, senza fa riferimento alla documentazione e il completamento del codice IDE fornirà più utili suggerimenti basati sull'interfaccia/protocollo.

### <a name="nscoding-protocol"></a>Protocollo NSCoding

All'associazione originale incluso un .ctor(NSCoder) per ogni tipo, anche se non supporta il `NSCoding` protocollo.  Un singolo `Encode(NSCoder)` era presente nel metodo il `NSObject` per codificare l'oggetto.
Tuttavia, questo metodo funziona solo se l'istanza è conforme al protocollo NSCoding.

L'API unificata è stato risolto.  I nuovi assembly disporrà solo di `.ctor(NSCoder)` se il tipo è conforme alle `NSCoding`. Anche questi tipi sono ora un `Encode(NSCoder)` metodo conforme al `INSCoding` interfaccia.

Basso impatto: Nella maggior parte dei casi questa modifica non influisce sulle applicazioni come i costruttori precedenti, rimossi, non è possibile usare.

## <a name="further-tips"></a>Ulteriori suggerimenti

Sono elencate le modifiche aggiuntive da tenere presenti nel [suggerimenti per l'aggiornamento di App per l'API unificata](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Codice di esempio

A partire da luglio 31, Microsoft ha pubblicato porte degli esempi di iOS per questa nuova API sul `magic-types` suddiviso [monotouch esempi](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Per Mac, corso un controllo negli esempi inclusi in entrambi i [mac esempi](https://github.com/xamarin/mac-samples) repository (rappresentato da nuove API in Mavericks o Yosemite), nonché esempi di 32/64 bit nel ramo di tipi di chiave [mac esempi](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Collegamenti correlati

- [Aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [L'aggiornamento di App Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [L'aggiornamento di App xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento delle associazioni](~/cross-platform/macios/unified/update-binding.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Aggiornamento dei suggerimenti](~/cross-platform/macios/unified/updating-tips.md)
- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
