---
title: Grafica e animazione
description: Android offre un framework molto ricco e diversificato per supportare la grafica e le animazioni 2D. Questo argomento introduce questi Framework e illustra come creare grafica e animazioni personalizzate da usare in un'applicazione Novell. Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: ea713b2b56f18c435f3ec676b42d0aa4802abc6a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755424"
---
# <a name="android-graphics-and-animation"></a>Grafica e animazione Android

_Android offre un framework molto ricco e diversificato per supportare la grafica e le animazioni 2D. Questo argomento introduce questi Framework e illustra come creare grafica e animazioni personalizzate da usare in un'applicazione Novell. Android._

## <a name="overview"></a>Panoramica

Nonostante sia in esecuzione su dispositivi che sono tradizionalmente di potenza limitata, le applicazioni per dispositivi mobili più votate hanno spesso un'esperienza utente sofisticata, completa con grafica e animazioni di alta qualità che offrono un aspetto intuitivo, reattivo e dinamico. Poiché le applicazioni per dispositivi mobili sono più sofisticate, gli utenti hanno iniziato a aspettarsi sempre più da applicazioni.

Fortunatamente per noi, le piattaforme mobili moderne hanno Framework molto potenti per la creazione di animazioni sofisticate e grafici personalizzati, mantenendo al tempo stesso semplicità d'uso. Ciò consente agli sviluppatori di aggiungere interattività avanzate con un impegno minimo.

I Framework API dell'interfaccia utente in Android possono essere suddivisi in due categorie: Grafica e animazione.

I grafici vengono suddivisi ulteriormente in diversi approcci per la grafica 2D e 3D. le immagini 3D sono disponibili tramite diversi Framework incorporati, ad esempio OpenGL ES (una versione specifica per dispositivi mobili di OpenGL) e Framework di terze parti, ad esempio monogame (un toolkit multipiattaforma compatibile con XNA Toolkit). Sebbene i grafici 3D non rientrino nell'ambito di questo articolo, verranno esaminate le tecniche di disegno 2D predefinite.

Android offre due API diverse per la creazione di immagini 2D. Uno è un approccio dichiarativo di alto livello e l'altro un'API di basso livello a livello di codice:

- **Risorse disegnatore** &ndash; Questi vengono usati per creare grafica personalizzata a livello di codice o (più in generale) incorporando le istruzioni di disegno nei file XML. Le risorse disegnatori vengono in genere definite come file XML che contengono istruzioni o azioni per Android per il rendering di un grafico 2D. 

- **Area di disegno** &ndash; si tratta di un'API di basso livello che prevede il disegno diretto su una bitmap sottostante. Fornisce un controllo con granularità fine sugli elementi visualizzati. 

Oltre a queste tecniche grafiche 2D, Android offre anche diversi modi per creare animazioni:

- **Animazioni disegnatori** Android supporta inoltre animazioni frame per frame note come animazioni che è possibile *creare.* &ndash; Si tratta dell'API di animazione più semplice. Android carica in modo sequenziale e visualizza in sequenza le risorse riportabili (molto simile a un fumetto).

- **Visualizza animazioni** Le animazioni di visualizzazione sono le API di animazione originali in Android e sono disponibili in tutte le versioni di Android. &ndash; Questa API è limitata perché funziona solo con gli oggetti visualizzazione ed è in grado di eseguire solo semplici trasformazioni su tali viste.
    Le `/Resources/anim` animazioni di visualizzazione vengono in genere definite nei file XML presenti nella cartella.

- **Animazioni delle proprietà** Android 3,0 ha introdotto un nuovo set di API di animazione note come *animazioni di proprietà*. &ndash; Queste nuove API hanno introdotto un sistema estensibile e flessibile che può essere usato per animare le proprietà di qualsiasi oggetto, non solo per gli oggetti di visualizzazione. Questa flessibilità consente di incapsulare le animazioni in classi distinte che semplificano la condivisione del codice.

Le animazioni di visualizzazione sono più adatte per le applicazioni che devono supportare le API precedenti Android 3,0 (livello API 11). In caso contrario, le applicazioni devono utilizzare le API di animazione delle proprietà più recenti per i motivi citati in precedenza.

Tutti questi Framework sono opzioni valide, tuttavia laddove possibile, è consigliabile assegnare una preferenza alle animazioni delle proprietà, poiché si tratta di un'API più flessibile da usare. Le animazioni di proprietà consentono di incapsulare la logica di animazione in classi distinte che facilitano la condivisione del codice e semplificano la manutenzione del codice.

## <a name="accessibility"></a>Accessibilità

Grafica e animazioni consentono di rendere le app Android attraenti e divertenti da usare. è tuttavia importante ricordare che alcune interazioni si verificano tramite utilità, dispositivi di input alternativi o con zoom assistito.
Inoltre, alcune interazioni possono verificarsi senza funzionalità audio.

In queste situazioni, le app sono più utilizzabili se sono state progettate con l'accessibilità, fornendo suggerimenti e assistenza per la navigazione nell'interfaccia utente e garantendo il contenuto di testo o le descrizioni degli elementi pittorici dell'interfaccia utente.

Per altre informazioni su come usare le API di accessibilità di Android, vedere la [Guida all'accesso facilitato di Google](https://developer.android.com/guide/topics/ui/accessibility/) .

## <a name="2d-graphics"></a>Grafica 2D

Le risorse di estrazione sono una tecnica molto diffusa nelle applicazioni Android. Come per le altre risorse, le risorse che possono &ndash; essere dichiarate sono dichiarative sono definite nei file XML. Questo approccio consente una netta separazione del codice dalle risorse. Questo può semplificare lo sviluppo e la manutenzione, perché non è necessario modificare il codice per aggiornare o modificare la grafica in un'applicazione Android. Tuttavia, mentre le risorse disegnabili sono utili per molti requisiti grafici semplici e comuni, non dispongono della potenza e del controllo dell'API Canvas.

L'altra tecnica, usando l'oggetto [Canvas](xref:Android.Graphics.Canvas) , è molto simile ad altri Framework API tradizionali, ad esempio System. Drawing o il disegno principale di iOS. L'uso dell'oggetto Canvas fornisce il massimo controllo sulla modalità di creazione della grafica 2D. È appropriato per le situazioni in cui una risorsa che può essere disegnato non funziona o sarà difficile da usare. Ad esempio, potrebbe essere necessario creare un controllo dispositivo di scorrimento personalizzato il cui aspetto cambierà in base ai calcoli correlati al valore del dispositivo di scorrimento.

Esaminiamo prima di tutto le risorse disegnatori. Sono più semplici e coprono i casi di disegno personalizzati più comuni.

### <a name="drawable-resources"></a>Risorse disegnatore

Le risorse disegnatore sono definite in un file XML nella directory `/Resources/drawable`. A differenza dell'incorporamento di PNG o JPEG, non è necessario fornire versioni specifiche della densità delle risorse che è possibile creare.
In fase di esecuzione, le risorse vengono caricate da un'applicazione Android e vengono usate le istruzioni contenute in questi file XML per creare immagini 2D.
Android definisce diversi tipi di risorse disegnatori:

- [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; Si tratta di un oggetto disegnabile che disegna una forma geometrica primitiva e applica un set limitato di effetti grafici a tale forma. Sono molto utili per elementi come la personalizzazione dei pulsanti o l'impostazione dello sfondo di TextViews. Verrà visualizzato un esempio di come usare `ShapeDrawable` più avanti in questo articolo.

- [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; Si tratta di una risorsa che può essere disegnato per modificare l'aspetto in base allo stato di un widget/controllo. Un pulsante, ad esempio, può modificare l'aspetto a seconda che sia premuto o meno.

- [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; Questa risorsa di cui è possibile creare uno stack di diversi altri drawables su un'altra. Un esempio di *LayerDrawable* è illustrato nello screenshot seguente:

    ![Esempio di LayerDrawable](graphics-and-animation-images/image1.png)

- [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) Si tratta di un LayerDrawable, ma con una differenza. &ndash; Un *TransitionDrawable* è in grado di animare un livello che viene visualizzato sopra l'altro.

- [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) Questa operazione è molto simile a una StateListDrawable in quanto consente di visualizzare un'immagine in base a determinate condizioni. &ndash; Tuttavia, a differenza di *StateListDrawable*, *LevelListDrawable* Visualizza un'immagine basata su un valore integer. Un esempio di *LevelListDrawable* è la visualizzazione della forza di un segnale Wi-Fi. Con l'attendibilità del segnale Wi-Fi, l'oggetto disegnatore visualizzato verrà modificato di conseguenza.

- [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; Come implica il nome, questi drawables forniscono funzionalità di ridimensionamento e ritaglio. Il *ScaleDrawable* ridimensiona un altro oggetto disegnatore, mentre *ClipDrawable* ridurrà un altro oggetto disegnatore.

- [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; Questo oggetto disegnatore applicherà i set sui lati di un'altra risorsa che può essere disegnato. Viene usato quando una visualizzazione necessita di uno sfondo inferiore ai limiti effettivi della visualizzazione.

- XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; questo file è un set di istruzioni, in formato XML, che devono essere eseguite su una bitmap effettiva. Alcune azioni che Android è in grado di eseguire sono affiancate, dithering e anti-aliasing. Uno degli usi molto comuni di questa operazione consiste nell'affiancare una bitmap sullo sfondo di un layout.

#### <a name="drawable-example"></a>Esempio disegnato

Viene ora esaminato un esempio di come creare un grafico 2D usando un `ShapeDrawable`. Un `ShapeDrawable` oggetto può definire una delle quattro forme di base, ovvero rettangolo, ovale, linea e anello. È anche possibile applicare effetti di base, ad esempio sfumature, colori e dimensioni. Il codice XML seguente è `ShapeDrawable` un oggetto che può essere trovato nel progetto complementare *AnimationsDemo* (nel `Resources/drawable/shape_rounded_blue_rect.xml`file).
Definisce un rettangolo con sfondo sfumato viola e angoli arrotondati:

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

È possibile fare riferimento a questa risorsa disegnato in modo dichiarativo in un layout o in un'altra tracciabile, come illustrato nel codice XML seguente:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

È anche possibile applicare le risorse di cui è possibile creare a livello di codice. Nel frammento di codice seguente viene illustrato come impostare a livello di codice lo sfondo di un oggetto TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Per visualizzare l'aspetto di questa operazione, eseguire il progetto *AnimationsDemo* e selezionare l'elemento disegnato per la forma dal menu principale. Verrà visualizzata una schermata simile alla seguente:

![TextView con uno sfondo personalizzato, disegnato con una sfumatura e angoli arrotondati](graphics-and-animation-images/image1.png)

Per altri dettagli sugli elementi XML e sulla sintassi delle risorse di cui è disponibile il progetto, vedere la [documentazione di Google](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).

### <a name="using-the-canvas-drawing-api"></a>Uso dell'API di disegno Canvas

Drawables sono potenti ma hanno limitazioni. Alcune operazioni non sono possibili o molto complesse (ad esempio, l'applicazione di un filtro a un'immagine eseguita da una fotocamera sul dispositivo). Sarebbe molto difficile applicare la riduzione degli occhi rossi usando una risorsa che è possibile creare.
L'API Canvas consente invece a un'applicazione di avere un controllo con granularità fine per modificare selettivamente i colori in una parte specifica dell'immagine.

Una classe comunemente utilizzata con l'area di disegno è la classe [Paint](xref:Android.Graphics.Paint) . Questa classe include informazioni su colori e stile su come creare. Viene usato per fornire elementi quali colore e trasparenza.

L'API Canvas usa il *modello del pittore* per creare grafica 2D.
Le operazioni vengono applicate a livelli successivi uno sull'altro. Ogni operazione riguarderà un'area della bitmap sottostante. Quando l'area si sovrappone a un'area disegnata in precedenza, il nuovo disegno ridurrà parzialmente o completamente la vecchia. Questo è lo stesso modo in cui molte altre API di disegno, ad esempio System. Drawing e la grafica di base di iOS, funzionano.

Esistono due modi per ottenere un `Canvas` oggetto. Il primo modo prevede la definizione di un oggetto [bitmap](xref:Android.Graphics.Bitmap) e la successiva creazione `Canvas` di un'istanza di un oggetto. Ad esempio, il frammento di codice seguente crea una nuova area di disegno con una bitmap sottostante:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

L'altro modo per ottenere un oggetto `Canvas` è tramite il metodo di callback [OnDraw](xref:Android.Views.View.OnDraw*) fornito dalla classe di base di [visualizzazione](xref:Android.Views.View). Questo metodo viene chiamato da Android quando decide che la visualizzazione deve essere disegnata e passa `Canvas` un oggetto affinché la visualizzazione funzioni con.

La classe Canvas espone i metodi per fornire le istruzioni di disegno a livello di codice. Ad esempio:

- [Canvas. DrawPaint](xref:Android.Graphics.Canvas.DrawPaint*) &ndash; riempie l'intera bitmap dell'area di disegno con il disegno specificato.

- [Canvas. DrawPath](xref:Android.Graphics.Canvas.DrawPath*) &ndash; disegna la forma geometrica specificata usando il disegno specificato.

- [Canvas. DrawText](xref:Android.Graphics.Canvas.DrawText*) &ndash; disegna il testo nell'area di disegno con il colore specificato. Il testo viene disegnato in corrispondenza `x,y` della posizione.

#### <a name="drawing-with-the-canvas-api"></a>Disegno con l'API Canvas

Di seguito è riportato un esempio dell'API Canvas in azione. Il frammento di codice seguente mostra come creare una visualizzazione:

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

Il codice precedente crea prima di tutto una vernice rossa e un oggetto di colore verde. Riempie il contenuto dell'area di disegno con il rosso, quindi indica all'area di disegno di creare un rettangolo verde che corrisponde al 25% della larghezza dell'area di disegno. Un esempio può essere visualizzato in Project incluso nel `AnimationsDemo` codice sorgente per questo articolo. Avviando l'applicazione e selezionando l'elemento del disegno dal menu principale, viene visualizzata una schermata simile alla seguente:

![Schermata con oggetti di colore rosso e colore verde](graphics-and-animation-images/image3.png)

## <a name="animation"></a>Animazione

Utenti come elementi che si spostano nelle proprie applicazioni. Le animazioni sono un ottimo modo per migliorare l'esperienza utente di un'applicazione e aiutarla a distinguersi. Le animazioni migliori sono quelle che gli utenti non sono in grado di notare perché si sentono naturali. Android offre le tre API seguenti per le animazioni:

- **Visualizza animazione** &ndash; Si tratta dell'API originale. Queste animazioni sono associate a una visualizzazione specifica e possono eseguire semplici trasformazioni sul contenuto della visualizzazione. Grazie alla semplicità, questa API è ancora utile per elementi come le animazioni Alpha, le rotazioni e così via.

- **Animazione proprietà** &ndash; Le animazioni delle proprietà sono state introdotte in Android 3,0. Consentono a un'applicazione di animare praticamente qualsiasi elemento. Le animazioni di proprietà possono essere usate per modificare qualsiasi proprietà di qualsiasi oggetto, anche se tale oggetto non è visibile sullo schermo.

- **Animazione disegnatore** &ndash; Si tratta di una risorsa speciale che viene utilizzata per applicare un effetto di animazione molto semplice ai layout.

In generale, l'animazione delle proprietà è il sistema preferito da usare perché è più flessibile e offre altre funzionalità.

### <a name="view-animations"></a>Visualizza animazioni

Le animazioni di visualizzazione sono limitate alle viste e possono eseguire animazioni solo su valori quali punti di inizio e di fine, dimensioni, rotazione e trasparenza.
Questi tipi di animazioni vengono in genere definiti *animazioni di interpolazione*. Le animazioni di visualizzazione possono essere definite &ndash; in due modi a livello di codice o tramite file XML. I file XML rappresentano il modo migliore per dichiarare le animazioni di visualizzazione, perché sono più leggibili e più semplici da gestire.

I file XML di animazione verranno archiviati nella `/Resources/anim` directory di un progetto Novell. Android. Questo file deve avere uno degli elementi seguenti come elemento radice:

- `alpha`&ndash; Animazione di dissolvenza o dissolvenza.

- `rotate`&ndash; Animazione di rotazione.

- `scale`&ndash; Animazione di ridimensionamento.

- `translate`&ndash; Movimento orizzontale e/o verticale.

- `set`&ndash; Contenitore che può ospitare uno o più elementi dell'animazione.

Per impostazione predefinita, tutte le animazioni in un file XML verranno applicate simultaneamente. Per rendere le animazioni sequenziali, impostare l' `android:startOffset` attributo su uno degli elementi definiti in precedenza.

È possibile influire sulla frequenza di modifica in un'animazione utilizzando un *interpolatore*. Un interpolatore rende possibile l'accelerazione, la ripetizione o la decelerazione degli effetti dell'animazione. Il Framework Android offre più interpolatori predefiniti, ad esempio (ma non limitati):

- `AccelerateInterpolator`/questiinterpolatori aumentano o diminuiscono il tasso di modifica in un'animazione. `DecelerateInterpolator` &ndash;

- `BounceInterpolator`&ndash; la modifica viene rimbalzata alla fine.

- `LinearInterpolator`&ndash; la frequenza delle modifiche è costante.

Nel codice XML seguente viene illustrato un esempio di file di animazione che combina alcuni di questi elementi:

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

Questa animazione eseguirà tutte le animazioni simultaneamente. La prima animazione con scalabilità estenderà l'immagine orizzontalmente e la ridurrà verticalmente, quindi l'immagine verrà ruotata simultaneamente di 45 gradi in senso antiorario e compatta, scomparendo dallo schermo.

L'animazione può essere applicata a livello di codice a una visualizzazione ingrandindo l'animazione e quindi applicando tale animazione a una visualizzazione. Android fornisce la classe `Android.Views.Animations.AnimationUtils` helper che consente di ingrandire una risorsa di animazione e restituire un'istanza di. `Android.Views.Animations.Animation` Questo oggetto viene applicato a una vista `StartAnimation` chiamando e passando l' `Animation` oggetto. Il frammento di codice seguente mostra un esempio di questo:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Ora che sono state apprese le nozioni di base sul funzionamento delle animazioni di visualizzazione, è possibile passare alle animazioni delle proprietà.

### <a name="property-animations"></a>Animazioni delle proprietà

Gli animatori di proprietà sono una nuova API introdotta in Android 3,0.
Forniscono un'API più estendibile che può essere usata per aggiungere un'animazione a qualsiasi proprietà di qualsiasi oggetto.

Tutte le animazioni di proprietà vengono create dalle istanze della sottoclasse [Animator](xref:Android.Animation.Animator). Le applicazioni non usano direttamente questa classe, ma usano una delle sottoclassi seguenti:

- [ValueAnimator](xref:Android.Animation.ValueAnimator) &ndash; Questa classe è la classe più importante nell'intera API di animazione delle proprietà. Calcola i valori delle proprietà che devono essere modificate. `ViewAnimator` Non aggiorna direttamente tali valori, bensì genera eventi che possono essere utilizzati per aggiornare gli oggetti animati.

- [ObjectAnimator](xref:Android.Animation.ObjectAnimator) Questa classe è una sottoclasse `ValueAnimator`di. &ndash; È progettato per semplificare il processo di animazione degli oggetti accettando un oggetto di destinazione e una proprietà da aggiornare.

- [Animazioni](xref:Android.Animation.AnimatorSet) &ndash; Questa classe è responsabile dell'orchestrazione dell'esecuzione delle animazioni in relazione tra loro. Le animazioni possono essere eseguite contemporaneamente, in sequenza o con un ritardo specificato tra di essi.

Gli *analizzatori* sono classi speciali utilizzate dagli animatori per calcolare i nuovi valori durante un'animazione. Android fornisce gli analizzatori seguenti:

- [IntEvaluator](xref:Android.Animation.IntEvaluator) &ndash; Calcola i valori per le proprietà Integer.

- [FloatEvaluator](xref:Android.Animation.FloatEvaluator) &ndash; Calcola i valori per le proprietà float.

- [ArgbEvaluator](xref:Android.Animation.ArgbEvaluator) &ndash; Calcola i valori per le proprietà dei colori.

Se la proprietà a cui si sta aggiungendo un'animazione `float`non `int` è un oggetto, o color, le applicazioni possono creare il proprio `ITypeEvaluator` analizzatore implementando l'interfaccia. (L'implementazione di analizzatori personalizzati esula dall'ambito di questo argomento).

#### <a name="using-the-valueanimator"></a>Uso di ValueAnimator

Per qualsiasi animazione sono disponibili due parti: il calcolo dei valori animati e l'impostazione di tali valori nelle proprietà di un oggetto. 
[ValueAnimator](xref:Android.Animation.ValueAnimator) calcolerà solo i valori, ma non funzionerà direttamente con gli oggetti. Gli oggetti verranno invece aggiornati all'interno dei gestori eventi che verranno richiamati durante la durata dell'animazione. Questa progettazione consente di aggiornare diverse proprietà da un valore animato.

Per ottenere un'istanza di `ValueAnimator` , chiamare uno dei metodi factory seguenti:

- `ValueAnimator.OfInt`
- `ValueAnimator.OfFloat`
- `ValueAnimator.OfObject`

Al termine dell'operazione, è `ValueAnimator` necessario impostare la durata dell'istanza, che può quindi essere avviata. Nell'esempio seguente viene illustrato come aggiungere un'animazione a un valore compreso tra 0 e 1 nell'intervallo di 1000 millisecondi:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Tuttavia, il frammento di codice precedente non è molto &ndash; utile perché l'animatore verrà eseguito, ma non esiste alcuna destinazione per il valore aggiornato. La `Animator` classe genererà l'evento di aggiornamento quando decide che è necessario informare i listener di un nuovo valore. Le applicazioni possono fornire un gestore eventi per rispondere a questo evento, come illustrato nel frammento di codice seguente:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

Ora che si è a conoscenza di `ValueAnimator`, è possibile ottenere altre informazioni `ObjectAnimator`su.

#### <a name="using-the-objectanimator"></a>Uso di ObjectAnimator

[ObjectAnimator](xref:Android.Animation.ObjectAnimator) è una sottoclasse `ViewAnimator` di che combina il motore di temporizzazione e il `ValueAnimator` calcolo del valore di con la logica necessaria per collegare i gestori eventi. Per `ValueAnimator` eseguire questa operazione, è necessario che le applicazioni per &ndash; collegare in modo esplicito un gestore `ObjectAnimator` eventi si occupino di questo passaggio.

L'API per `ObjectAnimator` è molto simile all'API per `ViewAnimator`, ma richiede di specificare l'oggetto e il nome della proprietà da aggiornare. Nell'esempio seguente viene illustrato un esempio di `ObjectAnimator`utilizzo di:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Come si può notare dal frammento di codice precedente `ObjectAnimator` , può ridurre e semplificare il codice necessario per animare un oggetto.

### <a name="drawable-animations"></a>Animazioni disegnatori

L'API di animazione finale è l'API di animazione che può essere disegnato. Le animazioni disegnatori caricano una serie di risorse che è possibile creare una dopo l'altra e le visualizzano in sequenza, in modo analogo a un fumetto Flip-it.

Le risorse disegnatore sono definite in un file XML che include `<animation-list>` un elemento come elemento radice e una serie di `<item>` elementi che definiscono ogni frame nell'animazione. Questo file XML è archiviato nella `/Resource/drawable` cartella dell'applicazione. Il codice XML seguente è un esempio di animazione disegnatore:

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

Questa animazione verrà eseguita con sei frame. L' `android:duration` attributo dichiara per quanto tempo verrà visualizzato ogni frame. Il frammento di codice successivo mostra un esempio di creazione di un'animazione disegnatore e di avvio quando l'utente fa clic su un pulsante sullo schermo:

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

A questo punto sono state analizzate le fondamenta delle API di animazione disponibili in un'applicazione Android.

## <a name="summary"></a>Riepilogo

In questo articolo sono stati introdotti numerosi nuovi concetti e API che consentono di aggiungere alcuni elementi grafici a un'applicazione Android. In primo luogo, sono state illustrate le diverse API grafiche 2D e viene illustrato come Android consente alle applicazioni di creare direttamente sullo schermo usando un oggetto Canvas. Sono state inoltre illustrate alcune tecniche alternative che consentono la creazione dichiarativa di elementi grafici tramite file XML. È stato quindi illustrato il vecchio e il nuovo API per la creazione di animazioni in Android.

## <a name="related-links"></a>Collegamenti correlati

- [Demo sull'animazione (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/animationdemo)
- [Animazione e grafica](https://developer.android.com/guide/topics/graphics/index.html)
- [Uso delle animazioni per la vita delle tue app per dispositivi mobili](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](xref:Android.Graphics.Drawables.AnimationDrawable)
- [Canvas](xref:Android.Graphics.Canvas)
- [Animatore oggetto](xref:Android.Animation.ObjectAnimator)
- [Animatore value](xref:Android.Animation.ValueAnimator)
