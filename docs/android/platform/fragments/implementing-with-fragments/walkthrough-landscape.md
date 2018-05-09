---
title: Frammenti procedura dettagliata - parte 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 58291388d375a4fd9273c8e0cd46db3799966766
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Procedura dettagliata di frammenti &ndash; orizzontale

Il [Fragments Walkthrough &ndash; parte 1](./walkthrough.md) è stato illustrato come creare e usare i frammenti in un'app per Android destinato a schermi più piccoli in un telefono. Il passaggio successivo di questa procedura dettagliata consiste nel modificare l'applicazione per poter sfruttare lo spazio extra orizzontale su tablet &ndash; sarà presente un'attività che sarà sempre l'elenco di riproduzione (la `TitlesFragment`) e `PlayQuoteFragment` verranno aggiunti in modo dinamico per l'attività in risposta a una selezione effettuata dall'utente:

[![App in esecuzione su un tablet](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Che sono in esecuzione in modalità orizzontale sui telefoni anche trarranno vantaggio da questa funzionalità avanzata:

[![App in esecuzione in un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Aggiornamento dell'app per gestire con orientamento orizzontale

Le modifiche seguenti verranno si basano su quelle eseguite nel [Fragments Walkthrough - telefono](./walkthrough.md)

1. Creare un layout diverso per visualizzare entrambi i `TitlesFragment` e `PlayQuoteFragment`.
1. Aggiornamento `TitlesFragment` per rilevare se il dispositivo è la visualizzazione contemporaneamente entrambi i frammenti e modificare il comportamento di conseguenza.
1. Aggiornamento `PlayQuoteActivity` per chiudere quando il dispositivo è in modalità orizzontale.

## <a name="1-create-an-alternate-layout"></a>1. Creare un layout di alternativo

Quando l'attività principale viene creata in un dispositivo Android, Android stabilirà layout da caricare in base all'orientamento del dispositivo. Per impostazione predefinita, Android fornirà il **Resources/layout/activity_main.axml** file di layout. Per i dispositivi che caricano in modalità orizzontale Android fornirà il **Resources/layout-land/activity_main.axml** file di layout. La Guida nel [risorse Android](/xamarin/android/app-fundamentals/resources-in-android) contiene ulteriori informazioni sulla modalità Android decide quali risorse di file per caricare per un'applicazione.

Creare un layout diverso che fa riferimento **orizzontale** orientamento seguendo i passaggi descritti nel [layout alternativi](/xamarin/android/user-interface/android-designer/alternative-layout-views) Guida. Questo deve aggiungere un nuovo file di risorse di layout per il progetto, **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Layout alternativi in Esplora soluzioni](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Layout alternativo nel riquadro soluzione](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

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

La visualizzazione principale dell'attività viene assegnata l'ID della risorsa `two_fragments_layout` e sono disponibili due visualizzazioni secondari, una `fragment` e un `FrameLayout`. Mentre il `fragment` viene caricato in modo statico, il `FrameLayout` "segnaposto" che verrà sostituito in fase di esecuzione per il `PlayQuoteFragment`. Ogni volta che un nuovo play sia selezionato nel `TitlesFragment`, il `playquote_container` verrà aggiornato con una nuova istanza del `PlayQuoteFragment`.

Ognuna delle viste del secondarie occupano l'altezza totale del relativo elemento padre. La larghezza di ogni visualizzazione secondaria è controllata dal `android:layout_weight` e `android:layout_width` attributi. In questo esempio, ogni secondaria occupano 50% della larghezza forniscono dall'elemento padre. Vedere [documento Google al massimo il LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) per informazioni dettagliate sulle _peso Layout_.

## <a name="2-changes-to-titlesfragment"></a>2. Modifiche a TitlesFragment

Dopo aver creato il layout alternativo, è necessario aggiornare `TitlesFragment`. Quando l'app verrà visualizzati i due frammenti in un'attività, quindi `TitlesFragment` devono essere caricati il `PlayQuoteFragment` nell'attività padre. In caso contrario, `TitlesFragment` deve essere avviato il `PlayQuoteActivity` host in cui il `PlayQuoteFragment`. Flag booleano consentirà `TitlesFragment` determinano quale comportamento deve utilizzare. Questo flag verrà inizializzato nel `OnActivityCreated` metodo.

In primo luogo, aggiungere una variabile di istanza in cima il `TitlesFragment` classe:

```csharp
bool showingTwoFragments;
```

Quindi, aggiungere il seguente frammento di codice `OnActivityCreated` per inizializzare la variabile: 

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

Se il dispositivo è in esecuzione in modalità orizzontale, la `FrameLayout` con l'ID della risorsa `playquote_container` sarà visibile sullo schermo, in modo `showingTwoFragments` verranno inizializzate ai `true`. Se il dispositivo è in esecuzione in modalità verticale, quindi `playquote_container` non sarà nella schermata, pertanto `showingTwoFragments` sarà `false`.

Il `ShowPlayQuote` metodo sarà necessario modificare la modalità di visualizzazione di un'offerta &ndash; in un frammento o avviare una nuova attività.  Aggiornamento di `ShowPlayQuote` metodo per caricare un frammento quando vengono visualizzate due frammenti di, in caso contrario deve essere avviato un'attività:

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

Se l'utente ha selezionato un play che è diverso da quello che viene attualmente visualizzata nell'elenco `PlayQuoteFragment`, quindi un nuovo `PlayQuoteFragment` viene creato e sostituirà il contenuto del `playquote_container` all'interno del contesto di un `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Codice completo per TitlesFragment

Dopo aver completato tutte le modifiche precedenti al `TitlesFragment`, la classe deve corrispondere a questo codice:

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

È un dettaglio finale da svolgere: `PlayQuoteActivity` non è necessario quando il dispositivo è in modalità orizzontale. Se il dispositivo è in modalità orizzontale il `PlayQuoteActivity` non devono essere visibili. Aggiornamento di `OnCreate` metodo `PlayQuoteActivity` in modo che verrà chiuso automaticamente. Questo codice è la versione finale di `PlayQuoteActivity.OnCreate`:

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

Questa modifica viene aggiunto un controllo per l'orientamento del dispositivo. Se si trova in modalità orizzontale, quindi `PlayQuoteActivity` verrà chiusa automaticamente.

## <a name="4-run-the-application"></a>4. Esecuzione dell'applicazione

Una volta queste modifiche sono state completate, eseguire l'app, ruota il dispositivo per orizzontale (se necessario) e quindi selezionare una riproduzione. L'offerta deve essere visualizzata nella schermata stessa come elenco di riproduzione:

[![App in esecuzione in un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![App in esecuzione su un tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
