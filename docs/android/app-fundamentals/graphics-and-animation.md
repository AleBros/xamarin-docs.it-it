---
title: Grafica e animazione
description: Android fornisce un framework molto avanzato e diverse per il supporto grafica 2D e le animazioni. In questo argomento introduce questi Framework e viene illustrato come creare grafici personalizzati e le animazioni per l'utilizzo in un'applicazione di xamarin.
ms.topic: article
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 90a2eb219ae1189e7a48e60cde9761e3e9e93e0b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="graphics-and-animation"></a>Grafica e animazione

_Android fornisce un framework molto avanzato e diverse per il supporto grafica 2D e le animazioni. In questo argomento introduce questi Framework e viene illustrato come creare grafici personalizzati e le animazioni per l'utilizzo in un'applicazione di xamarin._

<a name="Overview" />

## <a name="overview"></a>Panoramica

Nonostante in esecuzione su dispositivi che sono in genere di energia elettrica limitato, applicazioni per dispositivi mobili con classificazione più alta hanno spesso una sofisticata utente esperienza (UX), completo di grafica di alta qualità e le animazioni che forniscono un aspetto dinamico, intuitiva e reattiva. Quando più applicazioni per dispositivi mobili e più sofisticati, gli utenti hanno iniziato prevedere più dalle applicazioni.

Per fortuna, piattaforme per dispositivi mobili moderni sono molto potente Framework per la creazione di sofisticate animazioni e grafica personalizzata mantenendo la semplicità d'uso. Ciò consente agli sviluppatori di aggiungere interattività completo con il minimo sforzo.

Il framework di API dell'interfaccia utente in Android approssimativamente può essere diviso in due categorie: grafica e animazione.

Grafica viene suddivisa ulteriormente in diversi approcci per l'esecuzione di grafica 2D e 3D. Grafica 3D è disponibile tramite un numero di compilazione nel Framework, ad esempio OpenGL ES (una specifica versione mobile di OpenGL) e il framework di terze parti, ad esempio MonoGame (multipiattaforma toolkit compatibile con il toolkit XNA). Nonostante la grafica 3D non sia all'interno dell'ambito di questo articolo, esamineremo le tecniche di disegnare 2D incorporate.

Android fornisce due diverse API per la creazione di grafici 2D. Uno è un approccio dichiarativo di alto livello e l'altro è un'API di basso livello a livello di codice:

-   **Risorse drawable** &ndash; che vengono utilizzati per creare grafici personalizzati a livello di codice o (in genere) incorporando istruzioni di disegno in file XML. Risorse drawable sono in genere definite come file XML che contengono istruzioni o azioni per Android per il rendering di un grafico 2D. 

-   **Area di disegno** &ndash; si tratta di un'API di basso livello che è necessario disegnare direttamente in una bitmap sottostante. Consente di controllare in modo dettagliato gli elementi visualizzati. 

Oltre a queste tecniche di grafica 2D, Android offre diversi modi per creare animazioni:

-   **Le animazioni drawable** &ndash; Android supporta inoltre le animazioni frame singolarmente, note come *Drawable animazione*. Questo è l'API di animazione più semplice. Android in modo sequenziale carica e Visualizza risorse Drawable in sequenza (analogamente a un cartoni animati).

-   **Visualizzare le animazioni** &ndash; *animazioni visualizzazione* sono l'originale dell'API di animazione in Android e sono disponibili in tutte le versioni di Android. Questa API è limitata in quanto funziona solo con gli oggetti di visualizzazione e può eseguire solo le trasformazioni semplice in tali viste.
    Le animazioni di visualizzazione sono in genere definite in file XML, vedere il `/Resources/anim` cartella.

-   **Proprietà animazioni** &ndash; 3.0 Android introdotto un nuovo set di API di animazione, noto come *animazioni proprietà*. Queste API nuovo è stato introdotto un sistema estensibile e flessibile che consente di animare la proprietà di qualsiasi oggetto, non solo di visualizzare gli oggetti. Questa flessibilità consente le animazioni incapsulare le classi distinte in modo più semplice di condivisione del codice.


Le animazioni di visualizzazione sono più adatte per le applicazioni che devono supportare il precedente pre-Android 3.0 dell'API (API livello 11). In caso contrario, le applicazioni devono utilizzare dell'API di animazione proprietà più recenti per i motivi citati in precedenza.

Tutti questi Framework sono possibili opzioni, tuttavia se possibile, deve preferenza per le animazioni a proprietà, perché è un'API più flessibile per funzionare con. Proprietà animazioni consentono la logica di animazione per incapsulare le classi distinte che rende più semplice di condivisione del codice e semplifica la manutenzione del codice.


## <a name="accessibility"></a>Accessibilità

Grafica e le animazioni contribuiscono a rendere le app Android interessante e divertente da utilizzare. Tuttavia, è importante tenere presente che alcune interazioni eseguite tramite screenreaders, dispositivi di input alternativi, con zoom assistito.
Inoltre, alcune interazioni possono verificarsi senza le funzionalità audio.

Le app sono più facilmente utilizzabili in questi casi, se sono stati progettati con accessibilità: fornisce suggerimenti e assistenza di navigazione nell'interfaccia utente e verificare sia presente il contenuto di testo o le descrizioni per gli elementi di grafici dell'interfaccia utente.

Fare riferimento a [Guida di accessibilità di Google](http://developer.android.com/guide/topics/ui/accessibility/) per ulteriori informazioni su come utilizzare l'accessibilità di Android API.


<a name="2D_Graphics" />

## <a name="2d-graphics"></a>Grafica 2D

Risorse drawable sono una tecnica comune in applicazioni Android. Come con altre risorse, Drawable risorse dichiarative &ndash; sono definiti nel file XML. Questo approccio consente una netta separazione del codice dalle risorse. Ciò consente di semplificare lo sviluppo e manutenzione perché non è necessario modificare il codice per aggiornare o modificare gli elementi grafici in un'applicazione Android. Tuttavia, mentre le risorse Drawable sono utili per molti requisiti grafici semplici e comuni, dispongono di potenza e controllo dell'API di disegno.

La tecnica, utilizzando il [area di disegno](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) oggetto, è molto simile per altri Framework API tradizionali, ad esempio System. Drawing o disegno Core di iOS. Utilizzo dell'oggetto area di disegno fornisce maggiore controllo degli elementi grafici 2D come vengono creati. È adatto per situazioni in cui una risorsa Drawable non funzioneranno o sarà difficili da gestire. Ad esempio, potrebbe essere necessario disegnare un controllo dispositivo di scorrimento personalizzata il cui aspetto verrà modificato in base a calcoli relativi al valore del dispositivo di scorrimento.

Esaminiamo innanzitutto Drawable risorse. Sono più semplici e coprono il maggior parte dei casi di disegnati personalizzati.

<a name="Drawable Resources" />

### <a name="drawable-resources"></a>Risorse drawable

Risorse drawable vengono definite in un file XML nella directory `/Resources/drawable`. A differenza di incorporare PNG o del JPEG, non è necessario fornire versioni specifiche di densità di Drawable risorse.
In fase di esecuzione, un'applicazione Android caricherà queste risorse e utilizzare le istruzioni contenute in questi file XML per creare grafici 2D.
Android definisce diversi tipi di risorse Drawable:

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; questo è un oggetto Drawable che disegna una forma geometrica primitiva e applica un set limitato di effetti grafici in tale forma. Sono molto utili per operazioni quali la personalizzazione di pulsanti o impostazione dello sfondo di oggetti TextViews. Vedremo un esempio di come utilizzare un `ShapeDrawable` più avanti in questo articolo.

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; si tratta di una risorsa Drawable che cambia aspetto in base allo stato di un widget o controllo. Ad esempio, un pulsante può modificarne l'aspetto a seconda se si è premuto o meno.

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; della risorsa Drawable che vengono impilate su diversi altri drawables uno su altro. Un esempio di un *LayerDrawable* viene visualizzato nella schermata seguente:

    ![Esempio LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; si tratta di un *LayerDrawable* ma con una differenza. Oggetto *TransitionDrawable* è in grado di aggiungere un'animazione a un livello visualizzati sopra un altro.

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; è molto simile a un *StateListDrawable* , che venga visualizzata un'immagine in base a determinate condizioni. Tuttavia, a differenza di un *StateListDrawable*, *LevelListDrawable* Visualizza un'immagine basata su un valore intero. Un esempio di un *LevelListDrawable* , è possibile visualizzare il livello di attendibilità di un segnale Wi-Fi. Come la complessità delle modifiche di segnale Wi-Fi drawable visualizzato verrà modificato.

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; come suggerisce il nome, questi Drawables fornire scalabilità e funzionalità di ritaglio. Il *ScaleDrawable* consente di scalare in un altro while Drawable, il *ClipDrawable* ritagliano Drawable un altro.

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; questo Drawable applicherà Inset sui lati di un'altra risorsa Drawable. Viene utilizzato quando una vista deve uno sfondo a dimensioni inferiori a quelle dei limiti effettivi della vista.

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; questo file è un set di istruzioni nel codice XML, che devono essere eseguite su una bitmap effettivi. Alcune azioni che è possibile eseguire Android sono porzioni di dithering e anti-aliasing. Uno degli usi molto comuni di questo oggetto è affiancato lo sfondo di un layout di una bitmap.


#### <a name="drawable-example"></a>Esempio drawable

Esaminiamo un esempio semplice di come creare un grafico 2D utilizzando un `ShapeDrawable`. Oggetto `ShapeDrawable` possibile definire una delle quattro forme di base: rettangolo, ellissi, linee e anello. È inoltre possibile applicare gli effetti di base, ad esempio sfumatura, colore e dimensioni. Il codice XML seguente è un `ShapeDrawable` che può essere presente nella *AnimationsDemo* progetto complementare (nel file `Resources/drawable/shape_rounded_blue_rect.xml`).
Definisce un rettangolo con uno sfondo sfumato viola e gli angoli arrotondati:

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

È possibile fare riferimento a questa risorsa Drawable in modo dichiarativo in un Layout o altri Drawable come illustrato nel codice XML seguente:

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

Risorse drawable possono essere applicate anche a livello di codice. Frammento di codice riportato di seguito viene illustrato come impostare a livello di codice lo sfondo di un oggetto TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Per visualizzare questo aspetto, eseguire il *AnimationsDemo* del progetto e selezionare l'elemento forma Drawable dal menu principale. Dovremmo vedere qualcosa di simile per la schermata seguente:

![TextView con uno sfondo personalizzato, drawable con un angoli arrotondati e sfumatura](graphics-and-animation-images/image1.png)

Per ulteriori informazioni sugli elementi XML e sintassi di risorse Drawable, consultare [documentazione di Google](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).

<a name="Using the Canvas Drawing API" />

### <a name="using-the-canvas-drawing-api"></a>Tramite l'API di disegno di area di disegno

Drawables sono potenti ma presentano alcune limitazioni. Alcuni elementi sono molto complesso o non è possibile (ad esempio: applicazione di un filtro a un'immagine che è stata eseguita da una fotocamera del dispositivo). Sarebbe molto difficile applicare la riduzione occhi rossi utilizzando una risorsa Drawable.
L'API di disegno, invece, consente a un'applicazione a un controllo specifico da modificare in modo selettivo i colori in una parte specifica dell'immagine.

Una classe che viene comunemente utilizzata con l'area di disegno è il [Paint](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) classe. Questa classe contiene colori e lo stile di informazioni sulle modalità di disegno. Utilizzato per fornire elementi di un colore e una trasparenza.

Usa l'API di disegno di *modello di associazione* per disegnare la grafica 2D.
Le operazioni vengono applicate in livelli successivi sopra l'altro. Ogni operazione verrà illustrate alcune area dell'immagine bitmap della sottostante. Quando l'area si sovrappone a un'area disegnata in precedenza, il nuovo disegno parzialmente o completamente occultare precedente. Questo è analogo a quello di lavoro molte altre API di disegnati, ad esempio System. Drawing e grafica di base di iOS.

Esistono due modi per ottenere un `Canvas` oggetto. Il primo modo prevede la definizione di un [Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) oggetto e quindi creare un'istanza un `Canvas` oggetto con esso. Ad esempio, il frammento di codice seguente crea una nuova area di disegno con una bitmap sottostante:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

L'altro modo per ottenere un `Canvas` è oggetto di [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) metodo di callback che viene fornito il [vista](https://developer.xamarin.com/api/type/Android.Views.View/) classe di base. Android chiama questo metodo quando decide di una vista è necessario per il disegno e passa un `Canvas` oggetto per la visualizzazione da utilizzare.

La classe di area di disegno espone metodi per fornire a livello di codice le istruzioni di disegno. Ad esempio:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; riempie bitmap dell'intera area con il colore specificato.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; Disegna una forma geometrica specificata utilizzando il colore specificato.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; disegna il testo nell'area di disegno con il colore specificato. Il testo viene disegnato nella posizione `x,y` .


<a name="Drawing with the Canvas API" />

#### <a name="drawing-with-the-canvas-api"></a>Disegno con l'API di disegno

Di seguito viene illustrato un esempio dell'area di disegno API in azione. Frammento di codice riportato di seguito viene illustrato come creare una vista:

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

Questo codice è possibile creare prima un disegno rossa e un oggetto di colore verde. Inserisce il contenuto dell'area di disegno rosso e indica quindi l'area di disegno per disegnare un rettangolo verde 25% della larghezza dell'area di disegno. Un esempio di questo può essere visti da `AnimationsDemo` progetto incluso con il codice sorgente per questo articolo. Avvio dell'applicazione e selezionando l'elemento di disegno nel menu principale, è necessario una schermata simile alla seguente:

![Schermata con colore rosso e gli oggetti di colore verde](graphics-and-animation-images/image3.png)

<a name="Animation" />

## <a name="animation"></a>Animazione

Gli utenti come elementi che si spostano nelle proprie applicazioni. Le animazioni sono un ottimo modo per migliorare l'esperienza utente di un'applicazione e metterlo in risalto. Le animazioni migliore sono quelli che gli utenti non notare in quanto si sentono naturale. Android fornisce i seguenti tre dell'API per animazioni:

-   **Animazione** &ndash; è l'API originale. Le animazioni sono collegate a una specifica visualizzazione e possono eseguire trasformazioni semplice al contenuto della visualizzazione. A causa di motivi di semplicità, questa API è comunque utile per operazioni quali animazioni alfa, rotazioni e così via.

-   **Proprietà animazione** &ndash; animazioni di proprietà sono stati introdotti in Android 3.0. Essi consentono a un'applicazione aggiungere un'animazione a qualsiasi operazione. Le animazioni proprietà consente di modificare qualsiasi proprietà di qualsiasi oggetto, anche se tale oggetto non è visibile sullo schermo.

-   **Animazione drawable** &ndash; questo effetto una risorsa Drawable speciale che viene utilizzata per applicare un'animazione molto semplice al layout.

In generale, animazione di proprietà è il sistema preferito da utilizzare come è più flessibile e offre ulteriori funzionalità.

<a name="View Animations" />

### <a name="view-animations"></a>Animazioni di visualizzazione

Animazioni di visualizzazione sono limitate a viste e le animazioni possono eseguire solo su valori, ad esempio l'avvio e punti di fine, dimensioni, rotazione e la trasparenza.
Questi tipi di animazioni vengono in genere definiti come *interpolazioni*. Le animazioni di visualizzazione possono essere definite in due modi &ndash; a livello di programmazione nel codice o tramite i file XML. File XML sono il modo migliore per dichiarare le animazioni di visualizzazione, come se fossero più leggibile e facile da gestire.

Verranno archiviati i file XML di animazione nel `/Resources/anim` directory di un progetto xamarin. Questo file deve avere uno dei seguenti elementi come l'elemento radice:

-   `alpha` &ndash; Un'animazione di dissolvenza o dissolvenza.

-   `rotate` &ndash; Un'animazione di rotazione.

-   `scale` &ndash; Un'animazione di ridimensionamento.

-   `translate` &ndash; Un movimento orizzontale e/o verticale.

-   `set` &ndash; Un contenitore che può contenere uno o più degli altri elementi di animazione.

Per impostazione predefinita, verranno applicate contemporaneamente tutte le animazioni in un file XML. Per rendere le animazioni si verificano in modo sequenziale, impostare il `android:startOffset` attributo in uno degli elementi definiti in precedenza.

È possibile modificare la frequenza di modifica in un'animazione utilizzando un *interpolatore*. Un interpolatore rende possibile per gli effetti di animazione con accelerazione, ripetuto o subisce. Il framework Android fornisce diversi interpolators predefinita, ad esempio (ma non è limitato a):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Questi interpolators aumentare o diminuire il tasso di variazione in un'animazione.

-   `BounceInterpolator` &ndash; la modifica aperture alla fine.

-   `LinearInterpolator` &ndash; la frequenza delle modifiche è costante.


Il codice XML seguente viene illustrato un esempio di un file di animazione che vengono combinati alcuni di questi elementi:

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

Questa animazione eseguirà tutte le animazioni contemporaneamente. La prima animazione scala verrà allungato orizzontalmente l'immagine e ridurla in verticale e quindi l'immagine verrà contemporaneamente ruotato di 45 gradi in senso antiorario e compattare, che scompare dalla schermata.

L'animazione può essere a livello di codice applicato a una visualizzazione eccessi nella misurazione l'animazione e quindi applicarla a una vista. Android fornisce la classe helper `Android.Views.Animations.AnimationUtils` che sarà l'ingrandimento di una risorsa di animazione e restituire un'istanza di `Android.Views.Animations.Animation`. Questo oggetto viene applicato a una visualizzazione chiamando `StartAnimation` e passando il `Animation` oggetto. Frammento di codice seguente viene illustrato un esempio:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Ora che è disponibile una comprensione di base del funzionamento delle animazioni di visualizzazione, consente di spostare le animazioni di proprietà.

<a name="Property Animations" />

### <a name="property-animations"></a>Proprietà animazioni

Esperti di animazione di proprietà sono una nuova API che è stata introdotta in Android 3.0.
Forniscono un'API ed estensibile che può essere usata per aggiungere un'animazione a qualsiasi proprietà in qualsiasi oggetto.

Create da istanze di tutte le animazioni di proprietà di [animatore](https://developer.xamarin.com/api/type/Android.Animation.Animator/) sottoclasse. Le applicazioni non utilizzano direttamente questa classe, ma utilizza una delle sottoclassi di:

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; questa classe è la classe più importante dell'API di animazione intera proprietà. Calcola i valori delle proprietà che devono essere modificati. Il `ViewAnimator` non direttamente aggiornare tali valori; al contrario, genera eventi che possono essere usati per aggiornare gli oggetti animati.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; questa classe è una sottoclasse di `ValueAnimator` . Lo scopo è quello per semplificare il processo di animazione degli oggetti mediante l'accettazione di un oggetto di destinazione e la proprietà da aggiornare.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; questa classe è responsabile per l'orchestrazione come animazioni eseguite in relazione uno a altro. Le animazioni possono eseguire contemporaneamente, in modo sequenziale o con un periodo di tempo tra di essi.


*Gli analizzatori* sono classi speciali che vengono utilizzate da esperti di animazione per calcolare i nuovi valori durante un'animazione. Predefinita, Android offre gli analizzatori seguenti:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; calcola i valori per le proprietà di tipo integer.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; calcolare i valori di proprietà float.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; calcola i valori per le proprietà di colori.

Se la proprietà che viene animata non è un `float`, `int` o colori, applicazioni possono creare propri dell'analizzatore di espressioni mediante l'implementazione di `ITypeEvaluator` interfaccia. (Implementazione analizzatori personalizzati non rientra nell'ambito di questo argomento).

#### <a name="using-the-valueanimator"></a>Utilizzo di ValueAnimator

Esistono due parti relative a un'animazione: il calcolo dei valori animati e quindi impostando i valori alle proprietà in un oggetto. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) calcolerà solo i valori, ma non funziona per gli oggetti direttamente. Al contrario, verranno aggiornati gli oggetti all'interno di gestori di eventi che verranno richiamati durante il ciclo di vita di animazione. Questa progettazione consente diverse proprietà per l'aggiornamento da un valore di animazione.

Per ottenere un'istanza di `ValueAnimator` chiamando uno dei metodi factory seguenti:

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

Al termine, il `ValueAnimator` istanza deve essere impostata la sua durata, e quindi può essere avviato. Nell'esempio seguente viene illustrato come aggiungere un'animazione a un valore compreso tra 0 e 1 nell'arco di 1000 millisecondi:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Tuttavia, il frammento di codice precedente non è molto utile &ndash; eseguirà l'animatore ma nessuna destinazione per il valore aggiornato. La `Animator` classe genererà l'evento di aggiornamento quando decide che è necessario informare i listener di un nuovo valore. Le applicazioni possono fornire un gestore eventi per rispondere a questo evento, come illustrato nel frammento di codice seguente:

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

Dopo aver creato la comprensione di `ValueAnimator`, consente di altre informazioni, vedere il `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Utilizzo di ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) è una sottoclasse di `ViewAnimator` che combina il calcolo del motore e il valore di intervallo del `ValueAnimator` con la logica necessaria per associare i gestori eventi. Il `ValueAnimator` richiede alle applicazioni di associare in modo esplicito un gestore eventi &ndash; `ObjectAnimator` si occuperà di questo passaggio per noi.

L'API per `ObjectAnimator` è molto simile all'API per `ViewAnimator`, ma è necessario fornire l'oggetto e il nome della proprietà da aggiornare. Nell'esempio seguente viene illustrato un esempio di utilizzo `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Come si può notare dal frammento di codice precedente, `ObjectAnimator` può ridurre e semplificare il codice che è necessario aggiungere un'animazione a un oggetto.

<a name="Drawable Animations" />

### <a name="drawable-animations"></a>Animazioni drawable

L'API di animazione finale è l'API di animazione Drawable. Le animazioni drawable caricare una serie di risorse Drawable uno dopo l'altro e li visualizza in sequenza, simile a un fumetto capovolto it.

Risorse drawable vengono definite in un file XML contenente un `<animation-list>` elemento come elemento radice e una serie di `<item>` gli elementi che definiscono ogni frame di animazione. Questo file XML viene archiviato nel `/Resource/drawable` cartella dell'applicazione. Il codice XML seguente è riportato un esempio di un'animazione drawable:

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

Questa animazione verrà eseguita sei fotogrammi. Il `android:duration` attributo dichiara la durata di visualizzazione di ogni frame. Frammento di codice seguente viene illustrato un esempio di creazione di un'animazione Drawable e l'avvio quando l'utente sceglie un pulsante sulla schermata di:

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

A questo punto sono stati illustrati funzionalità alla base dell'animazione API disponibili in un'applicazione Android.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo introdotti molti nuovi concetti e dell'API consentono di aggiungere alcuni elementi grafici per un'applicazione Android. Innanzitutto descritti i vari elementi grafici 2D dell'API e dimostrato come Android consente alle applicazioni di tracciare direttamente alla schermata utilizzando un oggetto di area di disegno. Abbiamo visto anche alcune tecniche alternative che consentono di grafica deve essere creato in modo dichiarativo utilizzando file XML. Quindi si è verificato un a illustrare i nuovi e precedenti dell'API per la creazione di animazioni in Android.



## <a name="related-links"></a>Collegamenti correlati

- [Demo di animazione (esempio)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Grafica e animazione](http://developer.android.com/guide/topics/graphics/index.html)
- [Utilizzo di animazioni per portare l'App per dispositivi mobili a vita](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Oggetto animatore](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Valore animatore](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
