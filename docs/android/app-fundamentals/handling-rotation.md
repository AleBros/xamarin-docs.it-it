---
title: Gestione della rotazione
description: Questo argomento descrive come gestire le modifiche dell'orientamento del dispositivo in Xamarin.Android. Viene illustrato come usare il sistema di risorse Android per caricare automaticamente le risorse per un particolare orientamento del dispositivo e come gestire a livello di codice le modifiche dell'orientamento.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3277dd5eb7600500a5f60b2bbb13621aa237a235
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019279"
---
# <a name="handling-rotation"></a>Gestione della rotazione

_Questo argomento descrive come gestire le modifiche dell'orientamento del dispositivo in Xamarin.Android. Viene illustrato come usare il sistema di risorse Android per caricare automaticamente le risorse per un particolare orientamento del dispositivo e come gestire a livello di codice le modifiche dell'orientamento._

## <a name="overview"></a>Panoramica

Poiché i dispositivi mobili sono facilmente ruotati, la rotazione incorporata è una funzionalità standard di sistemi operativi mobili. Android fornisce un Framework sofisticato per la gestione della rotazione all'interno delle applicazioni, indipendentemente dal fatto che l'interfaccia utente venga creata in modo dichiarativo in XML o a livello di codice nel codice. Quando si gestiscono automaticamente le modifiche del layout dichiarativo in un dispositivo ruotato, un'applicazione può trarre vantaggio dalla stretta integrazione con il sistema di risorse Android. Per il layout a livello di codice, le modifiche devono essere gestite manualmente. Questo consente un controllo più preciso in fase di esecuzione, ma a scapito di più lavoro per lo sviluppatore. Un'applicazione può anche scegliere di rifiutare esplicitamente il riavvio dell'attività e di assumere il controllo manuale delle modifiche dell'orientamento.

Questa guida esamina gli argomenti di orientamento seguenti:

- **Rotazione del layout Dichiarativa** &ndash; come usare il sistema di risorse Android per creare applicazioni in grado di riconoscere l'orientamento, incluso il caricamento di layout e drawables per orientamenti particolari.

- **Rotazione del layout a livello di codice** &ndash; come aggiungere controlli a livello di codice e come gestire manualmente le modifiche dell'orientamento.

## <a name="handling-rotation-declaratively-with-layouts"></a>Gestione della rotazione in modo dichiarativo con layout

Includendo i file nelle cartelle che seguono le convenzioni di denominazione, Android carica automaticamente i file appropriati quando cambia l'orientamento.
Questo include il supporto per:

- *Risorse di layout* &ndash; specificare i file di layout per ogni orientamento.

- *Le risorse* di cui è stato disegnato &ndash; specificano quali drawables vengono caricati per ogni orientamento.

### <a name="layout-resources"></a>Risorse di layout

Per impostazione predefinita, i file di Android XML (AXML) inclusi nella cartella **Resources/layout** vengono usati per il rendering delle visualizzazioni per un'attività. Le risorse di questa cartella vengono usate per l'orientamento verticale e orizzontale se non vengono fornite risorse di layout aggiuntive per il panorama. Si consideri la struttura del progetto creata dal modello di progetto predefinito:

[![struttura del modello di progetto predefinito](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Questo progetto crea un singolo file **Main. aXML** nella cartella **Resources/layout** . Quando viene chiamato il metodo di `OnCreate` dell'attività, viene ingrandita la vista definita in **Main. aXML,** che dichiara un pulsante come illustrato nel codice XML seguente:

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

Se il dispositivo viene ruotato a orientamento orizzontale, il metodo di `OnCreate` dell'attività viene chiamato di nuovo e lo stesso file **Main. aXML** è inflat, come illustrato nella schermata seguente:

[![stessa schermata ma con orientamento orizzontale](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)

#### <a name="orientation-specific-layouts"></a>Layout specifici dell'orientamento

Oltre alla cartella layout (il cui valore predefinito è Portrait e può anche essere denominati in modo esplicito con una *cartella denominata `layout-land`* ), un'applicazione può definire le viste necessarie in orizzontale senza apportare modifiche al codice.

Si supponga che il file **Main. aXML** contenesse il codice XML seguente:

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

Se una cartella denominata layout-Land che contiene un file **Main. aXML** aggiuntivo viene aggiunta al progetto, se il layout viene aggiunto nel formato orizzontale, il caricamento di Android sarà stato aggiunto al nuovo file **Main. aXML.** Si consideri la versione orizzontale del file **Main. aXML** che contiene il codice seguente (per semplicità, questo XML è simile alla versione verticale predefinita del codice, ma usa una stringa diversa nella `TextView`):

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

L'esecuzione di questo codice e la rotazione del dispositivo da verticale a orizzontale illustrano il nuovo caricamento XML, come illustrato di seguito:

[schermate di![verticale e orizzontale che stampano la modalità verticale](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)

### <a name="drawable-resources"></a>Risorse disegnatore

Durante la rotazione, Android considera le risorse di disegno in modo analogo alle risorse di layout. In questo caso, il sistema ottiene il drawables dalle cartelle **Resources/disegnator** e **Resources/Landings** , rispettivamente.

Si immagini, ad esempio, che il progetto includa un'immagine denominata Monkey. png nella cartella **Resources/disegnatore** , in cui viene fatto riferimento all'oggetto disegnato da un `ImageView` in XML nel modo seguente:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Si supponga inoltre che una versione diversa di **Monkey. png** sia inclusa in **risorse/** che può essere disegnato. Come per i file di layout, quando il dispositivo viene ruotato, le modifiche di disegno per l'orientamento specificato, come illustrato di seguito:

[![versione diversa di Monkey. png mostrata in modalità verticale e orizzontale](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)

## <a name="handling-rotation-programmatically"></a>Gestione della rotazione a livello di codice

A volte i layout vengono definiti nel codice. Questo problema può verificarsi per diversi motivi, ad esempio limitazioni tecniche, preferenza per gli sviluppatori e così via. Quando si aggiungono controlli a livello di codice, un'applicazione deve tenere conto manualmente dell'orientamento del dispositivo, che viene gestito automaticamente quando si usano le risorse XML.

### <a name="adding-controls-in-code"></a>Aggiunta di controlli nel codice

Per aggiungere controlli a livello di codice, un'applicazione deve eseguire i passaggi seguenti:

- Creare un layout.
- Imposta i parametri di layout.
- Creazione di controlli.
- Imposta i parametri di layout del controllo.
- Aggiungere i controlli al layout.
- Impostare il layout come visualizzazione contenuto.

Si consideri, ad esempio, un'interfaccia utente costituita da un singolo controllo `TextView` aggiunto a una `RelativeLayout`, come illustrato nel codice seguente.

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

Questo codice crea un'istanza di una classe `RelativeLayout` e ne imposta la proprietà `LayoutParameters`. La classe `LayoutParams` è il modo in cui è possibile incapsulare il modo in cui i controlli vengono posizionati in modo riutilizzabile. Una volta creata un'istanza di un layout, i controlli possono essere creati e aggiunti. I controlli hanno anche `LayoutParameters`, ad esempio `TextView` in questo esempio. Una volta creato il `TextView`, aggiungerlo al `RelativeLayout` e impostare il `RelativeLayout` come visualizzazione contenuto nell'applicazione che Visualizza il `TextView` come illustrato:

[![pulsante incrementa contatore visualizzato in modalità verticale e orizzontale](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)

### <a name="detecting-orientation-in-code"></a>Rilevamento dell'orientamento nel codice

Se un'applicazione tenta di caricare un'interfaccia utente diversa per ogni orientamento quando viene chiamato `OnCreate` (questo errore si verifica ogni volta che un dispositivo viene ruotato), deve rilevare l'orientamento, quindi caricare il codice dell'interfaccia utente desiderato. Android dispone di una classe denominata `WindowManager`, che può essere usata per determinare la rotazione del dispositivo corrente tramite la proprietà `WindowManager.DefaultDisplay.Rotation`, come illustrato di seguito:

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

Questo codice imposta il `TextView` da posizionare a 100 pixel dalla parte superiore sinistra dello schermo, animando automaticamente il nuovo layout, quando viene ruotato in orizzontale, come illustrato di seguito:

[lo stato di visualizzazione![viene mantenuto in modalità verticale e orizzontale](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)

### <a name="preventing-activity-restart"></a>Impedisci riavvio attività

Oltre a gestire tutti gli elementi in `OnCreate`, un'applicazione può anche impedire il riavvio di un'attività quando l'orientamento cambia impostando `ConfigurationChanges` nel `ActivityAttribute` come indicato di seguito:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```

Ora, quando il dispositivo viene ruotato, l'attività non viene riavviata. Per gestire manualmente la modifica dell'orientamento in questo caso, un'attività può eseguire l'override del metodo `OnConfigurationChanged` e determinare l'orientamento dall'oggetto `Configuration` passato, come nella nuova implementazione dell'attività seguente:

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

In questo caso i parametri del layout `TextView's` vengono inizializzati sia per orizzontale che per verticale. Le variabili di classe contengono i parametri, insieme al `TextView` stesso, perché l'attività non verrà ricreata quando l'orientamento cambia. Il codice usa ancora il `surfaceOrientartion` in `OnCreate` per impostare il layout iniziale per il `TextView`. Successivamente, `OnConfigurationChanged` gestisce tutte le modifiche di layout successive.

Quando si esegue l'applicazione, Android carica le modifiche dell'interfaccia utente quando si verifica la rotazione del dispositivo e non riavvia l'attività.

## <a name="preventing-activity-restart-for-declarative-layouts"></a>Prevenzione del riavvio delle attività per i layout dichiarativi

I riavvii delle attività causati dalla rotazione del dispositivo possono anche essere evitati se si definisce il layout in XML. Ad esempio, è possibile usare questo approccio se si vuole impedire il riavvio di un'attività (per motivi di prestazioni, forse) e non è necessario caricare nuove risorse per orientamenti diversi.

A tale scopo, si seguirà la stessa procedura usata con un layout a livello di codice. È sufficiente impostare `ConfigurationChanges` nel `ActivityAttribute`, come nel `CodeLayoutActivity` precedente. Il codice che deve essere eseguito per la modifica dell'orientamento può essere implementato nuovamente nel metodo `OnConfigurationChanged`.

## <a name="maintaining-state-during-orientation-changes"></a>Gestione dello stato durante le modifiche dell'orientamento

Se si gestisce la rotazione in modo dichiarativo o a livello di codice, tutte le applicazioni Android devono implementare le stesse tecniche per la gestione dello stato quando l'orientamento del dispositivo cambia La gestione dello stato è importante perché il sistema riavvia un'attività in esecuzione quando un dispositivo Android viene ruotato. Android esegue questa operazione per facilitare il caricamento di risorse alternative, ad esempio layout e drawables progettati specificamente per un particolare orientamento. Quando viene riavviato, l'attività perde tutti gli stati temporanei che possono essere archiviati nelle variabili di classe locali. Di conseguenza, se un'attività è dipendente dallo stato, deve mantenerne lo stato a livello di applicazione. Un'applicazione deve gestire il salvataggio e il ripristino dello stato dell'applicazione che si desidera mantenere tra le modifiche all'orientamento.

Per altre informazioni sullo stato di salvataggio permanente in Android, vedere la guida del ciclo di vita delle [attività](~/android/app-fundamentals/activity-lifecycle/index.md) .

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare le funzionalità predefinite di Android per lavorare con la rotazione. In primo luogo, è stato illustrato come usare il sistema di risorse Android per creare applicazioni in grado di riconoscere l'orientamento. È stato quindi illustrato come aggiungere controlli nel codice e come gestire manualmente le modifiche dell'orientamento.

## <a name="related-links"></a>Collegamenti correlati

- [Demo sulla rotazione (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-rotationdemo)
- [Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Gestione delle modifiche di runtime](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Modifica dell'orientamento rapido dello schermo](https://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
