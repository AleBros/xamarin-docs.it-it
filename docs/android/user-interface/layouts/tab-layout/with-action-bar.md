---
title: Layout a schede con ActionBar
description: Questa guida presenta e spiega come usare le APIs ActionBar per creare un'interfaccia utente a schede in un'applicazione xamarin. Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: af5554d08ac6c45fc0c392bd17cef5d91251bb1a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106507"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Layout a schede con ActionBar

_Questa guida presenta e spiega come usare le APIs ActionBar per creare un'interfaccia utente a schede in un'applicazione xamarin. Android._


## <a name="overview"></a>Panoramica

Barra delle azioni è un modello di interfaccia utente Android che viene utilizzato per fornire un'interfaccia utente coerente per le principali funzionalità, ad esempio schede, identità dell'applicazione, i menu e ricerca. In Android 3.0 (API livello 11), Google ha introdotto le APIs ActionBar per la piattaforma Android. APIs ActionBar introducono i temi di interfaccia utente per fornire un aspetto uniforme e coerente e classi che consentono di interfacce utente a schede. Questa guida illustra come aggiungere le schede della barra delle azioni per un'applicazione xamarin. Android. Inoltre illustrato come usare la libreria di supporto Android v7 per eseguire il backporting ActionBar schede per le applicazioni xamarin. Android come destinazione di Android 2.1 per Android 2.3. 

Si noti che `Toolbar` è un componente barra azione più recente e più generalizzata che è consigliabile usare invece di `ActionBar` (`Toolbar` è stato progettato per sostituire `ActionBar`). Per altre informazioni, vedere [sulla barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Requisiti

Qualsiasi applicazione xamarin. Android destinata a livello API 11 (Android 3.0) o versione successiva può accedere a APIs ActionBar come parte delle API di Android native. 

Alcune APIs ActionBar sono state convertite nuovamente a livello di API 7 (Android 2.1) e sono disponibili tramite il [libreria AppCompat V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat), che viene reso disponibile per le app xamarin. Android tramite il [Xamarin Android libreria di supporto - V7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto.



## <a name="introducing-tabs-in-the-actionbar"></a>Introduzione a schede in ActionBar

Barra delle azioni prova a visualizzare contemporaneamente tutte le schede e apportare tutte le schede di dimensioni in base alla larghezza dell'etichetta della scheda più ampio. Come illustrato nello screenshot seguente: 

![Screenshot di esempio dell'ActionBar con tutte le schede di uguali dimensioni illustrato](with-action-bar-images/image1.png)

Quando ActionBar non è possibile visualizzare tutte le schede, configurerà le schede in una visualizzazione scorrevole orizzontalmente. L'utente può scorrere rapidamente verso sinistra o destra per visualizzare sulle schede rimanenti. Questa schermata da Google Play Mostra un esempio: 

![Screenshot di esempio delle schede in una visualizzazione scorrevole orizzontalmente](with-action-bar-images/image2.png)

Ogni scheda nella barra delle azioni deve essere associato un [ *frammento*](~/android/platform/fragments/index.md). Quando l'utente seleziona una scheda, l'applicazione visualizzerà il frammento che è associato la scheda. ActionBar non è responsabile della visualizzazione il frammento appropriato per l'utente. Al contrario, ActionBar informerà un'applicazione sulle modifiche di stato in una scheda tramite una classe che implementa l'interfaccia ActionBar.ITabListener. Questa interfaccia fornisce tre metodi di callback che Android verrà richiamato quando viene modificato lo stato della scheda: 

-  **OnTabSelected** -questo metodo viene chiamato quando l'utente seleziona la scheda. Viene visualizzato il frammento.

-  **OnTabReselected** -questo metodo viene chiamato quando la scheda è già selezionata, ma viene nuovamente selezionata dall'utente. Questo callback viene generalmente utilizzato per l'aggiornamento o aggiornare il frammento visualizzato.

-  **OnTabUnselected** -questo metodo viene chiamato quando l'utente seleziona un'altra scheda. Questo callback viene utilizzato per salvare lo stato nel frammento visualizzato prima non viene più visualizzato.

Xamarin. Android esegue il wrapping di `ActionBar.ITabListener` con gli eventi nel `ActionBar.Tab` classe. Le applicazioni possono assegnare i gestori eventi a uno o più di questi eventi. Esistono tre eventi (uno per ogni metodo in `ActionBar.ITabListener`) che genera una scheda sulla barra dell'azione: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Aggiunta di schede ActionBar

ActionBar è native per Android 3.0 (API livello 11) e versioni successive ed è disponibile a tutte le applicazioni xamarin. Android destinata a questa API come minimo. 

I passaggi seguenti illustrano come aggiungere schede ActionBar a un'attività Android: 

1. Nel `OnCreate` metodo di un'attività &ndash; *prima che venga inizializzato alcun widget dell'interfaccia utente* &ndash; un'applicazione deve impostare la `NavigationMode` nel `ActionBar` a `ActionBar.NavigationModeTabs` come illustrato in questo codice frammento di codice:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Creare una nuova scheda con `ActionBar.NewTab()`.

3. Assegnare i gestori eventi o fornire un oggetto personalizzato `ActionBar.ITabListener` implementazione che risponderà agli eventi che vengono generati quando l'utente interagisce con le schede ActionBar.

4. Aggiungere la scheda in cui è stata creata nel passaggio precedente per il `ActionBar`.


Il codice seguente è un esempio dell'uso di questi passaggi per aggiungere schede a un'applicazione che usa i gestori eventi per rispondere alle modifiche di stato: 

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


#### <a name="event-handlers-vs-actionbaritablistener"></a>Visual Studio di gestori di eventi ActionBar.ITabListener

Le applicazioni devono utilizzare i gestori eventi e `ActionBar.ITabListener` per scenari diversi. Gestori di eventi viene offerta una certa quantità di comodità sintattica; essi si eviterà di dover creare una classe e implementare `ActionBar.ITabListener`. La comodità comportano dei costi &ndash; xamarin. Android esegue questa trasformazione per l'utente, la creazione di una classe e l'implementazione `ActionBar.ITabListener` automaticamente. Ciò non causa problemi se un'applicazione ha un numero limitato di schede. 

Quando si lavora con molte schede, o condivisione delle funzionalità comuni tra le schede ActionBar, può essere più efficiente in termini di memoria e prestazioni per creare una classe personalizzata che implementa `ActionBar.ITabListener`e la condivisione di una singola istanza della classe. Ciò ridurrà il numero di GREF che usa un'applicazione xamarin. Android. 



### <a name="backwards-compatibility-for-older-devices"></a>Con le versioni precedenti la compatibilità dei dispositivi meno recenti

Il [libreria di supporto Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back porte ActionBar schede per Android 2.1 (API livello 7). Le schede sono accessibili in un'applicazione xamarin. Android dopo che questo componente è stato aggiunto al progetto.

Per usare ActionBar, un'attività deve creare una sottoclasse `ActionBarActivity` e utilizzare il tema AppCompat, come illustrato nel frammento di codice seguente:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Un'attività può ottenere un riferimento al relativo ActionBar dal `ActionBarActivity.SupportingActionBar` proprietà. Il frammento di codice seguente illustra un esempio di impostazione ActionBar in un'attività:

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

In questa guida è stato illustrato come creare un'interfaccia utente a schede in xamarin. Android usando ActionBar. Viene illustrato come aggiungere le schede a ActionBar e come un'attività può interagire con gli eventi di scheda tramite il `ActionBar.ITabListener` interfaccia. Abbiamo anche esaminato come la libreria di supporto Android v7 AppCompat pacchetto backports ActionBar schede per le versioni precedenti di Android. 


## <a name="related-links"></a>Collegamenti correlati

- [ActionBarTabs (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Frammenti](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Modello della barra delle azioni](http://developer.android.com/design/patterns/actionbar.html)
- [AppCompat Android v7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Pacchetto NuGet AppCompat v7 della libreria di supporto di xamarin. Android](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
