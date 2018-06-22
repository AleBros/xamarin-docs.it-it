---
title: Rotazione di gestione
description: In questo argomento viene descritto come gestire le modifiche di orientamento di dispositivo in xamarin. Viene illustrato come utilizzare con il sistema Android risorse per caricare automaticamente risorse per un orientamento del dispositivo specifico nonché come gestire a livello di programmazione orientamento cambia.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1cdc7928c45b99cdd8c8149b3ae9b06e790deeca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30768896"
---
# <a name="handling-rotation"></a>Rotazione di gestione

_In questo argomento viene descritto come gestire le modifiche di orientamento di dispositivo in xamarin. Viene illustrato come utilizzare con il sistema Android risorse per caricare automaticamente risorse per un orientamento del dispositivo specifico nonché come gestire a livello di programmazione orientamento cambia._


## <a name="overview"></a>Panoramica

Poiché i dispositivi mobili vengono facilmente ruotati, rotazione predefinita è una caratteristica standard in sistemi operativi per dispositivi mobili. Android fornisce un framework sofisticato per la gestione delle applicazioni, la rotazione, se l'interfaccia utente viene creato in modo dichiarativo nel codice XML o a livello di programmazione nel codice. Quando si gestisce automaticamente le modifiche di layout dichiarativo in un dispositivo ruotata, un'applicazione può trarre vantaggio da una stretta integrazione con il sistema Android risorse. Per il layout a livello di codice, le modifiche devono essere gestite manualmente. In questo modo un controllo più preciso in fase di esecuzione, ma a scapito della quantità di lavoro per lo sviluppatore. Un'applicazione può anche scegliere di rifiutare esplicitamente il riavvio di attività e assumere il controllo manuale delle modifiche di orientamento.

Questa guida viene esaminato gli argomenti di orientamento seguenti:

-   **Rotazione di Layout dichiarativa** &ndash; illustrato come utilizzare il sistema Android risorse per compilare applicazioni compatibili con orientamento, inclusa la modalità di caricamento layout sia drawables per gli orientamenti particolari.

-   **Rotazione di Layout a livello di codice** &ndash; come aggiungere controlli a livello di codice, nonché come gestire manualmente le modifiche di orientamento.


## <a name="handling-rotation-declaratively-with-layouts"></a>Rotazione di gestione in modo dichiarativo con layout

Includendo i file nelle cartelle che seguono le convenzioni di denominazione, Android carica automaticamente i file appropriati quando cambia l'orientamento.
Ciò include il supporto per:

-   *Risorse di layout* &ndash; specificando i file di layout sono ingranditi per ciascun orientamento.

-   *Risorse drawable* &ndash; specificando quali drawables vengono caricate per ogni orientamento.


### <a name="layout-resources"></a>Risorse di layout

Per impostazione predefinita, i file XML Android (AXML) inclusi nel **risorse/layout** cartella vengono utilizzati per le viste per il rendering di un'attività. Risorse di questa cartella vengono utilizzate per orientamento sia verticale e orizzontale se non ci sono risorse aggiuntive di layout vengono fornite specificamente per orizzontale. Si consideri la struttura del progetto creata dal modello di progetto predefinita:

[![Struttura modello di progetto predefinita](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Questo progetto viene creato un singolo **axml** file nel **risorse/layout** cartella. Quando l'attività `OnCreate` metodo viene chiamato, ingrandisce la visualizzazione definita **Main. axml,** che dichiara un pulsante, come illustrato nel codice XML riportato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

Se il dispositivo viene ruotato in orientamento orizzontale, l'attività del `OnCreate` metodo viene chiamato nuovamente e lo stesso **axml** file viene ingrandita, come illustrato nella schermata seguente:

[![Stessa schermata ma in orientamento orizzontale](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Layout orientamento specifici

Oltre alla cartella layout (che per impostazione predefinita in verticale e può anche essere denominata in modo esplicito *layout porta* includendo una cartella denominata `layout-land`), un'applicazione di definire le viste necessarie in orizzontale senza ricorrere al codice modifiche.

Si supponga che il **axml** file contiene il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Se una cartella denominata terreni layout che contiene un ulteriore **axml** file viene aggiunto al progetto, il layout in orizzontale eccessi nella misurazione ora genererà Android caricamento appena aggiunta **Main. axml.** Si consideri la versione orizzontale del **Main. axml** file che contiene il codice seguente (per motivi di semplicità, questo codice XML è simile a quella verticale predefinito del codice, ma usa un'altra stringa nel `TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

L'esecuzione del codice e il dispositivo da verticale a orizzontale di rotazione di seguito viene illustrato il nuovo caricamento di XML, come illustrato di seguito:

[![Schermate verticale e orizzontale stampa modalità verticale](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Risorse drawable

Durante la rotazione, Android considera drawable risorse in modo analogo alle risorse di layout. In questo caso, il sistema Ottiene il drawables dal **risorse/drawable** e **risorse/drawable-terreni** cartelle, rispettivamente.

Se ad esempio, il progetto include un'immagine denominata Monkey.png nel **risorse/drawable** cartella in cui il drawable viene fatto riferimento da un `ImageView` nel codice XML simile al seguente:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Si supponga che una versione diversa di **Monkey.png** è incluso in **risorse/drawable-terreni**. Proprio come con i file di layout, quando il dispositivo è ruotato, le modifiche drawable per l'orientamento specificato, come illustrato di seguito:

[![Versione diversa di Monkey.png illustrato in modalità verticale e orizzontale](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>La gestione di rotazione a livello di codice

In alcuni casi è possibile definire layout nel codice. Questa situazione può verificarsi per diversi motivi, tra cui limiti tecnici, delle preferenze di sviluppatore e così via. Quando si aggiungono controlli a livello di programmazione, un'applicazione deve manualmente account per l'orientamento del dispositivo, che viene gestita automaticamente quando si usano risorse XML.


### <a name="adding-controls-in-code"></a>Aggiunta di controlli nel codice

Per aggiungere controlli a livello di programmazione, un'applicazione deve eseguire la procedura seguente:

-  Creare un layout.
-  Impostare i parametri di layout.
-  Creare controlli.
-  Impostare i parametri di layout di controllo.
-  Aggiungere controlli al layout.
-  Impostare il layout della visualizzazione del contenuto.

Si consideri ad esempio un'interfaccia utente composta da una sola `TextView` controllo aggiunto a un `RelativeLayout`, come illustrato nel codice seguente.

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Questo codice crea un'istanza di un `RelativeLayout` classe e imposta il relativo `LayoutParameters` proprietà. La `LayoutParams` classe è modo di Android che incapsula la modalità di posizionamento dei controlli in modo riutilizzabile. Una volta creata un'istanza di un layout, controlli possono essere creati e aggiunto a esso. I controlli sono anche `LayoutParameters`, ad esempio il `TextView` in questo esempio. Dopo il `TextView` viene creato, è necessario il `RelativeLayout` e impostando il `RelativeLayout` come i contenuto di visualizzare i risultati nella visualizzazione applicazione il `TextView` come illustrato:

[![Pulsante di incremento del contatore visualizzato in modalità sia verticale e orizzontale](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Orientamento di rilevamento nel codice

Se un'applicazione tenta di caricare un'interfaccia utente diverso per ogni orientamento quando `OnCreate` viene chiamato (questa operazione verrà eseguita ogni volta che un dispositivo viene ruotato), è necessario rilevare l'orientamento e quindi caricare il codice dell'interfaccia utente desiderato. Android è una classe denominata la `WindowManager`, che può essere utilizzato per determinare la rotazione del dispositivo corrente tramite il `WindowManager.DefaultDisplay.Rotation` proprietà, come illustrato di seguito:

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Questo codice imposta il `TextView` per essere posizionata 100 pixel dalla parte superiore sinistra dello schermo, l'animazione automaticamente per il nuovo layout quando ruotata all'orientamento orizzontale, come illustrato di seguito:

[![Lo stato di visualizzazione viene mantenuto tra le diverse modalità verticale e orizzontale](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Impedisce il riavvio di attività

Oltre alla gestione di tutti gli elementi di `OnCreate`, un'applicazione può inoltre impedire un'attività in fase di riavvio quando cambia l'orientamento impostando `ConfigurationChanges` nel `ActivityAttribute` come indicato di seguito:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Ora quando il dispositivo viene ruotato, l'attività non viene riavviato. Per gestire manualmente le modifiche di orientamento in questo caso, è possibile eseguire l'override di un'attività di `OnConfigurationChanged` (metodo) e determinare l'orientamento dal `Configuration` oggetto che viene passato, come la nuova implementazione dell'attività seguente:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

In questo caso il `TextView's` parametri del layout vengono inizializzati per verticale e orizzontale. Le variabili di classe contenere i parametri, con la `TextView` stesso, poiché l'attività non verrà nuovamente creato quando si modifica l'orientamento. Il codice utilizza ancora la `surfaceOrientartion` in `OnCreate` per impostare il layout iniziale per il `TextView`. Successivamente, `OnConfigurationChanged` gestisce tutte le modifiche di layout successive.

Quando si esegue l'applicazione, Android carica le modifiche all'interfaccia utente come rotazione del dispositivo si verifica e non viene riavviato l'attività.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Impedisce il riavvio di attività per layout dichiarativo

È possibile evitare i riavvii attività causati dalla rotazione del dispositivo anche se è possibile definire il layout in formato XML. Ad esempio, è possibile utilizzare questo approccio se si desidera impedire il riavvio di un'attività (per motivi di prestazioni, ad esempio) e non sono necessarie caricare le nuove risorse per gli orientamenti diversi.

A tale scopo, si seguirà la stessa procedura usata con un layout a livello di codice. È sufficiente impostare `ConfigurationChanges` nel `ActivityAttribute`, come nel `CodeLayoutActivity` in precedenza. Qualsiasi codice che è necessario eseguire per la modifica dell'orientamento nuovamente può essere implementata nel `OnConfigurationChanged` metodo.


## <a name="maintaining-state-during-orientation-changes"></a>Mantenimento dello stato durante la modifica di orientamento

Se la gestione di rotazione in modo dichiarativo o a livello di codice, tutte le applicazioni Android devono implementare le stesse tecniche per la gestione dello stato quando viene modificato l'orientamento del dispositivo. Gestione dello stato è importante perché il riavvio del sistema un'attività in esecuzione quando un dispositivo Android viene ruotato. Android ha lo scopo di facilitare caricare le risorse alternative, ad esempio i layout e drawables progettate specificamente per l'orientamento specifico. Quando si riavvia, l'attività perde qualsiasi stato temporaneo che può archiviare in variabili locali di classe. Pertanto, se un'attività è stato affidamento, è necessario persistente lo stato a livello di applicazione. Un'applicazione deve gestire il salvataggio e ripristinare lo stato di qualsiasi applicazione che desidera mantenere le modifiche di orientamento.

Per ulteriori informazioni sullo stato di mantenimento in Android, vedere il [ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md) Guida.


## <a name="summary"></a>Riepilogo

In questo articolo viene descritto come utilizzare le funzionalità incorporate di Android per lavorare con rotazione. In primo luogo, è spiegato come utilizzare il sistema Android risorse per creare applicazioni di orientamento. Quindi presentato come aggiungere i controlli nel codice, nonché come gestire manualmente le modifiche di orientamento.



## <a name="related-links"></a>Collegamenti correlati

- [Demo di rotazione (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Gestione delle modifiche al Runtime](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Cambia l'orientamento dello schermo veloce](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
