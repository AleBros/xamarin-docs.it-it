---
title: Principi di progettazione dell'API Novell. Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 0b3d8fc4836f6f6d1f6bf30b555e3c5c285678f0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756857"
---
# <a name="xamarinandroid-api-design-principles"></a>Principi di progettazione dell'API Novell. Android

Oltre alle librerie di classi base core che fanno parte di mono, Novell. Android viene fornito con binding per varie API Android per consentire agli sviluppatori di creare applicazioni Android Native con mono.

Alla base di Novell. Android è disponibile un motore di interoperabilità che C# colma il mondo con Java World e fornisce agli sviluppatori l'accesso alle API Java C# da o altri linguaggi .NET.

## <a name="design-principles"></a>Principi di progettazione

Questi sono alcuni dei principi di progettazione per l'associazione di Novell. Android

- Sono conformi alle [linee guida di progettazione .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

- Consente agli sviluppatori di sottoclassare classi Java.

- La sottoclasse dovrebbe C# funzionare con costrutti standard.

- Derivare da una classe esistente.

- Chiamare il costruttore di base per concatenare.

- L'override dei metodi deve essere eseguito C#con il sistema di override di.

- Semplifica le attività Java comuni e le attività Java rigide.

- Esporre le proprietà JavaBean C# come proprietà.

- Esporre un'API fortemente tipizzata:

  - Aumentare l'indipendenza dai tipi.

  - Ridurre al minimo gli errori di Runtime.

  - Ottenere IntelliSense IDE sui tipi restituiti.

  - Consente la documentazione popup IDE.

- Incoraggiare l'esplorazione nell'IDE delle API:

  - Usare alternative del Framework per ridurre al minimo l'esposizione a CLASSLIB Java.

  - Esporre C# delegati (espressioni lambda, metodi anonimi e System. Delegate) anziché interfacce a metodo singolo quando appropriato e applicabile.

  - Fornire un meccanismo per chiamare le librerie Java arbitrarie ( [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv)).

## <a name="assemblies"></a>Assembly

Novell. Android include diversi assembly che costituiscono il *profilo monomobile*. Ulteriori informazioni sono contenute nella pagina [assembly](~/cross-platform/internals/available-assemblies.md) .

Le associazioni alla piattaforma Android sono contenute nell' `Mono.Android.dll` assembly. Questo assembly contiene l'intera associazione per l'utilizzo di API Android e la comunicazione con la VM di runtime di Android.

## <a name="binding-design"></a>Progettazione dell'associazione

### <a name="collections"></a>Raccolte

Le API Android utilizzano ampiamente le raccolte Java. util per fornire elenchi, set e mappe. Questi elementi vengono esposti usando le interfacce [System. Collections. Generic](xref:System.Collections.Generic) nell'associazione. I mapping fondamentali sono:

- [Java. util. set\<E >](https://developer.android.com/reference/java/util/Set.html) è mappato al tipo di sistema [\<ICollection T >](xref:System.Collections.Generic.ICollection`1), classe helper [Android. Runtime\<. JavaSet T >](xref:Android.Runtime.JavaSet`1).

- [Java. util. List\<E >](https://developer.android.com/reference/java/util/List.html) viene mappato al tipo di sistema [IList\<t >](xref:System.Collections.Generic.IList`1), classe helper [Android. Runtime.\<Java elenco >](xref:Android.Runtime.JavaList`1).

- [java. util. Map < K, v >](https://developer.android.com/reference/java/util/Map.html) esegue il mapping al tipo di sistema [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), classe helper [Android. Runtime. JavaDictionary < K, v >](xref:Android.Runtime.JavaDictionary`2).

- [Java. util. Collection\<E >](https://developer.android.com/reference/java/util/Collection.html) è mappato al tipo di sistema [ICollection\<T >](xref:System.Collections.Generic.ICollection`1), classe helper [Android. Runtime.\<javacollection t >](xref:Android.Runtime.JavaCollection`1).

Sono state fornite classi helper per facilitare il marshalling non copiato più veloce di questi tipi. Quando possibile, è consigliabile usare queste raccolte fornite anziché l'implementazione fornita dal Framework, ad [`List<T>`](xref:System.Collections.Generic.List`1) esempio [`Dictionary<TKey, TValue>`](xref:System.Collections.Generic.Dictionary`2)o. Le implementazioni di [Android. Runtime](xref:Android.Runtime) usano una raccolta Java nativa internamente e pertanto non richiedono la copia da e verso una raccolta nativa quando passano a un membro API Android.

È possibile passare qualsiasi implementazione dell'interfaccia a un metodo Android accettando tale interfaccia, ad esempio `List<int>` passando un oggetto al costruttore [classe ArrayAdapter&lt;&gt;int (context,&lt;int&gt;, IList int)](xref:Android.Widget.ArrayAdapter`1) . *Tuttavia*, per tutte le implementazioni *ad eccezione* delle implementazioni di Android. Runtime, ciò comporta la *copia* dell'elenco dalla VM mono alla VM di runtime di Android. Se l'elenco viene modificato in seguito nel runtime di Android, ad esempio richiamando [classe ArrayAdapter&lt;T&gt;. Aggiungere (T)](xref:Android.Widget.ArrayAdapter`1.Add*) il metodo), le modifiche *non* saranno visibili nel codice gestito. Se si `JavaList<int>` utilizza un oggetto, le modifiche saranno visibili.

Riformulate, le implementazioni dell'interfaccia di raccolte che *non* sono una delle **classi helper**elencate in precedenza eseguono solo il marshalling [in]:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```

### <a name="properties"></a>Proprietà

I metodi Java vengono trasformati in proprietà, quando necessario:

- La coppia `T getFoo()` di metodi Java `void setFoo(T)` e viene trasformata nella `Foo` proprietà. Esempio: [Activity. Intent](xref:Android.App.Activity.Intent).

- Il metodo `getFoo()` Java viene trasformato nella proprietà Foo di sola lettura. Esempio: [Context. PackageName](xref:Android.Content.Context.PackageName).

- Non vengono generate proprietà di sola impostazione.

- Le proprietà *non* vengono generate se il tipo di proprietà è una matrice.

### <a name="events-and-listeners"></a>Eventi e listener

Le API Android sono basate su Java e i relativi componenti seguono il modello Java per l'associazione di listener di eventi. Questo modello tende a essere complesso perché richiede che l'utente crei una classe anonima e dichiari i metodi di cui eseguire l'override. ad esempio, questo è il modo in cui le operazioni vengono eseguite in Android con Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

Il codice equivalente nell' C# utilizzo degli eventi sarà:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Si noti che entrambi i meccanismi precedenti sono disponibili con Novell. Android. È possibile implementare un'interfaccia del listener e associarla a View. SetOnClickListener oppure è possibile associare un delegato creato tramite uno dei paradigmi C# usuali all'evento click.

Quando il metodo di callback del listener ha un valore restituito void, vengono creati elementi API basati su un delegato [TEventArgs&lt;&gt; EventHandler](xref:System.EventHandler`1) . Viene generato un evento come l'esempio precedente per questi tipi di listener. Tuttavia, se il callback del listener restituisce un valore non void e non **booleano** , gli eventi e gli EventHandler non vengono usati. Viene invece generato un delegato specifico per la firma del callback e si aggiungono le proprietà anziché gli eventi. Il motivo è quello di gestire l'ordine di chiamata dei delegati e la gestione delle restituzione. Questo approccio rispecchia le operazioni eseguite con l'API Novell. iOS.

C#gli eventi o le proprietà vengono generati automaticamente solo se il metodo di registrazione di eventi Android:

1. Ha un `set` prefisso, ad esempio [ *set*OnClickListener](xref:Android.Views.View.SetOnClickListener*).

1. Ha un `void` tipo restituito.

1. Accetta un solo parametro, il tipo di parametro è un'interfaccia, l'interfaccia dispone di un solo metodo e il nome dell'interfaccia `Listener` termina in, ad esempio il [ *listener*View. OnClick](xref:Android.Views.View.IOnClickListener).

Inoltre, se il metodo dell'interfaccia del listener ha un tipo restituito **booleano** anziché **void**, la sottoclasse *EventArgs* generata conterrà una proprietà *gestita* . Il valore della proprietà *Handled* viene usato come valore restituito per il metodo *listener* e per `true`impostazione predefinita è.

Ad esempio, il metodo [View. setOnKeyListener ()](xref:Android.Views.View.SetOnKeyListener*) di Android accetta l'interfaccia [View. OnKeyListener](xref:Android.Views.View.IOnKeyListener) e il metodo [View. OnKeyListener. onKey (View, int, fileEvent)](xref:Android.Views.View.IOnKeyListener.OnKey*) ha un tipo restituito booleano. Novell. Android genera un evento [View. KeyPress](xref:Android.Views.View.KeyPress) corrispondente, che è una [visualizzazione&lt;EventHandler. KeyEventArgs&gt;](xref:Android.Views.View.KeyEventArgs).
La classe *KeyEventArgs* a sua volta ha una proprietà [View. KeyEventArgs. Handled](xref:Android.Views.View.KeyEventArgs.Handled) , che viene usata come valore restituito per il metodo *View. OnKeyListener. onKey ()* .

Si prevede di aggiungere overload per altri metodi e ctor per esporre la connessione basata su delegati. Inoltre, i listener con più callback richiedono un'ispezione aggiuntiva per determinare se l'implementazione di singoli callback è ragionevole, quindi la conversione di tali callback viene effettuata quando vengono identificati. Se non è presente alcun evento corrispondente, i listener devono essere usati C#in, ma è necessario riportare l'utilizzo dei delegati. Sono state inoltre eseguite alcune conversioni delle interfacce senza il suffisso "listener" quando era chiaro che trarrebbero vantaggio da un'alternativa del delegato.

Tutte le interfacce dei listener implementano[`Android.Runtime.IJavaObject`](xref:Android.Runtime.IJavaObject)
interfaccia, a causa dei dettagli di implementazione dell'associazione, pertanto le classi listener devono implementare questa interfaccia. Questa operazione può essere eseguita implementando l'interfaccia del listener su una sottoclasse di [java. lang. Object](xref:Java.Lang.Object) o di qualsiasi altro oggetto Java sottoposto a wrapper, ad esempio un'attività Android.

### <a name="runnables"></a>Eseguibili

Java usa l'interfaccia [java. lang. eseguibile](xref:Java.Lang.Runnable) per fornire un meccanismo di delega. La classe [java. lang. thread](xref:Java.Lang.Thread) è un consumatore rilevante di questa interfaccia. Android ha usato anche l'interfaccia nell'API.
Gli esempi [Activity. runOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) e [View.post ()](xref:Android.Views.View.Post*) sono significativi.

L' `Runnable` interfaccia contiene un solo metodo void, [Run ()](xref:Java.Lang.Runnable.Run). Si presta quindi al binding in C# come delegato [System. Action](xref:System.Action) . Nell'associazione sono stati forniti overload `Action` che accettano un parametro per tutti i membri dell'API che utilizzano un oggetto `Runnable` nell'API nativa, ad esempio [Activity. RunOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) e [View.post ()](xref:Android.Views.View.Post*).

Abbiamo lasciato invariati gli overload di [IRunnable](xref:Java.Lang.IRunnable) anziché sostituirli poiché diversi tipi implementano l'interfaccia e possono quindi essere passati direttamente come eseguibili.

### <a name="inner-classes"></a>Classi interne

Java dispone di due tipi diversi di [classi annidate](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html), ovvero classi annidate statiche e classi non statiche.

Le classi annidate statiche Java sono C# identiche ai tipi annidati.

Le classi annidate non statiche, denominate anche *classi interne*, sono molto diverse. Contengono un riferimento implicito a un'istanza del relativo tipo di inclusione e non possono contenere membri statici (tra le altre differenze al di fuori dell'ambito di questa panoramica).

Per quanto riguarda l'associazione e C# l'utilizzo, le classi annidate statiche vengono considerate come tipi annidati normali. Le classi interne, nel frattempo, presentano due differenze significative:

1. Il riferimento implicito al tipo che lo contiene deve essere specificato in modo esplicito come parametro del costruttore.

1. Quando si eredita da una classe interna, la classe interna *deve* essere annidata all'interno di un tipo che eredita dal tipo che lo contiene della classe interna di base e il tipo derivato deve fornire un costruttore dello stesso tipo C# del tipo che lo contiene.

Si consideri ad esempio la classe interna [Android. Service. wallpaper. WallpaperService. Engine](xref:Android.Service.Wallpaper.WallpaperService.Engine) . Poiché si tratta di una classe interna, il [Costruttore WallpaperService. Engine ()](xref:Android.Service.Wallpaper.WallpaperService.Engine#ctor) accetta un riferimento a un'istanza di [WallpaperService](xref:Android.Service.Wallpaper.WallpaperService) (confronto e contrasto con il costruttore Java WallpaperService. Engine () che non accetta parametri).

Un esempio di derivazione di una classe interna è CubeWallpaper. CubeEngine:

```csharp
class CubeWallpaper : WallpaperService {
    public override WallpaperService.Engine OnCreateEngine ()
    {
        return new CubeEngine (this);
    }

    class CubeEngine : WallpaperService.Engine {
        public CubeEngine (CubeWallpaper s)
                : base (s)
        {
        }
    }
}
```

Si noti `CubeWallpaper.CubeEngine` che la classe `CubeWallpaper`è annidata all'interno di, `WallpaperService.Engine`eredita dalla `CubeWallpaper.CubeEngine` classe contenitore di e dispone di un costruttore che accetta il `CubeWallpaper` tipo dichiarante, in questo caso, `CubeWallpaper` come specificato in precedenza.

### <a name="interfaces"></a>Interfacce

Le interfacce Java possono contenere tre set di membri, due dei quali provocano C#problemi:

1. Metodi

1. Tipi

1. Campi

Le interfacce Java vengono convertite in due tipi:

1. Interfaccia (facoltativa) che contiene dichiarazioni di metodo. Questa interfaccia ha lo stesso nome dell'interfaccia Java, *ma* ha anche un prefisso ' *I* '.

1. Classe statica (facoltativa) contenente tutti i campi dichiarati all'interno dell'interfaccia java.

I tipi annidati sono "rilocati" come elementi di pari livello dell'interfaccia di inclusione anziché dei tipi annidati, con il nome dell'interfaccia di inclusione come prefisso.

Si consideri ad esempio l'interfaccia [Android. OS.](xref:Android.OS.Parcelable) .
L'interfaccia a *pacchetti* contiene metodi, tipi annidati e costanti. I metodi di interfaccia a *pacchetti* sono inseriti nell'interfaccia [Android. OS. IParcelable](xref:Android.OS.IParcelable) .
Le costanti di interfaccia a *pacchetti* sono inserite nel tipo [Android. OS. ParcelableConsts](xref:Android.OS.ParcelableConsts) . I tipi di > di [Android. OS. ClassLoaderCreator\<> t](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) annidati e Android. OS. compilable [.\<Creator t](https://developer.android.com/reference/android/os/Parcelable.Creator.html) non sono attualmente associati a causa delle limitazioni del supporto per i generics; se sono supportati, sarebbero presenti come interfacce *Android. OS. IParcelableClassLoaderCreator* e *Android. OS. IParcelableCreator* . Ad esempio, l'interfaccia di [Android. OS. IBinder. DeathRecipient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) annidata è associata come interfaccia [Android. OS. IBinderDeathRecipient](xref:Android.OS.IBinderDeathRecipient) .

> [!NOTE]
> A partire da Novell. Android 1,9, le costanti di interfaccia Java vengono _duplicate_ nel tentativo di semplificare il porting del codice Java. Questo consente di migliorare il porting di codice Java che si basa sulle costanti dell'interfaccia del [provider Android](https://developer.android.com/reference/android/provider/package-summary.html) .

Oltre ai tipi precedenti, sono disponibili quattro ulteriori modifiche:

1. Un tipo con lo stesso nome dell'interfaccia Java viene generato per contenere costanti.

1. I tipi contenenti costanti di interfaccia contengono anche tutte le costanti derivanti dalle interfacce Java implementate.

1. Tutte le classi che implementano un'interfaccia Java che contiene costanti ottengono un nuovo tipo InterfaceConsts annidato che contiene costanti da tutte le interfacce implementate.

1. Il tipo di *svantaggi* è ora obsoleto.

Per l'interfaccia *Android. OS. Parcelable* , questo significa che ora sarà disponibile un tipo [*Android. OS. pacchi*](xref:Android.OS.Parcelable) per contenere le costanti. La costante [CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) , ad esempio, può essere associata come costante con [*estensione ContentsFileDescriptor*](xref:Android.OS.Parcelable.ContentsFileDescriptor) , anziché come costante *ParcelableConsts. ContentsFileDescriptor* .

Per le interfacce che contengono costanti che implementano altre interfacce che contengono ancora più costanti, viene ora generata l'Unione di tutte le costanti. Ad esempio, l'interfaccia [Android. provider. Mediastore. video. VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) implementa l'interfaccia [Android. provider. Mediastore. MediaColumns](xref:Android.Provider.MediaStore.MediaColumns) . Tuttavia, prima della 1,9, il tipo [Android. provider. Mediastore. video. VideoColumnsConsts](xref:Android.Provider.MediaStore.Video.VideoColumnsConsts) non ha alcun modo per accedere alle costanti dichiarate in [Android. provider. Mediastore. MediaColumnsConsts](xref:Android.Provider.MediaStore.MediaColumnsConsts).
Di conseguenza, l'espressione Java *Mediastore. video. VideoColumns. title* deve essere associata all' C# espressione *Mediastore. video. MediaColumnsConsts. title* , che è difficile da rilevare senza leggere molti documenti Java. In 1,9, l'espressione C# equivalente sarà [Mediastore. video. VideoColumns. title](xref:Android.Provider.MediaStore.Video.VideoColumns.Title).

Inoltre, si consideri il tipo [Android. OS. bundle](xref:Android.OS.Bundle) , che implementa l'interfaccia java per i *pacchetti* . Poiché implementa l'interfaccia, tutte le costanti su tale interfaccia sono accessibili da "tramite" al tipo di bundle, ad esempio *bundle. CONTENTS_FILE_DESCRIPTOR* è un'espressione Java perfettamente valida.
In precedenza, per trasferire questa espressione C# a è necessario esaminare tutte le interfacce implementate per vedere da quale tipo deriva *CONTENTS_FILE_DESCRIPTOR* . A partire da Novell. Android 1,9, le classi che implementano le interfacce Java che contengono costanti avranno un tipo *InterfaceConsts* annidato che conterrà tutte le costanti di interfaccia ereditate. Questa operazione consentirà di convertire *bundle. CONTENTS_FILE_DESCRIPTOR* in [*bundle. InterfaceConsts. ContentsFileDescriptor*](xref:Android.OS.Bundle.InterfaceConsts.ContentsFileDescriptor).

Infine, i tipi con suffisso *const* , ad esempio *Android. OS. ParcelableConsts* , sono ora obsoleti, oltre ai tipi annidati InterfaceConsts appena introdotti. Verranno rimossi in Novell. Android 3,0.

## <a name="resources"></a>Risorse

Le immagini, le descrizioni dei layout, i BLOB binari e i dizionari di stringa possono essere inclusi nell'applicazione come [file di risorse](https://developer.android.com/guide/topics/resources/providing-resources.html).
Diverse API Android sono progettate per [operare sugli ID di risorsa](https://developer.android.com/guide/topics/resources/accessing-resources.html) invece di gestire direttamente immagini, stringhe o BLOB binari.

Ad esempio, un'app Android di esempio che contiene un layout dell'interfaccia `main.axml`utente (), una stringa della tabella `strings.xml`di internazionalizzazione () e `drawable-*/icon.png`alcune icone () mantengono le risorse nella directory "Resources" dell'applicazione:

```
Resources/
    drawable-hdpi/
        icon.png

    drawable-ldpi/
        icon.png

    drawable-mdpi/
        icon.png

    layout/
        main.axml

    values/
        strings.xml
```

Le API native di Android non funzionano direttamente con i nomi di file, ma operano invece con gli ID di risorsa. Quando si compila un'applicazione Android che usa risorse, il sistema di compilazione comprimerà le risorse per la distribuzione e genererà una classe denominata `Resource` che contiene i token per ciascuna delle risorse incluse. Ad esempio, per il layout delle risorse sopra riportato, questo è ciò che verrà esposto dalla classe R:

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

`Resource.Drawable.icon` Si utilizzerà quindi per fare riferimento `drawable/icon.png` al file o `Resource.Layout.main` per fare riferimento `layout/main.xml` al file o `Resource.String.first_string` per fare riferimento alla prima stringa nel file `values/strings.xml`del dizionario.

## <a name="constants-and-enumerations"></a>Costanti ed enumerazioni

Le API native di Android hanno molti metodi che accettano o restituiscono un valore int che deve essere mappato a un campo costante per determinare il significato di int. Per usare questi metodi, è necessario che l'utente consulti la documentazione per vedere quali costanti sono valori appropriati, il che è inferiore alla soluzione ideale.

Ad esempio, si consideri [Activity. requestWindowFeature (int FeatureId)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

In questi casi, si tenterà di raggruppare le costanti correlate in un'enumerazione .NET e di eseguire il mapping del metodo in modo da assumere invece l'enumerazione.
In questo modo, è possibile offrire la selezione IntelliSense dei valori potenziali.

L'esempio precedente diventa: [Activity. RequestWindowFeature (WindowFeatures FeatureId)](xref:Android.App.Activity.RequestWindowFeature*).

Si noti che si tratta di un processo manuale per determinare quali costanti appartengono e quali API utilizzano tali costanti. Inserire i bug per tutte le costanti usate nell'API che verrebbero espresse in modo migliore come enumerazione.
