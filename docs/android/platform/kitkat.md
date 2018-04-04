---
title: Funzionalità KitKat
description: Android 4.4 (KitKat) viene caricato con numerose funzionalità per utenti e sviluppatori. Questa guida vengono evidenziate alcune di queste funzionalità e vengono forniti esempi di codice e i dettagli di implementazione che consentono di sfruttare al meglio KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 3c3eafc8dc18113080dd6c906025556292c43e1c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="kitkat-features"></a>Funzionalità KitKat

_Android 4.4 (KitKat) viene caricato con numerose funzionalità per utenti e sviluppatori. Questa guida vengono evidenziate alcune di queste funzionalità e vengono forniti esempi di codice e i dettagli di implementazione che consentono di sfruttare al meglio KitKat._

## <a name="overview"></a>Panoramica

Android 4.4 (API livello 19), noto anche come "KitKat", è stato rilasciato nel 2013 ad associazione tardiva. KitKat offre un'ampia gamma di nuove funzionalità e miglioramenti, tra cui:

-  [Esperienza utente](#user_experience) &ndash; animazioni semplice con framework di transizione, semitrasparente barre di stato e di navigazione e modalità a schermo intero coinvolgente aiutare a creare un'esperienza migliore per l'utente.

-  [Contenuto utente](#user_content) &ndash; la gestione di file utente semplificata con framework di accesso di archiviazione, stampa di immagini, siti web e altri contenuti è più semplice con le API di stampa migliorata.

-  [Hardware](#hardware) &ndash; trasformare qualsiasi applicazione in una scheda NFC con NFC basata su Host di emulazione della scheda; eseguire sensori di basso consumo con il `SensorManager` .

-  [Gli strumenti di sviluppo](#developer_tools) &ndash; applicazioni Screencast nell'azione con il client di Bridge Debug Android, disponibile come parte di Android SDK.


Questa guida fornisce indicazioni per la migrazione di un'applicazione esistente xamarin KitKat, nonché una panoramica generale di KitKat per gli sviluppatori di xamarin.

## <a name="requirements"></a>Requisiti

Per sviluppare applicazioni di xamarin utilizzando KitKat, è necessario *xamarin 4.11.0* o versione successiva e Android 4.4 (API livello 19) installato mediante Android SDK Manager, come illustrato nella schermata seguente:

[![Selezione di Android 4.4 in Android SDK Manager](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrazione dell'App a KitKat

In questa sezione fornisce alcuni elementi della prima risposta per la transizione delle applicazioni esistenti per Android 4.4.

### <a name="check-system-version"></a>Controllare la versione del sistema

Se un'applicazione deve essere compatibile con le versioni precedenti di Android, assicurarsi di eseguire il wrapping di qualsiasi codice KitKat specifico in un controllo della versione del sistema, come illustrato nell'esempio di codice riportato di seguito:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>L'invio in batch di allarme

Android utilizza servizi di allarme per riattivare un'app in background in un momento specificato. KitKat esegue un ulteriore passaggio raggruppando allarmi per mantenere l'alimentazione. Ciò significa che, anziché riattivazione ogni app in un momento esatto, KitKat si preferisce raggruppare diverse applicazioni che vengono registrate per la riattivazione durante lo stesso intervallo di tempo e riattivarli nello stesso momento.
Per indicare Android per riattivare un'app durante un intervallo di tempo specificato, chiamare `SetWindow` sul [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), passando il valore minimo e il tempo massimo, espresso in millisecondi, che può trascorrere prima che l'app viene riattivato e l'operazione da eseguire all'attivazione.
Il codice seguente viene fornito un esempio di un'applicazione che deve essere riattivati tra mezz'ora e un'ora dal momento in cui che la finestra è impostata:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Per continuare la riattivazione di un'app in un momento esatto, utilizzare `SetExact`, passando l'ora esatta in cui l'app deve essere riattivato e l'operazione da eseguire:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat non consente di impostare un allarme ripetuto esatto. Le applicazioni che utilizzano [ `SetRepeating` ](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/) e richiedono allarmi esatto funzionamento sarà necessario attivare manualmente ogni allarme.

### <a name="external-storage"></a>Archiviazione esterna

Archiviazione esterna ora è suddivisa in due tipi: archiviazione univoco per l'applicazione e dati condivisi da più applicazioni. Lettura e scrittura al percorso specifico dell'app nella risorsa di archiviazione esterna non richiede autorizzazioni speciali. Richiede l'interazione con i dati nell'archiviazione condivisa ora il `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` autorizzazione. È possibile classificare i due tipi di conseguenza:

-  Se si riceve un percorso del file o directory chiamando un metodo su `Context` , ad esempio [ `GetExternalFilesDir` ](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/) o [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - l'app non richiede alcuna autorizzazione aggiuntive.

-  Se si riceve un percorso del file o directory per l'accesso a una proprietà o chiamare un metodo su `Environment` , ad esempio [ `GetExternalStorageDirectory` ](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/) o [ `GetExternalStoragePublicDirectory` ](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/) , l'app richiede il `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` autorizzazione.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implica il `READ_EXTERNAL_STORAGE` autorizzazione, pertanto è necessario solo impostare un'autorizzazione.

### <a name="sms-consolidation"></a>Consolidamento di SMS

KitKat semplifica la messaggistica per l'utente mediante l'aggregazione di tutto il contenuto SMS nell'applicazione di un valore predefinito selezionato dall'utente. Lo sviluppatore è responsabile per rendere l'app selezionabili come l'applicazione di messaggistica predefinita e si comporta in modo appropriato nel codice e nella vita se l'applicazione non è selezionata. Per ulteriori informazioni sull'app SMS in KitKat in fase di transizione, vedere il [di SMS App preparazione per KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) Guida da Google.

### <a name="webview-apps"></a>App WebView

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) ottenuta una trasformazione in KitKat. La più importante modifica viene aggiunta la protezione per il caricamento del contenuto in un `WebView`. Se la maggior parte delle applicazioni destinate a versioni precedenti di API devono funzionare come previsto, testing delle applicazioni che utilizzano il `WebView` consiglia di classe. Per ulteriori informazioni sulle APIs WebView interessato fare riferimento per il Android [la migrazione a WebView in Android 4.4](http://developer.android.com/guide/webapps/migrating.html) documentazione.

<a name="user_experience" />

## <a name="user-experience"></a>Esperienza utente

KitKat include diverse nuove API per migliorare l'esperienza utente, incluso il nuovo framework di transizione per la gestione delle animazioni di proprietà e un'opzione dell'interfaccia utente trasparente per i temi. Queste modifiche sono illustrate più avanti.

### <a name="transition-framework"></a>Framework di transizione

Il framework di transizione rende più semplice implementare le animazioni. KitKat consente di eseguire un'animazione di proprietà semplice con una sola riga di codice o personalizzare le transizioni che utilizzano *scene*.

#### <a name="simple-property-animation"></a>Animazione di proprietà semplice

La nuova raccolta di transizioni Android semplifica il code-behind animazioni di proprietà. Il framework consente di eseguire semplici animazioni con una quantità minima di codice. Ad esempio, il codice seguente viene utilizzata [ `TransitionManager.BeginDelayedTransition` ](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/) animare mostrare e nascondere un `TextView`:

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

L'esempio precedente Usa il framework di transizione per creare un automatico, la transizione predefinita tra i valori delle proprietà modificabile. Poiché l'animazione viene gestito da una singola riga di codice, è possibile apportare facilmente questo compatibile con le versioni precedenti di Android eseguendo il wrapping di `BeginDelayedTransition` chiamare in un controllo della versione del sistema. Vedere il [la migrazione dell'App per KitKat](#Migrating_Your_App_to_KitKat) sezione per ulteriori informazioni.

La schermata seguente mostra l'app prima dell'animazione:

[![Schermata di App prima dell'inizio di animazione](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

La schermata seguente mostra l'app dopo l'animazione:

[![Schermata di App dopo il completamento dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

È possibile ottenere maggiore controllo sulla transizione con scene, che sono descritti nella sezione successiva.

#### <a name="android-scenes"></a>Scene Android

[Scene](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) sono stati introdotti come parte del framework di transizione per consentire allo sviluppatore più preciso delle animazioni. Scene di creare un'area dinamica nell'interfaccia utente: specificare un contenitore e più versioni o "quinte", per il contenuto XML all'interno del contenitore, e Android farà il resto del lavoro per le transizioni tra le scene di animazione. Android scene consentono di creare animazioni complesse con uno sforzo minimo sul lato di sviluppo.

Elemento dell'interfaccia utente statico che ospita il contenuto dinamico è un oggetto denominato un *contenitore* o *scena base*. L'esempio seguente usa la finestra di progettazione Android per creare un `RelativeLayout` chiamato `container`:

[![Utilizzo di progettazione Android per creare un contenitore RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

Il layout di esempio definisce anche un pulsante chiamato `sceneButton` sotto il `container`. Questo pulsante verrà attivata la transizione.

Il contenuto dinamico all'interno del contenitore richiede due nuovi layout Android. Questi layout specificare solo il codice *all'interno di* il contenitore.
Nell'esempio di codice seguente definisce un layout denominato *Scene1* contenente due campi di testo durante la lettura "Kit" e "Kat" rispettivamente, e un layout secondo chiamato *Scene2* che contiene i campi di testo stesso invertiti. Il codice XML è il seguente:

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

Nell'esempio precedente viene `merge` per rendere il codice di visualizzazione più breve e semplificare la gerarchia di visualizzazione. Ulteriori informazioni su `merge` layout [qui](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Viene creata una scena chiamando [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), passando l'oggetto contenitore, l'ID di risorsa del file di layout della scena e corrente `Context`, come illustrato nell'esempio di codice riportato di seguito:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Fare clic sul pulsante consente di capovolgere tra i due scene, Android aggiunge un'animazione con i valori di transizione predefiniti:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

La schermata seguente illustra la scena prima l'animazione:

[![Schermata dell'app prima dell'inizio dell'animazione](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

La schermata seguente illustra la scena dopo l'animazione:

[![Schermata dell'app dopo il completamento dell'animazione](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> È presente un [bug noto](https://code.google.com/p/android/issues/detail?id=62450) in Android le transizioni di libreria che causa scene creati utilizzando `GetSceneForLayout` per interrompere l'esecuzione quando un utente passa da un'attività la seconda volta. È descritta una soluzione alternativa java [qui](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transizioni personalizzate nelle scene

Può essere definita in un file di risorse xml in una transizione personalizzata di `transition` directory sotto `Resources`, come illustrato nella schermata riportata di seguito:

[![Percorso del file transition.xml nella directory di risorse/transizione](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

Esempio di codice seguente definisce una transizione che aggiunge un'animazione per 5 secondi e Usa il [superamento interpolatore](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transizione viene creata nell'attività utilizzando la [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), come illustrato nel codice seguente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nuova transizione viene quindi aggiunto al `Go` chiamata che inizia l'animazione:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interfaccia utente semitrasparente

KitKat consente un maggiore controllo su temi dell'app con barre di stato e navigazione transclucent facoltativo. È possibile modificare la traslucidità di elementi dell'interfaccia utente di sistema nello stesso file XML utilizzato per definire il tema di Android. KitKat introduce le seguenti proprietà:

-  `windowTranslucentStatus` -Se impostato su true, effettua la barra di stato superiore semitrasparente.

-  `windowTranslucentNavigation` -Se impostata su true, effettua la barra di spostamento inferiore semitrasparente.

-  `fitsSystemWindows` -Impostazione barra superiore o inferiore a transcluent sposta il contenuto di elementi dell'interfaccia utente trasparenti per impostazione predefinita. Impostando questa proprietà su `true` è un modo semplice per impedire il contenuto di sovrapposizione con gli elementi dell'interfaccia utente di sistema semitrasparente.


Il codice seguente definisce un tema con barre di stato e navigazione semitrasparente:

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

La schermata seguente mostra il tema in precedenza con stato semitrasparente e barre di navigazione:

[![Schermata di esempio di app con barre di stato e navigazione semitrasparente](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenuto dell'utente

### <a name="storage-access-framework"></a>Framework di accesso di archiviazione

Il Framework di accesso di archiviazione (SAF) è un nuovo modo per gli utenti di interagire con il contenuto archiviato, ad esempio immagini, video e documenti. Anziché presentare agli utenti con una finestra di dialogo per scegliere un'applicazione per gestire il contenuto, KitKat apre una nuova interfaccia utente che consente agli utenti di accedere ai dati in un'unica posizione di aggregazione. Dopo il contenuto è stato scelto, l'utente verrà restituito all'applicazione che ha richiesto il contenuto e l'esperienza app continuerà normalmente.

Questa modifica richiede due azioni sul lato developer: in primo luogo, le app che richiedono il contenuto da provider devono essere aggiornati a un nuovo modo di reqesting contenuto. In secondo luogo, applicazioni che scrivono dati in un `ContentProvider` devono essere modificate per utilizzare il nuovo framework. Entrambi questi scenari dipendono dal nuovo [ `DocumentsProvider` ](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/) API.

#### <a name="documentsprovider"></a>DocumentsProvider

In KitKat, le interazioni con `ContentProviders` vengono ricavati con la `DocumentsProvider` classe. Ciò significa che SAF non è rilevante in cui i dati sono fisicamente, fino a quando è accessibile attraverso il `DocumentsProvider` API. Provider locali, servizi cloud e dispositivi di archiviazione esterni utilizzano la stessa interfaccia e vengono trattata nello stesso modo, fornendo l'utente e lo sviluppatore con un'unica posizione per interagire con il contenuto dell'utente.

Questa sezione descrive come caricare e salvare il contenuto con il Framework di accesso di archiviazione.

#### <a name="request-content-from-a-provider"></a>Contenuto della richiesta da un Provider

È possibile sapere che si desidera selezionare contenuto utilizzando la UI SAF con KitKat il `ActionOpenDocument` finalità, che indica che si desidera connettersi a tutti i provider di contenuto disponibili per il dispositivo. È possibile aggiungere un filtro per questa finalità specificando `CategoryOpenable`, ovvero solo il contenuto che può essere aperto (ad esempio accessibile e utilizzabile contenuto) verrà restituito. KitKat consente anche il filtraggio del contenuto con il `MimeType`. Ad esempio, il codice seguente i filtri per i risultati di immagine, specificando l'immagine `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

La chiamata `StartActivityForResult` avvia la UI SAF, quale l'utente può passare quindi a scegliere un'immagine:

[![Schermata di esempio di un'app usando il Framework di accesso di archiviazione per la selezione di un'immagine](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Dopo che l'utente ha scelto di un'immagine, `OnActivityResult` restituisce il `Android.Net.Uri` di file scelto. Esempio di codice seguente consente di visualizzare la selezione di immagini dell'utente:

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

#### <a name="write-content-to-a-provider"></a>Scrivere il contenuto di un Provider

Oltre a caricare contenuto dalla UI SAF, KitKat consente inoltre di salvare il contenuto a qualsiasi `ContentProvider` che implementa il `DocumentProvider` API. Salvataggio del contenuto viene utilizzato un `Intent` con `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

L'esempio di codice precedente consente di caricare la UI SAF, consentendo all'utente di modificare il nome di file e selezionare una directory per ospitare il nuovo file.

[![Schermata dell'utente di modificare il nome del file in NewDoc nella directory di download](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando l'utente preme **salvare**, `OnActivityResult` viene passata la `Android.Net.Uri` del file appena creato, che è possibile accedere con `data.Data`. L'uri può essere utilizzato per trasmettere dati nel nuovo file:

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

Si noti che [ `ContentResolver.OpenOutputStream(Android.Net.Uri)` ](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri)) restituisce un `System.IO.Stream`, pertanto l'intero processo di streaming può essere scritto in .NET.

Per ulteriori informazioni sul caricamento, la creazione e modifica del contenuto con il Framework di accesso di archiviazione, consultare il [Android documentazione per il Framework di accesso di archiviazione](http://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Stampa

Contenuto di stampa viene semplificato in KitKat con l'introduzione del [servizi di stampa](https://developer.xamarin.com/api/namespace/Android.PrintServices/) e `PrintManager`. KitKat è anche la prima versione dell'API di sfruttare completamente la [API del servizio Google Cloud stampa](https://developers.google.com/cloud-print/) utilizzando il [applicazione Google Cloud stampa](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La maggior parte dei dispositivi dotati di KitKat automaticamente scaricare app di Google Cloud stampa e [plug-in servizi di stampa HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando è necessario prima connettersi al Wi-Fi. Un utente può controllare le impostazioni di stampa la propria del dispositivo passando alla **Impostazioni > sistema > Stampa**:

[![Schermata di esempio di schermata delle impostazioni di stampa](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Benché le API di stampare siano impostate per funzionare con Google Cloud stampare per impostazione predefinita, Android ancora consente agli sviluppatori di preparare il contenuto di stampato utilizzando le nuove API e inviarla ad altre applicazioni per gestire la stampa.



#### <a name="printing-html-content"></a>Stampa il contenuto HTML

KitKat crea automaticamente un [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) per una visualizzazione web con `WebView.CreatePrintDocumentAdapter`. Contenuto web di stampa è collaborazione tra un [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) che attende il contenuto HTML da caricare e consente di conoscere a disposizione l'opzione di stampa dal menu Opzioni di attività e Actvity, di cui è in attesa per l'utente Selezionare l'opzione di stampa e chiama `Print`sul `PrintManager`. Questa sezione descrive la configurazione di base necessaria per stampare sullo schermo contenuto HTML.

Si noti che il caricamento e stampa il contenuto web richiede l'autorizzazione di Internet:

[![L'impostazione di autorizzazioni Internet nelle opzioni di app](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Voce di Menu Stampa

L'opzione di stampa verrà visualizzato in genere dell'attività [menu delle opzioni](http://developer.android.com/guide/topics/ui/menus.html#options-menu).
Il menu di opzioni consente agli utenti di eseguire azioni su un'attività. È in alto a destra dello schermo ed è simile al seguente:

[![Schermata di esempio di applicato all'intero elemento di menu Stampa nell'angolo superiore destro dello schermo](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Voci di menu aggiuntivi possono essere definite nel *menu*directory sotto *risorse*. Il codice seguente definisce un menu di esempio chiamato [stampa](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

L'interazione con il menu di opzioni dell'attività avviene tramite il `OnCreateOptionsMenu` e `OnOptionsItemSelected` metodi.
`OnCreateOptionsMenu` è possibile aggiungere nuove voci di menu, analogamente all'opzione di stampa, dal *menu* directory delle risorse.
`OnOptionsItemSelected` è in ascolto per l'utente selezionando l'opzione di stampa dal menu e avvia la stampa:

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

Il codice precedente definisce anche una variabile denominata `dataLoaded` per tenere traccia dello stato del contenuto HTML. Il `WebViewClient` imposterà questa variabile su true quando tutto il contenuto è stato caricato, pertanto l'attività è in grado di aggiungere la voce di menu stampa al menu di opzioni.

##### <a name="webviewclient"></a>WebViewClient

Il processo della `WebViewClient` consiste nel verificare che i dati nel `WebView` è stato caricato completamente prima che venga visualizzata l'opzione di stampa nel menu, che avviene il `OnPageFinished` (metodo). `OnPageFinished` è in ascolto per il contenuto web completare il caricamento e indica l'attività per ricreare il relativo menu di opzioni con `InvalidateOptionsMenu`:

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

`OnPageFinished` Imposta inoltre il `dataLoaded` valore `true`, pertanto `OnCreateOptionsMenu` possibile ricreare il menu con l'opzione di stampa sul posto.

##### <a name="printmanager"></a>PrintManager

Esempio di codice seguente stampa il contenuto di un `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` accetta come argomenti: un nome per il processo di stampa ("MyWebPage" in questo esempio), un [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) che genera il documento di stampa dal contenuto, e [ `PrintAttributes` ](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/) (`null` nel esempio precedente). È possibile specificare `PrintAttributes` consentono di definire il layout del contenuto della pagina stampata, anche se gli attributi predefiniti devono gestire la maggior parte delle scenarions.

La chiamata `Print` carica l'interfaccia utente di stampa, che elenca le opzioni per il processo di stampa. L'interfaccia utente consente agli utenti la possibilità di stampa o il salvataggio del contenuto HTML in un file PDF, come illustrato schermate riportate di seguito:

[![Schermata di PrintHtmlActivity visualizzato il menu di stampa](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Schermata di PrintHtmlActivity la visualizzazione di salvataggio come menu PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat aggiunge diverse API per supportare le nuove funzionalità di dispositivo. Il più importante di queste sono basate su Host di emulazione della scheda e il nuovo `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulazione di Smart Card basate su host in NFC

Basato su host scheda emulazione (HCE) consente alle applicazioni di comportarsi come schede NFC o lettori di smart card NFC senza basarsi sull'elemento Secure proprietaria del vettore. Prima di configurare HCE, verificare HCE è disponibile nel dispositivo con `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE richiede che sia la funzionalità HCE e `Nfc` autorizzazione essere registrato con l'applicazione `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![L'impostazione dell'autorizzazione di NFC nelle opzioni di app](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Per funzionare, HCE deve essere in grado di eseguire in background e deve iniziare quando l'utente esegue una transazione NFC, anche se l'applicazione utilizzando HCE non è in esecuzione. A questo scopo, è possibile scrivere il codice HCE come un `Service`. Implementa un servizio HCE il `HostApduService` interfaccia, che implementa i metodi seguenti:

-  *ProcessCommandApdu* -un'applicazione Protocol Data Unit (APDU) è ciò che viene inviato tra il lettore NFC e il servizio HCE. Questo metodo utilizza un ADPU dal lettore e restituisce un'unità di dati nella risposta.

-  *OnDeactivated* - `HostAdpuService` viene disattivata quando il servizio HCE non comunica con il lettore NFC.


Un servizio HCE deve inoltre essere registrato con il manifesto dell'applicazione e decorati con le autorizzazioni appropriate, preventivo filtro e metadati. Il codice riportato di seguito è riportato un esempio di un `HostApduService` registrato con il manifesto Android utilizzando il `Service` attributo (per ulteriori informazioni sugli attributi, vedere il Xamarin [utilizzo manifesto Android](~/android/platform/android-manifest.md) Guida):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("andorid.nfc.cardemulation.host.apdu_service",
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

Il servizio sopra fornisce un modo per il lettore NFC interagire con l'applicazione, ma il lettore NFC ancora non ha modo di sapere se la smart card NFC che deve analizzare emula questo servizio. Per identificare il lettore NFC il servizio, è possibile assegnare il servizio univoco *ID applicazione (aiuto)*. È un aiuto, insieme ad altri metadati sul servizio HCE, specificare un file di risorse xml registrati con il `MetaData` attributo (vedere l'esempio di codice precedente). Questo file di risorse specifica uno o più filtri aiuto - stringhe dell'identificatore univoco in formato esadecimale che corrispondono agli strumenti di uno o più dispositivi di lettore NFC:

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

Oltre ai filtri di aiuto, il file di risorse xml fornisce inoltre una descrizione rivolta all'utente del servizio HCE, specifica un gruppo di supporto (applicazione di pagamento e "altro") e, nel caso di un'applicazione di pagamento, un'intestazione dp 260 x 96 da visualizzare all'utente.

Il programma di installazione descritto in precedenza fornisce blocchi predefiniti di base per un'applicazione che emula una scheda NFC. NFC stesso richiede ulteriori passaggi e ulteriori prove per configurare. Per ulteriori informazioni sull'emulazione della scheda di basato su Host, vedere il [portale di documentazione Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Per ulteriori informazioni sull'utilizzo NFC con Xamarin, consultare il [esempi NFC Xamarin](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensori

KitKat fornisce l'accesso a sensori del dispositivo tramite un [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
Il `SensorManager` consente al sistema operativo di pianificare il recapito di informazioni sensore a un'applicazione in batch, mantenendo la durata della batteria.

KitKat viene anche fornito con due nuovi tipi di sensore per tenere traccia delle operazioni dell'utente. Questi sono basati sulla accelerometro e includono:

-  *StepDetector* -App è una notifica o riattivati quando l'utente esegue un passaggio e il rilevamento fornisce un valore di ora per quando si è verificato durante il passaggio.

-  *StepCounter* -tiene traccia del numero di passaggi per l'utente ha effettuato poiché è stato registrato il sensore *fino al successivo riavvio dispositivo*.

La schermata seguente illustra il contatore di passaggio in azione:

[![Schermata dell'app SensorsActivity la visualizzazione di un contatore di passaggio](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

È possibile creare un `SensorManager` chiamando `GetSystemService(SensorService)` e il cast del risultato come un `SensorManager`. Per utilizzare il contatore di passaggio, chiamare `GetDeafultSensor` sul `SensorManager`. È possibile registrare il sensore e ascoltare le modifiche nel conteggio passaggio con l'aiuto del [ `ISensorEventListener` ](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/) interfaccia, come illustrato nell'esempio di codice riportato di seguito:

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

`OnSensorChanged` viene chiamato se il conteggio di passaggio degli aggiornamenti durante l'applicazione è in primo piano. Se l'applicazione passerà in background o il dispositivo è in stato di sospensione, `OnSensorChanged` non verrà chiamato; tuttavia, i passaggi continueranno a essere conteggiati fino a quando `UnregisterListener` viene chiamato.

Tenere presente che *il valore del conteggio passaggio è cumulativo per tutte le applicazioni che registrano il sensore*. Ciò significa che anche se si disinstalla e reinstalla l'applicazione e inizializzare il `count` variabile da 0 all'avvio dell'applicazione, il valore rilevato dal sensore rimarrà il numero totale di passaggi eseguiti quando il sensore è stato registrato, tramite il applicazione o un altro. È possibile impedire l'applicazione da aggiungere al contatore passaggio chiamando `UnregisterListener` sul `SensorManager`, come illustrato nel codice seguente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Il riavvio del dispositivo Reimposta il numero di passaggio su 0. L'app richiede codice aggiuntivo per assicurare che segnala un conteggio accurato per l'applicazione, indipendentemente dalle altre applicazioni che utilizzano i sensori di rilevamento o lo stato del dispositivo.


> [!NOTE]
> Durante l'API per il rilevamento di passaggio e il conteggio viene fornito con KitKat, non tutti i telefoni includono con il sensore. È possibile controllare se i sensori di rilevamento è disponibile eseguendo `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, verificare che il valore restituito o `GetDefaultSensor` non `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Strumenti di sviluppo

### <a name="screen-recording"></a>Registrazione schermo

KitKat include schermata nuova funzionalità di registrazione in modo che gli sviluppatori possono registrare applicazioni nell'azione. Registrazione dello schermo è disponibile tramite il [Bridge di Debug Android (ADB)](http://developer.android.com/tools/help/adb.html) client, che può essere scaricato come parte di Android SDK.

Per registrare lo schermo, connettere il dispositivo; quindi, individuare l'installazione di Android SDK, passare al **-gli strumenti della piattaforma** directory ed eseguire il **adb** client:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

Il comando precedente registrerà un video di 3 minuti predefinito di risoluzione predefinito di 4 Mbps. Per modificare la lunghezza, aggiungere il *-limite di tempo* flag.
Per modificare la risoluzione, aggiungere il *-velocità in bit* flag. Il comando seguente registrerà un video minuto lungo 8 Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

È possibile trovare il video sul dispositivo, viene visualizzato nella raccolta quando la registrazione è stata completata.


## <a name="other-kitkat-additions"></a>Altre aggiunte KitKat

Oltre alle modifiche descritte in precedenza, KitKat consente di:

-  *Modalità schermo intero* -KitKat introduce un nuovo [modalità Immersive](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) per l'esplorazione del contenuto, giochi e in esecuzione altre applicazioni che possono trarre vantaggio da un'esperienza a schermo intero.

-  *Personalizzare le notifiche* -ottenere ulteriori dettagli sulle notifiche di sistema con il [ `NotificationListenerService` ](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) . Ciò consente di presentare le informazioni in modo diverso all'interno dell'app.

-  *Eseguire il mirroring Drawable risorse* -risorse Drawable hanno un nuovo [ `autoMirrored` ](http://developer.android.com/reference/android/R.attr.html#autoMirrored) attributo che indica il sistema crea una versione con mirroring per le immagini che richiedono l'inversione dei layout da sinistra a destra.

-  *Sospendere le animazioni* -create con le animazioni di sospendere e riprendere il [ `Animator` ](https://developer.xamarin.com/api/type/Android.Animation.Animator/) classe.

-  *Testo in modo dinamico la modifica lettura* -indicano le parti dell'interfaccia utente che aggiorna in modo dinamico con il nuovo testo come "aree in tempo reale" con il nuovo [ `accesibilityLiveRegion` ](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion) attributo in modo leggerà il nuovo testo automaticamente in modalità di accesso facilitato.

-  *Migliorare l'esperienza Audio* -verificare tracce più alto con il [ `LoudnessEnhancer` ](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/) , trovare le ore di punta e RMS di un flusso audio con il [ `Visualizer` ](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/) classe e ottenere informazioni da un [timestamp audio](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) agevola la sincronizzazione di audio e video.

-  *Sincronizzazione ContentResolver a intervalli personalizzati* -KitKat aggiunge una certa variabilità nel momento in cui viene eseguita una richiesta di sincronizzazione. Sincronizzazione un `ContentResolver` ora personalizzato o intervallo chiamando `ContentResolver.RequestSync` e passando un `SyncRequest`.

-  *Distingui tra controller* -KitKat nel controller vengono assegnati gli identificatori di tipo integer univoco che è possibile accedere tramite il dispositivo `ControllerNumber` proprietà. Questo rende più semplice indicare i lettori separati in un gioco.

-  *Controllo remoto* -KitKat con poche modifiche sul lato di hardware e software, consente di attivare un dispositivo dotato di un trasmettitore IR in un controllo remoto utilizzando il `ConsumerIrService`e interagire con le periferiche con il nuovo [ `RemoteController` ](https://developer.xamarin.com/api/type/Android.Media.RemoteController/) API.

Per ulteriori informazioni sulle modifiche API precedente, consultare il Google [le API di Android 4.4](http://developer.android.com/about/versions/android-4.4.html) Panoramica.


## <a name="summary"></a>Riepilogo

In questo articolo introdotto alcune delle nuove API disponibile in Android 4.4 (API livello 19) e analizzate le procedure consigliate durante la transizione di un'applicazione di KitKat. IT verificano modifiche contorno per le API che interessano utente, inclusi il *framework transizione* e nuove opzioni per *temi*. Successivamente, è stato introdotto il *Framework di accesso di archiviazione* e `DocumentsProvider` classe, nonché il nuovo *stampa API*. Viene esplorato *emulazione smart card basate su host NFC* e sull'utilizzo di *sensori di basso consumo*, incluse due nuove sensori per tenere traccia delle operazioni dell'utente. Infine, è dimostrato acquisizione demo in tempo reale di applicazioni con *la registrazione schermo*e fornito un elenco dettagliato delle modifiche all'API KitKat e aggiunte.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio KitKat](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 API](http://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](http://developer.android.com/about/versions/kitkat.html)
