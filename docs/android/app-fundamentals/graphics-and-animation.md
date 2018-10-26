---
title: Grafica e animazione
description: Android fornisce un framework molto avanzato e diverse per supportare le animazioni ed elementi grafici 2D. In questo argomento vengono introdotti questi Framework e viene illustrato come creare le animazioni per l'uso e la grafica personalizzata in un'applicazione xamarin. Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: c49b8855bccaf2eca825096746769d7f201736c5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116888"
---
# <a name="graphics-and-animation"></a>Grafica e animazione

_Android fornisce un framework molto avanzato e diverse per supportare le animazioni ed elementi grafici 2D. In questo argomento vengono introdotti questi Framework e viene illustrato come creare le animazioni per l'uso e la grafica personalizzata in un'applicazione xamarin. Android._


## <a name="overview"></a>Panoramica

Nonostante in esecuzione sui dispositivi che sono in genere di alimentazione limitate, l'applicazioni per dispositivi mobili con classificazione più alta hanno spesso una sofisticata utente esperienza, complete di grafica di alta qualità e animazioni che forniscono un'idea intuitiva, reattiva e dinamico. Ottengono di più applicazioni per dispositivi mobili e più sofisticati, gli utenti hanno iniziato a prevede che più dalle applicazioni.

Per fortuna, piattaforme per dispositivi mobili moderne hanno molto potente Framework per la creazione di animazioni sofisticate e grafica personalizzata mantenendo la semplicità d'uso. Ciò consente agli sviluppatori di aggiungere interattività completa con il minimo sforzo.

Il Framework API dell'interfaccia utente in Android approssimativamente può essere suddivisa in due categorie: grafica e animazione.

Grafica viene suddivisi ulteriormente in diversi approcci per l'esecuzione di grafica 2D e 3D. Grafica 3D è disponibile tramite una vasta gamma compilato in Framework come OpenGL ES (una specifica versione mobile di OpenGL) e Framework di terze parti, ad esempio MonoGame (un toolkit multipiattaforma compatibile con il toolkit XNA). Sebbene la grafica 3D non rientrano nell'ambito di questo articolo, verranno esaminate le tecniche di disegnare 2D incorporate.

Android offre due diverse API per la creazione di grafica 2D. Uno è un approccio dichiarativo di alto livello e l'altra è un'API di basso livello a livello di codice:

-   **Risorse drawable** &ndash; questi vengono usati per creare grafici personalizzati a livello di codice o (in genere), incorporare istruzioni di disegno in file XML. Risorse drawable vengono in genere definite come file XML che contengono istruzioni o azioni per Android per il rendering di un grafico 2D. 

-   **Area di disegno** &ndash; si tratta di un'API di basso livello che è necessario disegnare direttamente in una bitmap sottostante. Fornisce un controllo molto accurato gli elementi visualizzati. 

Oltre a queste tecniche di grafica 2D, Android offre anche diversi modi per creare animazioni:

-   **Le animazioni drawable** &ndash; Android supporta anche le animazioni di ciascun frame singolarmente dette *animazione Drawable*. Si tratta dell'API di animazione più semplice. Android carica in modo sequenziale e visualizza le risorse Drawable in sequenza (analogamente un fumetto).

-   **Visualizzare le animazioni** &ndash; *animazioni vista* sono l'animazione originale dell'API in Android e sono disponibili in tutte le versioni di Android. Questa API è limitata in quanto funziona solo con gli oggetti di visualizzazione e possono solo eseguire trasformazioni semplici tali visualizzazioni.
    Le animazioni di visualizzazione vengono in genere definite nel file XML trovati nel `/Resources/anim` cartella.

-   **Proprietà animazioni** &ndash; 3.0 Android ha introdotto un nuovo set di animazione dell'API noto come *animazioni delle proprietà*. Queste nuove API ha introdotto un sistema estendibile e flessibile che consente di animare le proprietà di qualsiasi oggetto, non solo di visualizzare gli oggetti. Questa flessibilità consente le animazioni da copiare nelle classi distinte che renderanno più semplice di condivisione del codice.


Le animazioni di visualizzazione sono più adatte alle applicazioni che devono supportare le precedenti versioni precedenti ad Android 3.0 dell'API (API livello 11). In caso contrario, le applicazioni devono utilizzare dell'API di animazione proprietà più recenti per i motivi descritti sopra.

Tutti questi Framework sono vitali opzioni, tuttavia dove possibile, dovrebbe dare la preferenza per le animazioni di proprietà, poiché si tratta di un'API più flessibile per lavorare con. Le animazioni di proprietà consentono di logica dell'animazione essere incapsulate nelle classi distinte che rende più semplice di condivisione del codice e semplifica la manutenzione del codice.


## <a name="accessibility"></a>Accessibilità

Grafica e le animazioni sono utili per rendere le app Android e piacevoli da usare; Tuttavia, è importante ricordare che si verificano alcune interazioni tramite schermo, dispositivi di input alternativi, o con zoom assistito.
Inoltre, alcune interazioni possono verificarsi senza funzionalità audio.

Le app sono più facilmente utilizzabili in queste situazioni, se sono stati progettati con accessibilità presente: fornire suggerimenti e assistenza di navigazione nell'interfaccia utente e verificare sia presente contenuto di testo o le descrizioni degli elementi grafici dell'interfaccia utente.

Fare riferimento a [Guida di accessibilità di Google](http://developer.android.com/guide/topics/ui/accessibility/) per altre informazioni su come usare l'accesso facilitato di Android le API.



## <a name="2d-graphics"></a>Grafica 2D

Le risorse drawable sono una tecnica comune in applicazioni Android. Come con altre risorse, le risorse Drawable sono dichiarative &ndash; sono definiti nel file XML. Questo approccio consente una netta separazione del codice dalle risorse. Ciò consente di semplificare lo sviluppo e manutenzione perché non è necessario modificare il codice per aggiornare o modificare le immagini in un'applicazione Android. Tuttavia, mentre risorse Drawable sono utili per molti requisiti grafici semplici e comuni, dispongono di potenza e il controllo dell'area di disegno API.

La tecnica di altra tipo, tramite il [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) oggetto, è molto simile agli altri Framework API tradizionali, ad esempio System. Drawing o disegno di base di iOS. Utilizzo dell'oggetto Canvas offre il massimo controllo di grafica 2D come vengono creati. È adatto per situazioni in cui una risorsa Drawable non funzioneranno o saranno difficili da gestire. Ad esempio, potrebbe essere necessario disegnare un controllo dispositivo di scorrimento personalizzate il cui aspetto verrà modificato in base a calcoli relativi al valore del dispositivo di scorrimento.

Esaminiamo prima le risorse Drawable. Sono più semplici e coprono il maggior parte dei casi di disegni personalizzati.


### <a name="drawable-resources"></a>Risorse drawable

Le risorse drawable vengono definite in un file XML nella directory `/Resources/drawable`. A differenza di incorporamento del JPEG o PNG, non è necessario fornire versioni specifiche densità delle risorse Drawable.
In fase di esecuzione, un'applicazione Android caricherà queste risorse e usare le istruzioni contenute in questi file XML per creare grafica 2D.
Android definisce diversi tipi di risorse Drawable:

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; questo è un oggetto Drawable che consente di disegnare una forma geometrica primitiva e applica un set limitato di effetti grafici su tale forma. Sono molto utili per attività quali la personalizzazione pulsanti o impostazione dello sfondo di oggetti TextViews. Vedremo un esempio di come usare un `ShapeDrawable` più avanti in questo articolo.

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; si tratta di una risorsa Drawable che cambia aspetto basata sullo stato di un widget o controllo. Ad esempio, un pulsante può modificarne l'aspetto a seconda del fatto che si è premuto o meno.

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; This risorse Drawable che vengono impilate su diversi altri drawable uno su altro. Un esempio di un *LayerDrawable* viene illustrato nello screenshot seguente:

    ![Esempio LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; si tratta di un *LayerDrawable* ma con una differenza. Oggetto *TransitionDrawable* è in grado di aggiungere un'animazione a un livello visualizzato sopra un altro.

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; ciò è molto simile a un *StateListDrawable* in quanto mostrerà un'immagine basata su determinate condizioni. Tuttavia, a differenza di una *StateListDrawable*, il *LevelListDrawable* Visualizza un'immagine basata su un valore intero. Un esempio di un *LevelListDrawable* , è possibile visualizzare l'efficacia di un segnale Wi-Fi. Come il livello di codifica delle modifiche segnali Wi-Fi, drawable visualizzato verrà modificato.

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; come suggerisce il nome, questi Drawable fornire la scalabilità e la funzionalità di ritaglio. Il *ScaleDrawable* ridimensionerà while Drawable, un altro il *ClipDrawable* ritagliano Drawable un'altra.

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; This Drawable applicherà Inset sui lati di un'altra risorsa Drawable. Viene usato quando una vista deve uno sfondo a dimensioni inferiori a quelle dei limiti effettivi della visualizzazione.

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; questo file è un set di istruzioni, in formato XML, che devono essere eseguite su una bitmap effettivi. Alcune azioni che è possibile eseguire Android sono 67k dithering e anti-aliasing. Uno degli usi molto comuni di questo oggetto è affiancare un'immagine bitmap sullo sfondo di un layout.


#### <a name="drawable-example"></a>Esempio drawable

Verrà ora esaminato un rapido esempio di come creare un grafico 2D usando un `ShapeDrawable`. Oggetto `ShapeDrawable` può definire una delle quattro forme base: rettangolo, ellissi, linee e anello. È anche possibile applicare gli effetti di base, ad esempio sfumature, colori e dimensioni. Il codice XML seguente è un `ShapeDrawable` che possono trovarsi nel *AnimationsDemo* progetto complementare (nel file `Resources/drawable/shape_rounded_blue_rect.xml`).
Definisce un rettangolo con uno sfondo sfumato viola e angoli arrotondati:

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

Si può fare riferimento a questa risorsa Drawable in modo dichiarativo in un Layout o altri Drawable come illustrato nel codice XML seguente:

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

Risorse drawable possono anche essere applicate a livello di codice. Il frammento di codice seguente viene illustrato come impostare a livello di codice lo sfondo di un controllo TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Per visualizzare questo aspetto, eseguire la *AnimationsDemo* del progetto e selezionare l'elemento forma Drawable dal menu principale. Dovrebbe essere visualizzato qualcosa di simile allo screenshot seguente:

![TextView con uno sfondo personalizzato, drawable con un angoli arrotondati e sfumatura](graphics-and-animation-images/image1.png)

Per altre informazioni sugli elementi XML e sintassi di risorse Drawable, consultare [documentazione di Google](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).


### <a name="using-the-canvas-drawing-api"></a>Tramite l'API di disegno di Canvas

Drawable sono molto efficaci, ma presentano limitazioni. Alcuni aspetti non sono molto complessa o non è possibile (ad esempio: applica un filtro a un'immagine che è stata creata da una fotocamera del dispositivo). Sarebbe molto difficile applicare riduzione occhi rossi mediante una risorsa Drawable.
Al contrario, l'API di Canvas consente a un'applicazione a un controllo molto accurato per modificare in modo selettivo i colori in una parte specifica dell'immagine.

Una classe che viene comunemente usata con l'area di disegno è la [Paint](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) classe. Questa classe contiene colori e stile informazioni sulle modalità di disegno. Viene utilizzato per fornire aspetti un colore e una trasparenza.

L'API dell'area di disegno usa la *modello del pittore* per disegnare la grafica 2D.
Le operazioni vengono applicate in livelli successivi uno sopra l'altro. Ogni operazione verrà trattati alcuni area della bitmap sottostante. Quando l'area si sovrappone a un'area disegnata in precedenza, il nuovo disegno parzialmente o completamente nasconda la vecchia. Ciò è simile a quello che funzionano diverse altre API di disegni, ad esempio System. Drawing e grafica di base di iOS.

Esistono due modi per ottenere un `Canvas` oggetto. Il primo modo prevede la definizione di un [Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) oggetto e quindi creare un'istanza un `Canvas` oggetto con esso. Ad esempio, il frammento di codice seguente crea una nuova area di disegno con una bitmap sottostante:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

L'altro modo per ottenere un `Canvas` consiste nell'oggetto di [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) metodo di callback che viene fornito il [vista](https://developer.xamarin.com/api/type/Android.Views.View/) classe di base. Android chiama questo metodo quando decide di una vista deve disegnare se stesso e passa un `Canvas` oggetto per la visualizzazione per l'interazione con.

La classe di Canvas espone metodi per fornire a livello di codice le istruzioni di disegno. Ad esempio:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; riempie bitmap dell'area di disegno intera con pittura specificato.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; consente di disegnare la forma geometrica specificata utilizzando il colore specificato.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; disegna il testo nell'area di disegno con il colore specificato. Viene disegnato il testo nella posizione `x,y` .



#### <a name="drawing-with-the-canvas-api"></a>Disegno con area di disegno dell'API

Di seguito viene illustrato un esempio dell'API di Canvas in azione. Il frammento di codice seguente viene illustrato come creare una vista:

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

Il codice precedente crea innanzitutto un disegno rossa e un oggetto di colore verde. Inserisce il contenuto dell'area di disegno rosso e indica quindi l'area di disegno disegnare un rettangolo verde che 25% della larghezza dell'area di disegno. Un esempio di questo può essere visualizzato da `AnimationsDemo` progetto incluso con il codice sorgente per questo articolo. Avviando l'applicazione e selezionando l'elemento disegno dal menu principale, è consigliabile una schermata simile alla seguente:

![Schermata con oggetti paint verde e rosso paint](graphics-and-animation-images/image3.png)


## <a name="animation"></a>Animazione

Un approccio analogo va che gestiscono lo spostamento nelle proprie applicazioni agli utenti. Le animazioni sono un ottimo modo per migliorare l'esperienza utente di un'applicazione e metterlo in risalto. Le animazioni migliori sono quelli che gli utenti non notare perché pensano naturale. Android offre i seguenti tre dell'API per animazioni:

-   **Visualizzare l'animazione** &ndash; questa è l'API originale. Queste animazioni associate a una visualizzazione specifica e possono eseguire trasformazioni semplici sul contenuto della visualizzazione. A causa della semplicità, questa API ancora utile per determinate operazioni, ad esempio le animazioni alfa, rotazioni e così via.

-   **Proprietà animazione** &ndash; animazioni delle proprietà sono state introdotte in Android 3.0. Essi consentono a un'applicazione aggiungere un'animazione a qualsiasi elemento. Le animazioni di proprietà è utilizzabile per modificare qualsiasi proprietà di qualsiasi oggetto, anche se tale oggetto non è visibile sullo schermo.

-   **Animazione drawable** &ndash; questo effetto ai layout di una risorsa Drawable speciale che viene usata per applicare un'animazione molto semplice.

Animazione di proprietà è in genere, il sistema preferito da utilizzare poiché è più flessibile e offre più funzionalità.


### <a name="view-animations"></a>Animazioni di visualizzazione

Le animazioni di visualizzazione sono limitate alle visualizzazioni e possono solo eseguire animazioni sui valori, ad esempio avvio e il punto finale, dimensioni, rotazione e trasparenza.
Questi tipi di animazioni sono in genere detti *interpolazioni*. Le animazioni di visualizzazione possono essere definite due modi &ndash; a livello di programmazione nel codice o tramite i file XML. I file XML sono il modo migliore per dichiarare le animazioni di visualizzazione, così come sono più leggibile e facile da gestire.

Verranno archiviati i file XML di animazione nel `/Resources/anim` directory di un progetto xamarin. Android. Questo file deve avere uno dei seguenti elementi come elemento radice:

-   `alpha` &ndash; Un'animazione di dissolvenza o dissolvenza.

-   `rotate` &ndash; Un'animazione di rotazione.

-   `scale` &ndash; Un'animazione di ridimensionamento.

-   `translate` &ndash; Un movimento orizzontale e/o verticale.

-   `set` &ndash; Un contenitore che può contenere uno o più degli altri elementi animazione.

Per impostazione predefinita, tutte le animazioni in un file XML verranno applicate simultaneamente. Affinché le animazioni si verificano in modo sequenziale, impostare il `android:startOffset` attributo in uno degli elementi definiti in precedenza.

È possibile modificare la frequenza di modifica di un'animazione utilizzando un *interpolatore*. Un interpolatore rende possibile per gli effetti di animazione essere accelerato, ripetute o subisce. Il framework Android offre diversi interpolators per impostazione predefinita, ad esempio (ma non è limitata a):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Questi interpolators aumentare o ridurre la frequenza di modifica di un'animazione.

-   `BounceInterpolator` &ndash; la modifica bounces alla fine.

-   `LinearInterpolator` &ndash; la frequenza delle modifiche è costante.


Il codice XML seguente viene illustrato un esempio di un file di animazioni che vengono combinati alcuni di questi elementi:

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

Questa animazione eseguirà tutte le animazioni contemporaneamente. Prima animazione scala verrà allungano l'immagine in senso orizzontale e ridurla in verticale e quindi l'immagine verrà contemporaneamente ruotato di 45 gradi in senso antiorario e compatta, nascosta dallo schermo.

L'animazione è a livello di codice applicabile a una visualizzazione da ciò fa aumentare erroneamente l'animazione e quindi applicarlo a una visualizzazione. Android fornisce la classe helper `Android.Views.Animations.AnimationUtils` che verrà aumento una risorsa di animazione e restituire un'istanza di `Android.Views.Animations.Animation`. Questo oggetto viene applicato a una visualizzazione chiamando `StartAnimation` e passando il `Animation` oggetto. Il frammento di codice seguente viene illustrato un esempio:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Ora che abbiamo una conoscenza di come funzionano le animazioni di visualizzazione, consente di spostare le animazioni di proprietà.


### <a name="property-animations"></a>Animazioni di proprietà

Esperti di animazione di proprietà sono una nuova API che è stato introdotto in Android 3.0.
Forniscono un'API più estendibile che può essere utilizzata per aggiungere un'animazione a qualsiasi proprietà a tutti gli oggetti.

Tutte le animazioni di proprietà vengono create dalle istanze del [animatore](https://developer.xamarin.com/api/type/Android.Animation.Animator/) sottoclasse. Le applicazioni non utilizzano direttamente questa classe, utilizzano piuttosto una delle relative sottoclassi:

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; questa classe è la classe più importante nell'API di animazione di proprietà completo. Calcola i valori delle proprietà che devono essere modificati. Il `ViewAnimator` non aggiorna direttamente tali valori; al contrario, genera gli eventi che possono essere utilizzati per aggiornare gli oggetti animati.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; questa classe è una sottoclasse di `ValueAnimator` . Lo scopo è quello di semplificare il processo di animazione degli oggetti mediante l'accettazione di un oggetto di destinazione e proprietà da aggiornare.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; questa classe è responsabile per l'orchestrazione come eseguire animazioni in relazione uno a altro. Le animazioni possono essere eseguite contemporaneamente, in modo sequenziale o con un periodo di tempo tra di essi.


*Gli analizzatori* sono classi speciali che vengono utilizzate da esperti di animazione per calcolare i nuovi valori durante un'animazione. Per impostazione predefinita, Android offre gli analizzatori seguenti:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; calcola i valori per le proprietà integer.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; calcola i valori per le proprietà di float.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; calcola i valori per le proprietà del colore.

Se la proprietà animata non è un `float`, `int` o a colori, applicazioni possono creare i propri dell'analizzatore di espressioni mediante l'implementazione di `ITypeEvaluator` interfaccia. (Implementa gli analizzatori personalizzati è oltre l'ambito di questo argomento).

#### <a name="using-the-valueanimator"></a>Uso di ValueAnimator

Esistono due parti di qualsiasi animazione: il calcolo dei valori animati e quindi impostando questi valori alle proprietà in un oggetto. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) calcolerà solo i valori, ma non operano su oggetti direttamente. Al contrario, verranno aggiornati gli oggetti all'interno di gestori di eventi che verranno richiamati durante il ciclo di vita di animazione. Ne consegue che diverse proprietà per essere aggiornata da un valore animato.

Per ottenere un'istanza di `ValueAnimator` chiamando uno dei metodi factory seguenti:

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

Al termine, il `ValueAnimator` istanza deve avere impostato la sua durata, e quindi è possibile avviare il servizio. Nell'esempio seguente illustra come animare un valore compreso tra 0 e 1 nell'arco di 1000 millisecondi:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Tuttavia, il frammento di codice precedente non è molto utile &ndash; eseguirà l'animatore ma nessuna destinazione per il valore aggiornato. Il `Animator` classe genererà l'evento di aggiornamento quando si decide che è necessario informare i listener di un nuovo valore. Le applicazioni possono disporre di un gestore eventi per rispondere a questo evento, come illustrato nel frammento di codice seguente:

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

Ora che abbiamo comprendere `ValueAnimator`, consente altre informazioni sul `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Uso di ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) è una sottoclasse di `ViewAnimator` che combina il calcolo del motore e il valore di temporizzazione del `ValueAnimator` con la logica necessaria per collegare i gestori eventi. Il `ValueAnimator` richiede alle applicazioni di impostare in modo esplicito un gestore eventi &ndash; `ObjectAnimator` si occuperà di questo passaggio per noi.

L'API per `ObjectAnimator` è molto simile all'API per `ViewAnimator`, ma richiede di specificare l'oggetto e il nome della proprietà da aggiornare. L'esempio seguente illustra un esempio d'uso `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Come si può notare dal frammento di codice precedente, `ObjectAnimator` può ridurre e semplificare il codice che è necessario aggiungere un'animazione a un oggetto.


### <a name="drawable-animations"></a>Animazioni drawable

L'API di animazione finale è l'API di animazione Drawable. Le animazioni drawable caricamento una serie di risorse Drawable uno dopo l'altro e li visualizza in sequenza, simile a un fumetto di capovolgimento-it.

Risorse drawable sono definite in un file XML che ha un `<animation-list>` elemento come elemento radice e una serie di `<item>` gli elementi che definiscono ogni frame di animazione. Questo file XML viene archiviato nel `/Resource/drawable` cartella dell'applicazione. Il codice XML seguente è riportato un esempio di un'animazione drawable:

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

Questa animazione verrà eseguita tramite sei fotogrammi. Il `android:duration` attributo dichiara quanto tempo verrà visualizzato ogni fotogramma. Il frammento di codice successivo mostra un esempio di creazione di un'animazione Drawable e avviandolo quando l'utente fa clic su un pulsante sullo schermo:

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

A questo punto sono stati trattati i concetti fondamentali dell'animazione API disponibili in un'applicazione Android.


## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto numerosi nuovi concetti e di API per permettere di aggiungere alcuni elementi grafici per un'applicazione Android. Prima di tutto illustrato la grafica 2D diversi dell'API e descritto come Android consente alle applicazioni di tracciare direttamente alla schermata usando un oggetto area di disegno. Abbiamo visto anche alcune tecniche alternative che consentono di grafica deve essere creato in modo dichiarativo utilizzando file XML. Quindi siamo andati a discutere nuovi e precedenti dell'API per la creazione di animazioni in Android.



## <a name="related-links"></a>Collegamenti correlati

- [Demo di animazione (esempio)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Grafica e animazione](http://developer.android.com/guide/topics/graphics/index.html)
- [Uso di animazioni per dare vita App per dispositivi mobili](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Oggetto animatore](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Valore animatore](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
