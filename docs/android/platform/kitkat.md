---
title: Funzionalità di KitKat
description: Android 4,4 (KitKat) viene caricato con una cornucopia di funzionalità per utenti e sviluppatori. Questa guida mette in evidenza alcune di queste funzionalità e fornisce esempi di codice e dettagli di implementazione che consentono di sfruttare al meglio KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 3e68ac0a39d3268ce7c84f583c64b247e9f82362
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524192"
---
# <a name="kitkat-features"></a>Funzionalità di KitKat

_Android 4,4 (KitKat) viene caricato con una cornucopia di funzionalità per utenti e sviluppatori. Questa guida mette in evidenza alcune di queste funzionalità e fornisce esempi di codice e dettagli di implementazione che consentono di sfruttare al meglio KitKat._

## <a name="overview"></a>Panoramica

Android 4,4 (API level 19), noto anche come "KitKat", è stato rilasciato alla fine del 2013. KitKat offre un'ampia gamma di nuove funzionalità e miglioramenti, tra cui:

- [Esperienza utente](#user_experience) &ndash; Animazioni semplici con il Framework di transizione, le barre di spostamento e lo stato trasparenti e la modalità immersiva a schermo intero consentono di creare un'esperienza migliore per l'utente.

- [Contenuto utente](#user_content) &ndash; Gestione file utente semplificato con il Framework di accesso all'archiviazione; la stampa di immagini, siti Web e altro contenuto è più semplice grazie alle API di stampa migliorate.

- [Hardware](#hardware) Trasforma qualsiasi app in una scheda NFC con l' `SensorManager` emulazione di schede basata su host NFC; Esegui sensori a basso consumo con. &ndash;

- [Strumenti di sviluppo](#developer_tools) &ndash; Screencast sulle applicazioni in azione con il client di Android Debug Bridge, disponibile come parte del Android SDK.


Questa guida fornisce indicazioni per la migrazione di un'applicazione Novell. Android esistente a KitKat, oltre a una panoramica di alto livello di KitKat per sviluppatori Novell. Android.

## <a name="requirements"></a>Requisiti

Per sviluppare applicazioni Novell. Android con KitKat, è necessario *Novell. Android 4.11.0* o versione successiva e Android 4,4 (API level 19) installato tramite Android SDK Manager, come illustrato nello screenshot seguente:

[![Selezione di Android 4,4 in gestione Android SDK](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrazione dell'app a KitKat

Questa sezione fornisce alcuni elementi di prima risposta per facilitare la transizione delle applicazioni esistenti ad Android 4,4.

### <a name="check-system-version"></a>Verifica versione sistema

Se un'applicazione deve essere compatibile con le versioni precedenti di Android, assicurarsi di eseguire il wrapping di qualsiasi codice specifico di KitKat in un controllo della versione del sistema, come illustrato nell'esempio di codice seguente:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Batch di allarme

Android usa i servizi di allarme per riattivare un'app in background in un momento specifico. KitKat esegue ulteriormente questa operazione inviando in batch gli avvisi per mantenere l'energia. Ciò significa che, al posto di riattivare ogni app in un momento esatto, KitKat preferisce raggruppare diverse applicazioni registrate per la riattivazione durante lo stesso intervallo di tempo e riattivarle nello stesso momento.
Per indicare ad Android di riattivare un'app durante un intervallo di `SetWindow` tempo specificato [`AlarmManager`](xref:Android.App.AlarmManager), chiamare sul, passando il tempo minimo e massimo, in millisecondi, che può trascorrere prima che l'app venga riattivata e l'operazione da eseguire al riattivazione.
Il codice seguente fornisce un esempio di applicazione che deve essere riattivata tra mezz'ora e un'ora dal momento in cui la finestra è impostata:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Per continuare a riattivare un'app in un momento `SetExact`esatto, usare, passando l'ora esatta in cui l'app deve essere riattivata e l'operazione da eseguire:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat non consente più di impostare un allarme ripetuto esatto. Applicazioni che usano[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
e richiedono che gli avvisi esatti funzionino a questo punto, dovranno attivare manualmente ogni avviso.

### <a name="external-storage"></a>Archiviazione esterna

L'archiviazione esterna è ora divisa in due tipi, ovvero archiviazione univoca per l'applicazione e dati condivisi da più applicazioni. La lettura e la scrittura nel percorso specifico dell'app in una risorsa di archiviazione esterna non richiedono autorizzazioni speciali. L'interazione con i dati nell'archiviazione condivisa richiede ora `READ_EXTERNAL_STORAGE` l' `WRITE_EXTERNAL_STORAGE` autorizzazione o. I due tipi possono essere classificati come tali:

- Se si sta ottenendo un percorso di file o directory chiamando un metodo su `Context` , ad esempio,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
   o[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
   - l'app non richiede autorizzazioni aggiuntive.

- Se si sta ottenendo un percorso di file o directory accedendo a una proprietà o chiamando un metodo `Environment` su, ad esempio[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
   o[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
   , l'app richiede l' `READ_EXTERNAL_STORAGE` autorizzazione `WRITE_EXTERNAL_STORAGE` o.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE`implica l' `READ_EXTERNAL_STORAGE` autorizzazione, quindi è necessario impostare solo un'autorizzazione.

### <a name="sms-consolidation"></a>Consolidamento SMS

KitKat semplifica la messaggistica per l'utente aggregando tutto il contenuto SMS in un'applicazione predefinita selezionata dall'utente. Lo sviluppatore ha la responsabilità di rendere l'app selezionabile come applicazione di messaggistica predefinita e si comporta in modo appropriato nel codice e in vita se l'applicazione non è selezionata. Per altre informazioni sulla transizione dell'app SMS a KitKat, vedere la Guida introduttiva alle [app per SMS pronta per KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) da Google.

### <a name="webview-apps"></a>App WebView

[WebView](xref:Android.Webkit.WebView) ha ottenuto un rifacimento in KitKat. La modifica più importante è stata aggiunta alla sicurezza per il `WebView`caricamento di contenuto in un. Sebbene la maggior parte delle applicazioni destinate alle versioni precedenti dell'API funzioni come previsto, è `WebView` consigliabile testare le applicazioni che usano la classe. Per ulteriori informazioni sulle API WebView interessate, vedere la documentazione relativa alla [migrazione a WebView in android 4,4 in Android](https://developer.android.com/guide/webapps/migrating.html) .

<a name="user_experience" />

## <a name="user-experience"></a>Esperienza utente

KitKat include diverse nuove API per migliorare l'esperienza utente, incluso il nuovo Framework di transizione per la gestione delle animazioni delle proprietà e un'opzione di interfaccia utente traslucida per i temi. Queste modifiche sono descritte di seguito.

### <a name="transition-framework"></a>Framework di transizione

Il Framework di transizione rende le animazioni più facili da implementare. KitKat consente di eseguire un'animazione di proprietà semplice con una sola riga di codice oppure di personalizzare le transizioni usando le *scene*.

#### <a name="simple-property-animation"></a>Animazione di proprietà semplice

La nuova libreria di transizioni Android semplifica le animazioni delle proprietà code-behind. Il Framework consente di eseguire animazioni semplici con codice minimo. Ad esempio, l'esempio di codice seguente usa[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
per animare la visualizzazione e nascondere `TextView`un:

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

Nell'esempio precedente viene usato il Framework di transizione per creare una transizione automatica predefinita tra i valori delle proprietà modificabili. Poiché l'animazione viene gestita da una sola riga di codice, è possibile renderla facilmente compatibile con le versioni precedenti di Android eseguendo il `BeginDelayedTransition` wrapping della chiamata in un controllo della versione del sistema. Per ulteriori informazioni, vedere la sezione [migrazione dell'app a KitKat](#Migrating_Your_App_to_KitKat) .

La schermata seguente mostra l'app prima dell'animazione:

[![Screenshot dell'app prima dell'avvio dell'animazione](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La schermata seguente mostra l'app dopo l'animazione:

[![Screenshot dell'app dopo il completamento dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

È possibile ottenere un maggiore controllo sulla transizione con le scene, descritte nella sezione successiva.

#### <a name="android-scenes"></a>Scene Android

Le [scene](xref:Android.Transitions.Scene) sono state introdotte come parte del Framework di transizione per offrire agli sviluppatori maggiore controllo sulle animazioni. Scenes creare un'area dinamica nell'interfaccia utente: è possibile specificare un contenitore e diverse versioni, o "scene", per il contenuto XML all'interno del contenitore e Android esegue il resto del lavoro per animare le transizioni tra le scene. Le scene Android consentono di creare animazioni complesse con un lavoro minimo sul lato sviluppo.

L'elemento statico dell'interfaccia utente che ospita il contenuto dinamico è un oggetto denominato base del *contenitore* o della *scena*. Nell'esempio seguente viene usato il Android designer per creare `RelativeLayout` un `container`oggetto denominato:

[![Uso della Android Designer per creare un contenitore sul relativelayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

Il layout di esempio definisce anche un pulsante `sceneButton` denominato `container`sotto. Questo pulsante attiverà la transizione.

Il contenuto dinamico all'interno del contenitore richiede due nuovi layout Android. Questi layout specificano solo il codice *all'interno* del contenitore.
Il codice di esempio seguente definisce un layout denominato *Scene1* che contiene due campi di testo che leggono rispettivamente "kit" e "Kat" e un secondo layout denominato *scene2* che contiene gli stessi campi di testo invertiti. Il codice XML è il seguente:

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

 **Scene2. aXML**:

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

Nell'esempio precedente viene `merge` usato per rendere il codice di visualizzazione più breve e semplificare la gerarchia di visualizzazione. Per altre informazioni sui `merge` layout, vedere [qui](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Una scena viene creata [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*)chiamando, passando l'oggetto contenitore, l'ID risorsa del file di layout della scena e l'oggetto corrente `Context`, come illustrato nell'esempio di codice seguente:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Facendo clic sul pulsante si scorre tra le due scene, che Android anima con i valori di transizione predefiniti:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

La schermata seguente illustra la scena prima dell'animazione:

[![Screenshot dell'app prima dell'avvio dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

La schermata seguente illustra la scena dopo l'animazione:

[![Screenshot dell'app dopo il completamento dell'animazione](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> È presente un [bug noto](https://code.google.com/p/android/issues/detail?id=62450) nella libreria delle transizioni Android che fa sì che le `GetSceneForLayout` scene create con si interrompano quando un utente passa attraverso un'attività la seconda volta. Una soluzione alternativa Java è descritta [qui](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transizioni personalizzate in scenari

È possibile definire una transizione personalizzata in un file di risorse XML nella `transition` directory sotto `Resources`, come illustrato nello screenshot seguente:

[![Percorso del file Transition. XML in Resources/Transition directory](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

L'esempio di codice seguente definisce una transizione che aggiunge un'animazione per 5 secondi e usa l'[interpolatore di overshoot](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transizione viene creata nell'attività usando [TransitionInflater](xref:Android.Transitions.TransitionInflater), come illustrato nel codice seguente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nuova transizione viene quindi aggiunta alla `Go` chiamata che avvia l'animazione:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interfaccia utente traslucida

KitKat offre un maggiore controllo sulla gestione delle app con lo stato e le barre di navigazione facoltativi. È possibile modificare la traslucidità degli elementi dell'interfaccia utente del sistema nello stesso file XML usato per definire il tema Android. KitKat introduce le proprietà seguenti:

- `windowTranslucentStatus`-Se impostato su true, la barra di stato superiore diventa trasparente.

- `windowTranslucentNavigation`-Se impostato su true, la barra di spostamento inferiore diventa trasparente.

- `fitsSystemWindows`-Se si imposta la barra superiore o inferiore su transcluent, per impostazione predefinita il contenuto viene spostato sotto gli elementi trasparenti dell'interfaccia utente. L'impostazione di questa `true` proprietà su è un modo semplice per impedire la sovrapposizione di contenuto con gli elementi dell'interfaccia utente di sistema traslucidi.


Il codice seguente definisce un tema con lo stato e le barre di navigazione trasparenti:

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

La schermata seguente mostra il tema precedente con le barre di spostamento e lo stato di traslucido:

[![Schermata di esempio dell'app con lo stato e le barre di navigazione trasparenti](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenuto utente

### <a name="storage-access-framework"></a>Framework di accesso alla risorsa di archiviazione

Storage Access Framework (SAF) è un nuovo modo per consentire agli utenti di interagire con contenuto archiviato, ad esempio immagini, video e documenti. Anziché presentare agli utenti una finestra di dialogo per scegliere un'applicazione per la gestione del contenuto, KitKat apre una nuova interfaccia utente che consente agli utenti di accedere ai dati in un unico percorso di aggregazione. Una volta scelto il contenuto, l'utente tornerà all'applicazione che ha richiesto il contenuto e l'esperienza dell'app continuerà normalmente.

Questa modifica richiede due azioni sul lato sviluppatore: prima di tutto, le app che richiedono contenuto dai provider devono essere aggiornate a un nuovo modo di richiedere il contenuto. In secondo luogo, è `ContentProvider` necessario modificare le applicazioni che scrivono i dati in modo da usare il nuovo Framework. Entrambi gli scenari dipendono dalla nuova[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API.

#### <a name="documentsprovider"></a>DocumentsProvider

In KitKat le `DocumentsProvider` interazioni con `ContentProviders` sono astratte dalla classe. Questo significa che SAF non è interessato dalla posizione dei dati fisicamente, purché sia accessibile tramite l' `DocumentsProvider` API. I provider locali, i servizi cloud e i dispositivi di archiviazione esterni usano tutte la stessa interfaccia e vengono gestiti allo stesso modo, fornendo all'utente e allo sviluppatore un'unica posizione per interagire con il contenuto dell'utente.

Questa sezione illustra come caricare e salvare il contenuto con il Framework di accesso alle archiviazione.

#### <a name="request-content-from-a-provider"></a>Richiedi contenuto da un provider

È possibile indicare a KitKat che si vuole selezionare il contenuto usando l'interfaccia utente di `ActionOpenDocument` SAF con lo scopo, che significa che ci si vuole connettere a tutti i provider di contenuti disponibili per il dispositivo. È possibile aggiungere alcuni filtri a questa finalità specificando `CategoryOpenable`, il che significa che verrà restituito solo il contenuto che può essere aperto, ad esempio contenuto accessibile e utilizzabile. KitKat consente anche di filtrare il `MimeType`contenuto con. Ad esempio, il codice seguente Filtra i risultati dell'immagine specificando l' `MimeType`immagine:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

La `StartActivityForResult` chiamata a avvia l'interfaccia utente di SAF, che l'utente può quindi individuare per scegliere un'immagine:

[![Schermata di esempio di un'app che usa il Framework di accesso alla risorsa di archiviazione per l'esplorazione di un'immagine](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Dopo che l'utente ha scelto un'immagine `OnActivityResult` , restituisce `Android.Net.Uri` l'oggetto del file scelto. Nell'esempio di codice riportato di seguito viene visualizzata la selezione dell'immagine dell'utente:

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

#### <a name="write-content-to-a-provider"></a>Scrivere contenuto in un provider

Oltre a caricare contenuto dall'interfaccia utente di SAF, KitKat consente anche di salvare il contenuto in `ContentProvider` qualsiasi che implementi l' `DocumentProvider` API. Il salvataggio del contenuto `Intent` USA `ActionCreateDocument`un con:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

Nell'esempio di codice precedente viene caricata l'interfaccia utente di SAF, che consente all'utente di modificare il nome del file e di selezionare una directory in cui si trova il nuovo file:

[![Screenshot dell'utente che ha modificato il nome del file in NewDoc nella directory downloads](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando l'utente preme **Save** `OnActivityResult` , viene passato l'oggetto `Android.Net.Uri` del file appena creato, a cui è possibile accedere con `data.Data`. L'URI può essere usato per trasmettere i dati nel nuovo file:

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
Restituisce un `System.IO.Stream`oggetto, quindi l'intero processo di streaming può essere scritto in .NET.

Per altre informazioni sul caricamento, la creazione e la modifica del contenuto con il Framework di accesso alla risorsa di archiviazione, vedere la [documentazione di Android per il Framework di accesso alla](https://developer.android.com/guide/topics/providers/document-provider.html)risorsa di archiviazione.

### <a name="printing"></a>Stampa

Il contenuto di stampa è semplificato in KitKat con l'introduzione dei servizi `PrintManager`di [stampa](xref:Android.PrintServices) e. KitKat è anche la prima versione dell'API per sfruttare appieno le [API del servizio di stampa cloud di Google](https://developers.google.com/cloud-print/) usando l' [applicazione Google Cloud Print](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La maggior parte dei dispositivi forniti con KitKat Scarica automaticamente Google Cloud Print app e il plug-in del [servizio di stampa HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando si connette per la prima volta al Wi-Fi. Un utente può verificare le impostazioni di stampa del dispositivo passando a **impostazioni > sistema > stampa**:

[![Schermata di esempio della schermata impostazioni di stampa](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Sebbene le API di stampa siano configurate per l'uso con Google Cloud Print per impostazione predefinita, Android consente agli sviluppatori di preparare il contenuto di stampa usando le nuove API e di inviarlo ad altre applicazioni per gestire la stampa.



#### <a name="printing-html-content"></a>Stampa di contenuto HTML

KitKat crea automaticamente un [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) oggetto per una visualizzazione Web `WebView.CreatePrintDocumentAdapter`con. La stampa di contenuto Web è un lavoro coordinato tra un [`WebViewClient`](xref:Android.Webkit.WebViewClient) che attende il caricamento del contenuto HTML e consente all'attività di rendere disponibile l'opzione di stampa nel menu opzioni e l'attività, che attende che l'utente selezioni l'opzione di stampa e chiami `Print`nell' .`PrintManager` In questa sezione viene illustrata la configurazione di base necessaria per stampare contenuto HTML a schermo intero.

Si noti che il caricamento e la stampa del contenuto Web richiede l'autorizzazione Internet:

[![Impostazione dell'autorizzazione Internet nelle opzioni dell'app](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Voce di menu Stampa

L'opzione Print viene in genere visualizzata nel menu delle [Opzioni](https://developer.android.com/guide/topics/ui/menus.html#options-menu)dell'attività.
Il menu opzioni consente agli utenti di eseguire azioni su un'attività. Si trova nell'angolo superiore destro dello schermo e ha un aspetto simile al seguente:

[![Schermata di esempio della voce di menu Stampa visualizzata nell'angolo superiore destro della schermata](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


È possibile definire altre voci di menu nella directory *menu*sotto *risorse*. Il codice seguente definisce una voce di menu di esempio denominata [Print](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

L'interazione con il menu opzioni nell'attività viene eseguita tramite `OnCreateOptionsMenu` i `OnOptionsItemSelected` metodi e.
`OnCreateOptionsMenu`è la posizione in cui aggiungere nuove voci di menu, ad esempio l'opzione stampa, dalla directory delle risorse di *menu* .
`OnOptionsItemSelected`Ascolta l'utente che seleziona l'opzione di stampa dal menu e inizia la stampa:

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

Il codice precedente definisce anche una variabile denominata `dataLoaded` per tenere traccia dello stato del contenuto HTML. `WebViewClient` Imposta questa variabile su true quando tutto il contenuto è stato caricato, quindi l'attività sa aggiungere la voce di menu stampa al menu opzioni.

##### <a name="webviewclient"></a>WebViewClient

Il processo di `WebViewClient` consiste nel garantire che i dati `WebView` in vengano caricati completamente prima che l'opzione Print venga visualizzata nel menu, operazione eseguita con il `OnPageFinished` metodo. `OnPageFinished`resta in attesa del completamento del caricamento del contenuto Web e indica all'attività di ricreare il menu delle `InvalidateOptionsMenu`opzioni con:

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

`OnPageFinished`imposta anche il `dataLoaded` valore su `true`, in `OnCreateOptionsMenu` modo che sia possibile ricreare il menu con l'opzione di stampa sul posto.

##### <a name="printmanager"></a>PrintManager

Nell'esempio di codice seguente viene stampato il contenuto `WebView`di un oggetto:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`accetta come argomenti: un nome per il processo di stampa ("MyWebPage" in questo esempio), un[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
che genera il documento di stampa dal contenuto e[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null` nell'esempio precedente). È possibile specificare `PrintAttributes` per facilitare la disposizione del contenuto nella pagina stampata, sebbene gli attributi predefiniti debbano gestire la maggior parte degli scenari.

La `Print` chiamata di carica l'interfaccia utente di stampa, che elenca le opzioni per il processo di stampa. L'interfaccia utente offre agli utenti la possibilità di stampare o salvare il contenuto HTML in un file PDF, come illustrato negli screenshot seguenti:

[![Schermata di PrintHtmlActivity visualizzazione del menu Stampa](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Schermata di PrintHtmlActivity visualizzazione del menu Salva con nome PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat aggiunge diverse API per supportare le nuove funzionalità del dispositivo. Le più importanti sono l'emulazione di schede basate su host e la nuova `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulazione di schede basate su host in NFC

L'emulazione di schede basate su host (HCE) consente alle applicazioni di comportarsi come schede NFC o lettori di schede NFC senza basarsi sull'elemento protetto proprietario del vettore. Prima di configurare HCE, verificare che HCE sia disponibile nel dispositivo con `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE richiede che sia la funzionalità HCE che l' `Nfc` autorizzazione siano registrate con l' `AndroidManifest.xml`applicazione:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Impostazione dell'autorizzazione NFC nelle opzioni dell'app](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Per funzionare, HCE deve poter essere eseguito in background e deve essere avviato quando l'utente esegue una transazione NFC, anche se l'applicazione che usa HCE non è in esecuzione. A tale scopo, è possibile scrivere il codice HCE come `Service`. Un servizio HCE implementa l' `HostApduService` interfaccia, che implementa i metodi seguenti:

- *ProcessCommandApdu* : un'unità dati del protocollo dell'applicazione (APDU) è ciò che viene inviato tra il lettore NFC e il servizio HCE. Questo metodo usa un ADPU dal reader e restituisce un'unità dati in risposta.

- *OnDeactivated* : il `HostAdpuService` viene disattivato quando il servizio HCE non comunica più con il lettore NFC.


Un servizio HCE deve anche essere registrato con il manifesto dell'applicazione e decorato con le autorizzazioni appropriate, il filtro preventivo e i metadati. Il codice seguente è un esempio di `HostApduService` registrato con il manifesto Android usando l' `Service` attributo. per altre informazioni sugli attributi, vedere la guida del manifesto Novell [Working with Android](~/android/platform/android-manifest.md) :

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

Il servizio precedente consente al lettore NFC di interagire con l'applicazione, ma il lettore NFC non è ancora in grado di sapere se il servizio emula la scheda NFC necessaria per l'analisi. Per consentire al lettore NFC di identificare il servizio, è possibile assegnare al servizio un *ID applicazione univoco (supporto)* . Viene specificato un supporto, insieme ad altri metadati relativi al servizio HCE, in un file di risorse XML registrato con `MetaData` l'attributo (vedere l'esempio di codice precedente). Questo file di risorse specifica uno o più filtri di supporto-stringhe identificatore univoche in formato esadecimale che corrispondono ai supporti di uno o più dispositivi di lettore NFC:

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

Oltre ai filtri, il file di risorse XML fornisce anche una descrizione per l'utente del servizio HCE, specifica un gruppo di supporto (applicazione di pagamento rispetto a "altro") e, nel caso di un'applicazione di pagamento, un banner 260x96 DP da visualizzare all'utente.

Il programma di installazione illustrato in precedenza fornisce i blocchi predefiniti di base per un'applicazione che emula una scheda NFC. NFC richiede diversi passaggi e ulteriori test da configurare. Per altre informazioni sull'emulazione di schede basate su host, vedere il [portale della documentazione di Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Per altre informazioni sull'uso di NFC con Novell, vedere gli [esempi di Novell NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensori

KitKat consente di accedere ai sensori del dispositivo tramite un [`SensorManager`](xref:Android.Hardware.SensorManager).
`SensorManager` Consente al sistema operativo di pianificare il recapito delle informazioni del sensore a un'applicazione in batch, conservando la durata della batteria.

KitKat viene fornito anche con due nuovi tipi di sensore per tenere traccia dei passaggi dell'utente. Si basano sull'accelerometro e includono:

- *StepDetector* -l'app viene notificata/svegliata quando l'utente esegue un passaggio e il rilevatore fornisce un valore di ora per il momento in cui si è verificato il passaggio.

- *StepCounter* : tiene traccia del numero di passaggi eseguiti dall'utente dopo la registrazione del sensore *fino al riavvio successivo del dispositivo*.

Lo screenshot seguente illustra il contatore dei passaggi in azione:

[![Screenshot dell'app SensorsActivity che visualizza un contatore dei passaggi](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

È possibile creare un `SensorManager` oggetto `GetSystemService(SensorService)` chiamando ed `SensorManager`eseguendo il cast del risultato come. Per usare il contatore dei passaggi, `GetDefaultSensor` chiamare `SensorManager`su. È possibile registrare il sensore e restare in ascolto delle modifiche apportate al numero di passaggi con l'aiuto del[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
interfaccia, come illustrato nell'esempio di codice seguente:

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

`OnSensorChanged`viene chiamato se il conteggio dei passaggi viene aggiornato mentre l'applicazione è in primo piano. Se l'applicazione viene attivata in background o il dispositivo è in stato `OnSensorChanged` di sospensione, non verrà chiamato. Tuttavia, i passaggi continueranno a essere `UnregisterListener` conteggiati fino a quando non viene chiamato il metodo.

Tenere presente che *il valore del numero di passaggi è cumulativo in tutte le applicazioni che registrano il sensore*. Ciò significa che anche se si disinstalla e si reinstalla l'applicazione e si inizializza `count` la variabile su 0 all'avvio dell'applicazione, il valore restituito dal sensore rimarrà il numero totale di passaggi eseguiti durante la registrazione del sensore, indipendentemente dal fatto che il applicazione o un'altra. È possibile impedire l'aggiunta dell'applicazione al contatore dei passaggi chiamando `UnregisterListener` `SensorManager`su, come illustrato nel codice seguente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Il riavvio del dispositivo Reimposta il numero di passaggi su 0. L'app richiede codice aggiuntivo per assicurarsi che venga segnalato un conteggio accurato per l'applicazione, indipendentemente dalle altre applicazioni che usano il sensore o lo stato del dispositivo.


> [!NOTE]
> Mentre l'API per il rilevamento dei passaggi e il conteggio viene fornita con KitKat, non tutti i telefoni sono dotati del sensore. È possibile verificare se il sensore è disponibile `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`eseguendo oppure verificare che il valore restituito di `GetDefaultSensor` non sia `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Strumenti di sviluppo

### <a name="screen-recording"></a>Registrazione dello schermo

KitKat include nuove funzionalità di registrazione dello schermo che consentono agli sviluppatori di registrare le applicazioni in azione. La registrazione dello schermo è disponibile tramite il client [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) , che può essere scaricato come parte del Android SDK.

Per registrare lo schermo, connettere il dispositivo; individuare quindi l'installazione di Android SDK, passare alla directory **degli strumenti della piattaforma** ed eseguire il client **ADB** :

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

Il comando precedente registrerà un video di 3 minuti predefinito con la risoluzione predefinita di 4Mbps. Per modificare la lunghezza, aggiungere il flag *--limite di tempo* .
Per modificare la risoluzione, aggiungere il flag di *velocità in bit* . Il comando seguente registrerà un video di minuti a 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

È possibile trovare il video nel dispositivo che verrà visualizzato nella raccolta al termine della registrazione.


## <a name="other-kitkat-additions"></a>Altre aggiunte KitKat

Oltre alle modifiche descritte in precedenza, KitKat consente di:

- *Usare lo schermo intero* : KitKat introduce una nuova [modalità immersiva](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) per l'esplorazione del contenuto, la riproduzione di giochi e l'esecuzione di altre applicazioni che possono trarre vantaggio da un'esperienza a schermo intero.

- *Personalizzare le notifiche* : ottenere ulteriori dettagli sulle notifiche di sistema con il[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
   . Questo consente di presentare le informazioni in modo diverso all'interno dell'app.

- Risorse di cui è possibile *creare mirror* : le risorse di cui è possibile creare un nuovo[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
   attributo che indica al sistema di creare una versione con mirroring per le immagini che richiedono il capovolgimento per i layout da sinistra a destra.

- *Sospendere le animazioni* : sospendere e riprendere le animazioni create con il[`Animator`](xref:Android.Animation.Animator)
   .

- *Lettura testo in modifica dinamica* : indica le parti dell'interfaccia utente che vengono aggiornate dinamicamente con il nuovo testo come "aree attive" con il nuovo[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   attributo in modo che il nuovo testo venga letto automaticamente in modalità di accessibilità.

- *Migliorare l'esperienza audio* : è più potente con il[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
   , trovare il picco e RMS di un flusso audio con il[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
   e ottenere informazioni da un [timestamp audio](xref:Android.Media.AudioTimestamp) per semplificare la sincronizzazione del video audio.

- *Sync ContentResolver at Custom Interval* : KitKat aggiunge una certa variabilità al momento in cui viene eseguita una richiesta di sincronizzazione. Sincronizzare un `ContentResolver` oggetto all'ora o all'intervallo personalizzato `ContentResolver.RequestSync` chiamando e passando un `SyncRequest`oggetto.

- *Distinzione tra controller* : in KitKat, ai controller vengono assegnati identificatori di tipo integer univoci a cui è possibile `ControllerNumber` accedere tramite la proprietà del dispositivo. In questo modo è più semplice distinguere tra i diversi giocatori in un gioco.

- *Controllo remoto* : con alcune modifiche sul lato hardware e software, KitKat consente di trasformare un dispositivo fornito con un trasmettitore IR in un controllo remoto usando il `ConsumerIrService`e di interagire con i dispositivi periferici con il nuovo[`RemoteController`](xref:Android.Media.RemoteController)
   API.

Per altre informazioni sulle modifiche apportate all'API, vedere la panoramica delle API di Google [Android 4,4](https://developer.android.com/about/versions/android-4.4.html) .


## <a name="summary"></a>Riepilogo

In questo articolo sono state introdotte alcune delle nuove API disponibili in Android 4,4 (API level 19) e sono state descritte le procedure consigliate per la transizione di un'applicazione a KitKat. Sono state descritte le modifiche apportate alle API che influiscono sull'esperienza utente, tra cui il *Framework di transizione* e le nuove opzioni per il *tema*. Successivamente, è stata introdotta la classe e `DocumentsProvider` il Framework di accesso alla risorsa di *archiviazione* , nonché le nuove API di *stampa*. Ha esplorato l'emulazione delle *schede basata su host NFC* e come usare i *sensori a basso consumo*, inclusi due nuovi sensori per tenere traccia dei passaggi dell'utente. Infine, è stato illustrato come acquisire demo in tempo reale delle applicazioni con la *registrazione dello schermo*e fornito un elenco dettagliato delle modifiche e delle aggiunte dell'API KitKat.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio KitKat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [API Android 4,4](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
