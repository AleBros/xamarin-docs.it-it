---
title: Procedura dettagliata Frammenti - Parte 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 4d9ef88f39914f8fa5e578577ee9f6977c2bc88e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020273"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Scenario di &ndash; frammentazione dettagliataFragments walkthrough landscape

La [procedura &ndash; dettagliata frammenti parte 1](./walkthrough.md) ha illustrato come creare e usare frammenti in un'app Android destinata agli schermi più piccoli in un telefono. Il passaggio successivo di questa procedura dettagliata consiste nel modificare &ndash; l'applicazione per sfruttare lo spazio orizzontale `TitlesFragment`aggiuntivo `PlayQuoteFragment` sul tablet ci sarà sempre un'attività che sarà sempre l'elenco delle riproduzioni (il ) e verrà aggiunto dinamicamente all'attività in risposta a una selezione effettuata dall'utente:

[![App in esecuzione su tablet](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Anche i telefoni in esecuzione in modalità orizzontale trarranno vantaggio da questo miglioramento:

[![App in esecuzione su un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Aggiornamento dell'app per gestire l'orientamento orizzontaleUpdating the app to handle landscape orientation

Le seguenti modifiche si baseranno sul lavoro eseguito in [Procedura dettagliata per i frammenti - Telefono](./walkthrough.md)

1. Creare un layout alternativo `TitlesFragment` per `PlayQuoteFragment`visualizzare sia il metodo e .
1. Aggiornare `TitlesFragment` per rilevare se il dispositivo visualizza entrambi i frammenti contemporaneamente e modificare il comportamento di conseguenza.
1. Aggiornare `PlayQuoteActivity` per chiudere quando il dispositivo è in modalità orizzontale.

## <a name="1-create-an-alternate-layout"></a>1. Creare un layout alternativo

Quando attività principale viene creato su un dispositivo Android, Android deciderà quale layout caricare in base all'orientamento del dispositivo. Per impostazione predefinita, Android fornirà il file di layout **Resources/layout/activity_main.axml.** Per i dispositivi che vengono caricati in modalità orizzontale Android fornirà il file di layout **Resources/layout-land/activity_main.axml.** La guida su [Risorse Android](/xamarin/android/app-fundamentals/resources-in-android) contiene ulteriori dettagli su come Android decide quali file di risorse caricare per un'applicazione.

Creare un layout alternativo destinato all'orientamento **Orizzontale** seguendo i passaggi descritti nella guida [Layout alternativi.](/xamarin/android/user-interface/android-designer/alternative-layout-views) Verrà aggiunto un nuovo file di risorse di layout al progetto, **Resources/layout/activity_main.axml**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Layout alternativo in Esplora soluzioniAlternate layout in Solution Explorer](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

[![Layout alternativo nel riquadro della soluzione](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Dopo aver creato il layout alternativo, modificare l'origine del file **Resources/layout-land/activity_main.axml** in modo che corrisponda a questo codice XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

Alla visualizzazione radice dell'attività viene `two_fragments_layout` assegnato l'ID risorsa `fragment` e `FrameLayout`sono disponibili due sottovisualizzazioni, a e . Mentre `fragment` l'oggetto viene `FrameLayout` caricato in modo statico, funge da "segnaposto" `PlayQuoteFragment`che verrà sostituito in fase di esecuzione dal metodo . Ogni volta che viene selezionata una nuova riproduzione nella `TitlesFragment`, l'oggetto `playquote_container` verrà aggiornato con una nuova istanza di `PlayQuoteFragment`.

Ognuna delle sottoviste occuperà l'intera altezza del padre. La larghezza di ogni visualizzazione `android:layout_weight` secondaria `android:layout_width` è controllata dagli attributi e . In questo esempio, ogni visualizzazione secondaria occuperà il 50% della larghezza fornita dall'elemento padre. Vedere [il documento di Google sul LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) per informazioni dettagliate su Layout _spessore_.

## <a name="2-changes-to-titlesfragment"></a>2. Modifiche a TitlesFragment

Una volta creato il layout alternativo, `TitlesFragment`è necessario aggiornare . Quando l'app visualizza i due frammenti `TitlesFragment` in un'attività, è necessario caricare l'oggetto `PlayQuoteFragment` nell'attività padre. In `TitlesFragment` caso contrario, dovrebbe avviare l'oggetto `PlayQuoteActivity` che ospita il `PlayQuoteFragment`file . Un flag booleano consente `TitlesFragment` di determinare il comportamento da utilizzare. Questo flag verrà inizializzato `OnActivityCreated` nel metodo.

Aggiungere innanzitutto una variabile di `TitlesFragment` istanza all'inizio della classe:

```csharp
bool showingTwoFragments;
```

Quindi, aggiungere il frammento di codice seguente per `OnActivityCreated` inizializzare la variabile: 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

Se il dispositivo è in esecuzione `FrameLayout` in modalità orizzontale, l'ID `playquote_container` della risorsa sarà visibile sullo schermo, pertanto `showingTwoFragments` verrà inizializzato su `true`. Se il dispositivo è in `playquote_container` esecuzione in modalità verticale, non sarà sullo schermo, così `showingTwoFragments` sarà `false`.

Il `ShowPlayQuote` metodo dovrà modificare la &ndash; modalità di visualizzazione di un'offerta in un frammento o avviare una nuova attività.  Aggiornare `ShowPlayQuote` il metodo per caricare un frammento quando vengono visualizzati due frammenti, altrimenti dovrebbe avviare un'attività:Update the method to load a fragment when showing two fragments, otherwise it should launch an Activity:

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Se l'utente ha selezionato una riproduzione diversa da quella `PlayQuoteFragment`attualmente visualizzata in , ne viene creata una nuova `PlayQuoteFragment` che sostituisce il contenuto di all'interno `playquote_container` del contesto di un `FragmentTransaction`oggetto .

### <a name="complete-code-for-titlesfragment"></a>Codice completo per TitlesFragment

Dopo aver completato tutte `TitlesFragment`le modifiche precedenti a , la classe completa deve corrispondere a questo codice:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }

        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3. Modifiche a PlayQuoteActivity

C'è un ultimo dettaglio `PlayQuoteActivity` di cui prendersi cura: non è necessario quando il dispositivo è in modalità orizzontale. Se il dispositivo è `PlayQuoteActivity` in modalità orizzontale, il dispositivo non dovrebbe essere visibile. Aggiornare `OnCreate` il `PlayQuoteActivity` metodo di in modo che si chiuda. Questo codice è la `PlayQuoteActivity.OnCreate`versione finale di:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

Questa modifica aggiunge un controllo per l'orientamento del dispositivo. Se è in modalità `PlayQuoteActivity` orizzontale, allora si chiuderà da solo.

## <a name="4-run-the-application"></a>4. Eseguire l'applicazione

Una volta completate queste modifiche, eseguire l'applicazione, ruotare il dispositivo in modalità orizzontale (se necessario), quindi selezionare una riproduzione. Il preventivo dovrebbe essere visualizzato sulla stessa schermata dell'elenco dei giochi:

[![App in esecuzione su un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![App in esecuzione su un tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
