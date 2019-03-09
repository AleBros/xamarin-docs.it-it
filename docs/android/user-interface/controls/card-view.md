---
title: CardView
description: Il widget Cardview è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. Questa guida illustra come usare e personalizzare i widget CardView nelle applicazioni xamarin. Android, mantenendo la compatibilità con le versioni precedenti di Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: cdb75207bff3f15a54d0cdd90fa0833da9c145e6
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670649"
---
# <a name="cardview"></a>CardView

_Il widget Cardview è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. Questa guida illustra come usare e personalizzare i widget CardView nelle applicazioni xamarin. Android, mantenendo la compatibilità con le versioni precedenti di Android._


## <a name="overview"></a>Panoramica

Il `Cardview` widget, introdotti in Android 5.0 (Lollipop), è un componente dell'interfaccia utente che presenta il contenuto di testo e immagine in viste che sono simili a schede. `CardView` viene implementato come un `FrameLayout` widget con angoli arrotondati e un'ombreggiatura. In genere, un `CardView` viene usato per presentare un elemento di riga singola in un `ListView` o `GridView` gruppo della visualizzazione. Ad esempio, la schermata riportata di seguito è riportato un esempio di un'app di prenotazione di viaggio che implementa `CardView`-basato su schede di destinazione di viaggio in un scorrevole `ListView`:

![App di esempio con un widget CardView per ogni destinazione di viaggio](card-view-images/01-cardview-example.png)

Questa guida illustra come aggiungere il `CardView` pacchetto di xamarin. Android del progetto, come aggiungere `CardView` per il layout e su come personalizzare l'aspetto di `CardView` nell'app. Inoltre, questa guida fornisce un elenco dettagliato delle `CardView` gli attributi che è possibile modificare, inclusi gli attributi che consentono di usare `CardView` in versioni di Android precedenti alla Android 5.0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Requisiti

Per usare nuovi Android 5.0 e versioni successive funzionalità è necessario quanto segue (tra cui `CardView`) nelle App basate su Xamarin:

-  **Xamarin. Android** &ndash; xamarin. Android 4.20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-  **Android SDK** &ndash; Android 5.0 (API 21) o versione successiva deve essere installato tramite Android SDK Manager.

-  **Java JDK 1.8** &ndash; JDK 1.7 può essere usato se si è specificamente a livello di destinazione è API 23 e versioni precedente. È disponibile dal JDK 1.8 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

L'app deve includere anche il `Xamarin.Android.Support.v7.CardView` pacchetto. Per aggiungere il `Xamarin.Android.Support.v7.CardView` pacchetto in Visual Studio per Mac:

1. Aprire il progetto, fare doppio clic su **pacchetti** e selezionare **Aggiungi pacchetti**.

2. Nel **Aggiungi pacchetti** finestra di dialogo, cercare **widget CardView**.

3. Selezionare **libreria di supporto di Xamarin v7 CardView**, quindi fare clic su **Add Package**.

Per aggiungere il `Xamarin.Android.Support.v7.CardView` pacchetto in Visual Studio:

1. Aprire il progetto, fare doppio clic il **riferimenti** nodo (nelle **Esplora soluzioni** riquadro) e selezionare **Gestisci pacchetti NuGet...** .

2. Quando la **Gestisci pacchetti NuGet** finestra di dialogo è visualizzata, immettere **widget CardView** nella casella di ricerca.

3. Quando **libreria di supporto di Xamarin v7 CardView** viene visualizzata, fare clic su **installare**.

Per informazioni su come configurare un progetto di app Android 5.0, vedere [impostazione di un progetto Android 5.0](~/android/platform/lollipop.md).
Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: Inserimento di pacchetto NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Introduzione a CardView

Il valore predefinito `CardView` è simile a una carta bianca con un'ombreggiatura lieve e angoli arrotondati minima. Nell'esempio seguente **Main. axml** layout Visualizza una singola `CardView` widget che contiene un `TextView`:

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

Se si usa questo codice XML per sostituire il contenuto esistente dei **Main. axml**, assicurarsi di impostare come commento qualsiasi codice **MainActivity.cs** che fa riferimento alle risorse nel codice XML precedente.

Questo esempio di layout viene creato un valore predefinito `CardView` con una singola riga di testo, come illustrato nella schermata riportata di seguito:

[![Screenshot del widget CardView con riga di testo e sfondo bianco](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

In questo esempio, lo stile di app è impostato per la luce tema Material (`Theme.Material.Light`) in modo che il `CardView` ombreggiature e i bordi sono più facili da visualizzare. Per altre informazioni sulle app di Android 5.0 dei temi, vedere [tema Material](~/android/user-interface/material-theme.md). Nella sezione successiva si apprenderà come personalizzare `CardView` per un'applicazione.


## <a name="customizing-cardview"></a>Personalizzazione di widget CardView

È possibile modificare il basic `CardView` attributi per personalizzarne l'aspetto del `CardView` nell'app. Ad esempio, l'elevazione del `CardView` può essere aumentato di gettare un'ombra di dimensioni maggiori (che rende la carta sembra float superiore rispetto allo sfondo). Inoltre, è possibile aumentare il raggio dell'angolo per degli angoli della scheda più arrotondato.

Nell'esempio layout successivo, un oggetto personalizzato `CardView` viene usato per creare una simulazione di una fotografia di stampa (uno "snapshot"). Un' `ImageView` viene aggiunto al `CardView` per visualizzare l'immagine e una `TextView` è posizionata sotto il `ImageView` per visualizzare il titolo dell'immagine.
In questo layout di esempio, il `CardView` presenta le seguenti personalizzazioni:

-  Il `cardElevation` viene aumentata a 4dp per gettare un'ombra di dimensioni maggiori.
-  Il `cardCornerRadius` viene aumentata a 5dp per rendere più arrotondati gli angoli.

In quanto `CardView` viene fornito dalla libreria di supporto Android v7, i relativi attributi non sono disponibili dal `android:` dello spazio dei nomi. Pertanto, è necessario definire il proprio spazio dei nomi XML e usare tale spazio dei nomi come il `CardView` prefisso dell'attributo. Nell'esempio layout seguente, si userà questa riga per definire uno spazio dei nomi denominato `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

È possibile chiamare questo spazio dei nomi `card_view` o addirittura `myapp` se si sceglie (è accessibile solo all'interno dell'ambito di questo file). Indipendentemente dalla tua scelta chiamare questo spazio dei nomi, è necessario usarla come prefisso per il `CardView` attributo che si desidera modificare. In questo esempio di layout, il `cardview` dello spazio dei nomi è il prefisso per `cardElevation` e `cardCornerRadius`:

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

Quando questo esempio di layout viene usato per visualizzare un'immagine in un'app di visualizzazione di foto, il `CardView` ha l'aspetto di uno snapshot di foto, come illustrato nello screenshot seguente:

[![Widget CardView con un'immagine e la didascalia sotto l'immagine](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Da cui proviene questo screenshot il [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) app di esempio, che usa un `RecyclerView` widget per presentare un elenco a discesa di `CardView` immagini per la visualizzazione di foto. Per altre informazioni sulle `RecyclerView`, vedere la [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) Guida.

Si noti che un `CardView` può visualizzare più di una visualizzazione figlio nella relativa area di contenuto. Ad esempio, nella visualizzazione di esempio di app foto precedente, l'area del contenuto è costituito da un `ListView` che contiene un `ImageView` e un `TextView`. Sebbene `CardView` istanze spesso vengono disposte verticalmente, è anche possibile disporle in orizzontale (vedere [creando uno stile di visualizzazione personalizzata](~/android/user-interface/material-theme.md#customview) per uno screenshot di esempio).


### <a name="cardview-layout-options"></a>Opzioni di Layout di widget CardView

`CardView` layout può essere personalizzato tramite l'impostazione di uno o più attributi che interessano la spaziatura interna, l'elevazione dei privilegi, raggio dell'angolo e il colore di sfondo:

[![Diagramma di widget CardView attributi](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Ogni attributo può anche essere modificato in modo dinamico chiamando una controparte `CardView` metodo (per ulteriori informazioni sul `CardView` metodi, vedere la [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Si noti che questi attributi (ad eccezione di colore di sfondo) accettano un valore di dimensione, che è un numero decimale seguito dall'unità. Ad esempio, `11.5dp` specifica 11,5 pixel indipendenti dalla densità.


#### <a name="padding"></a>Spaziatura interna
`
CardView` offre cinque attributi di spaziatura interna per posizionare il contenuto all'interno della scheda. È possibile impostarli nel layout XML oppure è possibile chiamare metodi analoghi nel codice:

[![Diagramma del widget CardView spaziatura attributi](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Come indicato di seguito sono illustrati gli attributi di spaziatura interna:

-  `contentPadding` &ndash; Spaziatura tra le visualizzazioni figlio di interna di `CardView` e tutti i bordi della scheda.

-  `contentPaddingBottom` &ndash; Spaziatura tra le visualizzazioni figlio di interna di `CardView` e il bordo inferiore della scheda.

-  `contentPaddingLeft` &ndash; Spaziatura tra le visualizzazioni figlio di interna di `CardView` e il bordo sinistro della scheda.

-  `contentPaddingRight` &ndash; Spaziatura tra le visualizzazioni figlio di interna di `CardView` e il bordo destro della scheda.

-  `contentPaddingTop` &ndash; Spaziatura tra le visualizzazioni figlio di interna di `CardView` e il bordo superiore della scheda.

Gli attributi di spaziatura interna del contenuto sono rispetto al limite dell'area di contenuto anziché a qualsiasi dato widget che si trova all'interno dell'area del contenuto.
Ad esempio, se `contentPadding` erano sufficientemente aumentata nell'app per la visualizzazione di foto di `CardView` sarebbe ritagliare l'immagine e il testo visualizzato nella scheda.



#### <a name="elevation"></a>Elevazione dei privilegi

`CardView` offre due attributi di elevazione dei privilegi per il controllo relativo l'elevazione dei privilegi e, di conseguenza, le dimensioni della relativa ombreggiatura:

[![Diagramma degli attributi di widget CardView l'elevazione dei privilegi](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Come indicato di seguito sono illustrati gli attributi di elevazione dei privilegi:

-  `cardElevation` &ndash; L'elevazione del `CardView` (rappresenta l'asse Z).

-  `cardMaxElevation` &ndash; Il valore massimo del `CardView`dell'elevazione dei privilegi.

Valori più grandi dei `cardElevation` aumentare le dimensioni dell'ombreggiatura per rendere `CardView` sembra float superiore rispetto allo sfondo. Il `cardElevation` attributo determina anche l'ordine di disegno di sovrapposizione visualizzazioni, vale a dire, il `CardView` verranno disegnati sotto un'altra visualizzazione sovrapposta con un'impostazione maggiore di elevazione dei privilegi e versioni successive tutte le viste sovrapposte e il valore di elevazione dei privilegi più bassi.
Il `cardMaxElevation` impostazione è utile per quando l'app modifica l'elevazione dei privilegi in modo dinamico &ndash; impedisce l'ombreggiatura di estensione oltre il limite definito tramite questa impostazione.


#### <a name="corner-radius-and-background-color"></a>Raggio dell'angolo e il colore di sfondo

`CardView` offre attributi che è possibile usare per controllare il raggio dell'angolo e il colore di sfondo. Queste due proprietà consentono di modificare lo stile complessivo del `CardView`:

[![Diagramma di widget CardView angolo radious e attributi di colore di sfondo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Questi attributi eseguono le operazioni seguenti:

-  `cardCornerRadius` &ndash; Il raggio dell'angolo di tutti gli angoli del `CardView`.

-  `cardBackgroundColor` &ndash; Il colore di sfondo di `CardView`.

In questo diagramma `cardCornerRadius` è impostata su un 10dp più arrotondati e `cardBackgroundColor` è impostata su `"#FFFFCC"` (giallo chiaro).


## <a name="compatibility"></a>Compatibilità

È possibile usare `CardView` in versioni di Android precedenti alla Android 5.0 Lollipop. In quanto `CardView` fa parte della libreria di supporto Android v7, è possibile usare `CardView` con Android 2.1 (API livello 7) e versioni successive.
Tuttavia, è necessario installare il `Xamarin.Android.Support.v7.CardView` creare un pacchetto come descritto in [requisiti](#requirements)sopra.

`CardView` presenta un comportamento leggermente diverso nei dispositivi prima di Lollipop (API livello 21):

-  `CardView` Usa un'implementazione di ombreggiatura a livello di codice che aggiunge spaziature interne aggiuntive.

-  `CardView` non Ritaglia le visualizzazioni figlio che interseca il `CardView`di angoli arrotondati.

Per consentire la gestione di queste differenze di compatibilità, `CardView` fornisce diversi attributi aggiuntivi che è possibile configurare nel layout:

-   `cardPreventCornerOverlap` &ndash; Impostare questo attributo su `true` aggiungere spaziatura interna quando l'app è in esecuzione in versioni precedenti versioni di Android (livello API 20 e versioni precedente). Questa impostazione impedisce `CardView` contenuto che si intersecano con i `CardView`degli angoli arrotondati.

-   `cardUseCompatPadding` &ndash; Impostare questo attributo su `true` aggiungere spaziatura interna quando l'app è in esecuzione nelle versioni di Android a o maggiore di livello API 21. Se si desidera utilizzare `CardView` sui dispositivi pre-Lollipop e averlo presenta lo stesso in Lollipop (o versioni successive), impostare questo attributo su `true`. Quando questo attributo è abilitato, `CardView` aggiunge spaziature interne aggiuntive per disegnare shadows quando è in esecuzione sui dispositivi pre-Lollipop. Ciò consente di superare le differenze nella spaziatura interna che sono stati introdotti quando implementazioni pre-Lollipop replicate a livello di codice sono attive.

Per altre informazioni su come mantenere la compatibilità con le versioni precedenti di Android, vedere [compatibilità gestione](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Riepilogo

Questa guida ha presentato il nuovo `CardView` widget incluso in Android 5.0 (Lollipop). È stato illustrato il valore predefinito `CardView` aspetto e ha spiegato come personalizzare `CardView` modificando le elevazione dei privilegi, angoli, spaziatura interna del contenuto e il colore di sfondo. Viene visualizzato il `CardView` attributi di layout (con diagrammi di riferimento) e spiega come usare `CardView` nei dispositivi Android precedenti alla Android 5.0 Lollipop. Per altre informazioni sulle `CardView`, vedere la [riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introduzione al simbolo](~/android/platform/lollipop.md)
- [Riferimento alla classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
