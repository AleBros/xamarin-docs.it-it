---
title: Layout a schede con ActionBar
description: Questa guida introduce e illustra come usare le API ActionBar per creare un'interfaccia utente a schede in un'applicazione Novell. Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 4673b178512a886e5fdb154c57c8d659276bb392
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522323"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Layout a schede con ActionBar

_Questa guida introduce e illustra come usare le API ActionBar per creare un'interfaccia utente a schede in un'applicazione Novell. Android._


## <a name="overview"></a>Panoramica

La barra delle azioni è un modello di interfaccia utente Android usato per fornire un'interfaccia utente coerente per le funzionalità chiave, ad esempio le schede, l'identità dell'applicazione, i menu e la ricerca. In Android 3,0 (livello API 11), Google ha introdotto le API ActionBar alla piattaforma Android. Le API di ActionBar introducono temi dell'interfaccia utente per fornire un aspetto coerente e classi che consentono le interfacce utente a schede. Questa guida illustra come aggiungere Barra delle azioni schede a un'applicazione Novell. Android. Viene anche illustrato come usare la libreria di supporto Android V7 per backporting le schede ActionBar a Novell. Android destinate ad Android 2,1 ad Android 2,3. 

Si noti `Toolbar` che è un componente della `ActionBar` barra delle azioni più recente e generalizzato da usare anziché (`Toolbar` è stato progettato per `ActionBar`sostituire). Per ulteriori informazioni, vedere [barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Requisiti

Qualsiasi applicazione Novell. Android destinata a livello API 11 (Android 3,0) o versione successiva ha accesso alle API ActionBar come parte delle API native di Android. 

Alcune delle API di ActionBar sono state riportate al livello API 7 (Android 2,1) e sono disponibili tramite la [libreria V7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat), resa disponibile per le app Novell. Android tramite il pacchetto [Novell Android Support Library-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) .



## <a name="introducing-tabs-in-the-actionbar"></a>Introduzione alle schede in ActionBar

La barra delle azioni tenta di visualizzare tutte le schede simultaneamente e di fare in modo che tutte le schede siano di dimensioni uguali in base alla larghezza dell'etichetta della scheda più ampia. Questo è illustrato nello screenshot seguente: 

![Schermata di esempio di ActionBar con tutte le schede di uguale dimensione visualizzate](with-action-bar-images/image1.png)

Quando ActionBar non è in grado di visualizzare tutte le schede, imposta le schede in una visualizzazione scorrevole orizzontalmente. L'utente può scorrere rapidamente verso sinistra o verso destra per visualizzare le schede rimanenti. Questo screenshot di Google Play Mostra un esempio di questo: 

![Schermata di esempio delle schede in una visualizzazione scorrevole orizzontalmente](with-action-bar-images/image2.png)

Ogni scheda nella barra delle azioni deve essere associata a un [*frammento*](~/android/platform/fragments/index.md). Quando l'utente seleziona una scheda, l'applicazione visualizzerà il frammento associato alla scheda. Il ActionBar non è responsabile della visualizzazione del frammento appropriato per l'utente. Al contrario, ActionBar invierà una notifica all'applicazione sulle modifiche di stato in una scheda tramite una classe che implementa l'interfaccia ActionBar. ITabListener. Questa interfaccia fornisce tre metodi di callback che Android richiamerà quando lo stato della scheda cambia: 

- **OnTabSelected** : questo metodo viene chiamato quando l'utente seleziona la scheda. Dovrebbe visualizzare il frammento.

- **OnTabReselected** : questo metodo viene chiamato quando la scheda è già selezionata, ma viene nuovamente selezionata dall'utente. Questo callback viene in genere usato per aggiornare o aggiornare il frammento visualizzato.

- **OnTabUnselected** : questo metodo viene chiamato quando l'utente seleziona un'altra scheda. Questo callback viene utilizzato per salvare lo stato nel frammento visualizzato prima che venga visualizzato.

Novell. Android esegue il `ActionBar.ITabListener` wrapping di con eventi `ActionBar.Tab` sulla classe. Le applicazioni possono assegnare i gestori eventi a uno o più di questi eventi. Ci sono tre eventi (uno per ogni metodo in `ActionBar.ITabListener`) che verrà generato da una scheda della barra delle azioni: 

- TabSelected
- TabReselected
- TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Aggiunta di schede a ActionBar

ActionBar è nativo per Android 3,0 (livello API 11) e versioni successive ed è disponibile per qualsiasi applicazione Novell. Android destinata a questa API come minimo. 

I passaggi seguenti illustrano come aggiungere schede ActionBar a un'attività Android: 

1. &ndash; `NavigationMode` `ActionBar.NavigationModeTabs` `ActionBar` Nel metodo di un' attività&ndash; prima dell'inizializzazione di *qualsiasi widget dell'interfaccia utente* , un'applicazione deve impostare su come illustrato in questo frammento di codice: `OnCreate`

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Creare una nuova scheda utilizzando `ActionBar.NewTab()`.

3. Assegnare i gestori eventi o fornire un'implementazione `ActionBar.ITabListener` personalizzata che risponderà agli eventi generati quando l'utente interagisce con le schede ActionBar.

4. Aggiungere la scheda creata nel passaggio precedente a `ActionBar`.


Il codice seguente è un esempio dell'uso di questi passaggi per aggiungere schede a un'applicazione che usa i gestori eventi per rispondere alle modifiche dello stato: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>Gestori eventi rispetto a ActionBar. ITabListener

Le applicazioni devono utilizzare i gestori eventi `ActionBar.ITabListener` e per diversi scenari. I gestori di eventi offrono una certa quantità di praticità sintattica; non è necessario creare una classe e implementare `ActionBar.ITabListener`. Questa praticità comporta un costo &ndash; Novell. Android esegue questa trasformazione per l'utente, creando una classe e implementando `ActionBar.ITabListener` automaticamente. Si tratta di un problema quando un'applicazione dispone di un numero limitato di schede. 

Quando si gestiscono molte schede o si condividono funzionalità comuni tra le schede ActionBar, può essere più efficiente in termini di memoria e prestazioni per creare una classe personalizzata che implementa `ActionBar.ITabListener`e condivide una singola istanza della classe. Questa operazione ridurrà il numero di GREF che un'applicazione Novell. Android sta usando. 



### <a name="backwards-compatibility-for-older-devices"></a>Compatibilità con le versioni precedenti per i dispositivi meno recenti

[Android Support Library V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back Ports ActionBar Tabs to Android 2,1 (API Level 7). Le schede sono accessibili in un'applicazione Novell. Android dopo che il componente è stato aggiunto al progetto.

Per usare ActionBar, è necessario che un'attività `ActionBarActivity` sottoclassa e usi il tema AppCompat come illustrato nel frammento di codice seguente:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Un'attività può ottenere un riferimento al relativo ActionBar dalla `ActionBarActivity.SupportingActionBar` proprietà. Il frammento di codice seguente illustra un esempio di configurazione di ActionBar in un'attività:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```


## <a name="summary"></a>Riepilogo

In questa guida è stato illustrato come creare un'interfaccia utente a schede in un Novell. Android usando ActionBar. È stato illustrato come aggiungere schede a ActionBar e come un'attività può interagire con gli eventi di tabulazione `ActionBar.ITabListener` tramite l'interfaccia. È stato anche illustrato il modo in cui la libreria di supporto di Android V7 AppCompat pacchetto backports le schede ActionBar a versioni precedenti di Android. 


## <a name="related-links"></a>Collegamenti correlati

- [ActionBarTabs (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-actionbartabs)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Frammenti](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Modello di Barra delle azioni](https://developer.android.com/design/patterns/actionbar.html)
- [Android V7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Novell. Android libreria di supporto V7 AppCompat pacchetto NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
