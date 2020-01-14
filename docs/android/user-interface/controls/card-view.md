---
title: CardView
description: Il widget CardView è un componente dell'interfaccia utente che presenta il contenuto del testo e dell'immagine nelle visualizzazioni simili a quelle delle schede. Questa guida illustra come usare e personalizzare CardView in applicazioni Xamarin.Android mantenendo la compatibilità con le versioni precedenti di Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 053847426d770408826297d9a80b6e38d7f6bc44
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029274"
---
# <a name="xamarinandroid-cardview"></a>Xamarin.Android CardView

_Il widget CardView è un componente dell'interfaccia utente che presenta il contenuto del testo e dell'immagine nelle visualizzazioni simili a quelle delle schede. Questa guida illustra come usare e personalizzare CardView in applicazioni Xamarin.Android mantenendo la compatibilità con le versioni precedenti di Android._

## <a name="overview"></a>Panoramica

Il widget `Cardview`, introdotto in Android 5,0 (Lollipop), è un componente dell'interfaccia utente che presenta il contenuto di testo e immagini nelle visualizzazioni simili a schede. `CardView` viene implementato come un widget `FrameLayout` con angoli arrotondati e un'ombreggiatura. In genere, viene utilizzato un `CardView` per presentare un singolo elemento riga in un gruppo di `ListView` o `GridView` visualizzazione. Ad esempio, lo screenshot seguente è un esempio di app di prenotazione di viaggio che implementa le schede di destinazione di viaggio basate su `CardView`in una `ListView`scorrevole:

![App di esempio che usa un CardView per ogni destinazione di viaggio](card-view-images/01-cardview-example.png)

Questa guida illustra come aggiungere il pacchetto di `CardView` al progetto Xamarin.Android, come aggiungere `CardView` al layout e come personalizzare l'aspetto dei `CardView` nell'app. Questa guida fornisce inoltre un elenco dettagliato degli attributi di `CardView` che è possibile modificare, inclusi gli attributi che consentono di usare `CardView` versioni di Android precedenti a Android 5,0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Requisiti

Per usare le nuove funzionalità di Android 5,0 e versioni successive (incluso `CardView`) nelle app basate su Xamarin, è necessario quanto segue:

- **Xamarin.android** &ndash; Xamarin.Android 4,20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- **Android SDK** &ndash; Android 5,0 (API 21) o versione successiva deve essere installato tramite il Android SDK Manager.

- **Java jdk 1,8** &ndash; JDK 1,7 può essere usato se si destinazione in modo specifico il livello API 23 e versioni precedenti. JDK 1,8 è disponibile da [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

L'app deve includere anche il pacchetto di `Xamarin.Android.Support.v7.CardView`. Per aggiungere il pacchetto di `Xamarin.Android.Support.v7.CardView` in Visual Studio per Mac:

1. Aprire il progetto, fare clic con il pulsante destro del mouse su **pacchetti** e selezionare **Aggiungi pacchetti**.

2. Nella finestra di dialogo **Aggiungi pacchetti** cercare **CardView**.

3. Selezionare la **libreria di supporto Xamarin V7 CardView**e quindi fare clic su **Aggiungi pacchetto**.

Per aggiungere il pacchetto di `Xamarin.Android.Support.v7.CardView` in Visual Studio:

1. Aprire il progetto, fare clic con il pulsante destro del mouse sul nodo **riferimenti** (nel riquadro **Esplora soluzioni** ) e scegliere **Gestisci pacchetti NuGet...** .

2. Quando viene visualizzata la finestra di dialogo **Gestisci pacchetti NuGet** , immettere **CardView** nella casella di ricerca.

3. Quando viene visualizzata la **libreria di supporto Xamarin V7 CardView** , fare clic su **Installa**.

Per informazioni su come configurare un progetto di app Android 5,0, vedere [configurazione di un progetto android 5,0](~/android/platform/lollipop.md).
Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

## <a name="introducing-cardview"></a>Introduzione a CardView

Il `CardView` predefinito è simile a una scheda bianca con angoli arrotondati minimi e una lieve ombreggiatura. Nel layout **Main. aXML** di esempio seguente viene visualizzato un singolo widget di `CardView` contenente una `TextView`:

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

Questo esempio di layout crea una `CardView` predefinita con una sola riga di testo, come illustrato nella schermata seguente:

[![screenshot di CardView con sfondo bianco e riga di testo](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

In questo esempio, lo stile dell'app è impostato sul tema del materiale chiaro (`Theme.Material.Light`), in modo che i `CardView` ombreggiature e i bordi siano più semplici da visualizzare. Per altre informazioni sulle app Android 5,0, vedere tema del [materiale](~/android/user-interface/material-theme.md). Nella sezione successiva verrà illustrato come personalizzare `CardView` per un'applicazione.

## <a name="customizing-cardview"></a>Personalizzazione di CardView

È possibile modificare gli attributi di base del `CardView` per personalizzare l'aspetto della `CardView` nell'app. Ad esempio, l'elevazione del `CardView` può essere aumentata per eseguire il cast di un'ombreggiatura più grande (il che rende la scheda sembra fluttuare più in alto rispetto allo sfondo). Inoltre, è possibile aumentare il raggio dell'angolo per rendere più arrotondati gli angoli della scheda.

Nell'esempio di layout successivo viene usata una `CardView` personalizzata per creare una simulazione di una fotografia di stampa ("snapshot"). Viene aggiunto un `ImageView` al `CardView` per la visualizzazione dell'immagine e un `TextView` è posizionato sotto la `ImageView` per visualizzare il titolo dell'immagine.
In questo layout di esempio, il `CardView` presenta le seguenti personalizzazioni:

- Il `cardElevation` viene aumentato a 4DP per eseguire il cast di un'ombreggiatura maggiore.
- Il `cardCornerRadius` viene aumentato a 5DP per fare in modo che gli angoli risultino più arrotondati.

Poiché `CardView` viene fornito dalla libreria di supporto di Android V7, i relativi attributi non sono disponibili dallo spazio dei nomi `android:`. Pertanto, è necessario definire uno spazio dei nomi XML personalizzato e utilizzare tale spazio dei nomi come prefisso dell'attributo `CardView`. Nell'esempio di layout seguente verrà usata questa riga per definire uno spazio dei nomi denominato `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

È possibile chiamare questo spazio dei nomi `card_view` o persino `myapp` se si sceglie (è accessibile solo all'interno dell'ambito di questo file). Qualunque sia la scelta di chiamare questo spazio dei nomi, è necessario utilizzarlo per anteporre al `CardView` attributo che si desidera modificare. In questo esempio di layout lo spazio dei nomi `cardview` è il prefisso per `cardElevation` e `cardCornerRadius`:

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

Quando questo esempio di layout viene usato per visualizzare un'immagine in un'app di visualizzazione foto, il `CardView` ha l'aspetto di uno snapshot foto, come illustrato nello screenshot seguente:

[![CardView con un'immagine e una didascalia sotto l'immagine](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Questa schermata è ricavata dall'app di esempio [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , che usa un widget `RecyclerView` per presentare un elenco scorrevole di immagini `CardView` per visualizzare le foto. Per ulteriori informazioni su `RecyclerView`, vedere la Guida di [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) .

Si noti che in un `CardView` è possibile visualizzare più di una visualizzazione figlio nella relativa area di contenuto. Ad esempio, nell'esempio di app di visualizzazione foto precedente, l'area del contenuto è costituita da una `ListView` che contiene una `ImageView` e un `TextView`. Sebbene le istanze di `CardView` siano spesso disposte verticalmente, è anche possibile disporli orizzontalmente (vedere [creazione di uno stile di visualizzazione personalizzato](~/android/user-interface/material-theme.md#customview) per uno screenshot di esempio).

### <a name="cardview-layout-options"></a>Opzioni di layout di CardView

`CardView` layout possono essere personalizzati impostando uno o più attributi che influiscono sulla spaziatura interna, l'elevazione, il raggio dell'angolo e il colore di sfondo:

[Diagramma![degli attributi CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Ogni attributo può anche essere modificato dinamicamente chiamando un metodo di `CardView` controparte (per ulteriori informazioni sui metodi di `CardView`, vedere il [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Si noti che questi attributi (ad eccezione del colore di sfondo) accettano un valore di dimensione, ovvero un numero decimale seguito dall'unità. Ad esempio, `11.5dp` specifica 11,5 pixel indipendenti dalla densità.

#### <a name="padding"></a>Spaziatura interna

`CardView` offre cinque attributi di riempimento per posizionare il contenuto all'interno della scheda. È possibile impostarli nel codice XML del layout oppure è possibile chiamare metodi analoghi nel codice:

[Diagramma![degli attributi di riempimento CardView](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Gli attributi di riempimento vengono spiegati come segue:

- `contentPadding` &ndash; la spaziatura interna tra le visualizzazioni figlio del `CardView` e tutti i bordi della scheda.

- `contentPaddingBottom` &ndash; la spaziatura interna tra le visualizzazioni figlio del `CardView` e il bordo inferiore della scheda.

- `contentPaddingLeft` &ndash; la spaziatura interna tra le visualizzazioni figlio del `CardView` e il bordo sinistro della scheda.

- `contentPaddingRight` &ndash; la spaziatura interna tra le visualizzazioni figlio del `CardView` e il bordo destro della scheda.

- `contentPaddingTop` &ndash; la spaziatura interna tra le visualizzazioni figlio del `CardView` e il bordo superiore della scheda.

Gli attributi di riempimento del contenuto sono relativi al limite dell'area di contenuto anziché a qualsiasi widget specificato all'interno dell'area del contenuto.
Se ad esempio `contentPadding` è stato sufficientemente aumentato nell'app visualizzazione foto, il `CardView` Ritaglia sia l'immagine che il testo visualizzato sulla scheda.

#### <a name="elevation"></a>Elevazione

`CardView` offre due attributi di elevazione per controllarne l'elevazione e, di conseguenza, le dimensioni dell'ombreggiatura:

[Diagramma![degli attributi di elevazione CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Gli attributi di elevazione sono descritti di seguito:

- `cardElevation` &ndash; l'elevazione del `CardView` (rappresenta l'asse Z).

- `cardMaxElevation` &ndash; il valore massimo dell'elevazione `CardView`.

I valori più grandi di `cardElevation` aumentano le dimensioni dell'ombreggiatura per fare in modo che `CardView` sembrano fluttuare più in alto sullo sfondo. L'attributo `cardElevation` determina anche l'ordine di disegno delle visualizzazioni sovrapposte; ovvero, la `CardView` verrà disegnata in un'altra visualizzazione sovrapposta con un'impostazione di elevazione più elevata e sopra qualsiasi vista sovrapposta con un'impostazione di elevazione inferiore.
L'impostazione `cardMaxElevation` è utile quando l'applicazione cambia in modo dinamico l'elevazione &ndash; impedisce che l'ombreggiatura si estenda oltre il limite definito con questa impostazione.

#### <a name="corner-radius-and-background-color"></a>Raggio dell'angolo e colore di sfondo

`CardView` offre attributi che è possibile usare per controllare il raggio dell'angolo e il colore di sfondo. Queste due proprietà consentono di modificare lo stile generale del `CardView`:

[Diagramma di![degli attributi di colore di sfondo e radio di CardView angolo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Questi attributi sono descritti di seguito:

- `cardCornerRadius` &ndash; raggio dell'angolo di tutti gli angoli della `CardView`.

- `cardBackgroundColor` &ndash; il colore di sfondo della `CardView`.

In questo diagramma `cardCornerRadius` è impostato su un valore di 10DP più arrotondato e `cardBackgroundColor` è impostato su `"#FFFFCC"` (giallo chiaro).

## <a name="compatibility"></a>Compatibilità

È possibile usare `CardView` nelle versioni di Android precedenti a Android 5,0 Lollipop. Poiché `CardView` fa parte della libreria di supporto di Android V7, è possibile usare `CardView` con Android 2,1 (livello API 7) e versioni successive.
Tuttavia, è necessario installare il pacchetto di `Xamarin.Android.Support.v7.CardView` come descritto in [requisiti](#requirements), sopra.

`CardView` presenta un comportamento leggermente diverso nei dispositivi prima del Lollipop (livello API 21):

- `CardView` usa un'implementazione Shadow a livello di codice che aggiunge spaziatura interna aggiuntiva.

- `CardView` non Ritaglia le visualizzazioni figlio che si intersecano con gli angoli arrotondati dell'`CardView`.

Per semplificare la gestione di queste differenze di compatibilità, `CardView` fornisce diversi attributi aggiuntivi che è possibile configurare nel layout:

- `cardPreventCornerOverlap` &ndash; impostare questo attributo su `true` per aggiungere spaziatura interna quando l'app è in esecuzione in versioni precedenti di Android (livello API 20 e versioni precedenti). Questa impostazione impedisce a `CardView` contenuto di intersecarsi con gli angoli arrotondati del `CardView`.

- `cardUseCompatPadding` &ndash; impostare questo attributo su `true` per aggiungere spaziatura interna quando l'app è in esecuzione in versioni di Android a o superiore al livello API 21. Se si vuole usare `CardView` nei dispositivi pre-Lollipop e fare in modo che abbia lo stesso aspetto sul simbolo (o versione successiva), impostare questo attributo su `true`. Quando questo attributo è abilitato, `CardView` aggiunge una spaziatura interna aggiuntiva per creare ombre quando viene eseguito su dispositivi pre-Lollipop. Ciò consente di superare le differenze nella spaziatura interna che vengono introdotte quando sono attive le implementazioni Shadow programmatiche pre-Lollipop.

Per altre informazioni sulla gestione della compatibilità con le versioni precedenti di Android, vedere [gestione della compatibilità](https://developer.android.com/training/material/compatibility.html).

## <a name="summary"></a>Riepilogo

Questa guida ha introdotto il nuovo widget `CardView` incluso in Android 5,0 (Lollipop). È stato illustrato il `CardView` aspetto predefinito e viene illustrato come personalizzare `CardView` modificando l'elevazione, l'arrotondamento dell'angolo, la spaziatura interna del contenuto e il colore di sfondo. Elenca gli attributi di layout `CardView` (con diagrammi di riferimento) e spiega come usare `CardView` nei dispositivi Android precedenti a Android 5,0 Lollipop. Per ulteriori informazioni su `CardView`, vedere la pagina relativa al [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introduzione a lollipop](~/android/platform/lollipop.md)
- [Riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
