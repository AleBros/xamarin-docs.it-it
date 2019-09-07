---
title: Procedura dettagliata Frammenti - Parte 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 0363213d76d9a67b559614741edf37d296848075
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761663"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Scenario procedura dettagliata &ndash; frammenti

La [procedura dettagliata &ndash; relativa ai frammenti-parte 1](./walkthrough.md) ha illustrato come creare e usare frammenti in un'app Android destinata a schermate più piccole su un telefono. Il passaggio successivo di questa procedura dettagliata consiste nel modificare l'applicazione in modo da sfruttare lo spazio orizzontale aggiuntivo sul &ndash; tablet. ci sarà un'attività che sarà sempre l'elenco di Riproduci `TitlesFragment`(il `PlayQuoteFragment` ) e verrà aggiunto dinamicamente per l'attività in risposta a una selezione eseguita dall'utente:

[![App in esecuzione nel Tablet](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Anche i telefoni eseguiti in modalità orizzontale trarranno vantaggio da questa funzionalità avanzata:

[![App in esecuzione su un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Aggiornamento dell'app per la gestione dell'orientamento orizzontale

Le modifiche seguenti si basano sulle operazioni eseguite nella [procedura dettagliata relativa ai frammenti-Phone](./walkthrough.md)

1. Creare un layout alternativo per visualizzare sia il `TitlesFragment` che `PlayQuoteFragment`il.
1. Aggiornare `TitlesFragment` per rilevare se il dispositivo visualizza entrambi i frammenti simultaneamente e modificare il comportamento di conseguenza.
1. Aggiornamento `PlayQuoteActivity` da chiudere quando il dispositivo è in modalità orizzontale.

## <a name="1-create-an-alternate-layout"></a>1. Creare un layout alternativo

Quando si crea un'attività principale in un dispositivo Android, Android decide quale layout caricare in base all'orientamento del dispositivo. Per impostazione predefinita, Android fornirà il file di layout **Resources/layout/activity_main. aXML** . Per i dispositivi che vengono caricati in modalità orizzontale, Android fornirà il file di layout **Resources/layout-Land/activity_main. aXML** . La guida sulle [risorse Android](/xamarin/android/app-fundamentals/resources-in-android) contiene altre informazioni su come Android decide quali file di risorse caricare per un'applicazione.

Creare un layout alternativo destinato all'orientamento **orizzontale** attenendosi alla procedura descritta nella Guida [layout alternativi](/xamarin/android/user-interface/android-designer/alternative-layout-views) . Questa operazione dovrebbe aggiungere un nuovo file di risorse di layout al progetto, **Resources/layout/activity_main. aXML**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Layout alternativo in Esplora soluzioni](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Layout alternativo in riquadro della soluzione](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Dopo aver creato il layout alternativo, modificare l'origine del file **Resources/layout-Land/activity_main. aXML** in modo che corrisponda al codice XML seguente:

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

Alla visualizzazione radice dell'attività viene assegnato l'ID `two_fragments_layout` della risorsa e sono presenti due viste secondarie, una `fragment` e `FrameLayout`una. Mentre l' `fragment` oggetto viene caricato in modo statico `FrameLayout` , funge da "segnaposto" che verrà sostituito in fase di `PlayQuoteFragment`esecuzione da. Ogni volta che viene selezionato un nuovo gioco in `TitlesFragment`, l' `playquote_container` oggetto verrà aggiornato con `PlayQuoteFragment`una nuova istanza di.

Ognuna delle visualizzazioni secondarie occuperà l'altezza completa del rispettivo elemento padre. La larghezza di ogni Sottovisualizzazione è controllata dagli `android:layout_weight` attributi e. `android:layout_width` In questo esempio, ogni Sottovisualizzazione occuperà il 50% della larghezza fornita dall'elemento padre. Vedere [il documento di Google in LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) per informazioni dettagliate sul _peso del layout_.

## <a name="2-changes-to-titlesfragment"></a>2. Modifiche a TitlesFragment

Una volta creato il layout alternativo, è necessario aggiornarlo `TitlesFragment`. Quando l'app Visualizza i due frammenti in un'attività, `TitlesFragment` deve `PlayQuoteFragment` caricare nell'attività padre. In caso `TitlesFragment` contrario, deve `PlayQuoteActivity` avviare il che `PlayQuoteFragment`ospita. Un flag booleano consente `TitlesFragment` di determinare il comportamento da usare. Questo flag verrà inizializzato nel `OnActivityCreated` metodo.

In primo luogo, aggiungere una variabile di istanza all'inizio `TitlesFragment` della classe:

```csharp
bool showingTwoFragments;
```

Quindi, aggiungere il frammento di codice `OnActivityCreated` seguente a per inizializzare la variabile: 

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

Se il dispositivo è in esecuzione in modalità orizzontale, il `FrameLayout` con l'ID `playquote_container` risorsa sarà visibile sullo schermo, `true`quindi `showingTwoFragments` verrà inizializzato su. Se il dispositivo è in esecuzione in modalità verticale, `playquote_container` non sarà sullo schermo, quindi `showingTwoFragments` `false`sarà.

Il `ShowPlayQuote` metodo deve modificare il modo in cui viene visualizzata una &ndash; virgoletta in un frammento o viene avviata una nuova attività.  Aggiornare il `ShowPlayQuote` metodo per caricare un frammento quando vengono visualizzati due frammenti. in caso contrario, è necessario avviare un'attività:

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

Se l'utente ha selezionato una riproduzione diversa da quella `PlayQuoteFragment`attualmente visualizzata in, viene creato un nuovo `PlayQuoteFragment` oggetto che `playquote_container` sostituisce il contenuto di nel contesto di un oggetto `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Completare il codice per TitlesFragment

Dopo aver completato tutte le modifiche precedenti `TitlesFragment`a, la classe completa deve corrispondere al codice seguente:

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

È necessario prestare attenzione a un dettaglio finale: `PlayQuoteActivity` non è necessario quando il dispositivo è in modalità orizzontale. Se il dispositivo è in modalità `PlayQuoteActivity` orizzontale, non deve essere visibile. Aggiornare il `OnCreate` metodo di `PlayQuoteActivity` in modo che si chiuda. Questo codice è la versione finale di `PlayQuoteActivity.OnCreate`:

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

Questa modifica consente di aggiungere un controllo per l'orientamento del dispositivo. Se è in modalità orizzontale, `PlayQuoteActivity` si chiuderà automaticamente.

## <a name="4-run-the-application"></a>4. Esecuzione dell'applicazione

Una volta completate le modifiche, eseguire l'app, ruotare il dispositivo in modalità orizzontale (se necessario), quindi selezionare una riproduzione. L'offerta deve essere visualizzata nella stessa schermata dell'elenco di Riproduci:

[![App in esecuzione su un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![App in esecuzione in un Tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
