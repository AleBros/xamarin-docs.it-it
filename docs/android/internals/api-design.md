---
title: Principi di progettazione di API xamarin. Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: e762a286069d5ef1db90f3c45808eee0a7a04a7f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954285"
---
# <a name="xamarinandroid-api-design-principles"></a>Principi di progettazione di API xamarin. Android


## <a name="overview"></a>Panoramica

Oltre ai core di librerie di classi di Base che fanno parte di Mono, xamarin. Android viene fornito con le associazioni per varie API Android consente agli sviluppatori di creare applicazioni Android native con Mono.

Alla base di xamarin. Android si è un motore di interoperabilità questo mondo Bridge c# con il mondo di Java e offre agli sviluppatori l'accesso alle API Java da c# o altri linguaggi .NET.


## <a name="design-principles"></a>Principi di progettazione

Questi sono alcuni dei nostri principi di progettazione per l'associazione di xamarin. Android

-  Essere conforme per il [linee guida di progettazione di .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

-  Consentire agli sviluppatori di classi Java sottoclasse.

-  Sottoclasse dovrebbe funzionare con costrutti di linguaggio c# standard.

-  Derivare da una classe esistente.

-  Chiamare il costruttore base alla catena.

-  L'override dei metodi devono essere eseguite con C#di eseguire l'override del sistema.

-  Attività comuni di linguaggio facili e disco rigida attività Java rendono possibile.

-  Esporre JavaBean proprietà come proprietà di c#.

-  Esporre un'API fortemente tipizzata:

    - Aumentare l'indipendenza dai tipi.

    - Ridurre al minimo gli errori di runtime.

    - Ottenere intellisense IDE nei tipi restituiti.

    - Consente la documentazione dell'IDE di popup.

-  Incoraggiare esplorazione nell'IDE di API:

    - Utilizzare Framework alternative per l'esposizione di ridurre al minimo Classlib Java.

    - Esporre delegati c# (espressioni lambda, metodi anonimi e System. Delegate) anziché le interfacce singolo metodo, quando appropriato e applicabile.

    - Fornire un meccanismo per chiamare le librerie Java arbitrarie ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).


## <a name="assemblies"></a>Assembly

Xamarin. Android include un numero di assembly che costituiscono il *MonoMobile profilo*. Il [assembly](~/cross-platform/internals/available-assemblies.md) pagina sono disponibili altre informazioni.

I binding per la piattaforma Android sono contenuti nel `Mono.Android.dll` assembly. Questo assembly contiene l'intera associazione per le API di Android dispendiosa in termini di e la comunicazione con il runtime di Android della macchina virtuale.


## <a name="binding-design"></a>Progettazione di associazione


### <a name="collections"></a>Raccolte

Le API di Android usare le raccolte di Java. util in modo esteso per fornire elenchi, set e mappe. È necessario esporre tali elementi utilizzando il [System.Collections.Generic](xref:System.Collections.Generic) interfacce nell'associazione. I mapping fondamentali sono:

-   [java.util.Set<E> ](https://developer.android.com/reference/java/util/Set.html) esegue il mapping al tipo di sistema [ICollection<T>](xref:System.Collections.Generic.ICollection`1), la classe helper [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](https://developer.android.com/reference/java/util/List.html) esegue il mapping al tipo di sistema [IList<T>](xref:System.Collections.Generic.IList`1), la classe helper [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](https://developer.android.com/reference/java/util/Map.html) viene eseguito il mapping al tipo di sistema [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), la classe helper [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](https://developer.android.com/reference/java/util/Collection.html) esegue il mapping al tipo di sistema [ICollection<T>](xref:System.Collections.Generic.ICollection`1), la classe helper [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Sono state fornite le classi helper per semplificare più velocemente copyless marshalling di questi tipi. Quando possibile, è consigliabile usare questi fornito raccolte anziché l'implementazione di framework fornito, ad esempio [ `List<T>` ](xref:System.Collections.Generic.List`1) oppure [ `Dictionary<TKey, TValue>` ](xref:System.Collections.Generic.Dictionary`2). Il [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) implementazioni usano una raccolta nativa di Java internamente e non richiedono la copia verso e da una raccolta nativa quando si passa a un membro dell'API Android.

È possibile passare qualsiasi implementazione dell'interfaccia a un metodo di Android accettazione di tale interfaccia, ad esempio, passare un `List<int>` per il [ArrayAdapter&lt;int&gt;(contesto, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)costruttore. *Tuttavia*, per tutte le implementazioni *tranne* per le implementazioni Android.Runtime, ciò comporta *copia* elenco dalla macchina virtuale di Mono nel runtime di Android della macchina virtuale. Se l'elenco è successivo modificato all'interno del runtime Android (ad esempio, richiamando il [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) metodo), tali modifiche *non* sia visibile nel codice gestito. Se un `JavaList<int>` sono state utilizzate, tali modifiche dovrebbe essere visibile.

Riformulata, le implementazioni dell'interfaccia raccolte *non* nessuno dei precedenti elencati **classe Helper**solo effettuare il marshalling es [In]:

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

Metodi Java vengono trasformati in proprietà, quando appropriato:

-  La coppia di metodi Java `T getFoo()` e `void setFoo(T)` vengono trasformati nel `Foo` proprietà. Esempio: [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  Il metodo Java `getFoo()` viene trasformato nelle proprietà di Foo in sola lettura. Esempio: [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  Non vengono generate proprietà di sola.

-  Le proprietà sono *non* generato se il tipo di proprietà sarebbe una matrice.



### <a name="events-and-listeners"></a>Gli eventi e i listener

Le API di Android basate su Java e i relativi componenti seguono il modello Java per eseguire l'hook dei listener di eventi. Questo modello tende a essere complesso poiché richiede all'utente di creare una classe anonima e dichiarare i metodi per eseguire l'override, ad esempio, si tratta di come farebbe cose in Android con Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

Il codice in c# usando gli eventi equivalente sarebbe:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Si noti che entrambi i meccanismi precedenti sono disponibili con xamarin. Android. È possibile implementare un'interfaccia del listener e associarlo con View.SetOnClickListener, oppure è possibile associare un delegato creato usando uno qualsiasi dei consueto c# paradigmi da utilizzare per l'evento Click.

Quando il metodo di callback del listener ha un tipo restituito void, creiamo elementi API basati su un [EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1) delegare. Si generano un evento simile all'esempio precedente per questi tipi di listener. Tuttavia, se il callback del listener restituisce un oggetto diverso da void e non- **booleana** valore, eventi ed EventHandlers non vengono usati. È invece generare un delegato specifico per la firma del metodo di callback e aggiungere le proprietà anziché gli eventi. Il motivo è per occuparsi di ordine di chiamata delegato e restituire la gestione. Questo approccio riflette ciò che avviene con l'API di xamarin. IOS.

Gli eventi in c# o le proprietà vengono generate automaticamente solo se il metodo di registrazione di eventi Android:

1. È un `set` prefisso, ad esempio [ *set*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. Ha un `void` tipo restituito.

1. Accetta un solo parametro, il tipo di parametro è un'interfaccia, l'interfaccia ha un solo metodo e termina con il nome dell'interfaccia `Listener` , ad esempio [View.OnClick *Listener*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


Inoltre, se il listener del metodo di interfaccia ha un tipo restituito **booleana** invece di **void**, quindi generato *EventArgs* sottoclasse conterrà un *Handled* proprietà. Il valore della *Handled* proprietà viene utilizzata come valore restituito per il *Listener* metodo che per impostazione predefinita `true`.

Ad esempio, di Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) metodo accetta il [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) interfaccia e il [View.OnKeyListener.onKey (visualizzazione, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) metodo presenta un tipo restituito booleano. Xamarin. Android genera un oggetto corrispondente [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) evento, vale a dire una [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
Il *KeyEventArgs* classe a sua volta ha una [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) proprietà, che viene usata come valore restituito per il *View.OnKeyListener.onKey()* (metodo).

Si prevede di aggiungere gli overload per altri metodi e ctor di esporre la connessione basata sul delegato. Inoltre, i listener con più callback richiedono alcune analisi aggiuntive per determinare se implementare singoli callback è ragionevole, in modo da cui viene convertito queste non appena vengono identificati. Se è presente alcun evento corrispondente, i listener devono essere usati nel linguaggio c#, ma. ripristinare quelli che si ritiene che potrebbe avere l'utilizzo di delegato di prestare attenzione. È stato eseguito per alcune conversioni di interfacce senza il suffisso "Listener" anche se era chiaro che possono trarre vantaggio da un'alternativa di delegato.

Tutte le interfacce di listener di implementare il [`Android.Runtime.IJavaObject`](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)
interfaccia, a causa di dettagli di implementazione dell'associazione, in modo che le classi di listener devono implementare questa interfaccia. Questa operazione può essere eseguita implementando l'interfaccia del listener in una sottoclasse [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) o qualsiasi altro stato eseguito il wrapping di oggetti di Java, ad esempio un'attività Android.


### <a name="runnables"></a>Runnables

Java Usa il [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) interfaccia per fornire un meccanismo di delega. Il [java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) classe funge da consumer significativi di questa interfaccia. Android ha impiegato nell'API anche l'interfaccia.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) e [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) sono riportati alcuni esempi importanti.

Il `Runnable` interfaccia contiene un metodo void [Run ()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Pertanto si presta all'associazione in c# come una [System. Action](xref:System.Action) delegare. Sono disponibili overload nell'associazione che accettano un `Action` parametro per tutti i membri di API che usano una `Runnable` nell'API nativo, ad esempio [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) e [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

È stato lasciato il [IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) overload posto invece di sostituirli poiché diversi tipi implementano l'interfaccia e possono quindi essere passati direttamente come runnables.


### <a name="inner-classes"></a>Classi interne

Java ha due tipi diversi di [classi nidificate](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): static annidato classi e le classi statiche.

Le classi annidate statiche Java sono identiche ai tipi annidati c#.

Non statici classi nidificate, chiamate anche *le classi interne*, sono notevolmente diversi. Contengono un riferimento implicito a un'istanza del relativo tipo di inclusione e non può contenere membri statici (tra le altre differenze all'esterno dell'ambito di questa panoramica).

Quando si parla di associazione e l'utilizzo in c#, le classi annidate statiche vengono considerate come tipi annidati normali. Le classi interne, nel frattempo, sono due differenze significative:

1. Il riferimento implicito al tipo contenitore è obbligatorio in modo esplicito come parametro costruttore.

1. Quando si eredita da una classe interna, la classe interna *necessario* è possibile annidare un tipo che eredita dal tipo principale della classe base interna e il tipo derivato deve fornire un costruttore del tipo stesso come il codice c# contenente il tipo.


Si consideri, ad esempio, il [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) classe interna. Poiché si tratta di una classe interna, il [costruttore WallpaperService.Engine()](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) accetta un riferimento a un [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) istanza (confrontare e contrapporre per Java [WallpaperService.Engine ( costruttore),](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) che non accetta parametri).

Una derivazione di esempio di una classe interna è CubeWallpaper.CubeEngine:

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

Nota come `CubeWallpaper.CubeEngine` nidificato all'interno `CubeWallpaper`, `CubeWallpaper` eredita dalla classe di appartenenza del `WallpaperService.Engine`, e `CubeWallpaper.CubeEngine` ha un costruttore che accetta il tipo dichiarante: `CubeWallpaper` in questo caso, tutti come specificato in precedenza.


### <a name="interfaces"></a>Interfacce

Le interfacce Java possono contenere tre set di membri, due dei quali causare problemi da c#:

1. Metodi

1. Tipi

1. Campi


Interfacce Java vengono convertite in due tipi:

1. Interfaccia (facoltativa) che contiene le dichiarazioni di metodo. Questa interfaccia ha lo stesso nome come l'interfaccia Java *eccetto* dispone anche di un ' *ho* ' prefisso.

1. Una classe statica (facoltativa) che contiene tutti i campi dichiarati all'interno dell'interfaccia Java.

I tipi annidati sono "spostati" per essere elementi di pari livello dell'interfaccia di inclusione anziché i tipi annidati, con il nome dell'interfaccia che lo contiene come prefisso.

Si consideri, ad esempio, il [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) interfaccia.
Il *Parcelable* interfaccia contiene i metodi, i tipi annidati e costanti. Il *Parcelable* metodi di interfaccia vengono inseriti nel [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) interfaccia.
Il *Parcelable* costanti di interfaccia vengono inserite nel [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) tipo. Annidato [android.os.Parcelable.ClassLoaderCreator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) e [android.os.Parcelable.Creator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.Creator.html) typy nejsou attualmente associato a causa delle limitazioni nel nostro supporto dei generics; Se sono state supportate, potrebbe essere presente come il *Android.OS.IParcelableClassLoaderCreator* e *Android.OS.IParcelableCreator* interfacce. Ad esempio, annidata [android.os.IBinder.DeathRecpient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) interfaccia viene associata come le [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) interfaccia.


> [!NOTE]
> A partire da xamarin. Android 1.9, le costanti di interfaccia di Java sono <em>duplicato</em> allo scopo di semplificare la portabilità di linguaggio del codice. Ciò consente di migliorare la portabilità codice Java che si basa su [provider android](https://developer.android.com/reference/android/provider/package-summary.html) costanti dell'interfaccia.

Oltre ai tipi sopra, sono disponibili quattro ulteriori modifiche:

1. Per contenere costanti, viene generato un tipo con lo stesso nome come l'interfaccia Java.

1. Tipi che contengono costanti interfaccia anche contengono tutte le costanti che provengono da interfacce Java implementate.

1. Tutte le classi che implementano un'interfaccia di Java che contengono costanti ottenere un nuovo tipo InterfaceConsts annidato che contiene le costanti da tutte le interfacce implementate.

1. Il *Consts* tipo è obsoleto.


Per il *android.os.Parcelable* interfaccia, ciò significa che ora sia un [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) tipo per contenere le costanti. Ad esempio, il [Parcelable.CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) costante verrà associata come il [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) costante, anziché come il  *ParcelableConsts.ContentsFileDescriptor* costante.

Per le interfacce che contiene le costanti che implementano altre interfacce che contiene ancora più costanti, viene generato l'unione di tutte le costanti. Ad esempio, il [android.provider.MediaStore.Video.VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) interfaccia implementa la [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) interfaccia. Tuttavia, prima di 1.9, il [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) tipo non è in grado di accesso alle costanti dichiarate nel [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
Di conseguenza, l'espressione del linguaggio *MediaStore.Video.VideoColumns.TITLE* deve essere associato all'espressione c# *MediaStore.Video.MediaColumnsConsts.Title* che è di difficile individuazione senza la lettura numero elevato di documentazione Java. Nella versione 1.9, l'espressione c# equivalente saranno [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

Inoltre, prendere in considerazione la [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) tipo che implementa il linguaggio *Parcelable* interfaccia. Poiché implementa l'interfaccia, tutte le costanti in quell'interfaccia sono accessibili "tramite" del tipo di aggregazione, ad esempio *Bundle.CONTENTS_FILE_DESCRIPTOR* è un'espressione di Java perfettamente valida.
In precedenza, per questa espressione per la porta C# è necessario esaminare tutte le interfacce implementate per verificare da quale tipo di *CONTENTS_FILE_DESCRIPTOR* proviene. A partire da xamarin. Android 1.9, le classi che implementano le interfacce di Java che includono costanti saranno necessario nidificate *InterfaceConsts* tipo, che conterrà tutte le costanti di interfaccia ereditati. Questa operazione consentirà di traduzione *Bundle.CONTENTS_FILE_DESCRIPTOR* al [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Infine, i tipi con una *Consts* suffisso, ad esempio *Android.OS.ParcelableConsts* sono ora obsoleti, oltre il InterfaceConsts appena introdotti i tipi annidati. Verranno rimossi nella versione 3.0 di xamarin. Android.


## <a name="resources"></a>Risorse

Immagini, descrizioni di layout, i BLOB binari e i dizionari delle stringhe possono essere inclusi nell'applicazione come [file di risorse](https://developer.android.com/guide/topics/resources/providing-resources.html).
Varie API di Android sono progettate per [operano sugli ID risorsa](https://developer.android.com/guide/topics/resources/accessing-resources.html) anziché gestire le immagini, stringhe o binary BLOB direttamente.

Ad esempio, un'app Android di esempio che contiene un layout di interfaccia utente ( `main.axml`), una stringa di internazionalizzazione della tabella ( `strings.xml`) e alcune icone ( `drawable-*/icon.png`) consigliabile mantenere le relative risorse nella directory "Resources" dell'applicazione:

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

L'oggetto nativo API Android non funzionano direttamente con i nomi file, ma invece operare su ID di risorsa. Quando si compila un'applicazione Android che usa le risorse, creare un pacchetto le risorse per la distribuzione del sistema di compilazione e generare una classe denominata `Resource` che contiene i token per ognuna delle risorse incluse. Ad esempio, per il layout di risorse precedente, questo è ciò che consente di esporre la classe di R:

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

Si utilizzerà quindi `Resource.Drawable.icon` per fare riferimento al `drawable/icon.png` file, o `Resource.Layout.main` per fare riferimento al `layout/main.xml` file, o `Resource.String.first_string` a cui fare riferimento la prima stringa nel file del dizionario `values/strings.xml`.


## <a name="constants-and-enumerations"></a>Costanti ed enumerazioni

L'oggetto nativo API Android hanno molti metodi che accettano o restituiscono un valore int che deve essere mappato a un campo costante per determinare il significato di int. Per usare questi metodi, l'utente viene richiesto di consultare la documentazione per vedere quali le costanti sono valori appropriati, che è inferiore a quello ideale.

Ad esempio, prendere in considerazione [Activity.requestWindowFeature (int featureID)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

In questi casi, Microsoft si impegna a raggruppare costanti correlate in un'enumerazione di .NET e modificare il mapping del metodo per eseguire invece l'enumerazione.
In questo modo, siamo in grado di offrire la selezione di IntelliSense dei valori possibili.

Nell'esempio precedente diventa: [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/).

Si tratta di un processo molto manuale a capire quali costanti formano insieme e quali API utilizzano queste costanti. Inviare bug per tutte le costanti usate nell'API che sarebbe meglio espresso sotto forma di enumerazione.
