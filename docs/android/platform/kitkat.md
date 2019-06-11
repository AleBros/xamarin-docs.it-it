---
title: Funzionalità KitKat
description: Android 4.4 (KitKat) viene caricato con numerose funzionalità per utenti e sviluppatori. Questa guida vengono evidenziate alcune di queste funzionalità e vengono forniti esempi di codice e i dettagli di implementazione che consentono di sfruttare al meglio KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f957bd5b361d7287353542186916c7f934ee0490
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827792"
---
# <a name="kitkat-features"></a>Funzionalità KitKat

_Android 4.4 (KitKat) viene caricato con numerose funzionalità per utenti e sviluppatori. Questa guida vengono evidenziate alcune di queste funzionalità e vengono forniti esempi di codice e i dettagli di implementazione che consentono di sfruttare al meglio KitKat._

## <a name="overview"></a>Panoramica

Android 4.4 (API livello 19), noto anche come "KitKat," è stata rilasciata nel 2013 aggiornate. KitKat offre un'ampia gamma di nuove funzionalità e miglioramenti, tra cui:

-  [Esperienza utente](#user_experience) &ndash; semplici animazioni con framework di transizione, le barre di stato e la navigazione semitrasparente e modalità coinvolgenti a schermo intero consentono di creare un'esperienza migliore per l'utente.

-  [Contenuti di utenti](#user_content) &ndash; gestione file degli utenti semplificata con framework di accesso di archiviazione; stampa delle immagini, siti web e altro contenuto è più semplice con le API di stampa migliorata.

-  [Hardware](#hardware) &ndash; Trasforma qualsiasi app in una scheda NFC con NFC emulazione Smart Card basate su Host, eseguire i sensori di basso consumo con il `SensorManager` .

-  [Strumenti di sviluppo](#developer_tools) &ndash; applicazioni Screencast in azione con il client Android Debug Bridge, disponibile come parte di Android SDK.


Questa guida fornisce indicazioni per la migrazione di un'applicazione xamarin. Android esistente a KitKat, nonché una panoramica generale delle KitKat per gli sviluppatori di xamarin. Android.

## <a name="requirements"></a>Requisiti

Per sviluppare applicazioni xamarin. Android usando KitKat, occorre *xamarin. Android 4.11.0* o versioni successive e Android 4.4 (API livello 19) installato tramite Android SDK Manager, come illustrato nello screenshot seguente:

[![Selezione di Android 4.4 in Android SDK Manager](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrazione dell'App a KitKat

In questa sezione offre alcuni elementi first-risposta per la transizione delle applicazioni esistenti per Android 4.4.

### <a name="check-system-version"></a>Versione del sistema di controllo

Se un'applicazione deve essere compatibile con le versioni precedenti di Android, assicurarsi di eseguire il wrapping di qualsiasi codice KitKat specifico in un controllo della versione del sistema, come illustrato nell'esempio di codice riportato di seguito:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>L'invio in batch di allarme

Android Usa servizi di allarme per riattivare un'app in background in un momento specificato. KitKat ciò richiede un ulteriore passo raggruppando gli allarmi per mantenere la potenza. Ciò significa che, anziché risvegliare il gigante che ogni app in un momento esatto, si preferisce KitKat raggruppare diverse applicazioni che sono registrate per la riattivazione durante lo stesso intervallo di tempo e riattivarli nello stesso momento.
Per comunicare ad Android per riattivare un'app durante un intervallo di tempo specificato, chiamare `SetWindow` nella [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), passando il valore minimo e massimo tempo, espresso in millisecondi, che può trascorrere prima che l'app viene riattivato e l'operazione da eseguire alla riattivazione del computer.
Il codice seguente viene fornito un esempio di un'applicazione che deve essere riattivato tra mezz'ora e un'ora dal momento in cui che l'intervallo è impostato:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Per continuare risvegliare il gigante che un'app a un'ora esatta, utilizzare `SetExact`, passando l'ora esatta in cui l'app deve essere attivato e l'operazione da eseguire:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

Non è più KitKat consente di impostare una sveglia ripetuta esatta. Applicazioni che usano [`SetRepeating`](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)
e richiedono gli allarmi esatti funzionamento sarà ora necessario attivare manualmente ogni avviso.

### <a name="external-storage"></a>Archiviazione esterna

Archiviazione esterna ora è suddivisa in due tipi: univoco per l'applicazione e i dati condivisi da più applicazioni di archiviazione. Lettura e scrittura al percorso specifico dell'app nella risorsa di archiviazione esterna non richiede autorizzazioni speciali. Richiede l'interazione con i dati nell'archiviazione condivisa a questo punto il `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` autorizzazione. I due tipi possono essere classificati di conseguenza:

-  Se si riceve un percorso di file o directory chiamando un metodo su `Context` , ad esempio [`GetExternalFilesDir`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)
   o [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - l'app non richiede alcuna autorizzazione aggiuntive.

-  Se si riceve un percorso di file o directory per l'accesso a una proprietà o chiamare un metodo su `Environment` , ad esempio [`GetExternalStorageDirectory`](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)
   o [`GetExternalStoragePublicDirectory`](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/)
   , l'app richiede la `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` autorizzazione.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implica il `READ_EXTERNAL_STORAGE` autorizzazione, pertanto è necessario solo impostare un'autorizzazione.

### <a name="sms-consolidation"></a>Consolidamento di SMS

KitKat semplifica la messaggistica per l'utente mediante l'aggregazione di tutti i contenuti SMS nell'applicazione di un valore predefinito selezionato dall'utente. Lo sviluppatore è responsabile di rendere selezionabile dell'applicazione di messaggistica predefinito dell'app e si comporta in modo appropriato nel codice e la vita privata se l'applicazione non è selezionata. Per altre informazioni utili per la transizione all'app SMS di KitKat, consultare il [Getting Your SMS App pronta per KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) Guida da Google.

### <a name="webview-apps"></a>App WebView

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) ottenuto un makeover in KitKat. Il cambiamento più significativo viene aggiunta la protezione per il caricamento del contenuto in un `WebView`. Mentre la maggior parte delle applicazioni destinate a versioni precedenti dell'API dovrebbero funzionare come previsto, test delle applicazioni che usano il `WebView` consiglia di classe. Per altre informazioni su APIs WebView interessate, vedere il di Android [eseguire la migrazione a WebView in Android 4.4](https://developer.android.com/guide/webapps/migrating.html) documentazione.

<a name="user_experience" />

## <a name="user-experience"></a>Esperienza utente

KitKat include diverse nuove API per migliorare l'esperienza utente, tra cui il nuovo framework di transizione per la gestione delle animazioni di proprietà e l'opzione dell'interfaccia utente semitrasparente per i temi. Queste modifiche sono descritte sotto.

### <a name="transition-framework"></a>Framework di transizione

Il framework di transizione rende più semplice da implementare animazioni. KitKat consente di eseguire un'animazione di proprietà semplice con una sola riga di codice o personalizzare le transizioni che utilizzano *scene*.

#### <a name="simple-property-animation"></a>Animazione di proprietà semplice

La nuova raccolta di transizioni Android semplifica il codice dietro le animazioni di proprietà. Il framework consente di eseguire semplici animazioni con quantità minima di codice. Ad esempio, l'esempio di codice seguente usa [`TransitionManager.BeginDelayedTransition`](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)
Per aggiungere un'animazione mostrando e nascondendo una `TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

Nell'esempio precedente Usa il framework di transizione per creare un automatico, eseguire la transizione tra i valori delle proprietà modifica predefinito. Poiché l'animazione viene gestita da una singola riga di codice, è possibile apportare facilmente questo compatibile con le versioni precedenti di Android eseguendo il wrapping di `BeginDelayedTransition` chiamare in un controllo della versione del sistema. Vedere le [eseguire la migrazione dell'App da KitKat](#Migrating_Your_App_to_KitKat) sezione per altre informazioni.

La schermata seguente mostra l'app prima dell'animazione:

[![Schermata di App prima dell'inizio di animazione](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La schermata seguente mostra l'app dopo l'animazione:

[![Schermata di App al termine dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

È possibile ottenere maggiore controllo sulla transizione con scene, che sono illustrati nella sezione successiva.

#### <a name="android-scenes"></a>Scene Android

[Le scene](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) sono stati introdotti come parte del framework di transizione per offrire allo sviluppatore un maggiore controllo sulle animazioni. Le scene creare un'area dinamica nell'interfaccia utente: si specifica un contenitore e diverse versioni o "background", per il contenuto XML all'interno del contenitore, e Android fa il resto del lavoro, per animare le transizioni tra le quinte. Le scene Android consentono di creare animazioni complesse con uno sforzo minimo sul lato di sviluppo.

Elemento dell'interfaccia utente statico che contiene il contenuto dinamico viene definito un *contenitore* oppure *scena base*. L'esempio seguente usa la finestra di progettazione Android per creare un `RelativeLayout` chiamato `container`:

[![Utilizzo di progettazione Android per creare un contenitore RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

Il layout di esempio definisce anche un pulsante chiamato `sceneButton` sotto il `container`. Questo pulsante attiverà la transizione.

Il contenuto dinamico all'interno del contenitore richiede due nuovi layout di Android. Questi layout specificare solo il codice *all'interno di* il contenitore.
Codice di esempio seguente definisce un layout chiamato *Scene1* che contiene due campi di testo la lettura "Kit" e "Kat" rispettivamente, e un secondo layout chiamato *Scene2* che contiene gli stessi campi di testo invertiti. Il codice XML è il seguente:

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

L'esempio precedente Usa `merge` per migliorare la visualizzazione del codice più breve e semplificare la gerarchia di visualizzazione. Altre informazioni, vedere `merge` layout [qui](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Una scena viene creata chiamando [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), passando l'oggetto contenitore, l'ID risorsa del file di layout della scena e corrente `Context`, come illustrato nell'esempio di codice riportato di seguito:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Facendo clic sul pulsante alternativamente le due scene, aggiunge un'animazione Android con i valori di transizione predefinito:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

Lo screenshot seguente mostra la scena prima animazione:

[![Screenshot dell'app prima dell'inizio dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Lo screenshot seguente mostra la scena dopo l'animazione:

[![Screenshot dell'app dopo il completamento dell'animazione](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> È presente una [bug noto](https://code.google.com/p/android/issues/detail?id=62450) nelle transizioni Android libreria che fa sì che le scene creati utilizzando `GetSceneForLayout` per interrompere l'esecuzione quando un utente si sposta un'attività con la seconda volta. Viene descritta una soluzione alternativa java [qui](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transizioni personalizzate nelle scene

Una transizione personalizzata può essere definita in un file di risorse xml nel `transition` directory sotto `Resources`, come illustrato nello screenshot seguente:

[![Percorso del file transition.xml nella directory di risorse/transizione](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

Esempio di codice seguente definisce una transizione che aggiunge un'animazione per 5 secondi e Usa il [eccedere interpolatore](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transizione viene creata nell'attività usando il [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), come illustrato nel codice seguente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nuova transizione viene quindi aggiunto al `Go` chiamata che inizia l'animazione:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interfaccia utente semitrasparente

KitKat consente un maggiore controllo sul tema dell'app con barre di stato e la navigazione semitrasparente facoltative. È possibile modificare la traslucidità di elementi dell'interfaccia utente di sistema nello stesso file XML utilizzato per definire il tema di Android. KitKat introduce le seguenti proprietà:

-  `windowTranslucentStatus` -Se impostato su true, rende la barra di stato superiore semitrasparente.

-  `windowTranslucentNavigation` -Se impostato su true, rende la barra di spostamento inferiore semitrasparente.

-  `fitsSystemWindows` -Impostazione la barra superiore o inferiore a transcluent Sposta contenuto all'interno degli elementi dell'interfaccia utente transparent per impostazione predefinita. Impostando questa proprietà su `true` è un modo semplice per impedire la sovrapposizione con gli elementi dell'interfaccia utente di sistema semitrasparente contenuto.


Il codice seguente definisce un tema con le barre di stato e la navigazione semitrasparente:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

La schermata seguente mostra il tema in precedenza con lo stato semitrasparente e le barre di spostamento:

[![Screenshot di esempio di app con le barre di stato e la navigazione semitrasparente](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenuto dell'utente

### <a name="storage-access-framework"></a>Framework di accesso all'archiviazione

Il Framework di accesso di archiviazione (SAF) è un nuovo modo per gli utenti di interagire con il contenuto archiviato, ad esempio immagini, video e documenti. Anziché presentare agli utenti con una finestra di dialogo per scegliere un'applicazione per gestire il contenuto, KitKat apre una nuova interfaccia utente che consente agli utenti di accedere ai propri dati in un'unica posizione di aggregazione. Dopo che il contenuto è stato scelto, l'utente verrà restituito all'applicazione che ha richiesto il contenuto e l'esperienza delle app continua come di consueto.

Questa modifica richiede due azioni sul lato per gli sviluppatori: prima di tutto necessario aggiornare a un nuovo modo di contenuto che richiede le app che richiedono contenuto dal provider. Secondo, applicazioni che scrivono dati in un `ContentProvider` devono essere modificati per utilizzare il nuovo framework. Entrambi questi scenari dipendono dal nuovo [`DocumentsProvider`](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)
API.

#### <a name="documentsprovider"></a>DocumentsProvider

In KitKat, le interazioni con `ContentProviders` vengono estratti con il `DocumentsProvider` classe. Ciò significa che SAF non è rilevante in cui i dati sono fisicamente, purché sia accessibile attraverso la `DocumentsProvider` API. I provider locali, servizi cloud e dispositivi di archiviazione esterni utilizzare tutti la stessa interfaccia e vengono trattata nello stesso modo, fornendo all'utente e lo sviluppatore con un'unica posizione per interagire con il contenuto dell'utente.

Questa sezione descrive come caricare e salvare il contenuto con il Framework di accesso di archiviazione.

#### <a name="request-content-from-a-provider"></a>Contenuto della richiesta da un Provider

È possibile sapere che si desidera prelevare contenuti mediante la UI SAF con KitKat il `ActionOpenDocument` finalità, che significa che si desidera connettersi a tutti i provider di contenuti disponibili per il dispositivo. È possibile aggiungere un filtro per questa finalità specificando `CategoryOpenable`, ovvero solo il contenuto che può essere aperti (contenuto, ovvero accessibile e utilizzabile) verrà restituito. KitKat consente inoltre il filtraggio del contenuto con il `MimeType`. Ad esempio, il codice seguente i filtri per i risultati di immagine, specificando l'immagine `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

La chiamata a `StartActivityForResult` avvia la UI SAF, quale l'utente può quindi Sfoglia per scegliere un'immagine:

[![Screenshot di esempio di un'app usando il Framework di accesso di archiviazione per l'esplorazione di un'immagine](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Dopo che l'utente ha scelto un'immagine `OnActivityResult` restituisce il `Android.Net.Uri` del file scelto. Esempio di codice seguente consente di visualizzare la selezione di immagini dell'utente:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Scrivere il contenuto a un Provider

Oltre a caricamento del contenuto dalla UI SAF, KitKat consente inoltre di salvare il contenuto a qualsiasi `ContentProvider` che implementa il `DocumentProvider` API. Salvataggio del contenuto Usa un' `Intent` con `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

Nell'esempio di codice precedente consente di caricare l'UI SAF, consentendo all'utente di modificare il nome file e selezionare una directory in cui ospitare il nuovo file:

[![Screenshot di utente modificando in NewDoc il nome di file nella directory di download](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando l'utente preme **salvare**, `OnActivityResult` viene passato il `Android.Net.Uri` del file appena creato, che è possibile accedere tramite `data.Data`. L'uri è utilizzabile per trasmettere i dati nel nuovo file:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Si noti che [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))
Restituisce un `System.IO.Stream`, in modo che l'intero processo di streaming può essere scritti in .NET.

Per altre informazioni sul caricamento, creazione e modifica dei contenuti con il Framework di accesso di archiviazione, consultare il [documentazione Android per il Framework di accesso di archiviazione](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Stampa

Il contenuto di stampa viene semplificato in KitKat con l'introduzione del [servizi di stampa](https://developer.xamarin.com/api/namespace/Android.PrintServices/) e `PrintManager`. KitKat è anche la prima versione dell'API per sfruttare completamente la [API del servizio Cloud Print di Google](https://developers.google.com/cloud-print/) usando la [applicazione di stampa di Google Cloud](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La maggior parte dei dispositivi fornite automaticamente con KitKat scaricare app di stampa di Google Cloud e il [plug-in servizi di stampa di HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando avviene prima di tutto la connessione Wi-Fi. Un utente può controllare le impostazioni di stampa la propria del dispositivo passando alla **Impostazioni > sistema > Stampa**:

[![Screenshot di esempio di schermata delle impostazioni di stampa](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Benché le API di stampa sono impostate per lavorare con la stampa Cloud di Google per impostazione predefinita, Android comunque consente agli sviluppatori di preparare il contenuto di stampa usando le nuove API e inviarlo ad altre applicazioni per gestire la stampa.



#### <a name="printing-html-content"></a>Contenuto HTML di stampa

KitKat crea automaticamente un [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) per una visualizzazione web con `WebView.CreatePrintDocumentAdapter`. Contenuto web di stampa viene eseguita una volta coordinata tra un [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) che attende che il contenuto HTML per caricare e consente l'attività in grado di rendere disponibile nel menu di opzioni l'opzione di stampa e l'attività, che è in attesa per l'utente Selezionare l'opzione di stampa e le chiamate `Print`nella `PrintManager`. In questa sezione descrive la configurazione di base necessaria per stampare sullo schermo contenuto HTML.

Si noti che il caricamento e la stampa di contenuti web richiede l'autorizzazione Internet:

[![Impostazione di autorizzazioni Internet nelle opzioni di app](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Voce di Menu Stampa

In genere verrà visualizzata l'opzione stampa dell'attività [dal menu Opzioni](https://developer.android.com/guide/topics/ui/menus.html#options-menu).
Il menu di opzioni consente agli utenti di eseguire azioni su un'attività. È in alto a destra della schermata e si presenta come segue:

[![Screenshot di esempio di voce di menu Stampa visualizzata nell'angolo superiore destro dello schermo](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Altre voci di menu possono essere definite nel *dal menu*directory sotto *risorse*. Il codice seguente definisce un menu di esempio chiamato [stampa](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

L'interazione con il menu delle opzioni nell'attività avviene tramite il `OnCreateOptionsMenu` e `OnOptionsItemSelected` metodi.
`OnCreateOptionsMenu` è possibile aggiungere nuove voci di menu, analogamente all'opzione di stampa, dal *menu* directory delle risorse.
`OnOptionsItemSelected` è in ascolto per l'utente selezionando l'opzione di stampa dal menu di scelta e inizia la stampa:

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

Il codice precedente definisce anche una variabile denominata `dataLoaded` per tenere traccia dello stato del contenuto HTML. Il `WebViewClient` imposterà questa variabile su true quando tutto il contenuto è stato caricato, in modo che l'attività è in grado di aggiungere la voce di menu di stampa al menu di opzioni.

##### <a name="webviewclient"></a>WebViewClient

Il compito del `WebViewClient` consiste nel garantire che i dati nel `WebView` è stato caricato completamente prima che l'opzione di stampa viene visualizzato nel menu, che avviene il `OnPageFinished` (metodo). `OnPageFinished` è in ascolto per il contenuto web completare il caricamento e indica l'attività per ricreare il menu di opzioni con `InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` imposta anche il `dataLoaded` valore per `true`, quindi `OnCreateOptionsMenu` possibile ricreare il menu con l'opzione di stampa in posizione.

##### <a name="printmanager"></a>PrintManager

Esempio di codice seguente consente di stampare il contenuto di un `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` accetta come argomenti: un nome per il processo di stampa ("MyWebPage" in questo esempio), un [`PrintDocumentAdapter`](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)
che genera il documento di stampa dal contenuto, e [`PrintAttributes`](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/)
(`null` nell'esempio precedente). È possibile specificare `PrintAttributes` per definire il layout del contenuto della pagina stampata, anche se gli attributi predefiniti devono gestire la maggior parte degli scenari.

La chiamata a `Print` carica l'interfaccia utente di stampa, che elenca le opzioni per il processo di stampa. L'interfaccia utente offre agli utenti la possibilità di stampare o salvare il contenuto HTML in un file PDF, come illustrato schermate riportate di seguito:

[![Schermata di PrintHtmlActivity visualizzato il menu di stampa](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Schermata di PrintHtmlActivity menu PDF vengono visualizzati il salvataggio](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat aggiunge diverse API per supportare le nuove funzionalità di dispositivo. Il più importante di queste sono basate su Host di emulazione della scheda e il nuovo `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulazione di Smart Card basate su host in NFC

Basato su host scheda emulazione (HCE) consente alle applicazioni di comportarsi come le schede NFC o i lettori di smart card NFC senza basarsi sull'elemento protetto proprietarie del vettore. Prima di configurare HCE, verificare che è disponibile nel dispositivo con HCE `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE richiede che entrambe le funzionalità HCE e il `Nfc` autorizzazione essere registrati con l'applicazione `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Impostazione dell'autorizzazione NFC nelle opzioni di app](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Per funzionare, deve essere in grado di eseguire in background HCE e deve avere un inizio quando l'utente esegue una transazione NFC, anche se l'applicazione usando HCE non è in esecuzione. È possibile eseguire questa operazione scrivendo il codice HCE come un `Service`. Implementa un servizio HCE il `HostApduService` interfaccia che implementa i metodi seguenti:

-  *ProcessCommandApdu* -An Application Protocol Data Unit (APDU) è tra il lettore NFC e il servizio HCE ciò che viene inviato. Questo metodo utilizza un ADPU dal lettore e restituisce un'unità dati nella risposta.

-  *OnDeactivated* - il `HostAdpuService` viene disattivata quando il servizio HCE è non comunicano più con il lettore NFC.


Un servizio HCE deve anche essere registrati con il manifesto dell'applicazione e decorato con le autorizzazioni appropriate, filtro intent e metadati. Il codice seguente è riportato un esempio di un `HostApduService` registrata con il manifesto Android usando il `Service` attributo (per altre informazioni sugli attributi, vedere il Xamarin [lavora sul manifesto Android](~/android/platform/android-manifest.md) Guida):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

Il precedente servizio offre un modo per il lettore NFC interagire con l'applicazione, ma il lettore NFC ancora non ha modo di sapere se questo servizio emula la scheda NFC da analizzare. Per consentire al lettore NFC identificare il servizio, è possibile assegnare il servizio univoca *ID applicazione (AID)* . Si specifica un aiuto, insieme a altri metadati sul servizio HCE, in un file di risorse xml registrato con il `MetaData` attributo (vedere l'esempio di codice precedente). Questo file di risorse specifica uno o più filtri AID - stringhe dell'identificatore univoco in formato esadecimale che corrispondono agli strumenti di uno o più dispositivi di lettore NFC:

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

Oltre ai filtri di aiuto, il file di risorse xml fornisce inoltre una descrizione rivolta all'utente del servizio HCE, specifica un gruppo di aiuto (applicazione del pagamento e "altro") e, nel caso di un'applicazione di pagamento, un banner di dp 260 x 96 da visualizzare all'utente.

Il programma di installazione descritto in precedenza fornisce blocchi predefiniti di base per un'applicazione che emula una scheda NFC. NFC stesso richiede altri passaggi diversi e altri test per configurare. Per altre informazioni su emulazione Card basate su Host, vedere la [portale di documentazione Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Per altre informazioni sull'uso di NFC con Xamarin, consultare il [esempi di Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensori

KitKat fornisce l'accesso ai sensori del dispositivo tramite un [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
Il `SensorManager` consente al sistema operativo pianificare il recapito di informazioni sui sensori a un'applicazione in batch, mantenendo la durata della batteria.

KitKat viene inoltre fornito con due nuovi tipi di sensore per tenere traccia delle operazioni dell'utente. Questi sono basati su accelerometro e includono:

-  *StepDetector* -App riceve una notifica/attivato quando l'utente fa un passo avanti e il rilevamento fornisce un valore di ora per quando si è verificato il passaggio.

-  *StepCounter* -tiene traccia del numero di passaggi, l'utente ha eseguito dopo che è stato registrato il sensore *finché il successivo riavvio del dispositivo*.

Lo screenshot seguente illustra il contatore di passaggio in azione:

[![Screenshot dell'app SensorsActivity la visualizzazione di un contatore di passaggio](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

È possibile creare un `SensorManager` chiamando `GetSystemService(SensorService)` e il cast del risultato come un `SensorManager`. Per usare il contatore di passaggio, chiamare `GetDefaultSensor` nella `SensorManager`. È possibile registrare il sensore e l'ascolto delle modifiche nel conteggio di passaggio con l'aiuto del [`ISensorEventListener`](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)
interfaccia, come illustrato nell'esempio di codice riportato di seguito:

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` viene chiamato se il numero di passaggio Aggiorna mentre l'applicazione è in primo piano. Se l'applicazione passerà in background o il dispositivo è in stato di sospensione, `OnSensorChanged` non verranno chiamati; tuttavia, i passaggi continueranno a essere conteggiate fino al `UnregisterListener` viene chiamato.

Tenere presente che *il valore del conteggio passaggio è cumulativo per tutte le applicazioni che registrano il sensore*. Ciò significa che anche se si disinstalla e reinstalla l'applicazione e inizializzare il `count` variabile da 0 all'avvio dell'applicazione, il valore rilevato dal sensore rimarrà il numero totale di passaggi eseguiti mentre è stato registrato il sensore, se per il l'applicazione o un altro. È possibile impedire all'applicazione aggiunta per il contatore passaggio chiamando `UnregisterListener` nella `SensorManager`, come illustrato nel codice seguente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Il riavvio del dispositivo viene reimpostato il conteggio di passaggio su 0. L'app richiede codice aggiuntivo per assicurarsi che sta inviando report un conteggio accurato per l'applicazione, indipendentemente dalle altre applicazioni che utilizzano il sensore o lo stato del dispositivo.


> [!NOTE]
> Mentre l'API per il rilevamento di passaggio e il conteggio viene fornito con KitKat, non tutti i telefoni includono con il sensore. È possibile controllare se il sensore è visualizzabile eseguendo `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, o per verificare il valore restituito della `GetDefaultSensor` non è `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Strumenti di sviluppo

### <a name="screen-recording"></a>Registrazione dello schermo

KitKat include schermata nuova funzionalità di registrazione in modo che gli sviluppatori possono registrare applicazioni in azione. Registrazione dello schermo è disponibile tramite il [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) client, che possono essere scaricati come parte di Android SDK.

Per registrare lo schermo, connettere il dispositivo; quindi, individuare l'installazione di Android SDK, passare al **platform-tools** directory ed eseguire il **adb** client:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

Il comando precedente registra un video di 3 minuti predefinito con la risoluzione predefinita del 4 Mbps. Per modificare la lunghezza, aggiungere il *-limite di tempo* flag.
Per modificare la risoluzione, aggiungere il *-velocità in bit* flag. Il comando seguente registra un video minuto-lungo con 8 Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

È possibile trovare il video sul dispositivo, viene visualizzato nella raccolta quando la registrazione è stata completata.


## <a name="other-kitkat-additions"></a>Altre aggiunte KitKat

Oltre alle modifiche descritte sopra, KitKat consente di:

-  *Usare modalità schermo intero* -KitKat introduce una nuova [modalità Immersive](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) per l'esplorazione del contenuto, a giocare alle partite e in esecuzione altre applicazioni che possono trarre vantaggio da un'esperienza a schermo intero.

-  *Personalizzare le notifiche* -ottenere altri dettagli sulle notifiche di sistema con il [`NotificationListenerService`](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/)
   . Ciò consente di visualizzare le informazioni in modo diverso all'interno dell'app.

-  *Eseguire il mirroring risorse Drawable* -risorse Drawable dispone di un nuovo [`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
   attributo che indica al sistema di creare una versione con mirroring per le immagini che richiedono l'inversione dei layout da sinistra a destra.

-  *Sospendere le animazioni* -sospendere e riprendere le animazioni create con il [`Animator`](https://developer.xamarin.com/api/type/Android.Animation.Animator/)
   .

-  *Testo in modo dinamico la modifica lettura* -indicano le parti dell'interfaccia utente che aggiorna in modo dinamico con il nuovo testo come "le aree dinamiche" con il nuovo [ `accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   attributo in modo che leggerà il nuovo testo automaticamente in modalità di accesso facilitato.

-  *Migliorare l'esperienza di Audio* -tiene traccia di rendere più alto con il [`LoudnessEnhancer`](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/)
   , trovare il picco e un flusso audio con RMS di [`Visualizer`](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)
   classe e ottenere informazioni da un [timestamp audio](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) per facilitare la sincronizzazione di audio e video.

-  *Sincronizzare ContentResolver a intervalli personalizzati* -KitKat aggiunge una certa variabilità nel momento in cui viene eseguita una richiesta di sincronizzazione. Sincronizzazione una `ContentResolver` in fase di personalizzati o intervallo chiamando `ContentResolver.RequestSync` e passando un `SyncRequest`.

-  *Distingui tra i controller* -In KitKat, i controller vengono assegnati gli identificatori di tipo integer univoco che è possibile accedere tramite il dispositivo `ControllerNumber` proprietà. Questo rende più semplice indicare a distanza lettori in un gioco.

-  *Controllo remoto* -KitKat con poche modifiche sul lato di hardware e software, consente di attivare un dispositivo dotato di un strumento di trasmissione di runtime di integrazione in un controllo remoto usando il `ConsumerIrService`e interagire con i dispositivi con il nuovo [`RemoteController`](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)
   API.

Per altre informazioni sulle modifiche API precedente, fare riferimento a Google [API di Android 4.4](https://developer.android.com/about/versions/android-4.4.html) Panoramica.


## <a name="summary"></a>Riepilogo

Questo articolo introdotte alcune delle nuove API disponibile in Android 4.4 (API livello 19) e illustrato le procedure consigliate durante la transizione di un'applicazione a KitKat. IT contorni modifiche alle API che interessano utente esperienza, tra cui il *framework di transizione* e nuove opzioni per *temi*. Successivamente, introdotto il *Framework di accesso agli archivi* e `DocumentsProvider` classe, nonché la nuova *API stampa*. È stata esaminata *emulazione smart card basate su host NFC* e su come usare *sensori di basso consumo*, inclusi due nuovi sensori per tenere traccia delle operazioni dell'utente. Infine, è stato illustrato demo in tempo reale delle applicazioni con l'acquisizione *registrazione dello schermo*e fornito un elenco dettagliato delle modifiche apportate all'API KitKat e aggiunte.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio KitKat](https://developer.xamarin.com/samples/monodroid/KitKat/)
- [Android 4.4 le API](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
