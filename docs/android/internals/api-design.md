---
title: Principi di progettazione delle API di xamarin
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 611046954e8ef359476d2bd12a69f04041d869f1
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="xamarinandroid-api-design-principles"></a>Principi di progettazione delle API di xamarin


## <a name="overview"></a>Panoramica

Oltre alle librerie di classi di Base che fanno parte di Mono di core, xamarin viene fornito con le associazioni per varie API Android consente agli sviluppatori di creare applicazioni Android native con Mono.

Sono la base di xamarin è un motore di interoperabilità che world Bridge c# con il mondo Java e offre agli sviluppatori l'accesso alle API Java da c# o altri linguaggi .NET.


## <a name="design-principles"></a>Principi di progettazione

Questi sono alcuni dei nostri principi di progettazione per l'associazione di xamarin

-  Rispettare le [linee guida di progettazione di .NET Framework](http://msdn.microsoft.com/en-us/library/ms229042.aspx).

-  Consente agli sviluppatori di classi Java sottoclasse.

-  Sottoclasse dovrebbe funzionare con i costrutti di linguaggio c# standard.

-  Derivare una classe esistente.

-  Chiamare il costruttore base a catena.

-  L'override dei metodi deve essere eseguita con il sistema di sostituzione #.

-  Rendere possibili attività comuni di Java semplice e attività Java disco rigida.

-  Espongono proprietà JavaBean come proprietà in c#.

-  Esporre un'API fortemente tipizzata:

    - Aumentare l'indipendenza dai tipi.

    - Ridurre al minimo gli errori di runtime.

    - Ottenere intellisense IDE in tipi restituiti.

    - Consente di documentazione popup IDE.

-  Incoraggiare l'esplorazione nell'IDE di API:

    - Utilizzare Framework alternative per l'esposizione di ridurre al minimo Classlib Java.

    - Esporre c# delegati (espressioni lambda, metodi anonimi e System. Delegate) anziché interfacce singolo metodo, quando appropriato e applicabile.

    - Forniscono un meccanismo per chiamare librerie Java arbitrarie ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).


## <a name="assemblies"></a>Assembly

Xamarin include un numero di assembly che costituiscono il *MonoMobile profilo*. Il [assembly](~/cross-platform/internals/available-assemblies.md) pagina è disponibili altre informazioni.

Le associazioni per la piattaforma Android sono contenute nel `Mono.Android.dll` assembly. Questo assembly contiene l'intera associazione per le API di Android consumer e la comunicazione con il runtime Android VM.


## <a name="binding-design"></a>Progettazione di associazione


### <a name="collections"></a>Raccolte

Le API Android utilizzare le raccolte di Java. util in modo esteso per fornire gli elenchi, set e mappe. È necessario esporre questi elementi utilizzando il [System.Collections.Generic](https://developer.xamarin.com/api/namespace/System.Collections.Generic/) interfacce all'associazione. I mapping fondamentali sono:

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html) esegue il mapping al tipo di sistema [ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx), classe helper [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html) esegue il mapping al tipo di sistema [IList<T>](http://msdn.microsoft.com/en-us/library/5y536ey6.aspx), classe helper [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](http://developer.android.com/reference/java/util/Map.html) esegue il mapping al tipo di sistema [IDictionary < TKey, TValue >](http://msdn.microsoft.com/en-us/library/s4ys34ea.aspx), classe helper [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html) esegue il mapping al tipo di sistema [ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx), classe helper [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Vengono fornite le classi di supporto per facilitare copyless più velocemente il marshalling di questi tipi. Quando possibile, è consigliabile utilizzare questi fornito raccolte anziché l'implementazione del framework fornito, come [ `List<T>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.List%601/) o [ `Dictionary<TKey, TValue>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%602/). Il [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) implementazioni utilizzare internamente una raccolta di Java nativa e pertanto non richiedono la copia in e da una raccolta nativa quando si passa a un membro di API Android.

È possibile passare qualsiasi implementazione dell'interfaccia a un metodo Android accettazione di tale interfaccia, ad esempio, passare un `List<int>` per il [ArrayAdapter&lt;int&gt;(contesto, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)costruttore. *Tuttavia*, per tutte le implementazioni *tranne* per le implementazioni di Android.Runtime, questa operazione richiede *copia* elenco dalla macchina virtuale in fase di esecuzione Android VM Mono. Se l'elenco è successive modificato all'interno del runtime Android (ad esempio richiamando il [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) metodo), tali modifiche *non* visibile nel codice gestito. Se un `JavaList<int>` sono stati utilizzati, tali modifiche dovrebbe essere visibile.

Rephrased, le implementazioni di interfaccia raccolte *non* uno di quelli precedenti elencati **classe Helper**solo effettuare il marshalling es [In]:

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

Metodi di Java vengono trasformati in proprietà, quando appropriato:

-  La coppia di metodi Java `T getFoo()` e `void setFoo(T)` vengono trasformati nel `Foo` proprietà. Esempio: [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  Il metodo Java `getFoo()` viene trasformato nella proprietà Foo di sola lettura. Esempio: [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  Non vengono generate solo set di proprietà.

-  Le proprietà sono *non* generato se il tipo della proprietà è una matrice.



### <a name="events-and-listeners"></a>Gli eventi e i listener

Le API di Android sono basate su Java e i relativi componenti seguono il modello di linguaggio per il collegamento di listener di eventi. Questo modello tende a essere complessa poiché richiede all'utente di creare una classe anonima e dichiarare i metodi per eseguire l'override, ad esempio, si tratta come operazioni possono essere eseguite in Android con Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

È l'equivalente del codice in c# utilizzando gli eventi:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Si noti che entrambi i meccanismi sopra sono disponibili con xamarin. È possibile implementare l'interfaccia del listener e associarlo con View.SetOnClickListener, oppure è possibile associare un delegato creato tramite uno qualsiasi dei normali c# paradigmi all'evento clic.

Quando il metodo di callback del listener è un tipo restituito void, creare elementi API in base a un [EventHandler&lt;TEventArgs&gt; ](https://developer.xamarin.com/api/type/System.EventHandler%601/) delegato. Si generano un evento simile all'esempio precedente per questi tipi di listener. Tuttavia, se il callback del listener restituisce un oggetto diverso da void e non- **booleano** valore, eventi ed EventHandlers non vengono utilizzati. È invece di generare un delegato specifico per la firma del metodo di callback e di aggiungere proprietà anziché gli eventi. Il motivo consiste nel gestire con ordine di chiamata delegato e restituire la gestione. Questo approccio rispecchia quella che viene effettuata con l'API di xamarin. IOS.

Proprietà o eventi in c# vengono generate automaticamente solo se il metodo di registrazione di eventi Android:

1. È un `set` prefisso, ad esempio [ *impostare*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. È un `void` tipo restituito.

1. Accetta un solo parametro, il tipo di parametro è un'interfaccia, l'interfaccia dispone di un solo metodo e termina con il nome dell'interfaccia `Listener` , ad esempio [View.OnClick *Listener*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


Inoltre, se il listener del metodo di interfaccia ha un tipo restituito di **booleano** anziché **void**, quindi generato *EventArgs* sottoclasse conterrà un *Handled* proprietà. Il valore della *Handled* proprietà viene utilizzata come valore restituito per il *Listener* metodo e il valore predefinito è `true`.

Ad esempio, di Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) metodo accetta il [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) interfaccia e [View.OnKeyListener.onKey (vista, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) metodo presenta un tipo restituito boolean. Xamarin genera un corrispondente [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) evento, ovvero un [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
Il *KeyEventArgs* classe a sua volta ha un [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) proprietà, viene utilizzata come valore restituito per il *View.OnKeyListener.onKey()* metodo.

Si prevede di aggiungere gli overload per altri metodi e costruttori di esporre la connessione basata sul delegato. Inoltre, i listener con più callback richiedono alcuni controlli supplementari per determinare se è ragionevole, implementazione di callback singoli in modo in cui viene convertito questi non appena vengono identificati. Se è presente alcun evento corrispondente, i listener devono essere utilizzati in c#, ma. portare i che si ritiene che potrebbe avere sull'utilizzo di delegato a conoscenza. Alcune conversioni di interfacce senza il suffisso "Listener" inoltre svolto quando ne è stato crittografato che può costituire un vantaggio un'alternativa di delegato.

Tutte le interfacce del listener di implementare il [ `Android.Runtime.IJavaObject` ](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interfaccia, a causa di dettagli di implementazione dell'associazione, pertanto le classi di listener devono implementare questa interfaccia. Questa operazione può essere eseguita implementando l'interfaccia listener su una sottoclasse di [lang](https://developer.xamarin.com/api/type/Java.Lang.Object/) o qualsiasi altro stato eseguito il wrapping di oggetti Java, ad esempio un'attività di Android.


### <a name="runnables"></a>Runnables

Prevede l'utilizzo di Java di [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) interfaccia per fornire un meccanismo di delega. Il [java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) classe funge da consumer significative di questa interfaccia. Android è utilizzata l'interfaccia nell'API anche.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) e [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) sono riportati alcuni esempi importanti.

Il `Runnable` interfaccia contiene un singolo metodo void, [Run ()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Pertanto si presta all'associazione in c# come un [Action](http://msdn.microsoft.com/en-us/library/system.action.aspx) delegato. Sono disponibili nell'associazione gli overload che accettano un `Action` parametro per tutti i membri di API che utilizzano un `Runnable` nell'API nativo, ad esempio [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) e [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

È stato lasciato il [IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) overload sul posto, invece di sostituirli poiché diversi tipi di implementano l'interfaccia e pertanto possono essere passato come runnables direttamente.


### <a name="inner-classes"></a>Classi interne

Java esistono due tipi diversi di [classi annidate](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): statico annidati di classi e le classi statiche.

Classi annidate statiche Java sono identiche ai tipi annidati c#.

Non statico classi nidificate, dette anche *classi interne*, sono notevolmente diversi. Essi contengono un riferimento implicito a un'istanza del relativo tipo di inclusione e non può contenere membri statici (tra le altre differenze all'esterno dell'ambito di questa panoramica).

Quando si tratta di associazione e l'utilizzo di c#, classi annidate statiche vengono considerate tipi annidati normali. Classi interne, nel frattempo sono due differenze significative:

1. Il riferimento implicito per il tipo contenitore è obbligatorio in modo esplicito come un parametro di costruttore.

1. Quando si eredita da una classe interna, la classe interna *deve* essere annidata all'interno di un tipo che eredita dal tipo di contenitore della classe base interna e il tipo derivato deve fornire un costruttore dello stesso tipo c# contenente il tipo.


Si consideri ad esempio il [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) classe interna. Poiché si tratta di una classe interna, la [WallpaperService.Engine() costruttore](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) accetta un riferimento a un [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) istanza (confrontare e contrapporre per il linguaggio [(WallpaperService.Engine costruttore),](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) che non accetta parametri).

Derivazione un esempio di una classe interna è CubeWallpaper.CubeEngine:

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

Si noti come `CubeWallpaper.CubeEngine` viene nidificata all'interno `CubeWallpaper`, `CubeWallpaper` eredita dalla classe di appartenenza di `WallpaperService.Engine`, e `CubeWallpaper.CubeEngine` ha un costruttore che accetta il tipo dichiarante - `CubeWallpaper` in questo caso, tutti come specificato in precedenza.


### <a name="interfaces"></a>Interfacce

Interfacce di Java possono contenere tre set di membri, due dei quali causare problemi di c#:

1. Metodi

1. Tipi

1. Campi


Interfacce Java vengono convertite in due tipi:

1. Interfaccia (facoltativa) contenente le dichiarazioni di metodo. Questa interfaccia ha lo stesso nome dell'interfaccia Java, *tranne* include inoltre un ' *si* ' prefisso.

1. Una classe statica (facoltativa) che contiene tutti i campi dichiarati all'interno dell'interfaccia Java.

I tipi annidati sono "spostati" per essere elementi di pari livello dell'interfaccia inclusione anziché i tipi annidati, con il nome dell'interfaccia che lo contiene come prefisso.

Si consideri ad esempio il [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) interfaccia.
Il *Parcelable* interfaccia contiene i metodi, tipi annidati e costanti. Il *Parcelable* vengono inseriti i metodi dell'interfaccia di [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) interfaccia.
Il *Parcelable* costanti di interfaccia vengono inserite i [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) tipo. Nidificata [android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) e [android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html) tipi non sono attualmente associato a causa di limitazioni al supporto per generics; Se sono supportate, potrebbe essere presente come il *Android.OS.IParcelableClassLoaderCreator* e *Android.OS.IParcelableCreator* interfacce. Ad esempio, nidificata [android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) interfaccia viene associata come il [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) interfaccia.


> [!NOTE]
> A partire da xamarin 1.9, sono costanti interfaccia Java <em>duplicato</em> allo scopo di semplificare la portabilità di linguaggio del codice. Ciò consente di migliorare la conversione di codice Java che si basa su [provider android](http://developer.android.com/reference/android/provider/package-summary.html) interfaccia costanti.

Oltre ai tipi sopra, sono disponibili quattro ulteriori modifiche:

1. Per contenere costanti, viene generato un tipo con lo stesso nome dell'interfaccia Java.

1. Tipi che contengono costanti interfaccia inoltre contengono tutte le costanti che provengono da interfacce Java implementate.

1. Tutte le classi che implementano un'interfaccia Java che contengono costanti ottenere un nuovo tipo InterfaceConsts nidificato che contiene le costanti di tutte le interfacce implementate.

1. Il *costanti* tipo è obsoleto.


Per il *android.os.Parcelable* interfaccia, ciò significa che vi sarà un [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) tipo per contenere le costanti. Ad esempio, il [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) costante verrà associata come il [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) costante, anziché come il  *ParcelableConsts.ContentsFileDescriptor* costante.

Per le interfacce che contiene le costanti che implementano altre interfacce che contiene ancora più costanti, viene generata l'unione di tutte le costanti. Ad esempio, il [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) interfaccia implementa il [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) interfaccia. Tuttavia, prima di 1.9, il [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) tipo non ha modo di accesso alle costanti dichiarate in [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
Di conseguenza, l'espressione Java *MediaStore.Video.VideoColumns.TITLE* deve essere associato all'espressione c# *MediaStore.Video.MediaColumnsConsts.Title* che è difficile individuare senza lettura numero elevato di documentazione Java. In 1.9, sarà l'espressione c# equivalente [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

Inoltre, prendere in considerazione il [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) tipo che implementa il linguaggio *Parcelable* interfaccia. Poiché implementa l'interfaccia, tutte le costanti di tale interfaccia sono accessibili "" tipo di aggregazione, ad esempio *Bundle.CONTENTS_FILE_DESCRIPTOR* è un'espressione Java perfettamente valida.
In precedenza, eseguire il porting di questa espressione c# è necessario esaminare tutte le interfacce implementate per verificare da quale tipo di *CONTENTS_FILE_DESCRIPTOR* provenienza. A partire da xamarin 1.9, le classi che implementano interfacce Java che contengono costanti saranno necessario nidificate *InterfaceConsts* tipo, che conterrà tutte le costanti di interfaccia ereditati. In questo modo la traduzione *Bundle.CONTENTS_FILE_DESCRIPTOR* a [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Infine, i tipi con un *costanti* suffisso, ad esempio *Android.OS.ParcelableConsts* sono ora obsoleti, diverso da InterfaceConsts i nuovi tipi annidati. Verranno rimossi in xamarin 3.0.


## <a name="resources"></a>Risorse

Immagini, le descrizioni di layout, BLOB binari e dizionari stringa possono essere incluso nell'applicazione come [file di risorse](http://developer.android.com/guide/topics/resources/providing-resources.html).
Varie API di Android sono progettate per [operano su ID di risorsa](http://developer.android.com/guide/topics/resources/accessing-resources.html) anziché gestire immagini, stringhe o binario BLOB direttamente.

Ad esempio, un'app Android di esempio che contiene un layout dell'interfaccia utente ( `main.axml`), una stringa nella tabella di internazionalizzazione ( `strings.xml`) e alcune icone ( `drawable-*/icon.png`), mantiene le risorse nella directory "Resources" dell'applicazione:

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

L'oggetto nativo API Android non funzionano direttamente con i nomi di file, ma invece operare su ID di risorsa. Le risorse per la distribuzione del pacchetto quando si compila un'applicazione Android che utilizza le risorse, il sistema di compilazione e generare una classe denominata `Resource` che contiene i token per ognuna delle risorse incluse. Ad esempio, per il layout di risorse precedente, questo è ciò che consente di esporre la classe:

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

Si utilizzerà quindi `Resource.Drawable.icon` per fare riferimento il `drawable/icon.png` file, o `Resource.Layout.main` al riferimento di `layout/main.xml` file, o `Resource.String.first_string` per fare riferimento alla prima stringa nel file del dizionario `values/strings.xml`.


## <a name="constants-and-enumerations"></a>Costanti ed enumerazioni

Nativo API Android dispone di molti metodi che accettano o restituiscono un valore int che deve essere mappato a un campo costante per determinare il significato di int. Per utilizzare questi metodi, l'utente è necessario consultare la documentazione per visualizzare le costanti sono valori appropriati, che è minore di ideale.

Si consideri ad esempio [Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

In questi casi, Microsoft si impegna a raggruppare costanti correlate in un'enumerazione .NET e modificare il mapping del metodo per richiedere l'enumerazione invece.
In questo modo, siamo in grado di offrire selezione IntelliSense dei valori possibili.

L'esempio precedente diventa: [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/)).

Si noti che questo è un processo molto manuale per scoprire quali costanti appartengono, e le API utilizzano queste costanti. Inviare bug per l'utilizzo delle costanti dell'API che sarebbe meglio espresse sotto forma di enumerazione.
