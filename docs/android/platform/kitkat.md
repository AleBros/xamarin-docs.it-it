---
title: Caratteristiche di KitKat
description: Android 4.4 (KitKat) viene caricato con una cornucopia di funzionalità per gli utenti e gli sviluppatori. Questa guida evidenzia alcune di queste funzionalità e fornisce esempi di codice e dettagli di implementazione che consentono di sfruttare al meglio KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 43061272f3d3486926f38af792ee3b9df0c53670
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027237"
---
# <a name="kitkat-features"></a>Caratteristiche di KitKat

_Android 4.4 (KitKat) viene caricato con una cornucopia di funzionalità per gli utenti e gli sviluppatori. Questa guida evidenzia alcune di queste funzionalità e fornisce esempi di codice e dettagli di implementazione che consentono di sfruttare al meglio KitKat._

## <a name="overview"></a>Panoramica

Android 4.4 (API Level 19), noto anche come "KitKat", è stato rilasciato alla fine del 2013. KitKat offre una varietà di nuove funzionalità e miglioramenti, tra cui:

- [Esperienza utente](#user_experience) &ndash; Animazioni semplici con framework di transizione, stato traslucido e barre di navigazione, e modalità immersiva a schermo intero aiutano a creare un'esperienza migliore per l'utente.

- [Gestione dei](#user_content) &ndash; file utente Content User semplificata con il framework di accesso allo spazio di archiviazione; la stampa di immagini, siti Web e altri contenuti è più semplice grazie alle API di stampa migliorate.

- [Hardware](#hardware) &ndash; Trasforma qualsiasi app in una scheda NFC con l'emulazione della scheda basata su host NFC; utilizzare sensori a bassa `SensorManager` potenza con il file .

- [Applicazioni](#developer_tools) &ndash; Screencast degli strumenti di sviluppo in azione con il client Android Debug Bridge, disponibile come parte di Android SDK.

Questa guida fornisce indicazioni per la migrazione di un'applicazione Xamarin.Android esistente a KitKat, nonché una panoramica generale di KitKat per gli sviluppatori Xamarin.Android.This guide provides guidance for migrating an existing Xamarin.Android application to KitKat, as well as a high-level overview of KitKat for Xamarin.Android developers.

## <a name="requirements"></a>Requisiti

Per sviluppare applicazioni Xamarin.Android utilizzando KitKat, è necessario *Xamarin.Android 4.11.0* o superiore e Android 4.4 (API livello 19) installato tramite Android SDK Manager, come illustrato nella schermata seguente:

[![Selezione di Android 4.4 in Android SDK Manager](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrazione dell'app a KitKat

Questa sezione fornisce alcuni elementi di prima risposta per facilitare la transizione delle applicazioni esistenti ad Android 4.4.This section provides some first-response items to help transition existing applications to Android 4.4.

### <a name="check-system-version"></a>Controllare la versione del sistema

Se un'applicazione deve essere compatibile con le versioni precedenti di Android, assicurarsi di eseguire il wrapping di qualsiasi codice specifico KitKat in un controllo della versione di sistema, come illustrato nell'esempio di codice seguente:If an application needs to be compatible with older versions of Android, be sure to wrap any KitKat-specific code in a system version check, as illustrated by the code sample below:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Batch di allarmi

Android usa i servizi di allarme per riattivare un'app in background a un'ora specificata. KitKat fa un ulteriore passo avanti mettendo in batch gli allarmi per preservare la potenza. Ciò significa che, anziché riattivare ogni app in un'ora esatta, KitKat preferisce raggruppare diverse applicazioni che sono registrate per riattivare durante lo stesso intervallo di tempo e riattivarle contemporaneamente.
Per indicare ad Android di riattivare `SetWindow` un'app durante un intervallo di tempo specificato, chiama il [`AlarmManager`](xref:Android.App.AlarmManager)valore di , passando il tempo minimo e massimo, in millisecondi, che può trascorrere prima che l'app venga riattivata e che l'operazione venga eseguita alla riattivazione.
Il codice seguente fornisce un esempio di un'applicazione che deve essere riattivatra tra mezz'ora e un'ora dal momento in cui viene impostata la finestra:The following code provides an example of an application that needs to be woken between a half hour and an hour from the time the window is set:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Per continuare a eseguire la riattivazione di un'app in un'ora esatta, usa , passando `SetExact`l'ora esatta in cui l'app deve essere riattivazione e l'operazione da eseguire:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat non consente più di impostare un allarme ripetuto esatto. Applicazioni che utilizzano[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
e richiedono allarmi esatti per funzionare sarà ora necessario attivare ogni allarme manualmente.

### <a name="external-storage"></a>Archiviazione esterna

L'archiviazione esterna è ora suddivisa in due tipi: archiviazione univoca per l'applicazione e dati condivisi da più applicazioni. La lettura e la scrittura nella posizione specifica dell'app nell'archiviazione esterna non richiedono autorizzazioni speciali. L'interazione con i dati `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` nell'archiviazione condivisa richiede ora l'autorizzazione o . I due tipi possono essere classificati come tali:

- Se si ottiene un percorso di file o `Context` di directory chiamando un metodo su , ad esempio,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  O[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
  - l'app non richiede autorizzazioni aggiuntive.

- Se si ottiene un percorso di file o directory accedendo `Environment` a una proprietà o chiamando un metodo su , ad esempio[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  O[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
  , l'app `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` richiede l'autorizzazione o .

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE`implica `READ_EXTERNAL_STORAGE` l'autorizzazione, quindi è necessario impostare solo un'autorizzazione.

### <a name="sms-consolidation"></a>Consolidamento SMS

KitKat semplifica la messaggistica per l'utente aggregando tutto il contenuto SMS in un'applicazione predefinita selezionata dall'utente. Lo sviluppatore è responsabile di rendere l'app selezionabile come applicazione di messaggistica predefinita e si comporta in modo appropriato nel codice e nella vita se l'applicazione non è selezionata. Per ulteriori informazioni sulla transizione dell'app SMS a KitKat, consulta la guida [Preparazione di SMS Apps for KitKat](https://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) di Google.

### <a name="webview-apps"></a>App WebView

[WebView](xref:Android.Webkit.WebView) ha ottenuto un restyling in KitKat. La modifica più grande è la `WebView`sicurezza aggiuntiva per il caricamento del contenuto in un file . Mentre la maggior parte delle applicazioni destinate a `WebView` versioni API precedenti dovrebbe funzionare come previsto, è consigliabile testare le applicazioni che utilizzano la classe. Per ulteriori informazioni sulle API WebView interessate, fare riferimento alla documentazione di migrazione di Android [a WebView in Android 4.4.](https://developer.android.com/guide/webapps/migrating.html)

<a name="user_experience" />

## <a name="user-experience"></a>Esperienza utente

KitKat include diverse nuove API per migliorare l'esperienza utente, tra cui il nuovo framework di transizione per la gestione delle animazioni delle proprietà e un'opzione dell'interfaccia utente traslucida per i tesi. Queste modifiche sono trattate di seguito.

### <a name="transition-framework"></a>Quadro di transizione

Il framework di transizione semplifica l'implementazione delle animazioni. KitKat consente di eseguire una semplice animazione di proprietà con una sola riga di codice o di personalizzare le transizioni utilizzando *Scenes*.

#### <a name="simple-property-animation"></a>Animazione delle proprietà semplici

La nuova libreria Android Transitions semplifica le animazioni delle proprietà code-behind. Il framework consente di eseguire animazioni semplici con codice minimo. Ad esempio, l'esempio di codice seguente usa[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
per animare la `TextView`visualizzazione e l'occultamento di un :

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

Nell'esempio precedente viene utilizzato il framework di transizione per creare una transizione automatica predefinita tra i valori delle proprietà che cambiano. Poiché l'animazione viene gestita da una singola riga di codice, è `BeginDelayedTransition` possibile rendere facilmente compatibile con le versioni precedenti di Android eseguendo il wrapping della chiamata in un controllo della versione di sistema. Per ulteriori informazioni, consulta la sezione [Migrazione dell'app a KitKat.](#Migrating_Your_App_to_KitKat)

Lo screenshot qui sotto mostra l'app prima dell'animazione:

[![Schermata dell'app prima dell'inizio dell'animazione](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

Lo screenshot qui sotto mostra l'app dopo l'animazione:

[![Schermata dell'app dopo il completamento dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

È possibile ottenere un maggiore controllo sulla transizione con le scene, che sono trattate nella sezione successiva.

#### <a name="android-scenes"></a>Scene Android

[Le scene](xref:Android.Transitions.Scene) sono state introdotte come parte del framework di transizione per dare allo sviluppatore un maggiore controllo sulle animazioni. Le scene creano un'area dinamica nell'interfaccia utente: si specifica un contenitore e diverse versioni, o "scene", per il contenuto XML all'interno del contenitore e Android esegue il resto del lavoro per animare le transizioni tra le scene. Le scene Android consentono di creare animazioni complesse con un lavoro minimo sul lato dello sviluppo.

L'elemento statico dell'interfaccia utente che ospita il contenuto dinamico è denominato *contenitore* o *base di scene.* L'esempio seguente usa la `RelativeLayout` finestra `container`di progettazione Android per creare un oggetto chiamato :

[![Utilizzo della finestra di progettazione Android per creare un contenitore RelativeLayoutUsing the Android Designer to create a RelativeLayout container](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

Il layout di esempio definisce anche un pulsante denominato `sceneButton` sotto il `container`file . Questo pulsante attiverà la transizione.

Il contenuto dinamico all'interno del contenitore richiede due nuovi layout Android.The dynamic content inside the container requires two new Android layouts. Questi layout specificano solo il codice *all'interno* del contenitore.
Il codice di esempio seguente definisce un layout denominato *Scene1* che contiene due campi di testo che leggono rispettivamente "Kit" e "Kat" e un secondo layout denominato *Scene2* che contiene gli stessi campi di testo invertiti. Il codice XML è il seguente:

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

L'esempio `merge` precedente viene utilizzato per abbreviare il codice di visualizzazione e semplificare la gerarchia della visualizzazione. Ulteriori informazioni sui `merge` layout [sono disponibili qui](https://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Una scena viene [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*)creata chiamando , passando l'oggetto contenitore, l'ID risorsa `Context`del file di layout della scena e l'oggetto corrente , come illustrato nell'esempio di codice riportato di seguito:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Facendo clic sul pulsante si passa tra le due scene, che Android anima con i valori di transizione predefiniti:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

La schermata seguente illustra la scena prima dell'animazione:The screenshot below illustrates the scene before the animation:

[![Screenshot dell'app prima dell'inizio dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

La schermata seguente illustra la scena dopo l'animazione:The screenshot below illustrates the scene after the animation:

[![Screenshot dell'app al termine dell'animazione](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> C'è un [bug noto](https://code.google.com/p/android/issues/detail?id=62450) nella libreria transizioni Android che causa scene create utilizzando `GetSceneForLayout` per interrompere quando un utente si sposta attraverso un'attività la seconda volta. Una soluzione java è descritta [qui](http://www.doubleencore.com/2013/11/new-transitions-framework/).

##### <a name="custom-transitions-in-scenes"></a>Transizioni personalizzate nelle scene

Una transizione personalizzata può essere definita `transition` in `Resources`un file di risorse xml nella directory in , come illustrato nella schermata seguente:

[![Percorso del file transition.xml in Resources/transition directory](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

L'esempio di codice seguente definisce una transizione che aggiunge un'animazione per 5 secondi e usa [l'interpolatore di overshoot:](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html)

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transizione viene creata nell'attività utilizzando [TransitionInflater](xref:Android.Transitions.TransitionInflater), come illustrato dal codice seguente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nuova transizione viene `Go` quindi aggiunta alla chiamata che inizia l'animazione:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interfaccia utente traslucida

KitKat ti offre un maggiore controllo sui tesi della tua app con barre di stato e di navigazione traslucide facoltative. Puoi modificare la traslucenza degli elementi dell'interfaccia utente di sistema nello stesso file XML che usi per definire il tema Android. KitKat introduce le seguenti proprietà:

- `windowTranslucentStatus`- Se impostato su true, rende la barra di stato superiore traslucida.

- `windowTranslucentNavigation`- Se impostato su true, rende la barra di navigazione inferiore traslucida.

- `fitsSystemWindows`- Impostando la barra superiore o inferiore su transcluent il contenuto sotto gli elementi trasparenti dell'interfaccia utente per impostazione predefinita. L'impostazione `true` di questa proprietà su un modo semplice per impedire la sovrapposizione del contenuto con gli elementi dell'interfaccia utente di sistema traslucidi.

Il codice seguente definisce un tema con barre di stato e di spostamento traslucide:The following code defines a theme with traslucid status and navigation bars:

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

La schermata qui sotto mostra il tema sopra con lo stato traslucido e le barre di navigazione:

[![Schermata di esempio dell'app con barre di stato e di spostamento traslucide](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenuto utente

### <a name="storage-access-framework"></a>Framework di archiviazione-accesso

Storage Access Framework (SAF) è un nuovo modo per gli utenti di interagire con il contenuto archiviato, ad esempio immagini, video e documenti. Invece di presentare agli utenti una finestra di dialogo per scegliere un'applicazione per gestire il contenuto, KitKat apre una nuova interfaccia utente che consente agli utenti di accedere ai dati in un'unica posizione aggregata. Dopo aver scelto il contenuto, l'utente tornerà all'applicazione che ha richiesto il contenuto e l'esperienza dell'app continuerà normalmente.

Questa modifica richiede due azioni sul lato sviluppatore: in primo luogo, le app che richiedono contenuto dai provider devono essere aggiornate a un nuovo modo di richiedere contenuto. In secondo luogo, le `ContentProvider` applicazioni che scrivono dati in una necessità deve essere modificata per utilizzare il nuovo framework. Entrambi gli scenari dipendono dal nuovo[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
.

#### <a name="documentsprovider"></a>Provider di documenti

In KitKat, le `ContentProviders` interazioni con `DocumentsProvider` sono astratte con la classe. Ciò significa che SAF non si preoccupa dove i dati sono `DocumentsProvider` fisicamente, purché siano accessibili tramite l'API. I provider locali, i servizi cloud e i dispositivi di archiviazione esterni utilizzano tutti la stessa interfaccia e vengono trattati allo stesso modo, fornendo all'utente e allo sviluppatore un'unica posizione per interagire con il contenuto dell'utente.

In questa sezione viene illustrato come caricare e salvare il contenuto con Storage Access Framework.

#### <a name="request-content-from-a-provider"></a>Richiedere contenuto a un providerRequest Content From a Provider

Possiamo dire KitKat che vogliamo scegliere il contenuto utilizzando `ActionOpenDocument` l'interfaccia utente SAF con l'intento, che significa che vogliamo connettersi a tutti i provider di contenuti disponibili per il dispositivo. È possibile aggiungere alcuni filtri a `CategoryOpenable`questo intento specificando , il che significa che verrà restituito solo il contenuto che può essere aperto (ad esempio contenuto accessibile e utilizzabile). KitKat consente anche il `MimeType`filtraggio dei contenuti con il . Ad esempio, il codice seguente filtra i `MimeType`risultati dell'immagine specificando l'immagine :

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

La `StartActivityForResult` chiamata avvia l'interfaccia utente SAF, che l'utente può quindi cercare di scegliere un'immagine:Calling launches the SAF UI, which the user can then browse to choose an image:

[![Schermata di esempio di un'app che usa Storage Access Framework per l'esplorazione di un'immagine](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Dopo che l'utente `OnActivityResult` ha `Android.Net.Uri` scelto un'immagine, restituisce il file scelto. L'esempio di codice seguente visualizza la selezione dell'immagine dell'utente:The code sample below displays the user's image selection:

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

#### <a name="write-content-to-a-provider"></a>Scrivere contenuto in un providerWrite Content To a Provider

Oltre a caricare il contenuto dall'interfaccia utente di SAF, KitKat consente anche di salvare il contenuto in qualsiasi `ContentProvider` che implementi l'API. `DocumentProvider` Il salvataggio `Intent` del `ActionCreateDocument`contenuto utilizza un con:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

L'esempio di codice precedente carica l'interfaccia utente SAF, consentendo all'utente di modificare il nome del file e selezionare una directory per ospitare il nuovo file:The above code sample loads the SAF UI, letting the user change the file name and select a directory to house the new file:

[![Screenshot della modifica del nome del file in NewDoc nella directory Download](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando l'utente **Save**preme `OnActivityResult` Salva `Android.Net.Uri` , viene passato il file appena `data.Data`creato, a cui è possibile accedere con . L'uri può essere utilizzato per trasmettere i dati nel nuovo file:The uri can be used to stream data into the new file:

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

Si noti che[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
restituisce `System.IO.Stream`un oggetto , in modo che l'intero processo di streaming possa essere scritto in .NET.

Per ulteriori informazioni sul caricamento, la creazione e la modifica del contenuto con Storage Access Framework, fare riferimento alla [documentazione](https://developer.android.com/guide/topics/providers/document-provider.html)di Android per Storage Access Framework .

### <a name="printing"></a>Stampa

La stampa del contenuto è semplificata in `PrintManager`KitKat con l'introduzione dei servizi di [stampa](xref:Android.PrintServices) e . KitKat è anche la prima versione API a sfruttare appieno le [API del servizio Cloud Print di Google](https://developers.google.com/cloud-print/) utilizzando [l'applicazione Google Cloud Print.](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)
La maggior parte dei dispositivi che vengono spediti con KitKat scaricano automaticamente l'app Google Cloud Print e il [plug-in HP Print Service](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando si connettono per la prima volta al WiFi. Un utente può controllare le impostazioni di stampa del proprio dispositivo accedendo a **Impostazioni > sistema > stampa:**

[![Schermata di esempio della schermata Impostazioni di stampa](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> Anche se le API di stampa sono configurate per funzionare con Google Cloud Print per impostazione predefinita, Android consente comunque agli sviluppatori di preparare la stampa di contenuto utilizzando le nuove API e inviarlo ad altre applicazioni per gestire la stampa.

#### <a name="printing-html-content"></a>Stampa di contenuto HTML

KitKat crea [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) automaticamente un oggetto `WebView.CreatePrintDocumentAdapter`per una visualizzazione Web con . La stampa di contenuto Web [`WebViewClient`](xref:Android.Webkit.WebViewClient) è uno sforzo coordinato tra un che attende il caricamento del contenuto HTML e consente all'Attività di rendere `Print`disponibile `PrintManager`l'opzione di stampa nel menu delle opzioni, e l'attività, che attende che l'utente selezioni l'opzione Stampa e le chiamate sul . In questa sezione viene illustrata la configurazione di base necessaria per stampare il contenuto HTML su schermo.

Si noti che il caricamento e la stampa di contenuto Web richiede l'autorizzazione Internet:

[![Impostazione dell'autorizzazione Internet nelle opzioni dell'app](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Voce di menu Stampa

L'opzione di stampa viene in genere visualizzata nel [menu delle opzioni](https://developer.android.com/guide/topics/ui/menus.html#options-menu)dell'attività.
Il menu delle opzioni consente agli utenti di eseguire azioni su un'attività. Si trova nell'angolo in alto a destra dello schermo e ha questo aspetto:

[![Schermata di esempio della voce di menu Stampa visualizzata nell'angolo in alto a destra dello schermo](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

È possibile definire ulteriori voci di menu nella directory dei *menu*in *Risorse*. Il codice seguente definisce una voce di menu di esempio denominata [Print](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

L'interazione con il menu `OnCreateOptionsMenu` delle `OnOptionsItemSelected` opzioni nell'attività avviene tramite i metodi e .
`OnCreateOptionsMenu`è la posizione in cui aggiungere nuove voci di menu, ad esempio l'opzione Stampa, dalla directory delle risorse di *menu.*
`OnOptionsItemSelected`l'utente seleziona l'opzione Stampa dal menu e inizia a stampare:

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

Il codice precedente definisce `dataLoaded` anche una variabile chiamata per tenere traccia dello stato del contenuto HTML. La `WebViewClient` variabile imposterà questa variabile su true quando tutto il contenuto è stato caricato, in modo che l'attività sappia aggiungere la voce di menu Stampa al menu delle opzioni.

##### <a name="webviewclient"></a>WebViewClient (informazioni in lingua utente)

Il lavoro `WebViewClient` del è quello `WebView` di garantire che i dati nel è completamente caricato `OnPageFinished` prima che l'opzione di stampa appare nel menu, che fa con il metodo. `OnPageFinished`rimane in ascolto del contenuto Web per completare il `InvalidateOptionsMenu`caricamento e indica all'Attività di ricreare il relativo menu delle opzioni con:

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

`OnPageFinished`imposta anche `dataLoaded` il `true`valore `OnCreateOptionsMenu` su , in modo da poter ricreare il menu con l'opzione Stampa sul posto.

##### <a name="printmanager"></a>PrintManager

Nell'esempio di codice riportato `WebView`di seguito viene stampato il contenuto di un oggetto :

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`accetta come argomenti: un nome per il lavoro di stampa ("MyWebPage" in questo esempio),[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
che genera il documento di stampa dal contenuto, e[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(nell'esempio`null` precedente). È possibile `PrintAttributes` specificare per facilitare il layout del contenuto nella pagina stampata, anche se gli attributi predefiniti devono gestire la maggior parte degli scenari.

La `Print` chiamata carica l'interfaccia utente di stampa, che elenca le opzioni per il processo di stampa. L'interfaccia utente offre agli utenti la possibilità di stampare o salvare il contenuto HTML in un PDF, come illustrato dalle schermate seguenti:

[![Screenshot di PrintHtmlActivity che visualizza il menu Stampa](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Screenshot di PrintHtmlActivity che visualizza il menu Salva come PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat aggiunge diverse API per supportare le nuove funzionalità del dispositivo. I più importanti di questi sono Host-Based Card `SensorManager`Emulation e il nuovo .

### <a name="host-based-card-emulation-in-nfc"></a>Emulazione della scheda basata su host in NFC

L'HCE (Host-Based Card Emulation) consente alle applicazioni di comportarsi come schede NFC o lettori di schede NFC senza fare affidamento sull'elemento proprietario del vettore Secure Element. Prima di configurare HCE, assicurarsi che `PackageManager.HasSystemFeature`HCE sia disponibile sul dispositivo con:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE richiede che sia la `Nfc` funzionalità HCE che l'autorizzazione siano registrate con: `AndroidManifest.xml`

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Impostazione dell'autorizzazione NFC nelle opzioni dell'app](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Per funzionare, HCE deve essere in grado di essere eseguito in background e deve essere avviato quando l'utente effettua una transazione NFC, anche se l'applicazione che utilizza HCE non è in esecuzione. È possibile eseguire questa operazione scrivendo `Service`il codice HCE come file . Un servizio HCE `HostApduService` implementa l'interfaccia, che implementa i metodi seguenti:An HCE Service implements the interface, which implements the following methods:

- *ProcessCommandApdu* - Un'unità dati del protocollo dell'applicazione (APDU, Application Protocol Data Unit) è ciò che viene inviato tra il lettore NFC e il servizio HCE. Questo metodo utilizza un ADPU dal lettore e restituisce un'unità di dati in risposta.

- *OnDeactivated* - `HostAdpuService` Il viene disattivato quando il servizio HCE non comunica più con il lettore NFC.

Un servizio HCE deve inoltre essere registrato con il manifesto dell'applicazione e decorato con le autorizzazioni, il filtro finalità e i metadati appropriati. Il codice seguente è `HostApduService` un esempio di un `Service` registrato con il manifesto Android utilizzando l'attributo (per ulteriori informazioni sugli attributi, fare riferimento alla Guida Xamarin [Working with Android Manifest):The](~/android/platform/android-manifest.md) following code is an example of a registered with the Android Manifest using the Attribute (for more information on attributes, refer to the Xamarin Working with Android Manifest guide):

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

Il servizio di cui sopra fornisce un modo per il lettore NFC di interagire con l'applicazione, ma il lettore NFC non ha ancora modo di sapere se questo servizio sta emulando la scheda NFC che deve eseguire la scansione. Per consentire al lettore NFC di identificare il Servizio, è possibile assegnare al Servizio un ID applicazione univoco *(AID).* Viene specificato un AID, insieme ad altri metadati relativi al servizio `MetaData` HCE, in un file di risorse xml registrato con l'attributo (vedere l'esempio di codice precedente). Questo file di risorse specifica uno o più filtri AID: stringhe di identificatori univoci in formato esadecimale che corrispondono agli AID di uno o più dispositivi di lettura NFC:

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

Oltre ai filtri AID, il file di risorse xml fornisce anche una descrizione rivolta all'utente del servizio HCE, specifica un gruppo AID (applicazione di pagamento rispetto a "altra") e, nel caso di un'applicazione di pagamento, un banner dp 260x96 da visualizzare all'utente.

La configurazione descritta in precedenza fornisce i blocchi predefiniti di base per un'applicazione che emula una scheda NFC. NFC stesso richiede diversi passaggi aggiuntivi e ulteriori test da configurare. Per ulteriori informazioni sull'emulazione della scheda basata su host, fare riferimento al portale di [documentazione di Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Per ulteriori informazioni sull'utilizzo di NFC con Xamarin, consultare i [campioni Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensori

KitKat fornisce l'accesso ai sensori [`SensorManager`](xref:Android.Hardware.SensorManager)del dispositivo tramite un file .
Il `SensorManager` sistema operativo consente al sistema operativo di pianificare la consegna delle informazioni del sensore a un'applicazione in lotti, preservando la durata della batteria.

KitKat viene inoltre fornito con due nuovi tipi di sensori per il monitoraggio dei passi dell'utente. Questi sono basati su accelerometro e includono:

- *StepDetector:* l'app riceve una notifica o una rilassa quando l'utente esegue un passaggio e il rilevatore fornisce un valore temporale per il momento in cui si è verificato il passaggio.

- *StepCounter* - Tiene traccia del numero di passaggi che l'utente ha eseguito dopo la registrazione del sensore *fino al successivo riavvio del dispositivo.*

Lo screenshot qui sotto mostra il contatore dei passaggi in azione:The screenshot below depicts the step counter in action:

[![Screenshot dell'app SensorsActivity che visualizza un contatore dei passaggi](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

È possibile `SensorManager` creare `GetSystemService(SensorService)` un chiamando e `SensorManager`eseguendo il cast del risultato come un oggetto . Per utilizzare il contatore dei passaggi, chiamare `GetDefaultSensor` il `SensorManager`file . È possibile registrare il sensore e ascoltare le modifiche nel conteggio dei passaggi con l'aiuto del[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
come illustrato nell'esempio di codice riportato di seguito:

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

`OnSensorChanged`viene chiamato se il numero di passaggi viene aggiornato mentre l'applicazione è in primo piano. Se l'applicazione entra in background, o `OnSensorChanged` il dispositivo è in stato di sospensione, non verrà chiamato; tuttavia, i passaggi continueranno `UnregisterListener` a essere conteggiati fino a quando non viene chiamato.

Tenere presente che *il valore del conteggio dei passaggi è cumulativo in tutte le applicazioni che registrano il sensore*. Ciò significa che anche se si disinstalla `count` e reinstalla l'applicazione e si inizializza la variabile su 0 all'avvio dell'applicazione, il valore riportato dal sensore rimarrà il numero totale di passaggi eseguiti durante la registrazione del sensore, sia dall'applicazione che da un'altra. È possibile impedire all'applicazione di aggiungere `UnregisterListener` al `SensorManager`contatore dei passaggi chiamando il metodo , come illustrato nel codice seguente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Il riavvio del dispositivo reimposta il numero di passaggi su 0. L'app richiederà codice aggiuntivo per garantire che stia segnalando un conteggio accurato per l'applicazione, indipendentemente dalle altre applicazioni che usano il sensore o lo stato del dispositivo.

> [!NOTE]
> Mentre l'API per il rilevamento e il conteggio dei passaggi viene fornita con KitKat, non tutti i telefoni sono dotati del sensore. È possibile verificare se il `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`sensore è disponibile eseguendo `GetDefaultSensor` o verificare che il valore restituito di non `null`sia .

<a name="developer_tools" />

## <a name="developer-tools"></a>Strumenti di sviluppo

### <a name="screen-recording"></a>Registrazione dello schermo

KitKat include nuove funzionalità di registrazione dello schermo in modo che gli sviluppatori possano registrare le applicazioni in azione. La registrazione dello schermo è disponibile tramite il client [Android Debug Bridge (ADB),](https://developer.android.com/tools/help/adb.html) che può essere scaricato come parte di Android SDK.

Per registrare lo schermo, collegare il dispositivo; quindi, individuare l'installazione di Android SDK, passare alla directory **platform-tools** ed eseguire il client **adb:**

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

Il comando precedente registrerà un video predefinito di 3 minuti alla risoluzione predefinita di 4Mbps. Per modificare la lunghezza, aggiungere il flag *--time-limit.*
Per modificare la risoluzione, aggiungere il flag *--bit-rate.* Il seguente comando registrerà un video di un minuto a 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Puoi trovare il tuo video sul tuo dispositivo - apparirà nella tua Galleria quando la registrazione è completa.

## <a name="other-kitkat-additions"></a>Altre aggiunte KitKat

Oltre alle modifiche descritte in precedenza, KitKat consente di:

- *Usa la modalità Schermo intero* - KitKat introduce una nuova [modalità Immersive](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) per l'esplorazione dei contenuti, la riproduzione di giochi e l'esecuzione di altre applicazioni che potrebbero trarre vantaggio da un'esperienza a schermo intero.

- *Personalizza notifiche* - Ottieni ulteriori dettagli sulle notifiche di sistema[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . In questo modo è possibile presentare le informazioni in modo diverso all'interno dell'app.

- *Mirror Drawable Resources* - Le risorse drawable hanno un nuovo[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  che indica al sistema di creare una versione speculare per le immagini che richiedono lo capovolgimento per i layout da sinistra a destra.

- *Pause Animations* - Sospendi e riprendi le animazioni create con[`Animator`](xref:Android.Animation.Animator)
  .

- *Leggi testo* a modifica dinamica - Denota parti dell'interfaccia utente che si aggiornano dinamicamente con il nuovo testo come "aree attive" con il nuovo[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  in modo che il nuovo testo venga letto automaticamente in modalità di accessibilità.

- *Migliorare l'esperienza audio* - Rendere le tracce più forti con il[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  , trovare il picco e RMS di un flusso audio con il[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  e ottenere informazioni da un [timestamp audio](xref:Android.Media.AudioTimestamp) per facilitare la sincronizzazione audio-video.

- *Sincronizza ContentResolver a intervalli personalizzati* - KitKat aggiunge una certa variabilità al momento in cui viene eseguita una richiesta di sincronizzazione. Sincronizzare `ContentResolver` un'ora o `ContentResolver.RequestSync` un intervallo `SyncRequest`personalizzati chiamando e passando un file .

- *Distingue Tra controller* - In KitKat, ai controller vengono assegnati `ControllerNumber` identificatori integer univoci a cui è possibile accedere tramite la proprietà del dispositivo. Questo rende più facile distinguere i giocatori in un gioco.

- *Controllo remoto* - Con alcune modifiche sia sul lato hardware che software, KitKat consente di trasformare un `ConsumerIrService`dispositivo dotato di un trasmettitore a iR in un telecomando utilizzando il , e interagire con i dispositivi periferici con il nuovo[`RemoteController`](xref:Android.Media.RemoteController)
  Api.

Per ulteriori informazioni sulle modifiche alle API di cui sopra, fai riferimento alla panoramica delle API di Google [Android 4.4.](https://developer.android.com/about/versions/android-4.4.html)

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto alcune delle nuove API disponibili in Android 4.4 (livello API 19) e ha trattato le procedure consigliate per la transizione di un'applicazione a KitKat.This article introduced some of the new APIs available in Android 4.4 (API Level 19), and covered best practices when transitioning an application to KitKat. Sono state descritte le modifiche alle API che influiscono sull'esperienza utente, incluso il framework di *transizione* e le nuove opzioni per i *tei.* Successivamente, è stato introdotto `DocumentsProvider` il Framework di *archiviazione-accesso* e la classe , nonché le nuove API di *stampa*. Ha esplorato *l'emulazione* di schede basate su host NFC e come lavorare con *sensori a bassa potenza,* inclusi due nuovi sensori per il monitoraggio dei passaggi dell'utente. Infine, ha dimostrato l'acquisizione di demo in tempo reale di applicazioni con *registrazione dello schermo*e ha fornito un elenco dettagliato delle modifiche e delle aggiunte all'API KitKat.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio KitKat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [API di Android 4.4](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
