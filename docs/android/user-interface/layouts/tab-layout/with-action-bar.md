---
title: Layout a schede con il ActionBar
description: Questa guida vengono presentati e come usare le APIs ActionBar per creare un'interfaccia utente a schede in un'applicazione di xamarin.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 3e96ce2064391d585943f4d79453f8b4f8c6f583
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
ms.locfileid: "32020127"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Layout a schede con il ActionBar

_Questa guida vengono presentati e come usare le APIs ActionBar per creare un'interfaccia utente a schede in un'applicazione di xamarin._


## <a name="overview"></a>Panoramica

La barra delle azioni è un modello di interfaccia utente di Android che viene utilizzato per fornire un'interfaccia utente coerente per le principali funzionalità, ad esempio schede, identità dell'applicazione, i menu e ricerca. In Android 3.0 (livello API 11), Google ha introdotto le APIs ActionBar per la piattaforma Android. Le APIs ActionBar introdurre temi dell'interfaccia utente per fornire un aspetto coerente e classi che consentono di interfacce utente a schede. Questa guida viene descritto come aggiungere schede sulla barra delle azioni in un'applicazione di xamarin. Viene inoltre illustrato come utilizzare la libreria di supporto Android v7 alle schede di ActionBar backport per applicazioni xamarin destinate a Android 2.1 per Android 2.3. 

Si noti che `Toolbar` è un componente di barra più recenti e più generalizzato di azione che è necessario utilizzare invece di `ActionBar` (`Toolbar` è stato progettato per sostituire `ActionBar`). Per ulteriori informazioni, vedere [barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Requisiti

Qualsiasi applicazione xamarin destinata a livello dell'API 11 (Android 3.0) o versioni successive ha accesso alle APIs ActionBar come parte delle API di Android native. 

Alcune delle APIs ActionBar sono stati trasferiti nuovamente a livello di API 7 (Android 2.1) e sono disponibili tramite il [V7 delle applicazioni libreria](http://developer.android.com/tools/support-library/features.html#v7-appcompat), che viene reso disponibile per le app xamarin mediante i [Xamarin Android libreria di supporto - V7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto.



## <a name="introducing-tabs-in-the-actionbar"></a>Introduzione a schede dell'ActionBar

La barra delle azioni tenta di visualizzare contemporaneamente tutte le schede e apportare tutte le schede di uguale dimensione in base alla larghezza dell'etichetta della scheda più ampio. Questo comportamento è illustrato nella schermata seguente: 

![Schermata di esempio del ActionBar con tutte le schede di uguali dimensioni visualizzate](with-action-bar-images/image1.png)

Quando il ActionBar non è possibile visualizzare tutte le schede, imposterà le schede in una visualizzazione scorrevole orizzontalmente. L'utente può scorrere verso sinistra o verso destra per visualizzare sulle schede rimanenti. Questa schermata da Google Play è riportato un esempio: 

![Schermata di esempio delle schede in una visualizzazione scorrevole orizzontalmente](with-action-bar-images/image2.png)

Ogni scheda nella barra delle azioni deve essere associato un [ *frammento*](~/android/platform/fragments/index.md). Quando l'utente seleziona una scheda, l'applicazione visualizzerà il frammento che è associato la scheda. Il ActionBar non è responsabile per la visualizzazione nel frammento appropriato per l'utente. Al contrario, il ActionBar verrà notificare a un'applicazione sulle modifiche di stato in una scheda tramite una classe che implementa l'interfaccia ActionBar.ITabListener. Questa interfaccia fornisce tre metodi di callback che verrà richiamato Android quando cambia lo stato della scheda: 

-  **OnTabSelected** -questo metodo viene chiamato quando l'utente seleziona la scheda. Viene visualizzato il frammento.

-  **OnTabReselected** -questo metodo viene chiamato quando la scheda è già selezionata, ma viene nuovamente selezionata dall'utente. Questo callback viene generalmente utilizzato per l'aggiornamento/aggiornare il frammento visualizzato.

-  **OnTabUnselected** -questo metodo viene chiamato quando l'utente seleziona un'altra scheda. Questo callback viene utilizzato per salvare lo stato nel frammento di visualizzati prima di scomparire.

Xamarin esegue il wrapping di `ActionBar.ITabListener` con gli eventi nella `ActionBar.Tab` classe. Le applicazioni possono assegnare i gestori eventi per uno o più di questi eventi. Esistono tre eventi (una per ogni metodo in `ActionBar.ITabListener`) che genera una scheda della barra di azione: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Aggiunta di schede di ActionBar

Il ActionBar è native per Android 3.0 (livello API 11) e versioni successive ed è disponibile per qualsiasi applicazione xamarin destinato a questa API come minimo. 

La procedura seguente viene illustrato come aggiungere schede ActionBar a un'attività Android: 

1. Nel `OnCreate` metodo di un'attività &ndash; *prima di inizializzare qualsiasi widget dell'interfaccia utente* &ndash; un'applicazione deve impostare la `NavigationMode` sul `ActionBar` per `ActionBar.NavigationModeTabs` come mostrato nel codice frammento di codice:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Crea una nuova scheda con `ActionBar.NewTab()`.

3. Assegnare i gestori eventi o fornire un oggetto personalizzato `ActionBar.ITabListener` implementazione che risponde agli eventi che vengono generati quando l'utente interagisce con le schede ActionBar.

4. La scheda in cui è stata creata nel passaggio precedente per aggiungere il `ActionBar`.


Il codice seguente è un esempio di utilizzo di questi passaggi per aggiungere schede a un'applicazione che utilizza i gestori eventi per rispondere alle modifiche di stato: 

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


#### <a name="event-handlers-vs-actionbaritablistener"></a>I gestori di eventi e ActionBar.ITabListener

Le applicazioni devono utilizzare i gestori eventi e `ActionBar.ITabListener` per scenari diversi. I gestori di eventi offrono una certa quantità di sintattica; si salvano dalla necessità di creare una classe e implementare `ActionBar.ITabListener`. Questo metodo pratico forniti a un costo &ndash; xamarin esegue questa trasformazione per la creazione di una classe e l'implementazione `ActionBar.ITabListener` automaticamente. L'operazione è corretta quando un'applicazione dispone di un numero limitato di schede. 

Quando si lavora con molte schede, o condivisione funzionalità comuni tra le schede ActionBar, può essere più efficiente in termini di memoria e prestazioni per creare una classe personalizzata che implementa `ActionBar.ITabListener`e la condivisione di una singola istanza della classe. Ciò riduce il numero di GREF che utilizza un'applicazione di xamarin. 



### <a name="backwards-compatibility-for-older-devices"></a>La compatibilità per i dispositivi meno recenti

Il [delle applicazioni di libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back porte schede ActionBar per Android 2.1 (API livello 7). Le schede sono accessibili in un'applicazione di xamarin dopo che questo componente è stato aggiunto al progetto.

Per utilizzare il ActionBar, un'attività deve creare una sottoclasse `ActionBarActivity` e utilizzare il tema delle applicazioni, come illustrato nel frammento di codice seguente:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Un'attività può ottenere un riferimento relativo ActionBar dal `ActionBarActivity.SupportingActionBar` proprietà. Frammento di codice seguente viene illustrato un esempio di configurazione di ActionBar in un'attività:

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

In questa guida è illustrata la procedura per creare un'interfaccia utente a schede in un xamarin utilizzando il ActionBar. Trattati come aggiunta di schede di ActionBar e l'interazione di un'attività con gli eventi di scheda tramite le `ActionBar.ITabListener` interfaccia. È stato illustrato anche come schede del backports di pacchetto delle applicazioni di libreria di supporto Android v7 di ActionBar alle versioni precedenti di Android. 


## <a name="related-links"></a>Collegamenti correlati

- [ActionBarTabs (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Frammenti](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Pattern della barra di azione](http://developer.android.com/design/patterns/actionbar.html)
- [Delle applicazioni Android v7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Pacchetto NuGet di xamarin supporto libreria v7 delle applicazioni](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
