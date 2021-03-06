---
title: Creazione di un'espressione di controllo per Android Wear 1,0
description: Questa guida illustra come implementare un servizio di controllo del viso personalizzato per Android Wear 1,0. Vengono fornite istruzioni dettagliate per la creazione di un servizio di controllo del viso digitale rimovibile, seguito da un altro codice per creare un quadrante di espressione di controllo analogo.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/23/2018
ms.openlocfilehash: 38123e2b1ef20144606bcc77ad33af572aa3707a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030390"
---
# <a name="creating-a-watch-face"></a>Creazione di una watch face

_Questa guida illustra come implementare un servizio di controllo del viso personalizzato per Android Wear 1,0. Vengono fornite istruzioni dettagliate per la creazione di un servizio di controllo del viso digitale rimovibile, seguito da un altro codice per creare un quadrante di espressione di controllo analogo._

## <a name="overview"></a>Panoramica

In questa procedura dettagliata viene creato un servizio di controllo del viso di base per illustrare gli elementi essenziali della creazione di un quadrante Android Wear 1,0 personalizzato.
Il servizio del quadrante dell'orologio iniziale Visualizza un semplice controllo digitale che Visualizza l'ora corrente in ore e minuti:

[![Quadrante di orologio digitale](creating-a-watchface-images/01-initial-face.png "Schermata di esempio del quadrante dell'orologio digitale iniziale")](creating-a-watchface-images/01-initial-face.png#lightbox)

Dopo aver sviluppato e testato il quadrante dell'orologio digitale, viene aggiunto altro codice per aggiornarlo a un'espressione di controllo analoga più sofisticata con tre mani:

[![Quadrante Watch analogico](creating-a-watchface-images/02-example-watchface.png "Schermata di esempio del quadrante dell'orologio analogico finale")](creating-a-watchface-images/02-example-watchface.png#lightbox)

I servizi Watch Face sono aggregati e installati come parte di un'app Wear 1,0. Negli esempi seguenti `MainActivity` non contiene nient'altro del codice del modello di app Wear 1,0, in modo che il servizio Watch Face possa essere incluso nel pacchetto e distribuito allo Smart Watch come parte dell'app. In pratica, questa app fungerà esclusivamente da veicolo per far caricare il servizio Watch Face nel dispositivo Wear 1,0 (o emulatore) per il debug e il test.

## <a name="requirements"></a>Requisiti

Per implementare un servizio di controllo del viso, è necessario quanto segue:

- Android 5,0 (livello API 21) o versione successiva nel dispositivo o nell'emulatore Wear.

- È necessario aggiungere le [librerie di supporto Xamarin Android Wear](https://www.nuget.org/packages/Xamarin.Android.Wear) al progetto Xamarin.Android.

Sebbene Android 5,0 sia il livello API minimo per l'implementazione di un servizio Watch Face, è consigliabile Android 5,1 o versione successiva. I dispositivi Android Wear che eseguono Android 5,1 (API 22) o versioni successive consentono di usare app per il controllo di ciò che viene visualizzato sullo schermo quando il dispositivo è in modalità di *ambiente* a basso consumo. Quando il dispositivo lascia la modalità di *ambiente* a basso consumo, è in modalità *interattiva* . Per altre informazioni su queste modalità, vedere [mantenere visibile l'app](https://developer.android.com/training/wearables/apps/always-on.html).

## <a name="start-an-app-project"></a>Avviare un progetto di app

Creare un nuovo progetto Android Wear 1,0 denominato **WatchFace** (per altre informazioni sulla creazione di nuovi progetti Xamarin.Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Finestra di dialogo nuovo progetto](creating-a-watchface-images/03-wear-project-vs-sml.png "Selezionare Wear app nella finestra di dialogo nuovo progetto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Finestra di dialogo nuovo progetto](creating-a-watchface-images/03-wear-project-xs-sml.png "Selezionare Wear app nella finestra di dialogo nuovo progetto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----

Impostare il nome del pacchetto su `com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Impostazione del nome del pacchetto](creating-a-watchface-images/04-package-name-vs.png "Impostare il nome del pacchetto su com. Xamarin.Watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Impostazione del nome del pacchetto](creating-a-watchface-images/04-package-name-xs.png "Impostare il nome del pacchetto su com. Xamarin.Watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Scorrere inoltre verso il basso e abilitare le autorizzazioni **Internet** e **WAKE_LOCK** :

[![Autorizzazioni necessarie](creating-a-watchface-images/05-required-permissions-vs.png "Abilitare le autorizzazioni INTERNET e WAKE_LOCK")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Impostare la versione minima di Android su **android 5,1 (livello API 22)** .
Abilitare inoltre le autorizzazioni **Internet** e **Wakelock** :

[![Autorizzazioni necessarie](creating-a-watchface-images/05-required-permissions-xs.png "Abilitare le autorizzazioni Internet e WakeLock")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Scaricare quindi [anteprima. png](creating-a-watchface-images/preview.png) &ndash; questa operazione verrà aggiunta alla cartella **drawables** più avanti in questa procedura dettagliata.

## <a name="add-the-xamarinandroid-wear-package"></a>Aggiungere il pacchetto di Xamarin.Android Wear

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Avviare Gestione pacchetti NuGet (in Visual Studio fare clic con il pulsante destro del mouse su **riferimenti** nel **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet...** ). Aggiornare il progetto alla versione stabile più recente di **Xamarin.Android. Wear**:

[![Aggiunta di gestione pacchetti NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "Aggiungere il pacchetto Xamarin.Android. Wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Successivamente, se è installato **Xamarin.Android. support. v13** , disinstallarlo:

[![Rimozione di gestione pacchetti NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Rimuovere Xamarin.support. v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Avviare Gestione pacchetti NuGet (in Visual Studio per Mac, fare clic con il pulsante destro del mouse su **pacchetti** nel **riquadro soluzione** e selezionare **Aggiungi pacchetti...** ). Aggiornare il progetto alla versione stabile più recente di **Xamarin.Android. Wear**:

[![Aggiunta di gestione pacchetti NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "Aggiungere il pacchetto Xamarin.Android. Wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----

Compilare ed eseguire l'app in un dispositivo o in un emulatore Wear (per altre informazioni su come eseguire questa operazione, vedere la Guida [Introduzione](~/android/wear/get-started/index.md) ). Nel dispositivo Wear dovrebbe essere visualizzata la schermata dell'app seguente:

[![Screenshot dell'app](creating-a-watchface-images/08-app-screen.png "Schermata dell'app sul dispositivo Wear")](creating-a-watchface-images/08-app-screen.png#lightbox)

A questo punto, l'App Basic Wear non ha una funzionalità di controllo del viso perché non fornisce ancora un'implementazione del servizio Watch Face. Questo servizio verrà aggiunto successivamente.

## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android Wear implementa i visi di controllo tramite la classe `CanvasWatchFaceService`. `CanvasWatchFaceService` deriva da `WatchFaceService`, che a sua volta deriva da `WallpaperService` come illustrato nel diagramma seguente:

[![Diagramma di ereditarietà](creating-a-watchface-images/09-inheritance-diagram-sml.png "Diagramma di ereditarietà di CanvasWatchFaceService")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` include un `CanvasWatchFaceService.Engine` annidato; Crea un'istanza di un oggetto `CanvasWatchFaceService.Engine` che esegue il lavoro effettivo di disegno del quadrante dell'orologio. `CanvasWatchFaceService.Engine` deriva da `WallpaperService.Engine`, come illustrato nel diagramma precedente.

Non illustrato in questo diagramma è un `Canvas` che `CanvasWatchFaceService` USA per disegnare il quadrante dell'orologio &ndash; questa `Canvas` viene passata tramite il metodo `OnDraw` come descritto di seguito.

Nelle sezioni seguenti viene creato un servizio di controllo del viso personalizzato attenendosi alla procedura seguente:

1. Definire una classe denominata `MyWatchFaceService` derivata da `CanvasWatchFaceService`.

2. All'interno `MyWatchFaceService` creare una classe annidata denominata `MyWatchFaceEngine` derivata da `CanvasWatchFaceService.Engine`.

3. In `MyWatchFaceService` implementare un metodo `CreateEngine` che crea un'istanza `MyWatchFaceEngine` e la restituisce.

4. In `MyWatchFaceEngine` implementare il metodo `OnCreate` per creare lo stile del quadrante dell'espressione di controllo ed eseguire altre attività di inizializzazione.

5. Implementare il metodo di `OnDraw` di `MyWatchFaceEngine`. Questo metodo viene chiamato ogni volta che è necessario ricreare il quadrante dell'orologio (ovvero *invalidato*). `OnDraw` è il metodo che disegna (e ridisegna) gli elementi del quadrante Watch, ad esempio hour, minute e second hands.

6. Implementare il metodo di `OnTimeTick` di `MyWatchFaceEngine`.
    `OnTimeTick` viene chiamato almeno una volta al minuto (in modalità ambiente e interattivo) o quando la data/ora è cambiata.

Per ulteriori informazioni su `CanvasWatchFaceService`, vedere la documentazione relativa all'API [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) Android.
Analogamente, [CanvasWatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) illustra l'implementazione effettiva del quadrante dell'orologio.

### <a name="add-the-canvaswatchfaceservice"></a>Aggiungere CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Aggiungere un nuovo file denominato **MyWatchFaceService.cs** (in Visual Studio, fare clic con il pulsante destro del mouse su **WatchFace** nella **Esplora soluzioni**, fare clic su **Aggiungi > nuovo elemento**e selezionare **classe**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Aggiungere un nuovo file denominato **MyWatchFaceService.cs** (in Visual Studio per Mac, fare clic con il pulsante destro del mouse sul progetto **WatchFace** , scegliere **Aggiungi > nuovo file**e selezionare **classe vuota**).

-----

Sostituire il contenuto del file con il codice seguente:

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

`MyWatchFaceService` (derivato da `CanvasWatchFaceService`) è il "programma principale" del quadrante dell'orologio. `MyWatchFaceService` implementa un solo metodo, `OnCreateEngine`, che crea un'istanza di e restituisce un oggetto `MyWatchFaceEngine` (`MyWatchFaceEngine` deriva da `CanvasWatchFaceService.Engine`). L'oggetto `MyWatchFaceEngine` di cui è stata creata un'istanza deve essere restituito come `WallpaperService.Engine`. L'oggetto incapsulamento `MyWatchFaceService` viene passato al costruttore.

`MyWatchFaceEngine` è l'implementazione effettiva del quadrante dell'orologio &ndash; contiene il codice che disegna il quadrante dell'orologio. Gestisce anche gli eventi di sistema, ad esempio le modifiche dello schermo (modalità ambiente/interattiva, spegnimento dello schermo e così via).

### <a name="implement-the-engine-oncreate-method"></a>Implementare il metodo OnCreate del motore

Il metodo `OnCreate` Inizializza il quadrante dell'orologio. Aggiungere il campo seguente a `MyWatchFaceEngine`:

```csharp
Paint hoursPaint;
```

Questo `Paint` oggetto verrà usato per creare l'ora corrente sul quadrante dell'orologio. Aggiungere quindi il metodo seguente per `MyWatchFaceEngine`:

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

`OnCreate` viene chiamato poco dopo l'avvio di `MyWatchFaceEngine`. Configura il `WatchFaceStyle` (che controlla il modo in cui il dispositivo Wear interagisce con l'utente) e crea un'istanza dell'oggetto `Paint` che verrà usato per visualizzare l'ora.

La chiamata a `SetWatchFaceStyle` esegue le operazioni seguenti:

1. Imposta la *modalità* di visualizzazione su `PeekModeShort`, che fa sì che le notifiche vengano visualizzate come piccole schede "Peek" sullo schermo.

2. Imposta la visibilità dello sfondo su `Interruptive`, in modo da visualizzare lo sfondo di una scheda di anteprima solo brevemente se rappresenta una notifica di interruzione.

3. Disabilita l'ora dell'interfaccia utente di sistema predefinita da disegnare sul quadrante dell'orologio, in modo che il quadrante dell'orologio personalizzato possa visualizzare l'ora.

Per ulteriori informazioni su queste e altre opzioni relative allo stile del quadrante, vedere la documentazione dell'API Android [WatchFaceStyle. Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) .

Al termine dell'`SetWatchFaceStyle`, `OnCreate` crea un'istanza dell'oggetto `Paint` (`hoursPaint`) e ne imposta il colore su bianco e le dimensioni del testo su 48 pixel ([TEXTSIZE](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) deve essere specificato in pixel).

### <a name="implement-the-engine-ondraw-method"></a>Implementare il metodo onpare del motore

Il metodo `OnDraw` è probabilmente il metodo di `CanvasWatchFaceService.Engine` più importante &ndash; è il metodo che disegna effettivamente gli elementi del quadrante di osservazione, ad esempio le cifre e le lancette dell'orologio.
Nell'esempio seguente viene disegnata una stringa di tempo sul quadrante dell'orologio.
Aggiungere il metodo seguente a `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

Quando Android chiama `OnDraw`, viene passata un'istanza `Canvas` e i limiti in cui è possibile disegnare la superficie. Nell'esempio di codice precedente, `DateTime` viene usato per calcolare l'ora corrente in ore e minuti (in formato a 12 ore). La stringa temporale risultante viene disegnata nell'area di disegno usando il metodo `Canvas.DrawText`. La stringa verrà visualizzata 70 pixel sul bordo sinistro e 80 pixel dal bordo superiore.

Per ulteriori informazioni sul metodo `OnDraw`, vedere la documentazione relativa all'API [Onpare](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) di Android.

### <a name="implement-the-engine-ontimetick-method"></a>Implementare il metodo OnTimeTick del motore

Android chiama periodicamente il metodo `OnTimeTick` per aggiornare l'ora visualizzata dal quadrante dell'orologio. Viene chiamato almeno una volta al minuto (sia in modalità ambiente che interattiva) oppure quando la data/ora o il fuso orario è stato modificato. Aggiungere il metodo seguente a `MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Questa implementazione di `OnTimeTick` chiama semplicemente `Invalidate`. Il metodo `Invalidate` Pianifica `OnDraw` per ricreare il quadrante dell'orologio.

Per ulteriori informazioni sul metodo `OnTimeTick`, vedere la documentazione dell'API [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) di Android.

## <a name="register-the-canvaswatchfaceservice"></a>Registrare il CanvasWatchFaceService

`MyWatchFaceService` deve essere registrato in **file AndroidManifest. XML** dell'app Wear associata. A tale scopo, aggiungere il codice XML seguente alla sezione `<application>`:

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

1. Imposta l'autorizzazione `android.permission.BIND_WALLPAPER`. Questa autorizzazione concede al servizio di controllo dell'espressione l'autorizzazione per modificare lo sfondo del sistema nel dispositivo. Si noti che questa autorizzazione deve essere impostata nella sezione `<service>` invece che nella sezione `<application>` esterna.

2. Definisce una risorsa `watch_face`. Questa risorsa è un file XML breve che dichiara una risorsa `wallpaper` (questo file verrà creato nella sezione successiva).

3. Dichiara un'immagine disegnatore denominata `preview` che verrà visualizzata dalla schermata di selezione della selezione del controllo.

4. Include un `intent-filter` per informare Android che `MyWatchFaceService` visualizzerà il quadrante dell'orologio.

Che completa il codice per l'esempio di `WatchFace` di base. Il passaggio successivo consiste nell'aggiungere le risorse necessarie.

## <a name="add-resource-files"></a>Aggiungi file di risorse

Prima di poter eseguire il servizio Watch, è necessario aggiungere la risorsa **watch_face** e l'immagine di anteprima. Per prima cosa, creare un nuovo file XML in **Resources/XML/watch_face. XML** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Impostare l'azione di compilazione del file su **AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Azione di compilazione](creating-a-watchface-images/10-android-resource-vs.png "Impostare l'azione di compilazione su AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Azione di compilazione](creating-a-watchface-images/10-android-resource-xs.png "Impostare l'azione di compilazione su AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Questo file di risorse definisce un elemento di `wallpaper` semplice che verrà usato per il quadrante dell'orologio.

Se non è ancora stato fatto, scaricare [Preview. png](creating-a-watchface-images/preview.png).
Installarlo in **Resources/disegnator/Preview. png**. Assicurarsi di aggiungere questo file al progetto `WatchFace`. Questa immagine di anteprima viene visualizzata all'utente nella selezione del quadrante dell'orologio sul dispositivo Wear. Per creare un'immagine di anteprima per il proprio quadrante, è possibile scattare una schermata del quadrante dell'orologio mentre è in esecuzione. Per altre informazioni su come ottenere schermate dai dispositivi Wear, vedere [acquisizione di schermate](~/android/wear/deploy-test/debug-on-device.md#screenshots).

## <a name="try-it"></a>Prova!

Compilare e distribuire l'app nel dispositivo Wear. Verrà visualizzata la schermata Wear app come prima. Per abilitare la nuova espressione di controllo, eseguire le operazioni seguenti:

1. Scorrere verso destra fino a visualizzare lo sfondo della schermata espressioni di controllo.

2. Toccare e mantenere in qualsiasi punto lo sfondo dello schermo per due secondi.

3. Scorrere rapidamente da sinistra verso destra per esplorare i vari visi.

4. Selezionare l'espressione di controllo di **esempio Xamarin** (visualizzata a destra):

    [![Selezione Watchface](creating-a-watchface-images/11-watchface-picker.png "Scorrere rapidamente per individuare il quadrante dell'orologio di esempio Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5. Toccare l'espressione di controllo di **esempio Xamarin** per selezionarla.

In questo modo il quadrante dell'orologio del dispositivo Wear viene modificato per l'uso del servizio di controllo del viso personalizzato implementato fino a questo punto:

[![Quadrante di orologio digitale](creating-a-watchface-images/12-digital-watchface.png "Controllo digitale personalizzato in esecuzione sul dispositivo Wear")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Si tratta di un aspetto relativamente grezzo perché l'implementazione dell'app è così minima (ad esempio, non include uno sfondo del quadrante dell'orologio e non chiama `Paint` metodi anti-alias per migliorare l'aspetto).
Tuttavia, implementa la funzionalità bare-Bones necessaria per creare un quadrante di controllo personalizzato.

Nella sezione successiva questa espressione di controllo verrà aggiornata a un'implementazione più sofisticata.

## <a name="upgrading-the-watch-face"></a>Aggiornamento del quadrante dell'orologio

Nella parte restante di questa procedura dettagliata, `MyWatchFaceService` viene aggiornato per visualizzare un quadrante di orologio analogo ed è esteso per supportare più funzionalità. Verranno aggiunte le funzionalità seguenti per creare il quadrante dell'orologio aggiornato:

1. Indica l'ora con l'ora, il minuto e il secondo lancette analoghi.

2. Reagisce alle modifiche della visibilità.

3. Risponde alle modifiche tra la modalità ambiente e la modalità interattiva.

4. Legge le proprietà del dispositivo di usura sottostante.

5. Aggiorna automaticamente l'ora in cui viene apportata una modifica del fuso orario.

Prima di implementare le modifiche al codice seguenti, scaricare il file con [estensione zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), decomprimerlo e spostare i file con estensione png decompressi in **Resources/disegnator** (sovrascrivere il file **Preview. png**precedente). Aggiungere i nuovi file con estensione png al progetto `WatchFace`.

### <a name="update-engine-features"></a>Funzionalità del motore di aggiornamento

Il passaggio successivo consiste nell'aggiornamento di **MyWatchFaceService.cs** a un'implementazione di che disegna un quadrante analogo e supporta le nuove funzionalità. Sostituire il contenuto di **MyWatchFaceService.cs** con la versione analoga del codice dell'espressione di controllo in [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) . è possibile tagliare e incollare questa origine nel **MyWatchFaceService.cs**esistente.

Questa versione di **MyWatchFaceService.cs** aggiunge altro codice ai metodi esistenti e include ulteriori metodi sottoposti a override per aggiungere altre funzionalità. Le sezioni seguenti forniscono una presentazione guidata del codice sorgente.

#### <a name="oncreate"></a>OnCreate

Il metodo **Oncreat** aggiornato configura lo stile del quadrante dell'espressione di controllo come prima, ma include alcuni passaggi aggiuntivi:

1. Imposta l'immagine di sfondo sulla risorsa **xamarin_background** che risiede in **Resources/drawable-HDPI/xamarin_background. png**.

2. Inizializza `Paint` oggetti per disegnare la lancetta dell'ora, la lancetta dei minuti e la seconda mano.

3. Inizializza un oggetto `Paint` per disegnare i segni di ora intorno al bordo del quadrante dell'orologio.

4. Crea un timer che chiama il metodo `Invalidate` (ridisegnato) in modo che la seconda mano venga ridisegnato ogni secondo. Si noti che questo timer è necessario perché `OnTimeTick` chiama `Invalidate` una sola volta ogni minuto.

Questo esempio include solo un'immagine **xamarin_background. png** ; Tuttavia, potrebbe essere necessario creare un'immagine di sfondo diversa per ogni densità dello schermo supportata dal quadrante dell'orologio personalizzato.

#### <a name="ondraw"></a>OnDraw

Il metodo **OnDraw** aggiornato disegna un tipo di espressione di controllo analogo attenendosi alla procedura seguente:

1. Ottiene l'ora corrente, ora gestita in un oggetto `time`.

2. Determina i limiti della superficie di disegno e del relativo centro.

3. Disegna lo sfondo, ridimensionato per adattarsi al dispositivo quando viene disegnato lo sfondo.

4. Disegna dodici *tick* intorno al quadrante dell'orologio, corrispondente alle ore sul quadrante dell'orologio.

5. Calcola l'angolo, la rotazione e la lunghezza per ogni mano del controllo.

6. Disegna ogni mano sull'area di controllo. Si noti che la seconda mano non viene disegnata se l'espressione di controllo è in modalità ambiente.

#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Questo metodo viene chiamato per informare `MyWatchFaceEngine` sulle proprietà del dispositivo Wear, ad esempio la modalità ambiente a basso bit e la protezione dell'ustione. In `MyWatchFaceEngine`, questo metodo verifica solo la modalità di ambiente a basso bit (in modalità ambiente a basso bit, la schermata supporta un minor numero di bit per ogni colore).

Per altre informazioni su questo metodo, vedere la documentazione dell'API [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) di Android.

#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Questo metodo viene chiamato quando il dispositivo Wear entra o esce dalla modalità ambiente. Nell'implementazione di `MyWatchFaceEngine`, il quadrante dell'orologio Disabilita l'anti-aliasing quando è in modalità ambiente.

Per altre informazioni su questo metodo, vedere la documentazione dell'API [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) di Android.

#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Questo metodo viene chiamato ogni volta che l'espressione di controllo diventa visibile o nascosta. In `MyWatchFaceEngine`, questo metodo registra/Annulla la registrazione del ricevitore del fuso orario (descritto di seguito) in base allo stato di visibilità.

Per altre informazioni su questo metodo, vedere la documentazione dell'API [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) di Android.

### <a name="time-zone-feature"></a>Funzionalità del fuso orario

Il nuovo **MyWatchFaceService.cs** include anche la funzionalità per aggiornare l'ora corrente ogni volta che il fuso orario viene modificato (ad esempio durante il viaggio tra fusi orari). Alla fine di **MyWatchFaceService.cs**, viene definita una modifica del fuso orario `BroadcastReceiver` che gestisce gli oggetti Intent modificati dal fuso orario:

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

I metodi `RegisterTimezoneReceiver` e `UnregisterTimezoneReceiver` vengono chiamati dal metodo `OnVisibilityChanged`.
`UnregisterTimezoneReceiver` viene chiamato quando lo stato di visibilità del quadrante dell'orologio viene modificato in nascosto. Quando il quadrante dell'orologio è nuovamente visibile, viene chiamato `RegisterTimezoneReceiver` (vedere il metodo `OnVisibilityChanged`).

Il metodo `RegisterTimezoneReceiver` del motore dichiara un gestore per l'evento `Receive` del ricevitore del fuso orario; Questo gestore aggiorna l'oggetto `time` con la nuova ora ogni volta che viene attraversato un fuso orario:

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Viene creato e registrato un filtro preventivo per il ricevitore del fuso orario:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

Il metodo `UnregisterTimezoneReceiver` Annulla la registrazione del ricevitore del fuso orario:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Esegui il quadrante dell'orologio migliorato

Compilare e distribuire di nuovo l'app nel dispositivo Wear. Selezionare il quadrante dell'orologio dalla selezione del quadrante dell'orologio come prima. L'anteprima nella selezione espressioni di controllo è visualizzata a sinistra e il nuovo quadrante è visualizzato a destra:

[![Quadrante Watch analogico](creating-a-watchface-images/13-analog-watchface.png "Miglioramento della faccia analoga in selezione e nel dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

In questa schermata la seconda mano si muove una volta al secondo. Quando si esegue questo codice in un dispositivo Wear, la seconda parte scompare quando l'espressione di controllo entra in modalità ambiente.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato implementato e testato un Watchface Android Wear 1,0 personalizzato. Sono state introdotte le classi `CanvasWatchFaceService` e `CanvasWatchFaceService.Engine` e i metodi essenziali della classe Engine sono stati implementati per creare un semplice quadrante digitale. Questa implementazione è stata aggiornata con altre funzionalità per la creazione di un quadrante analogo ed è stata implementata una maggiore quantità di metodi per gestire le modifiche alla visibilità, alla modalità ambiente e alle differenze nelle proprietà del dispositivo. Infine, è stato implementato un ricevitore di broadcast di fuso orario in modo che l'espressione di controllo aggiorni automaticamente l'ora in cui viene attraversato un fuso orario.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di espressioni di controllo](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Esempio WatchFace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)
- [WatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
