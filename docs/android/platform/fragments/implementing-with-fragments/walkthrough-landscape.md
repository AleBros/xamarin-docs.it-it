---
title: Procedura dettagliata Frammenti - Parte 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 7ec8ad6ce428107d2255dd07c7e69c9e77780c09
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026271"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Procedura dettagliata frammenti &ndash; orizzontale

Il [Fragments Walkthrough &ndash; parte 1](./walkthrough.md) è stato illustrato come creare e usare i frammenti in un'app Android destinata a schermi più piccoli in un telefono. Il passaggio successivo di questa procedura dettagliata consiste nel modificare l'applicazione per poter sfruttare lo spazio extra orizzontale sul tablet &ndash; esisterà un'attività che sarà sempre l'elenco di riproduzione (la `TitlesFragment`) e `PlayQuoteFragment` verrà aggiunto in modo dinamico per l'attività in risposta a una selezione effettuata dall'utente:

[![App in esecuzione sul tablet](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

I telefoni in esecuzione in modalità orizzontale trarranno vantaggio anche da questa funzionalità avanzata:

[![App in esecuzione in un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>L'aggiornamento dell'app per la gestione con orientamento orizzontale

Le modifiche seguenti compilerà partendo dal lavoro che è stato eseguito nel [Fragments Walkthrough - telefono](./walkthrough.md)

1. Creare un layout alternativo per visualizzare sia le `TitlesFragment` e `PlayQuoteFragment`.
1. Aggiornamento `TitlesFragment` per rilevare se il dispositivo verrà visualizzati contemporaneamente entrambi i frammenti e modificare il comportamento di conseguenza.
1. Aggiornamento `PlayQuoteActivity` chiudere quando il dispositivo è in modalità orizzontale.

## <a name="1-create-an-alternate-layout"></a>1. Creare un layout alternativo

Quando l'attività principale viene creata in un dispositivo Android, Android stabilirà che il layout da caricare in base all'orientamento del dispositivo. Per impostazione predefinita, Android fornirà il **Resources/layout/activity_main.axml** file di layout. Per i dispositivi che viene caricato in modalità orizzontale Android fornirà il **Resources/layout-land/activity_main.axml** file di layout. La Guida sul [risorse Android](/xamarin/android/app-fundamentals/resources-in-android) contiene altre informazioni su come Android decide quali risorse di file da caricare per un'applicazione.

Creare un layout alternativo destinato **panorama** orientamento seguendo i passaggi descritti nel [layout alternativi](/xamarin/android/user-interface/android-designer/alternative-layout-views) Guida. Questo deve aggiungere un nuovo file di risorse di layout per il progetto, **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Layout alternativi in Esplora soluzioni](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Layout alternativo nel riquadro della soluzione](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Dopo aver creato il layout alternativo, modificare l'origine del file **Resources/layout-land/activity_main.axml** in modo che corrisponda il codice XML:

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

La visualizzazione principale dell'attività viene assegnata l'ID risorsa `two_fragments_layout` e sono disponibili due visualizzazioni secondarie, una `fragment` e un `FrameLayout`. Mentre il `fragment` viene caricato in modo statico, il `FrameLayout` funge da "segnaposto" che verrà sostituita in fase di esecuzione per il `PlayQuoteFragment`. Ogni volta che viene selezionato un nuovo gioco nel `TitlesFragment`, il `playquote_container` verrà aggiornato con una nuova istanza del `PlayQuoteFragment`.

Ognuna delle visualizzazioni secondarie occupano l'altezza totale del relativo elemento padre. La larghezza di ogni visualizzazione secondaria è controllata dal `android:layout_weight` e `android:layout_width` attributi. In questo esempio, ogni visualizzazione secondaria verrà occupata dal 50% della larghezza forniscono dall'elemento padre. Vedere [documento di Google nel LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) per informazioni dettagliate sulle _Layout peso_.

## <a name="2-changes-to-titlesfragment"></a>2. Modifiche apportate a TitlesFragment

Dopo aver creato il layout alternativo, è necessario aggiornare `TitlesFragment`. Quando l'app Visualizza i due frammenti in un'attività, quindi `TitlesFragment` dovrà essere caricata la `PlayQuoteFragment` nell'attività padre. In caso contrario, `TitlesFragment` deve avviare il `PlayQuoteActivity` host a cui il `PlayQuoteFragment`. Flag booleano aiuterà `TitlesFragment` determinano quale comportamento deve utilizzare. Questo flag verrà inizializzato nel `OnActivityCreated` (metodo).

In primo luogo, aggiungere una variabile di istanza all'inizio del `TitlesFragment` classe:

```csharp
bool showingTwoFragments;
```

Quindi, aggiungere il frammento di codice seguente per `OnActivityCreated` per inizializzare la variabile: 

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

Se il dispositivo è in esecuzione in modalità orizzontale, la `FrameLayout` con l'ID risorsa `playquote_container` sarà visibile sullo schermo, in modo `showingTwoFragments` verranno inizializzate ai `true`. Se il dispositivo è in esecuzione in modalità verticale, quindi `playquote_container` non è sullo schermo, pertanto `showingTwoFragments` saranno `false`.

Il `ShowPlayQuote` metodo sarà necessario modificare la modalità di visualizzazione di un'offerta &ndash; in un frammento o avviare una nuova attività.  Aggiornamento di `ShowPlayQuote` per caricare un frammento quando vengono visualizzati due frammenti, in caso contrario, deve avviare un'attività:

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

Se l'utente ha selezionato un gioco che è diverso da quello che è attualmente visualizzato nel `PlayQuoteFragment`, quindi un nuovo `PlayQuoteFragment` viene creato e sostituirà il contenuto delle `playquote_container` all'interno del contesto di un `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Codice completo per TitlesFragment

Dopo aver completato tutte le modifiche precedenti al `TitlesFragment`, la classe completa deve corrispondere a questo codice:

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

## <a name="3-changes-to-playquoteactivity"></a>3. Modifiche apportate a PlayQuoteActivity

È un dettaglio finale che si occupi dei: `PlayQuoteActivity` non è necessario quando il dispositivo è in modalità orizzontale. Se il dispositivo è in modalità orizzontale il `PlayQuoteActivity` non devono essere visibili. Aggiorna il `OnCreate` metodo di `PlayQuoteActivity` in modo che verranno chiuse se stesso. Questo codice è la versione finale di `PlayQuoteActivity.OnCreate`:

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

Questa modifica aggiunge un controllo per l'orientamento del dispositivo. Se si trova in modalità orizzontale, quindi `PlayQuoteActivity` verranno chiudersi.

## <a name="4-run-the-application"></a>4. Esecuzione dell'applicazione

Dopo che queste modifiche sono state completate, eseguire l'app, ruota il dispositivo all'orientamento orizzontale modalità (se necessario) e quindi selezionare riprodurre. L'offerta deve essere visualizzato nella stessa schermata dell'elenco di riproduzione:

[![App in esecuzione in un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![App in esecuzione in un tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
