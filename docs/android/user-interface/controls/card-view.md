---
title: CardView
description: "Il widget Cardview è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. Questa guida viene illustrato come utilizzare e personalizzare CardView nelle applicazioni di xamarin, mantenendo la compatibilità con le versioni precedenti di Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: b8f643c8158c5a3a849a3d8ee3dd8d0e7e30addf
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="cardview"></a>CardView

_Il widget Cardview è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. Questa guida viene illustrato come utilizzare e personalizzare CardView nelle applicazioni di xamarin, mantenendo la compatibilità con le versioni precedenti di Android._

<a name="overview" />

## <a name="overview"></a>Panoramica

Il `Cardview` widget, introdotto in Android 5.0 (simbolo), è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. `CardView` viene implementato come un `FrameLayout` widget con angoli arrotondati e un'ombreggiatura. In genere, un `CardView` viene usato per presentare un elemento di singola riga in un `ListView` o `GridView` gruppo di visualizzazione. Ad esempio, la schermata seguente è riportato un esempio di un'applicazione di prenotazione di viaggio che implementa `CardView`-basato su schede di destinazione di viaggi in un scorrevole `ListView`:

![App di esempio con un CardView per ogni destinazione di viaggio](card-view-images/01-cardview-example.png)

Questa guida viene illustrato come aggiungere il `CardView` pacchetto per il xamarin del progetto, come aggiungere `CardView` il layout e come personalizzare l'aspetto di `CardView` nell'app. Inoltre, questa guida fornisce un elenco dettagliato dei `CardView` gli attributi che è possibile modificare, inclusi gli attributi che consentono di utilizzare `CardView` nelle versioni precedenti di Android 5.0 simbolo di Android.

<a name="requirements" />

## <a name="requirements"></a>Requisiti

Le operazioni seguenti sono necessaria per utilizzare di nuovo Android 5.0 e versioni successive funzionalità (inclusi `CardView`) nelle App basate su Xamarin:

-  **Xamarin** &ndash; xamarin 4.20 o versioni successive deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-  **Android SDK** &ndash; Android 5.0 (API 21) o versioni successive deve essere installato mediante Android SDK Manager.

-  **Java JDK 1.8** &ndash; 1.7 JDK può essere utilizzata se sono specificamente con API livello 23 e versioni precedente. 1.8 JDK disponibile da [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

L'app deve includere anche il `Xamarin.Android.Support.v7.CardView` pacchetto. Per aggiungere il `Xamarin.Android.Support.v7.CardView` pacchetto in Visual Studio per Mac:

1. Aprire il progetto, fare doppio clic su **pacchetti** e selezionare **Aggiungi pacchetti**.

2. Nel **Aggiungi pacchetti** finestra di dialogo, cercare **CardView**.

3. Selezionare **libreria di supporto Xamarin v7 CardView**, quindi fare clic su **Aggiungi pacchetto**.

Per aggiungere il `Xamarin.Android.Support.v7.CardView` pacchetto in Visual Studio:

1. Aprire il progetto, fare doppio clic su di **riferimenti** nodo (nel **Esplora** riquadro) e selezionare **Gestisci pacchetti NuGet...** .

2. Quando il **Gestisci pacchetti NuGet** finestra di dialogo verrà visualizzata la finestra **CardView** nella casella di ricerca.

3. Quando **libreria di supporto Xamarin v7 CardView** viene visualizzata, fare clic su **installare**.

Per informazioni su come configurare un progetto di app Android 5.0, vedere [impostazione di un progetto Android 5.0](~/android/platform/lollipop.md).
Per ulteriori informazioni sull'installazione dei pacchetti NuGet, vedere [procedura dettagliata: inclusi un NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

<a name="basic" />

## <a name="introducing-cardview"></a>Introduzione a CardView

Il valore predefinito `CardView` è simile a una scheda bianca con angoli arrotondati minima e un'ombreggiatura in piccole. Nell'esempio seguente viene **axml** visualizzazione layout di un singolo `CardView` widget che contiene un `TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Se si usa questo codice XML per sostituire il contenuto esistente di **axml**, assicurarsi di impostare come commento il codice **Mainactivity** che fa riferimento alle risorse nel codice XML precedente.

In questo esempio di layout crea predefinito `CardView` con una singola riga di testo, come mostrato nella schermata seguente:

[![Schermata di CardView con sfondo bianco e riga di testo](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png)

In questo esempio, lo stile di app è impostato su light tema materiale (`Theme.Material.Light`) in modo che il `CardView` bordi e ombreggiature sono più facili da visualizzare. Per ulteriori informazioni sulle app di temi Android 5.0, vedere [tema materiale](~/android/user-interface/material-theme.md). Nella sezione successiva, si apprenderà come personalizzare `CardView` per un'applicazione.

<a name="customizing" />

## <a name="customizing-cardview"></a>Personalizzazione CardView

È possibile modificare gli elementi di base `CardView` attributi per personalizzare l'aspetto del `CardView` nell'app. Ad esempio l'elevazione del `CardView` può essere aumentato di un'ombra più grande (rendendo la smart card sembra maggiore rispetto allo sfondo float). Inoltre, il raggio dell'angolo può essere aumentato per rendere gli angoli della scheda ulteriori arrotondati.

Nell'esempio layout successivo, un oggetto personalizzato `CardView` viene utilizzato per creare una simulazione di una fotografia di stampa ("snapshot"). Un `ImageView` viene aggiunto per il `CardView` per la visualizzazione dell'immagine e un `TextView` si trova sotto il `ImageView` per visualizzare il titolo dell'immagine.
In questo layout di esempio, il `CardView` presenta le seguenti personalizzazioni:

-  Il `cardElevation` viene aumentato a 4dp per eseguire il cast di un'ombreggiatura più grande.
-  Il `cardCornerRadius` viene aumentato a 5dp per rendere gli angoli arrotondati più.

Poiché `CardView` viene fornito dalla libreria di supporto Android v7, i relativi attributi non sono disponibili le `android:` dello spazio dei nomi. Pertanto, è necessario definire il proprio spazio dei nomi XML e utilizzare tale spazio dei nomi come il `CardView` prefisso dell'attributo. Nell'esempio di layout seguente, si utilizzerà questa riga per definire uno spazio dei nomi denominato `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

È possibile chiamare questo spazio dei nomi `card_view` o addirittura `myapp` se si sceglie (è accessibile solo all'interno dell'ambito di questo file). Qualsiasi scegliere di chiamare questo spazio dei nomi, è necessario utilizzarlo come prefisso per il `CardView` attributo che si desidera modificare. In questo esempio di layout, il `cardview` dello spazio dei nomi è il prefisso per `cardElevation` e `cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Quando viene utilizzato in questo esempio di layout per visualizzare un'immagine in un'app di visualizzazione di foto di `CardView` ha l'aspetto di uno snapshot di foto, come illustrato nella schermata seguente:

[![CardView con un'immagine e la didascalia sotto l'immagine](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png)

Questa schermata viene recuperata dal [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) app di esempio, che utilizza un `RecyclerView` widget per presentare un elenco a discesa di `CardView` immagini per la visualizzazione di foto. Per ulteriori informazioni su `RecyclerView`, vedere il [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) Guida.

Si noti che un `CardView` può visualizzare più di una visualizzazione figlio nella relativa area di contenuto. Ad esempio, nella visualizzazione di esempio di app foto precedente, l'area del contenuto è costituito da un `ListView` che contiene un `ImageView` e `TextView`. Sebbene `CardView` istanze spesso sono disposti verticalmente, è anche possibile disporli orizzontalmente (vedere [la creazione di uno stile di visualizzazione personalizzato](~/android/user-interface/material-theme.md#customview) per una schermata di esempio).

<a name="layout" />

### <a name="cardview-layout-options"></a>Opzioni di Layout CardView

`CardView` layout può essere personalizzato tramite l'impostazione di uno o più attributi che influiscono sul relativo riempimento, l'elevazione dei privilegi, raggio dell'angolo e il colore di sfondo:

[![Diagramma di attributi CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png)

Ogni attributo può anche essere modificata dinamicamente chiamando una controparte `CardView` (metodo) (per ulteriori informazioni su `CardView` metodi, vedere il [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Si noti che questi attributi (ad eccezione di colore di sfondo) accettano un valore di dimensione, ovvero un numero decimale seguito dall'unità. Ad esempio, `11.5dp` specifica 11.5 independent densità di pixel.

<a name="padding" />

#### <a name="padding"></a>Spaziatura interna
`
CardView` sono disponibili cinque attributi di riempimento per posizionare il contenuto nella scheda. È possibile impostarle nel layout XML oppure è possibile chiamare metodi analoghi nel codice:

[![Diagramma di CardView spaziatura attributi](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png)

Gli attributi di riempimento sono descritte come segue:

-  `contentPadding` &ndash; Interna spaziatura tra le visualizzazioni figlio del `CardView` e tutti i bordi della scheda.

-  `contentPaddingBottom` &ndash; Interna spaziatura tra le visualizzazioni figlio del `CardView` e il bordo inferiore della scheda.

-  `contentPaddingLeft` &ndash; Interna spaziatura tra le visualizzazioni figlio del `CardView` e il bordo sinistro della scheda.

-  `contentPaddingRight` &ndash; Interna spaziatura tra le visualizzazioni figlio del `CardView` e il bordo destro della scheda.

-  `contentPaddingTop` &ndash; Interna spaziatura tra le visualizzazioni figlio del `CardView` e il bordo superiore della scheda.

Gli attributi di spaziatura interna del contenuto sono rispetto al limite dell'area di contenuto anziché a qualsiasi widget specificato che si trova all'interno dell'area del contenuto.
Ad esempio, se `contentPadding` sufficientemente un incremento nell'applicazione, la visualizzazione di foto di `CardView` sarebbe ritagliare l'immagine sia il testo visualizzato nella scheda.


<a name="elevation" />

#### <a name="elevation"></a>Elevazione dei privilegi

`CardView` offre due attributi di elevazione dei privilegi per controllare il relativo l'elevazione dei privilegi e, di conseguenza, le dimensioni l'ombreggiatura:

[![Diagramma degli attributi di elevazione CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png)

Gli attributi di elevazione eseguono le operazioni seguenti:

-  `cardElevation` &ndash; L'elevazione del `CardView` (rappresenta l'asse Z).

-  `cardMaxElevation` &ndash; Il valore massimo del `CardView`dell'elevazione dei privilegi.

Valori più grandi di `cardElevation` aumentare le dimensioni dell'ombreggiatura per rendere `CardView` sembra float maggiore rispetto allo sfondo. Il `cardElevation` attributo determina anche l'ordine di disegno di viste sovrapposte, vale a dire, il `CardView` verranno disegnati sotto un'altra visualizzazione sovrapposta, con un'impostazione maggiore di elevazione dei privilegi e versioni successive le viste sovrapposte con un'impostazione inferiore di elevazione dei privilegi.
Il `cardMaxElevation` impostazione è utile per quando l'app viene modificato l'elevazione dei privilegi dinamicamente &ndash; impedisce l'ombreggiatura di si estende oltre il limite definito con questa impostazione.

<a name="radius" />

#### <a name="corner-radius-and-background-color"></a>Raggio dell'angolo e il colore di sfondo

`CardView` offre gli attributi che è possibile utilizzare per controllare il raggio dell'angolo e il colore di sfondo. Queste due proprietà consentono di modificare lo stile generale del `CardView`:

[![Diagramma dell'angolo CardView radious e gli attributi di colore di sfondo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png)

Questi attributi sono le seguenti:

-  `cardCornerRadius` &ndash; Tutti gli angoli del raggio dell'angolo di `CardView`.

-  `cardBackgroundColor` &ndash; Il colore di sfondo di `CardView`.

In questo diagramma `cardCornerRadius` è impostata su un 10dp più arrotondato e `cardBackgroundColor` è impostato su `"#FFFFCC"` (giallo chiaro).


<a name="compatibility" />

## <a name="compatibility"></a>Compatibilità

È possibile utilizzare `CardView` nelle versioni precedenti di Android 5.0 simbolo di Android. Poiché `CardView` fa parte della libreria di supporto Android v7, è possibile utilizzare `CardView` con Android 2.1 (API livello 7) e versioni successive.
Tuttavia, è necessario installare il `Xamarin.Android.Support.v7.CardView` pacchetto come descritto in [requisiti](#requirements)sopra.

`CardView` presenta un comportamento leggermente diverso nei dispositivi prima di simbolo (livello API 21):

-  `CardView` Usa un'implementazione di ombreggiatura a livello di codice che aggiunge riempimento aggiuntivi.

-  `CardView` Ritaglia le visualizzazioni figlio che interseca il `CardView`degli angoli arrotondati.

Consente di semplificare la gestione di tali differenze, compatibilità `CardView` fornisce diversi attributi aggiuntivi che è possibile configurare nel layout:

-   `cardPreventCornerOverlap` &ndash; Impostare questo attributo su `true` per aggiungere spaziatura interna durante l'esecuzione dell'app in Android le versioni precedenti (livello API 20 e versioni precedente). Questa impostazione impedisce `CardView` contenuto da intersecare con il `CardView`degli angoli arrotondati.

-   `cardUseCompatPadding` &ndash; Impostare questo attributo su `true` per aggiungere spaziatura interna, quando l'app è in esecuzione nelle versioni di Android a o maggiore di livello API 21. Se si desidera utilizzare `CardView` sui dispositivi il pre- simbolo di ed è la stessa in simbolo (o versioni successive), impostare questo attributo su `true`. Quando questo attributo è abilitato, `CardView` viene aggiunto un riempimento aggiuntivo per disegnare shadows quando viene eseguita nel pre-simbolo di dispositivi. Ciò consente di risolvere le differenze nel riempimento verranno introdotti quando le implementazioni di ombreggiatura a livello di codice il pre-simbolo di sono attive.

Per ulteriori informazioni su come mantenere la compatibilità con le versioni precedenti di Android, vedere [mantenendo la compatibilità](https://developer.android.com/training/material/compatibility.html).

<a name="summary" />

## <a name="summary"></a>Riepilogo

Questa guida è stato introdotto il nuovo `CardView` widget inclusi in Android 5.0 (simbolo). Illustrato il valore predefinito `CardView` aspetto e illustrato come personalizzare `CardView` modificando il relativo l'elevazione dei privilegi, angoli, spaziatura interna del contenuto e il colore di sfondo. Verrà elencato il `CardView` gli attributi di layout (con diagrammi di riferimento) e viene illustrato come utilizzare `CardView` nei dispositivi Android precedenti alla Android 5.0 simbolo. Per ulteriori informazioni su `CardView`, vedere il [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introduzione al simbolo](~/android/platform/lollipop.md)
- [Riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
