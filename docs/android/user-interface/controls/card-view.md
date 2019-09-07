---
title: CardView
description: Il widget CardView è un componente dell'interfaccia utente che presenta il contenuto del testo e dell'immagine nelle visualizzazioni simili a quelle delle schede. Questa guida illustra come usare e personalizzare CardView in applicazioni Novell. Android mantenendo la compatibilità con le versioni precedenti di Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 2051c7c904dedf8b41f405d3ec7b9c1a003b7fd5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758786"
---
# <a name="xamarinandroid-cardview"></a>Novell. Android CardView

_Il widget CardView è un componente dell'interfaccia utente che presenta il contenuto del testo e dell'immagine nelle visualizzazioni simili a quelle delle schede. Questa guida illustra come usare e personalizzare CardView in applicazioni Novell. Android mantenendo la compatibilità con le versioni precedenti di Android._

## <a name="overview"></a>Panoramica

Il `Cardview` widget, introdotto in Android 5,0 (Lollipop), è un componente dell'interfaccia utente che presenta il contenuto di testo e immagini in visualizzazioni simili a schede. `CardView`viene implementato come un `FrameLayout` widget con angoli arrotondati e un'ombreggiatura. In genere, `CardView` viene usato un oggetto per presentare un singolo elemento di `ListView` riga `GridView` in un gruppo di visualizzazione o. Ad esempio, lo screenshot seguente è un esempio di app di prenotazione di viaggio che implementa `CardView`le schede di destinazione di viaggio basate su in `ListView`uno scorrevole:

![App di esempio che usa un CardView per ogni destinazione di viaggio](card-view-images/01-cardview-example.png)

Questa guida illustra come aggiungere il `CardView` pacchetto al progetto Novell. Android, come aggiungere `CardView` al layout e come personalizzare l'aspetto di `CardView` nell'app. Questa guida fornisce inoltre un elenco dettagliato degli `CardView` attributi che è possibile modificare, inclusi gli attributi che consentono di usare `CardView` le versioni di Android precedenti a Android 5,0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Requisiti

Per usare le nuove funzionalità di Android 5,0 e versioni successive (incluso `CardView`) nelle app basate su Novell, è necessario quanto segue:

- **Novell. Android** &ndash; Novell. Android 4,20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- **Android SDK** &ndash; Android 5,0 (API 21) o versione successiva deve essere installato tramite Android SDK Manager.

- **Java JDK 1,8** &ndash; JDK 1,7 può essere usato se si destinazione in modo specifico il livello API 23 e versioni precedenti. JDK 1,8 è disponibile da [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

L'app deve includere anche il `Xamarin.Android.Support.v7.CardView` pacchetto. Per aggiungere il `Xamarin.Android.Support.v7.CardView` pacchetto in Visual Studio per Mac:

1. Aprire il progetto, fare clic con il pulsante destro del mouse su **pacchetti** e selezionare **Aggiungi pacchetti**.

2. Nella finestra di dialogo **Aggiungi pacchetti** cercare **CardView**.

3. Selezionare la **libreria di supporto Novell V7 CardView**e quindi fare clic su **Aggiungi pacchetto**.

Per aggiungere il `Xamarin.Android.Support.v7.CardView` pacchetto in Visual Studio:

1. Aprire il progetto, fare clic con il pulsante destro del mouse sul nodo **riferimenti** (nel riquadro **Esplora soluzioni** ) e scegliere **Gestisci pacchetti NuGet...** .

2. Quando viene visualizzata la finestra di dialogo **Gestisci pacchetti NuGet** , immettere **CardView** nella casella di ricerca.

3. Quando viene visualizzata la **libreria di supporto Novell V7 CardView** , fare clic su **Installa**.

Per informazioni su come configurare un progetto di app Android 5,0, vedere [configurazione di un progetto android 5,0](~/android/platform/lollipop.md).
Per ulteriori informazioni sull'installazione di pacchetti NuGet, [vedere Procedura dettagliata: Inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

## <a name="introducing-cardview"></a>Introduzione a CardView

Il valore `CardView` predefinito è simile a una scheda bianca con angoli arrotondati minimi e una lieve ombreggiatura. Nel layout **Main. aXML** di esempio seguente viene visualizzato `CardView` un singolo `TextView`widget che contiene:

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

Se si usa questo XML per sostituire il contenuto esistente di **Main. aXML**, assicurarsi di impostare come commento il codice in **MainActivity.cs** che fa riferimento alle risorse nel codice XML precedente.

Questo esempio di layout crea un `CardView` valore predefinito con una sola riga di testo, come illustrato nella schermata seguente:

[![Screenshot di CardView con sfondo bianco e riga di testo](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

In questo esempio, lo stile dell'app è impostato sul tema materiale chiaro (`Theme.Material.Light`) in modo che `CardView` le ombreggiature e i bordi risultino più facili da vedere. Per altre informazioni sulle app Android 5,0, vedere tema del [materiale](~/android/user-interface/material-theme.md). Nella sezione successiva verrà illustrato come personalizzare `CardView` per un'applicazione.

## <a name="customizing-cardview"></a>Personalizzazione di CardView

È possibile modificare gli attributi `CardView` di base per personalizzare l'aspetto `CardView` di nell'app. Ad esempio, l'elevazione di `CardView` può essere aumentata per eseguire il cast di un'ombreggiatura maggiore (il che rende la scheda mobile più in alto rispetto allo sfondo). Inoltre, è possibile aumentare il raggio dell'angolo per rendere più arrotondati gli angoli della scheda.

Nell'esempio di layout successivo, viene usato `CardView` un oggetto personalizzato per creare una simulazione di una fotografia di stampa ("snapshot"). Un `ImageView` oggetto viene aggiunto all' `CardView` oggetto per la visualizzazione dell'immagine e `TextView` un oggetto viene posizionato `ImageView` sotto l'oggetto per visualizzare il titolo dell'immagine.
In questo layout di esempio, `CardView` presenta le seguenti personalizzazioni:

- `cardElevation` Viene aumentata a 4DP per eseguire il cast di un'ombreggiatura maggiore.
- `cardCornerRadius` Viene aumentata a 5DP per fare in modo che gli angoli risultino più arrotondati.

Poiché `CardView` è fornito dalla libreria di supporto per Android V7, i relativi attributi non sono disponibili `android:` dallo spazio dei nomi. Pertanto, è necessario definire uno spazio dei nomi XML personalizzato e utilizzare tale spazio `CardView` dei nomi come prefisso dell'attributo. Nell'esempio di layout seguente verrà usata questa riga per definire uno spazio dei nomi denominato `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

È possibile chiamare questo spazio `card_view` dei nomi `myapp` o anche se si sceglie (è accessibile solo all'interno dell'ambito di questo file). Indipendentemente dal modo in cui si sceglie di chiamare questo spazio dei nomi, `CardView` è necessario utilizzarlo per anteporre il prefisso all'attributo che si desidera modificare. In questo esempio di layout lo `cardview` spazio dei nomi è il `cardElevation` prefisso `cardCornerRadius`per e:

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

Quando questo esempio di layout viene usato per visualizzare un'immagine in un'app di visualizzazione foto `CardView` , ha l'aspetto di uno snapshot foto, come illustrato nello screenshot seguente:

[![CardView con un'immagine e una didascalia sotto l'immagine](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Questa schermata è ricavata dall'app di esempio [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , che `RecyclerView` usa un widget per presentare un elenco di `CardView` immagini a scorrimento per visualizzare le foto. Per ulteriori informazioni su `RecyclerView`, vedere la Guida di [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) .

Si noti che `CardView` un oggetto può visualizzare più di una visualizzazione figlio nella relativa area di contenuto. Ad esempio, nell'esempio di app per la visualizzazione di foto precedente l'area di contenuto è costituita da `ListView` un `ImageView` oggetto che `TextView`contiene e. Sebbene `CardView` le istanze siano spesso disposte verticalmente, è anche possibile disporli orizzontalmente (vedere [creazione di uno stile di visualizzazione personalizzato](~/android/user-interface/material-theme.md#customview) per uno screenshot di esempio).

### <a name="cardview-layout-options"></a>Opzioni di layout di CardView

`CardView`è possibile personalizzare i layout impostando uno o più attributi che interessano la spaziatura interna, l'elevazione, il raggio dell'angolo e il colore di sfondo:

[![Diagramma degli attributi di CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Ogni attributo può anche essere modificato dinamicamente chiamando un metodo di `CardView` controparte (per ulteriori informazioni `CardView` sui metodi, vedere il [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Si noti che questi attributi (ad eccezione del colore di sfondo) accettano un valore di dimensione, ovvero un numero decimale seguito dall'unità. Ad esempio, `11.5dp` specifica 11,5 pixel indipendenti dalla densità.

#### <a name="padding"></a>Spaziatura interna

`CardView`offre cinque attributi di riempimento per posizionare il contenuto all'interno della scheda. È possibile impostarli nel codice XML del layout oppure è possibile chiamare metodi analoghi nel codice:

[![Diagramma degli attributi di riempimento CardView](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Gli attributi di riempimento vengono spiegati come segue:

- `contentPadding`Spaziatura interna tra le visualizzazioni figlio `CardView` di e tutti i bordi della scheda. &ndash;

- `contentPaddingBottom`Spaziatura interna tra le visualizzazioni figlio `CardView` di e il bordo inferiore della scheda. &ndash;

- `contentPaddingLeft`Spaziatura interna tra le visualizzazioni figlio `CardView` di e il bordo sinistro della scheda. &ndash;

- `contentPaddingRight`Spaziatura interna tra le visualizzazioni figlio `CardView` di e il bordo destro della scheda. &ndash;

- `contentPaddingTop`Spaziatura interna tra le visualizzazioni figlio `CardView` di e il bordo superiore della scheda. &ndash;

Gli attributi di riempimento del contenuto sono relativi al limite dell'area di contenuto anziché a qualsiasi widget specificato all'interno dell'area del contenuto.
Ad esempio, se `contentPadding` l'app di visualizzazione foto è sufficientemente aumentata, il `CardView` Ritaglia l'immagine e il testo visualizzato sulla scheda.

#### <a name="elevation"></a>Elevazione

`CardView`offre due attributi di elevazione per controllarne l'elevazione e, di conseguenza, le dimensioni dell'ombreggiatura:

[![Diagramma degli attributi di elevazione CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Gli attributi di elevazione sono descritti di seguito:

- `cardElevation`L'elevazione di (rappresenta l'asse Z). `CardView` &ndash;

- `cardMaxElevation`Valore massimo dell'elevazione `CardView`di. &ndash;

I valori maggiori `cardElevation` di aumentano le dimensioni dell' `CardView` ombreggiatura in modo da sembrare fluttuare più in alto rispetto allo sfondo. L' `cardElevation` attributo determina anche l'ordine di disegno delle visualizzazioni sovrapposte, ovvero l'oggetto `CardView` verrà disegnato in un'altra visualizzazione sovrapposta con un'impostazione di elevazione più elevata e sopra qualsiasi vista sovrapposta con un'impostazione di elevazione inferiore.
Questa impostazione è utile quando l'applicazione cambia in &ndash; modo dinamico l'elevazione dei privilegi evitando che l'ombreggiatura si estenda oltre il limite definito con questa impostazione. `cardMaxElevation`

#### <a name="corner-radius-and-background-color"></a>Raggio dell'angolo e colore di sfondo

`CardView`offre attributi che è possibile usare per controllare il raggio dell'angolo e il colore di sfondo. Queste due proprietà consentono di modificare lo stile complessivo di `CardView`:

[![Diagramma di attributi di colore di sfondo e radio di CardView angolo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Questi attributi sono descritti di seguito:

- `cardCornerRadius`Raggio dell'angolo di tutti gli angoli dell' `CardView`oggetto. &ndash;

- `cardBackgroundColor`Colore di sfondo dell'oggetto `CardView`. &ndash;

In questo diagramma, `cardCornerRadius` è impostato su un 10DP più arrotondato `cardBackgroundColor` ed è impostato `"#FFFFCC"` su (giallo chiaro).

## <a name="compatibility"></a>Compatibilità

È possibile usare `CardView` nelle versioni di Android precedenti a Android 5,0 Lollipop. Poiché `CardView` fa parte della libreria di supporto di Android V7, è possibile `CardView` usare con Android 2,1 (livello API 7) e versioni successive.
Tuttavia, è necessario installare il `Xamarin.Android.Support.v7.CardView` pacchetto come descritto in [requisiti](#requirements), sopra.

`CardView`presenta un comportamento leggermente diverso nei dispositivi prima del Lollipop (livello API 21):

- `CardView`Usa un'implementazione Shadow a livello di codice che aggiunge spaziatura interna aggiuntiva.

- `CardView`non Ritaglia le `CardView`visualizzazioni figlio che si intersecano con gli angoli arrotondati.

Per semplificare la gestione di queste differenze di `CardView` compatibilità, in sono disponibili diversi attributi aggiuntivi che è possibile configurare nel layout:

- `cardPreventCornerOverlap`Impostare questo attributo su per aggiungere spaziatura interna quando l'app è in esecuzione in versioni precedenti di Android (livello API 20 e versioni precedenti). `true` &ndash; Questa impostazione impedisce `CardView` che il `CardView`contenuto venga intersecato con gli angoli arrotondati.

- `cardUseCompatPadding`Impostare questo attributo su `true` per aggiungere spaziatura interna quando l'app è in esecuzione in versioni di Android a o superiore al livello API 21. &ndash; Se si vuole usare `CardView` nei dispositivi pre-Lollipop e si ha lo stesso aspetto sul simbolo (o versione successiva), impostare questo `true`attributo su. Quando questo attributo è abilitato, `CardView` aggiunge spaziatura interna aggiuntiva per creare ombre quando viene eseguito su dispositivi pre-Lollipop. Ciò consente di superare le differenze nella spaziatura interna che vengono introdotte quando sono attive le implementazioni Shadow programmatiche pre-Lollipop.

Per altre informazioni sulla gestione della compatibilità con le versioni precedenti di Android, vedere [gestione della compatibilità](https://developer.android.com/training/material/compatibility.html).

## <a name="summary"></a>Riepilogo

Questa guida ha introdotto il `CardView` nuovo widget incluso in Android 5,0 (Lollipop). È stato illustrato l' `CardView` aspetto predefinito e viene spiegato come `CardView` personalizzarlo cambiando l'elevazione, l'arrotondamento dell'angolo, il riempimento del contenuto e il colore di sfondo. Elenca gli `CardView` attributi di layout (con diagrammi di riferimento) e spiega come usarli `CardView` nei dispositivi Android precedenti a Android 5,0 Lollipop. Per ulteriori informazioni su `CardView`, vedere la pagina relativa al [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introduzione a lollipop](~/android/platform/lollipop.md)
- [Riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
