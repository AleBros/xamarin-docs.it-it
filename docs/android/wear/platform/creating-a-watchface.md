---
title: Creazione di un tipo di carattere di controllo
description: Questa guida illustra come implementare un servizio faccia espressioni di controllo personalizzato per Android con accenti. Vengono fornite istruzioni dettagliate per la creazione di un' servizio faccia digitale espressioni di controllo, seguito da altro codice per creare un quadrante di orologio analogico stile.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c02755cc3ff5b46a5a97b6c14185794d8ad538d8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772939"
---
# <a name="creating-a-watch-face"></a>Creazione di un tipo di carattere di controllo

_Questa guida illustra come implementare un servizio faccia espressioni di controllo personalizzato per Android con accenti. Vengono fornite istruzioni dettagliate per la creazione di un' servizio faccia digitale espressioni di controllo, seguito da altro codice per creare un quadrante di orologio analogico stile._

## <a name="overview"></a>Panoramica 

In questa procedura dettagliata viene creato un servizio di tipo di carattere di base espressioni di controllo per illustrare gli aspetti principali di creazione di un'immagine personalizzata, usare Android di espressioni di controllo. Il servizio di tipo di carattere iniziale di espressioni di controllo consente di visualizzare un'espressione di controllo digitale semplice che visualizza l'ora corrente in ore e minuti: 

[![Quadrante watch digitale](creating-a-watchface-images/01-initial-face.png "schermata di esempio del tipo di carattere iniziale di espressioni di controllo digitale")](creating-a-watchface-images/01-initial-face.png#lightbox)

Dopo questa faccia digitale espressioni di controllo è sviluppata e testata, viene aggiunto altro codice per l'aggiornamento a una più sofisticate quadrante di orologio analogico con tre mani: 

[![Quadrante orologio analogico](creating-a-watchface-images/02-example-watchface.png "schermata di esempio del tipo di carattere finale analogico espressioni di controllo")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Espressioni di controllo faccia servizi sono inclusi e installati come parte di un'app di scadenza. Negli esempi seguenti, `MainActivity` contiene semplicemente il codice del modello di app usura in modo che il servizio faccia espressioni di controllo può essere incluso nel pacchetto e le espressioni di controllo smart distribuito come parte dell'applicazione. In effetti, questa app verrà utilizzato esclusivamente come mezzo per ottenere il servizio di controllo faccia caricato il dispositivo usura (o l'emulatore) per il debug e test. 

## <a name="requirements"></a>Requisiti

Per implementare un servizio faccia espressioni di controllo, è necessario quanto segue:

-   Android 5.0 (livello API 21) o versione successiva in un emulatore o il dispositivo usura.

-   Il [Xamarin Android accenti supporto librerie](https://www.nuget.org/packages/Xamarin.Android.Wear) deve essere aggiunto al progetto xamarin. 

Sebbene Android 5.0 è l'API minimo livello per implementare un servizio di controllo faccia, Android 5.1 o versioni successive è consigliabile. Android, usare i dispositivi che eseguono Android 5.1 (API 22) o versione successiva consentono App usura controllare cosa viene visualizzata sullo schermo mentre il dispositivo è in modalità basso consumo *ambiente* modalità. Quando il dispositivo lascia la modalità basso consumo *ambiente* modalità, è in *interattivo* modalità. Per altre informazioni su queste modalità, vedere [mantenendo Your App visibile](https://developer.android.com/training/wearables/apps/always-on.html). 


## <a name="start-an-app-project"></a>Avviare un progetto di App

Creare un nuovo progetto, usare Android denominato **WatchFace** (per ulteriori informazioni sulla creazione di nuovi progetti di xamarin, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Finestra di dialogo Nuovo progetto](creating-a-watchface-images/03-wear-project-vs-sml.png "selezionare accenti App nella finestra di dialogo Nuovo progetto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Finestra di dialogo Nuovo progetto](creating-a-watchface-images/03-wear-project-xs-sml.png "selezionare accenti App nella finestra di dialogo Nuovo progetto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Impostare il nome del pacchetto `com.xamarin.watchface`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Impostazione del nome del pacchetto](creating-a-watchface-images/04-package-name-vs.png "impostare il nome del pacchetto com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Impostazione del nome del pacchetto](creating-a-watchface-images/04-package-name-xs.png "impostare il nome del pacchetto com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Inoltre, scorrere verso il basso e abilitare il **INTERNET** e **WAKE_LOCK** autorizzazioni: 

[![Autorizzazioni necessarie per](creating-a-watchface-images/05-required-permissions-vs.png "abilitare INTERNET e WAKE_LOCK autorizzazioni")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Impostare la versione minima di Android **Android 5.1 (livello API 22)**. Inoltre, abilitare il **Internet** e **WakeLock** autorizzazioni:

[![Autorizzazioni necessarie per](creating-a-watchface-images/05-required-permissions-xs.png "abilitare Internet e WakeLock autorizzazioni")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Successivamente, scaricare [preview.png](creating-a-watchface-images/preview.png) &ndash; questo verrà aggiunto al **drawables** cartella più avanti in questa procedura dettagliata.


## <a name="add-the-xamarin-android-wear-package"></a>Aggiungere il pacchetto di usura Xamarin Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Avviare Gestione pacchetti NuGet (in Visual Studio, fare doppio clic su **riferimenti** nel **Esplora** e selezionare **Gestisci pacchetti NuGet...** ). Aggiornare il progetto per l'ultima versione stabile di **Xamarin.Android.Wear**: 

[![Aggiungere pacchetti NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "aggiungere il pacchetto Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Successivamente, se **Xamarin.Android.Support.v13** è installato, disinstallarlo:

[![Rimozione di gestione pacchetti NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Xamarin.Support.v13 rimuovere")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Avviare Gestione pacchetti NuGet (in Visual Studio per Mac, fare doppio clic su **pacchetti** nel **riquadro soluzione** e selezionare **Aggiungi pacchetti** ). Aggiornare il progetto per l'ultima versione stabile di **Xamarin.Android.Wear**: 

[![Aggiungere pacchetti NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "aggiungere il pacchetto Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Compilare ed eseguire l'app in un emulatore o il dispositivo usura (per ulteriori informazioni su come eseguire questa operazione, vedere il [Introduzione](~/android/wear/get-started/index.md) Guida). La seguente schermata dell'app verrà visualizzato nel dispositivo usura:

[![Schermata di app](creating-a-watchface-images/08-app-screen.png "schermata dell'App sul dispositivo usura")](creating-a-watchface-images/08-app-screen.png#lightbox)

A questo punto, l'app usura base non dispone di funzionalità faccia espressioni di controllo perché non fornisce ancora un'implementazione del servizio faccia espressioni di controllo. Questo servizio verrà aggiunti successivamente. 

 
## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Implementa usura Android Guarda i caratteri tipografici tramite la `CanvasWatchFaceService` classe. `CanvasWatchFaceService` derivato da `WatchFaceService`, che a sua volta è derivato da `WallpaperService` come illustrato nel diagramma seguente: 

[![Diagramma di ereditarietà](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService diagramma di ereditarietà")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` include un tipo annidato `CanvasWatchFaceService.Engine`; crea un'istanza di un `CanvasWatchFaceService.Engine` oggetto che esegue il lavoro effettivo di disegno il quadrante dell'orologio. `CanvasWatchFaceService.Engine` derivato da `WallpaperService.Engine` come illustrato nel diagramma sopra riportato. 

Non è stato illustrato in questo diagramma è un `Canvas` che `CanvasWatchFaceService` viene utilizzato per disegnare il quadrante dell'orologio &ndash; questo `Canvas` passato tramite la `OnDraw` metodo come descritto di seguito. 

Nelle sezioni seguenti, verrà creato un servizio faccia espressioni di controllo personalizzato eseguendo la procedura seguente: 

1.  Definire una classe denominata `MyWatchFaceService` che deriva da `CanvasWatchFaceService`. 

2.  All'interno di `MyWatchFaceService`, creare una classe annidata denominata `MyWatchFaceEngine` che deriva da `CanvasWatchFaceService.Engine`. 

3.  In `MyWatchFaceService`, implementare un `CreateEngine` metodo che crea un'istanza `MyWatchFaceEngine` e lo restituisce.

4.  In `MyWatchFaceEngine`, implementare il `OnCreate` metodo per creare lo stile del carattere espressioni di controllo ed eseguire altre attività di inizializzazione. 

5.  Implementare il `OnDraw` metodo `MyWatchFaceEngine`. Questo metodo viene chiamato ogni volta che deve essere ridisegnato il quadrante dell'orologio (ad esempio *invalidati*). `OnDraw` il metodo che consente di disegnare elementi faccia espressioni di controllo, ad esempio ora, minuto e secondo mani (e ridisegna). 

6.  Implementare il `OnTimeTick` metodo `MyWatchFaceEngine`. 
    `OnTimeTick` viene chiamato almeno una volta per ogni minuto (in modalità interattiva sia ambiente) o quando la data/ora è stato modificato. 

Per ulteriori informazioni su `CanvasWatchFaceService`, vedere il Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) documentazione dell'API.
Analogamente, [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) viene illustrata l'implementazione effettiva del tipo di carattere espressioni di controllo.


### <a name="add-the-canvaswatchfaceservice"></a>Aggiungere il CanvasWatchFaceService

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Aggiungere un nuovo file denominato **MyWatchFaceService.cs** (in Visual Studio, fare doppio clic su **WatchFace** nel **Esplora**, fare clic su **Aggiungi > Nuovo elemento...** e selezionare **classe**).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Aggiungere un nuovo file denominato **MyWatchFaceService.cs** (in Visual Studio per Mac, fare doppio clic su di **WatchFace** del progetto, fare clic su **Aggiungi > Nuovo File...** e selezionare **classe vuota**). 

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

`MyWatchFaceService` (derivato da `CanvasWatchFaceService`) è il programma"principale" del tipo di carattere espressioni di controllo. `MyWatchFaceService` implementa un solo metodo, `OnCreateEngine`, che crea e restituisce un `MyWatchFaceEngine` oggetto (`MyWatchFaceEngine` è derivato da `CanvasWatchFaceService.Engine`). Istanziata `MyWatchFaceEngine` oggetto deve essere restituito come un `WallpaperService.Engine`. L'incapsulamento `MyWatchFaceService` oggetto viene passato al costruttore. 

`MyWatchFaceEngine` è l'implementazione di espressioni di controllo effettivo faccia &ndash; contiene il codice che disegna la faccia di espressioni di controllo. Gestisce anche gli eventi di sistema, ad esempio modifiche dello schermo (modalità ambiente interattivi, schermata la disattivazione e così via). 

 
### <a name="implement-the-engine-oncreate-method"></a>Implementare il metodo OnCreate motore

Il `OnCreate` metodo inizializza il quadrante dell'orologio. Aggiungere il seguente campo `MyWatchFaceEngine`: 

```csharp
Paint hoursPaint;
```

Questo `Paint` oggetto verrà utilizzato per disegnare l'ora corrente sul quadrante dell'espressione di controllo. Successivamente, aggiungere il metodo seguente alla `MyWatchFaceEngine`: 

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

`OnCreate` viene chiamato subito dopo `MyWatchFaceEngine` viene avviato. Imposta il `WatchFaceStyle` (che controlla come il dispositivo usura interagisce con l'utente) e crea un'istanza di `Paint` oggetto utilizzato per visualizzare l'ora. 

La chiamata a `SetWatchFaceStyle` esegue le operazioni seguenti:

1.  Set *modalità anteprima* a `PeekModeShort`, che fa sì che le notifiche vengono visualizzati come schede "anteprima" piccolo sullo schermo. 

2.  Imposta la visibilità di sfondo `Interruptive`, che comporta lo sfondo di una scheda di anteprima da visualizzare solo brevemente se rappresenta una notifica di interruzione.

3.  Disabilita l'ora dell'interfaccia utente di sistema predefinito da disegnare sul quadrante dell'espressione di controllo in modo che il tipo di carattere del controllo personalizzato è possibile visualizzare l'ora invece.

Per ulteriori informazioni su queste e altre opzioni di stile faccia espressioni di controllo, vedere il Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) documentazione dell'API.

Dopo aver `SetWatchFaceStyle` viene completato, `OnCreate` crea un'istanza di `Paint` oggetto (`hoursPaint`) e imposta il colore bianco e le dimensioni di testo a 48 pixel ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) deve essere specificata in pixel). 


### <a name="implement-the-engine-ondraw-method"></a>Implementare il metodo OnDraw motore

Il `OnDraw` metodo è probabilmente il più importante `CanvasWatchFaceService.Engine` metodo &ndash; è il metodo che effettivamente Guarda gli elementi di tipo di carattere, ad esempio cifre e disegna viso lancette. Nell'esempio seguente disegna una stringa ora sul quadrante dell'espressione di controllo.
Aggiungere il seguente metodo alla `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str, 
        (float)(frame.Left + 70), 
        (float)(frame.Top  + 80), hoursPaint);
}
```

Quando chiama il metodo Android `OnDraw`, passa un `Canvas` istanza e i limiti in cui è possibile disegnare l'immagine. Nell'esempio di codice precedente, `DateTime` viene usato per calcolare l'ora corrente in ore e minuti (in formato 12 ore). La stringa di ora risultante viene disegnata nell'area di lavoro utilizzando il `Canvas.DrawText` metodo. La stringa verrà visualizzata 70 pixel su 80 pixel dal bordo sinistro verso il basso dalla parte superiore. 

Per ulteriori informazioni sul `OnDraw` metodo, vedere il Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html#onDraw(android.graphics.Canvas, android.graphics.Rect)) documentazione dell'API.


### <a name="implement-the-engine-ontimetick-method"></a>Implementare il metodo OnTimeTick motore

Android chiama periodicamente il `OnTimeTick` metodo per aggiornare l'ora visualizzata per il tipo di carattere di controllo. Viene chiamato in almeno una volta al minuto (in modalità interattiva e ambiente), o quando la data/ora o un fuso orario sono stati modificati. Aggiungere il seguente metodo alla `MyWatchFaceEngine`: 

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Questa implementazione di `OnTimeTick` chiama semplicemente `Invalidate`. Il `Invalidate` pianificazioni metodo `OnDraw` per ridisegnare il quadrante dell'orologio. 

Per ulteriori informazioni sul `OnTimeTick` metodo, vedere il Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) documentazione dell'API.

 
## <a name="register-the-canvaswatchfaceservice"></a>Registrare il CanvasWatchFaceService

`MyWatchFaceService` deve essere registrato nel **AndroidManifest.xml** dell'app usura associato. A tale scopo, aggiungere il seguente codice XML per il `<application>` sezione: 

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

1.  Imposta il `android.permission.BIND_WALLPAPER` autorizzazione. Questa autorizzazione concede l'autorizzazione di servizio faccia espressioni di controllo per modificare lo sfondo del sistema nel dispositivo. Si noti che questa autorizzazione deve essere impostata il `<service>` sezione piuttosto che in esterna `<application>` sezione. 

2.  Definisce un `watch_face` risorse. Questa risorsa è un file XML breve che dichiara un `wallpaper` risorsa (questo file verrà creato nella sezione successiva). 

3.  Dichiara un'immagine drawable denominata `preview` che verranno visualizzate la schermata di selezione di selezione di espressioni di controllo. 

4.  Include un `intent-filter` per informare Android che `MyWatchFaceSevice` visualizzazione di un tipo di carattere di controllo. 

È stata completata il codice per gli elementi di base `WatchFace` esempio. Il passaggio successivo consiste nell'aggiungere le risorse necessarie.

 
## <a name="add-resource-files"></a>Aggiungere i file di risorse

Prima di poter eseguire il servizio di controllo, è necessario aggiungere il **watch_face** risorsa e l'immagine di anteprima. Innanzitutto, creare un nuovo file XML in **Resources/xml/watch_face.xml** e sostituirne il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Impostare l'azione di compilazione di questo file **AndroidResource**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Azione di compilazione](creating-a-watchface-images/10-android-resource-vs.png "Set compila azione AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Azione di compilazione](creating-a-watchface-images/10-android-resource-xs.png "Set compila azione AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Questo file di risorse definisce un semplice `wallpaper` elemento che verrà utilizzato per il tipo di carattere di controllo. 

Se non è ancora stato fatto, scaricare [preview.png](creating-a-watchface-images/preview.png). Installare in **Resources/drawable/preview.png**. Assicurarsi di aggiungere questo file per il `WatchFace` progetto. Questa immagine di anteprima viene visualizzata all'utente nella selezione del dispositivo usura faccia espressioni di controllo. Per creare un'immagine di anteprima per la propria quadrante dell'orologio, è possibile acquisire uno screenshot del quadrante dell'espressione di controllo durante l'esecuzione. (Per ulteriori informazioni sul recupero schermate da usura dispositivi, vedere [screenshot](~/android/wear/deploy-test/debug-on-device.md#screenshots)). 


## <a name="try-it"></a>Prova

Compilare e distribuire l'app al dispositivo usura. Visualizzata la schermata app usura vengono visualizzati come in precedenza. Eseguire le operazioni seguenti per abilitare il nuovo stile di espressioni di controllo: 

1.  Scorrere verso destra fino a quando non viene visualizzato lo sfondo della finestra Espressioni di controllo. 

2.  Toccare e tenere in un punto qualsiasi dello sfondo della schermata per due secondi.

3.  Scorrere rapidamente da sinistra a destra per esplorare le varie facce di espressioni di controllo. 

4.  Selezionare il **esempio Xamarin** guardare faccia (illustrato a destra): 

    [![Selezione Watchface](creating-a-watchface-images/11-watchface-picker.png "scorrere per individuare quadrante watch esempio Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Toccare il **esempio Xamarin** guardare faccia per selezionarlo. 

Questa operazione modificherà il quadrante dell'orologio del dispositivo per utilizzare il servizio faccia espressioni di controllo personalizzati implementato usura: 

[![Quadrante watch digitale](creating-a-watchface-images/12-digital-watchface.png "espressioni di digitale controllo personalizzato in esecuzione sul dispositivo usura")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Si tratta di un tipo di carattere relativamente essenziale è costituita dall'espressione di controllo perché l'implementazione di app non è pertanto minimo (ad esempio, non include uno sfondo faccia espressioni di controllo e questo non chiamare `Paint` metodi di anti-alias per migliorare l'aspetto). Tuttavia, implementa la funzionalità di base che è necessaria creare un tipo di carattere del controllo personalizzato. 

Nella sezione successiva, verrà aggiornato il quadrante dell'orologio in un'implementazione più sofisticata. 

 
## <a name="upgrading-the-watch-face"></a>L'aggiornamento il quadrante dell'orologio

Nella parte restante di questa procedura dettagliata, `MyWatchFaceService` viene aggiornato per visualizzare un quadrante di orologio analogico stile ed è esteso per supportare altre funzionalità. Per creare il tipo di carattere di controllo aggiornato verranno aggiunte le funzionalità seguenti: 

1.  Indica l'ora con analogico ora, minuto e secondo mani.

2.  Visibilità di risposta alle modifiche.

3.  Risposta alle modifiche tra la modalità di ambiente e la modalità interattiva. 

4.  Legge le proprietà del dispositivo usura sottostante.

5.  Aggiorna automaticamente il tempo quando avviene una modifica del fuso orario. 

Prima di implementare le modifiche di codice riportato di seguito, scaricare [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), decomprimerlo e spostare i file con estensione png decompresso **risorse/drawable** (sovrascrivere precedente **preview.png**). Aggiungere i nuovi file con estensione png il `WatchFace` progetto.


### <a name="update-engine-features"></a>Funzionalità del motore di aggiornamento

Il passaggio successivo consiste aggiornamento **MyWatchFaceService.cs** a un'implementazione che disegna un quadrante di orologio analogico e supporta le nuove funzionalità. Sostituire il contenuto di **MyWatchFaceService.cs** con la versione del codice faccia watch analogica [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (è possibile tagliare e incollare l'origine di esistente  **MyWatchFaceService.cs**). 

Questa versione di **MyWatchFaceService.cs** aggiunto altro codice per i metodi esistenti e include ulteriori metodi sottoposti a override per aggiungere ulteriori funzionalità. Le sezioni seguenti forniscono una presentazione del codice sorgente.

#### <a name="oncreate"></a>OnCreate

L'aggiornamento **OnCreate** metodo consente di configurare lo stile del carattere espressioni di controllo come in precedenza, ma include alcuni passaggi aggiuntivi: 

1.  Imposta l'immagine di sfondo il **xamarin_background** risorsa che si trova in **Resources/drawable-hdpi/xamarin_background.png**. 

2.  Inizializza `Paint` oggetti per il disegno lancette, ore e minuti.

3.  Inizializza un `Paint` oggetto per il disegno di segni di graduazione ora intorno al bordo della faccia di espressioni di controllo. 

4.  Crea un timer che chiama il `Invalidate` (metodo) (aggiornamento) in modo che l'icona della mano secondo verrà ridisegnata ogni secondo. Si noti che questo timer è necessario perché `OnTimeTick` chiamate `Invalidate` solo una volta al minuto.

In questo esempio include una sola **xamarin_background.png** immagine; tuttavia, si desidera creare un'immagine di sfondo diversi per ogni densità di schermata che supporterà quadrante dell'orologio personalizzato. 

#### <a name="ondraw"></a>OnDraw

L'aggiornamento **OnDraw** metodo disegna un quadrante di orologio analogico stile utilizzando la procedura seguente: 

1.  Ottiene l'ora corrente, ora viene mantenuto un `time` oggetto. 

2.  Determina i limiti dell'area di disegno e al centro.

3.  Disegna lo sfondo, adattato il dispositivo quando lo sfondo viene disegnato.

4.  Disegna dodici *segni di graduazione* intorno il quadrante dell'orologio (corrispondente alle ore sul quadrante dell'orologio). 

5.  Calcola l'angolo di rotazione e lunghezza per ogni indicatore di espressioni di controllo.

6.  Disegna ogni indicatore nell'area dell'espressione di controllo. Si noti che l'icona della mano secondo non viene tracciato se il controllo è in modalità ambiente. 

 
#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Questo metodo viene chiamato per segnalare `MyWatchFaceEngine` sulle proprietà del dispositivo usura (ad esempio modalità ambiente bit bassa e protezione burn-in). In `MyWatchFaceEngine`, questo metodo verifica solo la modalità ambiente bit bassa (in modalità ambiente velocità in bit bassa, la schermata supporta meno bit per ogni colore). 

Per ulteriori informazioni su questo metodo, vedere il Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) documentazione dell'API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Questo metodo viene chiamato quando il dispositivo usura entra o esce dalla modalità di ambiente. Nel `MyWatchFaceEngine` implementazione, il tipo di carattere di controllo Disabilita anti-aliasing quando è in modalità ambiente. 

Per ulteriori informazioni su questo metodo, vedere il Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) documentazione dell'API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Questo metodo viene chiamato ogni volta che l'orologio diventa visibile o nascosto. In `MyWatchFaceEngine`, questo metodo registra/Annulla la registrazione il destinatario del fuso orario (descritto di seguito) in base allo stato di visibilità. 

Per ulteriori informazioni su questo metodo, vedere il Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) documentazione dell'API.

 
### <a name="time-zone-feature"></a>Funzionalità di fuso orario

Il nuovo **MyWatchFaceService.cs** include inoltre funzionalità per aggiornare l'ora corrente ogni volta che l'ora della zona modifiche (ad esempio durante gli spostamenti tra fusi orari). Verso la fine della **MyWatchFaceService.cs**, una volta zona modifica `BroadcastReceiver` è definito che gestisce gli oggetti intento modificato fuso orario: 

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

Il `RegisterTimezoneReceiver` e `UnregisterTimezoneReceiver` metodi vengono chiamati dal `OnVisibilityChanged` metodo. 
`UnregisterTimezoneReceiver` viene chiamato quando viene modificato lo stato di visibilità del tipo di carattere espressioni di controllo a nascosto. Quando il tipo di carattere di controllo è visibile, `RegisterTimezoneReceiver` viene chiamato (vedere il `OnVisibilityChanged` (metodo)). 

Il motore di `RegisterTimezoneReceiver` metodo dichiara un gestore per il ricevitore di fuso orario `Receive` eventi; questo gestore degli aggiornamenti di `time` oggetto con la nuova ora ogni volta che viene superato un fuso orario: 

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Un filtro preventivo viene creato e registrato per il ricevitore del fuso orario:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

Il `UnregisterTimezoneReceiver` metodo annulla la registrazione il destinatario del fuso orario:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Eseguire il tipo di carattere maggiore di espressioni di controllo

Compilare e distribuire nuovamente l'app al dispositivo usura. Selezionare il tipo di carattere di controllo dalla selezione faccia espressioni di controllo come prima. Viene visualizzata l'anteprima nel selettore di espressioni di controllo a sinistra e il nuovo stile di espressioni di controllo viene visualizzato sulla destra:

[![Quadrante orologio analogico](creating-a-watchface-images/13-analog-watchface.png "migliorata faccia analogico nella selezione e sul dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

In questa schermata si spostano le lancette una volta al secondo. Quando si esegue questo codice in un dispositivo di scadenza, l'icona della mano secondo scompare quando il controllo passa alla modalità di ambiente.

 
## <a name="summary"></a>Riepilogo

In questa procedura dettagliata un watchface personalizzato, usare Android è stato implementato e testato. Il `CanvasWatchFaceService` e `CanvasWatchFaceService.Engine` sono state introdotte le classi e i metodi della classe motore essenziali sono stati implementati per creare un tipo di carattere di controllo digitale semplice. Questa implementazione è stata aggiornata con altre funzionalità per creare un quadrante di orologio analogico e sono stati implementati metodi aggiuntivi per gestire le modifiche in visibilità, modalità di ambiente e le differenze nelle proprietà del dispositivo. Infine, un ricevitore di broadcast di fuso orario è stato implementato in modo che l'orologio Aggiorna automaticamente il tempo quando viene superato un fuso orario. 


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di espressioni di controllo dei caratteri tipografici](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Esempio WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
