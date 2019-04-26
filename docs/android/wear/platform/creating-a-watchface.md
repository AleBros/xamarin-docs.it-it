---
title: Creazione di una Watch Face per Android Wear 1.0
description: Questa guida illustra come implementare un servizio di volti espressioni di controllo personalizzato per Android Wear 1.0. Vengono fornite istruzioni dettagliate per la creazione di un' servizio faccia watch digitale, seguito da altro codice per creare un quadrante orologio analogico-style.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/23/2018
ms.openlocfilehash: 067a39838fbfe3f1b33ac0d30b5069366b11e407
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61287220"
---
# <a name="creating-a-watch-face"></a>Creazione di una watch face

_Questa guida illustra come implementare un servizio di volti espressioni di controllo personalizzato per Android Wear 1.0. Vengono fornite istruzioni dettagliate per la creazione di un' servizio faccia watch digitale, seguito da altro codice per creare un quadrante orologio analogico-style._

## <a name="overview"></a>Panoramica

In questa procedura dettagliata, viene creato un servizio di volti watch base per illustrare i passaggi fondamentali della creazione di una watch face personalizzata di Android Wear 1.0.
Il servizio di volti watch iniziale consente di visualizzare un'espressione di controllo digitale semplice che visualizza l'ora corrente in ore e minuti:

[![Quadrante orologio digitale](creating-a-watchface-images/01-initial-face.png "screenshot di esempio del quadrante dell'orologio digitale iniziale")](creating-a-watchface-images/01-initial-face.png#lightbox)

Dopo questa quadrante orologio digitale viene sviluppato e testato, viene aggiunto altro codice per eseguirne l'aggiornamento a una più sofisticati quadrante orologio analogico con tre mani:

[![Quadrante orologio analogico](creating-a-watchface-images/02-example-watchface.png "screenshot di esempio del quadrante dell'orologio analogico finale")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Watch face servizi sono in bundle e installati come parte di un'app per Wear 1.0. Negli esempi seguenti, `MainActivity` nient'altro che il codice del modello di app Wear 1.0 contiene in modo che il servizio di volti espressioni di controllo può essere incluso nel pacchetto e distribuito per le espressioni di controllo smart come parte dell'app. In effetti, questa app verrà usato esclusivamente come un veicolo per ottenere il servizio di volti watch caricato nel dispositivo Wear 1.0 (o un emulatore) per il debug e test.

## <a name="requirements"></a>Requisiti

Per implementare un servizio di volti espressioni di controllo, è necessario quanto segue:

-   Android 5.0 (livello API 21) o versione successiva in usura del dispositivo o emulatore.

-   Il [Xamarin Android Wear supporto librerie](https://www.nuget.org/packages/Xamarin.Android.Wear) deve essere aggiunto al progetto xamarin. Android.

Anche se Android 5.0 è livello per implementare un servizio di volti watch, Android 5.1 l'API minima o in un secondo momento è consigliabile. Android Wear i dispositivi che eseguono Android 5.1 (API 22) o versione successiva consentire alle App per Wear controllare ciò che viene visualizzato sullo schermo mentre il dispositivo è in modalità a basso consumo *ambiente* modalità. Quando il dispositivo lascia a bassa potenza *ambientale* modalità, è in *interattivo* modalità. Per altre informazioni su queste modalità, vedere [mantenendo Your App visibili](https://developer.android.com/training/wearables/apps/always-on.html).


## <a name="start-an-app-project"></a>Avviare un progetto di App

Creare un nuovo progetto Android Wear 1.0 chiamato **WatchFace** (per altre informazioni sulla creazione di nuovi progetti xamarin. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Finestra di dialogo Nuovo progetto](creating-a-watchface-images/03-wear-project-vs-sml.png "selezionare App Wear nella finestra di dialogo Nuovo progetto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Finestra di dialogo Nuovo progetto](creating-a-watchface-images/03-wear-project-xs-sml.png "selezionare App Wear nella finestra di dialogo Nuovo progetto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Impostare il nome del pacchetto su `com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Impostazione del nome del pacchetto](creating-a-watchface-images/04-package-name-vs.png "impostare il nome del pacchetto su com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Impostazione del nome del pacchetto](creating-a-watchface-images/04-package-name-xs.png "impostare il nome del pacchetto su com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Inoltre, scorrere verso il basso e abilitare la **INTERNET** e **WAKE_LOCK** autorizzazioni:

[![Autorizzazioni necessarie](creating-a-watchface-images/05-required-permissions-vs.png "abilitare INTERNET e WAKE_LOCK autorizzazioni")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Impostare la versione minima di Android **5.1 Android (livello API 22)**.
Inoltre, attivare il **Internet** e **WakeLock** autorizzazioni:

[![Autorizzazioni necessarie](creating-a-watchface-images/05-required-permissions-xs.png "abilitare Internet e WakeLock autorizzazioni")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Successivamente, scaricare [preview.png](creating-a-watchface-images/preview.png) &ndash; questa opzione verrà aggiunta per il **drawable** cartella più avanti in questa procedura dettagliata.


## <a name="add-the-xamarinandroid-wear-package"></a>Aggiungere il pacchetto xamarin. Android Wear

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Avviare Gestione pacchetti NuGet (in Visual Studio, fare doppio clic **riferimenti** nel **Esplora soluzioni** e selezionare **Gestisci pacchetti NuGet** ). Aggiornare il progetto alla versione stabile più recente di **wear**:

[![Aggiungere pacchetti NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "aggiungere il pacchetto wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Successivamente, se **Xamarin.Android.Support.v13** è installato, disinstallarlo:

[![Rimozione di gestione pacchetti NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Xamarin.Support.v13 rimuovere")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Avviare Gestione pacchetti NuGet (in Visual Studio per Mac, fare doppio clic su **pacchetti** nel **riquadro della soluzione** e selezionare **Aggiungi pacchetti** ). Aggiornare il progetto alla versione stabile più recente di **wear**:

[![Aggiungere pacchetti NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "aggiungere il pacchetto wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Compilare ed eseguire l'app in un dispositivo Wear o un emulatore (per altre informazioni su come eseguire questa operazione, vedere la [introduttiva](~/android/wear/get-started/index.md) Guida). La seguente schermata dell'app dovrebbe sul dispositivo Wear:

[![Schermata di app](creating-a-watchface-images/08-app-screen.png "schermata dell'App nel dispositivo Wear")](creating-a-watchface-images/08-app-screen.png#lightbox)

A questo punto, l'app Wear base non ha watch face funzionalità perché non offre ancora un'implementazione del servizio faccia espressioni di controllo. Questo servizio verrà aggiunti successivamente.


## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android Wear implementa Guarda i volti tramite il `CanvasWatchFaceService` classe. `CanvasWatchFaceService` è derivato da `WatchFaceService`, che a sua volta è derivato da `WallpaperService` come illustrato nel diagramma seguente:

[![Diagramma di ereditarietà](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService diagramma di ereditarietà")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` include nidificate `CanvasWatchFaceService.Engine`; crea un'istanza di un `CanvasWatchFaceService.Engine` oggetto che esegue il lavoro effettivo del quadrante dell'orologio di disegno. `CanvasWatchFaceService.Engine` è derivato da `WallpaperService.Engine` come illustrato nel diagramma precedente.

Non illustrato nella figura seguente è un `Canvas` che `CanvasWatchFaceService` viene utilizzato per disegnare quadrante dell'orologio &ndash; ciò `Canvas` viene passato tramite la `OnDraw` metodo come descritto di seguito.

Nelle sezioni seguenti, verrà creato un servizio di volti verifica personalizzato seguendo questa procedura:

1.  Definire una classe denominata `MyWatchFaceService` che deriva da `CanvasWatchFaceService`.

2.  All'interno `MyWatchFaceService`, creare una classe annidata denominata `MyWatchFaceEngine` che deriva da `CanvasWatchFaceService.Engine`.

3.  Nelle `MyWatchFaceService`, implementare una `CreateEngine` metodo che crea un'istanza `MyWatchFaceEngine` e lo restituisce.

4.  Nelle `MyWatchFaceEngine`, implementare il `OnCreate` metodo per creare lo stile di watch face ed eseguire altre attività di inizializzazione.

5.  Implementare il `OnDraw` metodo `MyWatchFaceEngine`. Questo metodo viene chiamato ogni volta che deve essere ridisegnato quadrante dell'orologio (vale a dire *invalidati*). `OnDraw` è il metodo che consente di disegnare (e lo ridisegna) watch face elementi, ad esempio ora, minuto e secondo mani.

6.  Implementare il `OnTimeTick` metodo `MyWatchFaceEngine`.
    `OnTimeTick` viene chiamato almeno una volta al minuto (in modalità ambiente sia interattive) o quando è stata modificata la data/ora.

Per altre informazioni sulle `CanvasWatchFaceService`, vedere l'Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) documentazione dell'API.
Analogamente, [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) spiega l'implementazione effettiva del quadrante dell'orologio.


### <a name="add-the-canvaswatchfaceservice"></a>Aggiungere il CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Aggiungere un nuovo file denominato **MyWatchFaceService.cs** (in Visual Studio, fare doppio clic **WatchFace** nel **Esplora soluzioni**, fare clic su **Aggiungi > Nuovo elemento...** e selezionare **classe**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Aggiungere un nuovo file denominato **MyWatchFaceService.cs** (in Visual Studio per Mac, fare doppio clic il **WatchFace** del progetto, fare clic su **Aggiungi > Nuovo File...** e selezionare **classe vuota**).

-----

Sostituire il contenuto di questo file con il codice seguente:

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (derivata da `CanvasWatchFaceService`) è il "programma principale" del quadrante dell'orologio. `MyWatchFaceService` implementa un solo metodo, `OnCreateEngine`, che crea un'istanza e restituisce un `MyWatchFaceEngine` oggetto (`MyWatchFaceEngine` è derivato da `CanvasWatchFaceService.Engine`). Istanziata `MyWatchFaceEngine` oggetto deve essere restituito come un `WallpaperService.Engine`. L'incapsulamento `MyWatchFaceService` oggetto viene passato al costruttore.

`MyWatchFaceEngine` è l'implementazione di volti watch effettivo &ndash; contiene il codice che consente di disegnare quadrante dell'orologio. Gestisce anche gli eventi di sistema, ad esempio modifiche dello schermo (modalità ambiente/interattiva, schermata di disattivazione e così via.).


### <a name="implement-the-engine-oncreate-method"></a>Implementare il metodo OnCreate motore

Il `OnCreate` metodo inizializza quadrante dell'orologio. Aggiungere il campo seguente al `MyWatchFaceEngine`:

```csharp
Paint hoursPaint;
```

Ciò `Paint` oggetto verrà utilizzato per disegnare l'ora corrente nel quadrante dell'orologio. Successivamente, aggiungere il metodo seguente alla `MyWatchFaceEngine`:

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` viene chiamato subito dopo `MyWatchFaceEngine` viene avviato. Configura il `WatchFaceStyle` (che controlla l'interazione con l'utente al dispositivo Wear) e crea un'istanza il `Paint` oggetto che verrà usato per visualizzare l'ora.

La chiamata a `SetWatchFaceStyle` esegue le operazioni seguenti:

1.  Set *modalità peek* a `PeekModeShort`, in modo che le notifiche vengono visualizzati come schede "anteprima" di piccole dimensioni sullo schermo.

2.  Imposta la visibilità in background `Interruptive`, in modo che lo sfondo di una scheda di visualizzazione da mostrare solo brevemente se rappresenta una notifica comporta interruzione.

3.  Disabilita l'ora dell'interfaccia utente di sistema predefinito vengano disegnati nel quadrante dell'orologio in modo che quadrante dell'orologio personalizzato può visualizzare l'ora invece.

Per altre informazioni su queste e altre opzioni di stile watch face, vedere l'Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) documentazione dell'API.

Dopo aver `SetWatchFaceStyle` viene completato, `OnCreate` crea un'istanza di `Paint` oggetto (`hoursPaint`) e imposta il colore bianco e delle relative dimensioni testo 48 pixel ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) deve essere specificato in pixel).


### <a name="implement-the-engine-ondraw-method"></a>Implementare il metodo OnDraw motore

Il `OnDraw` metodo è probabilmente la più importante `CanvasWatchFaceService.Engine` metodo &ndash; è il metodo che effettivamente guarda elementi viso come cifre e disegna lancette viso.
Nell'esempio seguente disegna una stringa di tempo nel quadrante dell'orologio.
Aggiungere il metodo seguente alla `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

Quando Android chiama `OnDraw`, passa una `Canvas` istanza e i limiti in cui è possibile disegnare l'immagine. Nell'esempio di codice riportato sopra, `DateTime` viene usato per calcolare l'ora corrente in ore e minuti (in formato 12 ore). La stringa di ora risultante viene disegnata nell'area di disegno utilizzando il `Canvas.DrawText` (metodo). La stringa verrà visualizzato 70 pixel dal bordo sinistro e 80 pixel verso il basso tra il bordo superiore.

Per altre informazioni sul `OnDraw` metodo, vedere l'Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) documentazione dell'API.


### <a name="implement-the-engine-ontimetick-method"></a>Implementare il metodo OnTimeTick motore

Android chiama periodicamente la `OnTimeTick` metodo per aggiornare l'ora visualizzata da quadrante dell'orologio. Viene chiamato in almeno una volta al minuto (in modalità interattiva e ambiente), oppure quando la data/ora o fuso orario sono state modificate. Aggiungere il metodo seguente alla `MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Questa implementazione di `OnTimeTick` chiama semplicemente `Invalidate`. Il `Invalidate` pianificazioni metodo `OnDraw` ridisegnare quadrante dell'orologio.

Per altre informazioni sul `OnTimeTick` metodo, vedere l'Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) documentazione dell'API.


## <a name="register-the-canvaswatchfaceservice"></a>Registrare il CanvasWatchFaceService

`MyWatchFaceService` deve essere registrata nel **androidmanifest. XML** dell'app per Wear associata. A questo scopo, aggiungere il seguente codice XML per il `<application>` sezione:

```xml
<service
    android:name="watchface.MyWatchFaceService"
    android:label="Xamarin Sample"
    android:allowEmbedded="true"
    android:taskAffinity=""
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data
        android:name="android.service.wallpaper"
        android:resource="@xml/watch_face" />
    <meta-data
        android:name="com.google.android.wearable.watchface.preview"
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

Questo codice XML esegue le operazioni seguenti:

1.  Imposta il `android.permission.BIND_WALLPAPER` l'autorizzazione. Questa autorizzazione concede l'autorizzazione del servizio faccia espressioni di controllo per cambiare lo sfondo del sistema nel dispositivo. Si noti che questa autorizzazione deve essere impostata il `<service>` sezione invece che in esterna `<application>` sezione.

2.  Definisce un `watch_face` risorsa. Questa risorsa è un file XML brevi che dichiara un `wallpaper` risorse (questo file verrà creato nella sezione successiva).

3.  Dichiara un'immagine drawable chiamata `preview` che verranno visualizzati dalla schermata di selezione selettore di espressioni di controllo.

4.  Include un' `intent-filter` informare Android che `MyWatchFaceService` venga visualizzato una watch face.

È stata completata il codice per basic `WatchFace` esempio. Il passaggio successivo consiste nell'aggiungere le risorse necessarie.


## <a name="add-resource-files"></a>Aggiungere i file di risorse

Prima di poter eseguire il servizio di espressioni di controllo, è necessario aggiungere il **watch_face** risorsa e l'immagine di anteprima. In primo luogo, creare un nuovo file XML al **Resources/xml/watch_face.xml** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Imposta l'operazione di generazione del file **AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Azione di compilazione](creating-a-watchface-images/10-android-resource-vs.png "imposta compilazione azione AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Azione di compilazione](creating-a-watchface-images/10-android-resource-xs.png "imposta compilazione azione AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Questo file di risorse definisce una semplice `wallpaper` elemento che verrà usato per quadrante dell'orologio.

Se non è ancora stato fatto, scaricare [preview.png](creating-a-watchface-images/preview.png).
Installare, vedere **Resources/drawable/preview.png**. Assicurarsi di aggiungere questo file per il `WatchFace` progetto. Questa immagine di anteprima viene visualizzata all'utente nella casella di selezione watch face sul dispositivo Wear. Per creare un'immagine di anteprima per il proprio quadrante dell'orologio, è possibile acquisire uno screenshot del quadrante dell'orologio mentre è in esecuzione. (Per altre informazioni sull'acquisizione di schermate dai dispositivi Wear, vedere [screenshot](~/android/wear/deploy-test/debug-on-device.md#screenshots)).


## <a name="try-it"></a>Provalo!

Compilare e distribuire l'app al dispositivo Wear. Verrà visualizzato nella schermata dell'app Wear vengono visualizzati come indicato in precedenza. Eseguire il comando seguente per abilitare la nuova veste di espressioni di controllo:

1.  Scorrere verso destra fino a quando non viene visualizzato lo sfondo della schermata di espressioni di controllo.

2.  Toccare e tenere in un punto qualsiasi dello sfondo della schermata per due secondi.

3.  Scorrere rapidamente da sinistra a destra per esplorare le diverse facce di espressioni di controllo.

4.  Selezionare il **esempio di Xamarin** quadrante (illustrata a destra):

    [![Selezione Watchface](creating-a-watchface-images/11-watchface-picker.png "scorrere rapidamente per individuare i volti di espressioni di controllo di esempio di Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Toccare il **esempio di Xamarin** quadrante per selezionarlo.

Questa operazione modificherà quadrante dell'orologio del dispositivo Wear usare il servizio di volti verifica personalizzato implementato finora:

[![Quadrante orologio digitale](creating-a-watchface-images/12-digital-watchface.png "watch digitale personalizzato in esecuzione nel dispositivo Wear")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Si tratta di una rudimentale relativamente watch face perché l'implementazione di app è pertanto minimo (ad esempio, non include uno sfondo a watch face e non chiami `Paint` anti-alias metodi per migliorare l'aspetto).
Tuttavia implementare la funzionalità pressoché essenziale che è necessaria per creare una personalizzata watch face.

Nella sezione successiva, questa faccia di espressioni di controllo verrà aggiornata a un'implementazione più sofisticata.


## <a name="upgrading-the-watch-face"></a>L'aggiornamento quadrante dell'orologio

Nella parte restante di questa procedura dettagliata, `MyWatchFaceService` viene aggiornato per visualizzare un quadrante orologio analogico stile ed è esteso per supportare altre funzionalità. Per creare quadrante dell'orologio aggiornato verranno aggiunte le funzionalità seguenti:

1.  Indica l'ora con analogico ora, minuto e secondo mani.

2.  Reagisce alle modifiche apportate nella visibilità.

3.  Risponde alle modifiche tra la modalità di ambiente e la modalità interattiva.

4.  Legge le proprietà del dispositivo Wear sottostante.

5.  Aggiorna automaticamente il tempo quando avviene una modifica del fuso orario.

Prima di implementare le modifiche al codice riportato di seguito, scaricare [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), decomprimerlo e spostare i file con estensione png decompresso **risorse/drawable** (sovrascrivere precedente **preview.png**). Aggiungere i nuovi file con estensione PNG di `WatchFace` progetto.


### <a name="update-engine-features"></a>Funzionalità del motore di aggiornamento

Il passaggio successivo è upgrade **MyWatchFaceService.cs** a un'implementazione che consente di disegnare un quadrante orologio analogico e supporta le nuove funzionalità. Sostituire il contenuto del **MyWatchFaceService.cs** con la versione del codice di volti watch analogica [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (è possibile tagliare e incollare questa origine esistente  **MyWatchFaceService.cs**).

Questa versione di **MyWatchFaceService.cs** aggiunge maggiore quantità di codice ai metodi esistenti e include altri metodi sottoposti a override per aggiungere ulteriori funzionalità. Le sezioni seguenti forniscono una presentazione del codice sorgente.

#### <a name="oncreate"></a>OnCreate

Aggiornato **OnCreate** metodo consente di configurare lo stile di watch face come indicato in precedenza, ma con alcuni passaggi aggiuntivi:

1.  Imposta l'immagine di sfondo il **xamarin_background** risorsa che si trova nel **Resources/drawable-hdpi/xamarin_background.png**.

2.  Inizializza `Paint` gli oggetti per tracciare la mano ora, minuti e seconda lancetta.

3.  Inizializza un `Paint` oggetto per disegnare i segni di graduazione ora intorno al bordo del quadrante dell'orologio.

4.  Crea un timer che chiama il `Invalidate` (metodo) (aggiornamento) in modo che l'icona della mano secondo verrà ridisegnata ogni secondo. Si noti che questo timer è necessario perché `OnTimeTick` chiamate `Invalidate` solo una volta al minuto.

In questo esempio include una sola **xamarin_background.png** immagine; tuttavia, è possibile creare un'immagine di sfondo diversi per ogni densità dello schermo che supporterà quadrante dell'orologio personalizzato.

#### <a name="ondraw"></a>OnDraw

Aggiornato **OnDraw** metodo consente di disegnare un quadrante di orologio analogico-style usando la procedura seguente:

1.  Ottiene l'ora corrente, che ora viene mantenuta in una `time` oggetto.

2.  Determina i limiti dell'area di disegno e al relativo centro.

3.  Disegna lo sfondo, adattato il dispositivo quando viene disegnato lo sfondo.

4.  Disegna dodici *segni di graduazione* tutto il quadrante dell'orologio (corrispondente alle ore sul quadrante dell'orologio).

5.  Calcola l'angolo di rotazione e lunghezza per ogni indicatore di espressioni di controllo.

6.  Disegna ogni indicatore nell'area di espressioni di controllo. Si noti che l'icona della mano secondo non viene tracciato se l'espressione di controllo è in modalità ambiente.


#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Questo metodo viene chiamato per informare `MyWatchFaceEngine` sulle proprietà del dispositivo Wear (ad esempio modalità ambiente bit bassa e la protezione burn-in). In `MyWatchFaceEngine`, questo metodo verifica solo la modalità ambiente bit bassa (in modalità ambiente velocità in bit bassa, la schermata supporta un minor numero di bit per ogni colore).

Per altre informazioni su questo metodo, vedere l'Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) documentazione dell'API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Questo metodo viene chiamato quando il dispositivo Wear entra o esce dalla modalità di ambiente. Nel `MyWatchFaceEngine` implementazione, quadrante dell'orologio disabilita l'anti-aliasing quando è in modalità ambiente.

Per altre informazioni su questo metodo, vedere l'Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) documentazione dell'API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Questo metodo viene chiamato ogni volta che l'espressione di controllo diventa visibile o nascosto. In `MyWatchFaceEngine`, questo metodo registra/Annulla la registrazione il ricevitore di fuso orario (descritto di seguito) in base allo stato di visibilità.

Per altre informazioni su questo metodo, vedere l'Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) documentazione dell'API.


### <a name="time-zone-feature"></a>Funzionalità del fuso orario

Il nuovo **MyWatchFaceService.cs** include anche funzionalità per aggiornare l'ora corrente ogni volta che il tempo di zona modifiche (ad esempio durante gli spostamenti tra fusi orari). Verso la fine della **MyWatchFaceService.cs**, una volta modifica zona `BroadcastReceiver` viene definito che gestisce gli oggetti Intent modificato fuso orario:

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

Il `RegisterTimezoneReceiver` e `UnregisterTimezoneReceiver` metodi vengono chiamati dal `OnVisibilityChanged` (metodo).
`UnregisterTimezoneReceiver` viene chiamato quando lo stato di visibilità del quadrante dell'orologio viene modificato in nascosto. Quando quadrante dell'orologio è visibile anche in questo caso `RegisterTimezoneReceiver` viene chiamato (vedere il `OnVisibilityChanged` (metodo)).

Il motore `RegisterTimezoneReceiver` metodo dichiara un gestore per questo fuso orario del ricevitore `Receive` eventi; questo gestore aggiorna il `time` oggetto con la nuova ora ogni volta che viene superato un fuso orario:

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Un filtro intent viene creato e registrato per il ricevitore di fuso orario:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

Il `UnregisterTimezoneReceiver` metodo annulla la registrazione di ricevitore di fuso orario:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Eseguire quadrante dell'orologio migliorata

Compilare e distribuire nuovamente l'app nel dispositivo Wear. Selezionare quadrante dell'orologio dalla selezione watch face come prima. L'anteprima nel selettore di espressioni di controllo viene visualizzato a sinistra e la nuova veste di espressioni di controllo viene visualizzata a destra:

[![Quadrante orologio analogico](creating-a-watchface-images/13-analog-watchface.png "migliorata analogico viso nello strumento di selezione e sul dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

In questo screenshot, l'icona della mano secondo è lo spostamento di una volta al secondo. Quando si esegue questo codice in un dispositivo Wear, della seconda lancetta scompare quando l'espressione di controllo passa alla modalità di ambiente.


## <a name="summary"></a>Riepilogo

In questa procedura dettagliata, un watchface 1.0 Android Wear personalizzato è stato implementato e testato. Il `CanvasWatchFaceService` e `CanvasWatchFaceService.Engine` sono state introdotte classi e i metodi della classe motore essenziali sono stati implementati per creare una semplice digitale watch face. Questa implementazione è stata aggiornata con altre funzionalità per creare un quadrante orologio analogico e metodi aggiuntivi sono stati implementati per gestire le modifiche nella visibilità, modalità di ambiente e le differenze nelle proprietà del dispositivo. Infine, un ricevitore di trasmissioni fuso orario è stato implementato in modo che le espressioni di controllo Aggiorna automaticamente la volta quando viene superato un fuso orario.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di volti Watch](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Esempio WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
