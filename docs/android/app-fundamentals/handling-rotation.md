---
title: Gestione della rotazione
description: In questo argomento viene descritto come gestire le modifiche apportate orientamento di dispositivo in xamarin. Android. Viene illustrato come lavorare con il sistema risorse Android per caricare automaticamente le risorse per un orientamento del dispositivo specifico nonché come gestire a livello di codice l'orientamento viene modificato.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 62e64be89e26e5a8412cd34221da581e99fc5e6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61017333"
---
# <a name="handling-rotation"></a>Gestione della rotazione

_In questo argomento viene descritto come gestire le modifiche apportate orientamento di dispositivo in xamarin. Android. Viene illustrato come lavorare con il sistema risorse Android per caricare automaticamente le risorse per un orientamento del dispositivo specifico nonché come gestire a livello di codice l'orientamento viene modificato._


## <a name="overview"></a>Panoramica

Poiché i dispositivi mobili con facilità vengono ruotati, rotazione incorporata è una funzionalità standard di sistemi operativi per dispositivi mobili. Android fornisce un framework sofisticato per la gestione della rotazione all'interno delle applicazioni, se l'interfaccia utente viene creato in modo dichiarativo nel codice XML o a livello di programmazione nel codice. Quando si gestisce automaticamente le modifiche di layout dichiarativo in un dispositivo ruotato, un'applicazione può trarre vantaggio da una stretta integrazione con il sistema risorse Android. Per il layout a livello di codice, è necessario gestire manualmente le modifiche. Ciò consente un maggiore controllo in fase di esecuzione, ma a scapito della quantità di lavoro per gli sviluppatori. Un'applicazione può anche scegliere di rifiutare esplicitamente il riavvio di attività e assumere il controllo manuale delle modifiche apportate orientamento.

Questa guida esamina gli argomenti di orientamento seguenti:

-   **Rotazione di Layout dichiarativa** &ndash; come usare il sistema risorse Android per compilare applicazioni compatibili con l'orientamento, incluse le procedure caricare i layout sia drawable per gli orientamenti particolari.

-   **Rotazione di Layout a livello di programmazione** &ndash; come aggiungere controlli a livello di codice, nonché come gestire manualmente le modifiche apportate orientamento.


## <a name="handling-rotation-declaratively-with-layouts"></a>Gestione della rotazione in modo dichiarativo con layout

Includendo i file nelle cartelle che seguono le convenzioni di denominazione, Android carica automaticamente i file appropriati quando cambia l'orientamento.
Ciò include il supporto per:

-   *Le risorse di layout* &ndash; che specifica quali file di layout sono a inflating per ogni orientamento.

-   *Risorse drawable* &ndash; specificando quali drawable vengono caricate per ogni orientamento.


### <a name="layout-resources"></a>Risorse di layout

Per impostazione predefinita, i file XML Android (AXML) inclusi nel **risorse/layout** cartella vengono utilizzati per le visualizzazioni per il rendering per un'attività. Risorse della cartella vengono usate per l'orientamento sia verticale e orizzontale se non sono presenti risorse aggiuntive di layout vengono fornite appositamente per orizzontale. Si consideri la struttura del progetto creata dal modello di progetto predefinita:

[![Struttura modello di progetto predefinita](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Questo progetto viene creato un singolo **Main. axml** del file nei **risorse/layout** cartella. Quando l'attività `OnCreate` viene chiamato il metodo, ingrandisce la visualizzazione definita in **Main. axml,** che dichiara un pulsante come illustrato nel codice XML riportato di seguito:

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

Se il dispositivo viene ruotato in orientamento orizzontale, l'attività del `OnCreate` metodo viene chiamato nuovamente e lo stesso **Main. axml** file è sottoposto a inflating, come illustrato nello screenshot seguente:

[![Ma nella stessa schermata orientamento orizzontale](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Layout orientamento specifici

Aggiunta alla cartella di layout (quale verticale per impostazione predefinita e possono anche essere denominate in modo esplicito *layout-port* includendo una cartella denominata `layout-land`), un'applicazione di definire le viste necessarie quando nel panorama senza codice modifiche.

Si supponga che il **Main. axml** file contiene il codice XML seguente:

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

Se una cartella denominata land layout che contiene un'ulteriore **Main. axml** file viene aggiunto al progetto, ciò fa aumentare erroneamente il layout quando nel panorama ora genererà in Android il caricamento appena aggiunta **Main. axml.** Prendere in considerazione la versione del panorama applicativo del **Main. axml** file che contiene il codice seguente (per motivi di semplicità, questo codice XML è simile a quella verticale predefinito del codice, ma usa una stringa diversa nel `TextView`):

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

Esecuzione del codice e ruotare il dispositivo da verticale a orizzontale viene illustrato il nuovo caricamento di XML, come illustrato di seguito:

[![Screenshot Portrait e landscape stampa la modalità verticale](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Risorse drawable

Durante la rotazione, Android considera risorse drawable in modo analogo alle risorse di layout. In questo caso, il sistema Ottiene i drawable dal **risorse/drawable** e **risorse/drawable-land** cartelle, rispettivamente.

Ad esempio, il progetto include un'immagine denominata Monkey.png nel **risorse/drawable** cartella, dove il drawable viene fatto riferimento da un `ImageView` nel codice XML simile al seguente:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Si supponga che una versione diversa di **Monkey.png** è incluso nella **risorse/drawable-land**. Proprio come con i file di layout, quando il dispositivo viene ruotato, le modifiche drawable per l'orientamento specificato, come illustrato di seguito:

[![Versione diversa di Monkey.png visualizzato in modalità verticale e orizzontale](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Gestione della rotazione a livello di codice

In alcuni casi definiamo layout nel codice. Questa situazione può verificarsi per diversi motivi, tra cui limiti tecnici delle preferenze per gli sviluppatori, e così via. Quando si aggiungono i controlli a livello di codice, un'applicazione manualmente è necessario tenere conto di orientamento del dispositivo, che viene gestito automaticamente quando si usano risorse XML.


### <a name="adding-controls-in-code"></a>Aggiunta di controlli nel codice

Per aggiungere controlli a livello di codice, un'applicazione deve eseguire la procedura seguente:

-  Creare un layout.
-  Impostare i parametri di layout.
-  Creare i controlli.
-  Impostare i parametri di layout di controllo.
-  Aggiungere controlli al layout.
-  Impostare il layout della visualizzazione del contenuto.

Si consideri ad esempio un'interfaccia utente costituito da una singola `TextView` aggiunto al controllo un `RelativeLayout`, come illustrato nel codice seguente.

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

Questo codice crea un'istanza di un `RelativeLayout` classi e i set relativo `LayoutParameters` proprietà. Il `LayoutParams` classe è modo di Android che incapsula la modalità con cui i controlli vengono posizionati in modo riutilizzabile. Dopo aver creata un'istanza di un layout, controlli possono essere creati e aggiunti a esso. Controlli dispone anche `LayoutParameters`, ad esempio il `TextView` in questo esempio. Dopo il `TextView` viene creato, aggiungendolo al `RelativeLayout` e impostando il `RelativeLayout` dei risultati di visualizzazione di contenuto la visualizzazione delle applicazioni il `TextView` come illustrato:

[![Pulsante di contatore incremento visualizzato in modalità sia verticale e orizzontale](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Rilevamento di orientamento nel codice

Se un'applicazione tenta di caricare un'interfaccia utente diversa per ogni orientamento quando `OnCreate` viene chiamato (ciò si verifica ogni volta che un dispositivo viene ruotato), deve rilevare l'orientamento e quindi caricare il codice dell'interfaccia utente desiderato. Android ha una classe denominata la `WindowManager`, che può essere usato per determinare la rotazione del dispositivo corrente tramite il `WindowManager.DefaultDisplay.Rotation` proprietà, come illustrato di seguito:

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

Questo codice imposta la `TextView` sia posizionato 100 pixel dalla parte superiore sinistra della schermata, l'animazione automaticamente per il nuovo layout quando ruotata all'orientamento orizzontale, come illustrato di seguito:

[![Lo stato di visualizzazione viene mantenuto tra le diverse modalità verticale e orizzontale](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Impedisce il riavvio di attività

Oltre alla gestione di tutti gli elementi in `OnCreate`, un'applicazione può inoltre impedire un'attività in fase di riavvio quando viene modificato impostando l'orientamento `ConfigurationChanges` nel `ActivityAttribute` come indicato di seguito:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Ora quando il dispositivo viene ruotato, l'attività non viene riavviato. Per gestire manualmente le modifiche di orientamento in questo caso, è possibile eseguire l'override di un'attività di `OnConfigurationChanged` metodo e determinare l'orientamento dal `Configuration` oggetto che viene passata, come la nuova implementazione dell'attività seguente:

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

In questo caso il `TextView's` parametri del layout vengono inizializzati per verticale e orizzontale. Le variabili di classe contenere i parametri, insieme al `TextView` stesso, poiché l'attività non verrà nuovamente creato quando viene modificato l'orientamento. Il codice Usa ancora la `surfaceOrientartion` nelle `OnCreate` per impostare il layout iniziale per il `TextView`. Successivamente, `OnConfigurationChanged` gestisce tutte le modifiche di layout successive.

Quando si esegue l'applicazione, Android carica le modifiche all'interfaccia utente quando si verifica, rotazione del dispositivo e non riavvia l'attività.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Impedisce il riavvio di attività per layout dichiarativo

Riavvio di attività ha causato dalla rotazione del dispositivo può essere evitato anche se si definisce il layout in formato XML. Ad esempio, è possibile usare questo approccio se si vuole impedire il riavvio di un'attività (per motivi di prestazioni, ad esempio) e non sono necessarie caricare le nuove risorse per gli orientamenti diversi.

A tale scopo, si seguirà la stessa procedura che viene usato con un layout a livello di codice. È sufficiente impostare `ConfigurationChanges` nella `ActivityAttribute`, come abbiamo fatto `CodeLayoutActivity` in precedenza. Qualsiasi codice che è necessario eseguire la modifica l'orientamento può essere implementata nuovamente nel `OnConfigurationChanged` (metodo).


## <a name="maintaining-state-during-orientation-changes"></a>Gestione dello stato durante le modifiche apportate orientamento

Se Gestione della rotazione in modo dichiarativo o a livello di codice, tutte le applicazioni Android devono implementare le stesse tecniche per la gestione dello stato quando viene modificato l'orientamento del dispositivo. La gestione dello stato è importante perché il riavvio del sistema un'attività in esecuzione quando un dispositivo Android viene ruotato. Android ha lo scopo rendono più semplice caricare risorse alternative, ad esempio i layout e drawable progettati specificamente per l'orientamento specifico. Se il riavvio, l'attività perde qualsiasi stato temporaneo che siano stati archiviati nelle variabili locali di classe. Pertanto, se un'attività è stato dipendenze, è necessario rendere persistente lo stato a livello di applicazione. Un'applicazione deve gestire il salvataggio e il ripristino dello stato qualsiasi applicazione che vuole conservare in tutte le modifiche apportate orientamento.

Per altre informazioni sullo stato persistente in Android, vedere la [ciclo di vita attività](~/android/app-fundamentals/activity-lifecycle/index.md) Guida.


## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare le funzionalità integrate di Android per lavorare con rotazione. In primo luogo, spiega come usare il sistema risorse Android per creare applicazioni compatibili con l'orientamento. Quindi presentato come aggiungere i controlli nel codice, nonché come gestire manualmente le modifiche apportate orientamento.



## <a name="related-links"></a>Collegamenti correlati

- [Demo di rotazione (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Gestione delle modifiche di Runtime](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Cambia l'orientamento dello schermo veloce](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
