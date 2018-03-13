---
title: Procedura dettagliata
ms.topic: article
ms.prod: xamarin
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: e5c058f173f64efe4a5c777872e9ea67120115f0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="walkthrough"></a>Procedura dettagliata

Nei passaggi seguenti, un'applicazione di base viene creata con frammenti. Il primo passaggio consiste nel creare un nuovo xamarin per il progetto Android.

## <a name="1-create-a-project"></a>1. Creare un progetto

Creare un nuovo progetto xamarin denominato **FragmentSample**. Il **minimo Android** versione deve essere impostata per Android 3.1 o versione successiva, come illustrato nell'immagine riportata di seguito:

[![Impostazione della versione minima di Android](walkthrough-images/00.png)](walkthrough-images/00.png#lightbox)


## <a name="2-create-the-mainactivity"></a>2. Creare il MainActivity

Successivamente, è necessario creare la `MainActivity` classe. Questo è l'attività di avvio per l'applicazione. Questa attività verrà ospitato frammenti di uno o entrambi, a seconda delle dimensioni dello schermo. `MainActivity` esegue questa caricando il file di layout più appropriato per il dispositivo.

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

> [!NOTE]
> `Note:` Le sottoclassi di frammento non devono avere valore predefinito è pubblico costruttore argomenti.

## <a name="3-create-the-layout-files"></a>3. Creare i file di Layout

Le due dimensioni dello schermo diverse richiedono due file di layout diverso. Questo punto, creare una nuova cartella **risorse Layout-grandi dimensioni/**e creare un nuovo layout denominato **activity_main.axml**. Verranno inoltre rinominati i file di layout predefinito come **Resources/Layout/activity_main.axml**. Dopo aver apportato queste modifiche, le cartelle di layout dovrebbero essere simile a cattura di schermata seguente:

[![Schermata di cartelle di layout nell'IDE](walkthrough-images/01.png)](walkthrough-images/01.png#lightbox)


Tutti i dispositivi vengono caricati e utilizzare il file di layout in **risorse/Layout**.
Si tratta di un layout molto semplice che visualizza solo un `TitlesFragment`:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_width="fill_parent"
           android:layout_height="fill_parent" />
</LinearLayout>
```

Per i dispositivi dotati di schermi di grandi dimensioni, Android caricherà il file di layout in **risorse Layout-grandidimensioni/**. Il contenuto del layout per i Tablet è come segue:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_weight="1"
           android:layout_width="0px"
           android:layout_height="match_parent" />
 <FrameLayout android:id="@+id/details"
              android:layout_weight="1"
              android:layout_width="0px"
              android:layout_height="match_parent" />
</LinearLayout>
```

Il file di layout per schermi di dimensioni maggiori è leggermente diverso. Non solo è il `TitlesFragment` visualizzato in questo file di layout, ma un `FrameLayout` viene aggiunto a destra accanto il frammento. Su schermi di dimensioni maggiori, il `DetailsFragment` a livello di codice viene aggiunto a `MainActivity` quando l'utente seleziona una riproduzione. Successivamente, verrà illustrato in dettaglio la procedura.

3.2 Android è stato introdotto un nuovo modo per specificare il layout dello schermo. Questi qualificatori nuovo specificano la quantità di spazio, è necessario il layout, anziché le dimensioni dello schermo. Se l'applicazione è stato deve essere eseguito solo in Android 3.2 o versione successiva, è necessario creare un **Layout/risorsa-sw600dp** cartella (invece che alla cartella **risorse Layout-grandi dimensioni/**) per il file di layout  **activity_main.axml**. Questo file di risorse verrà caricato da tutti i dispositivi che hanno una larghezza minima dello schermo di 600 pixel indipendenti dalla densità. Tuttavia, come l'applicazione è impostata su destinazione Android 3.1 o versione successiva, viene utilizzato il qualificatore di risorsa precedente.

## <a name="4-create-the-titlesfragment"></a>4. Creare il TitlesFragment

`TitlesFragment` verranno visualizzare i titoli della riproduzione diverse, sarà quindi necessario aggiungere un nuovo frammento al progetto denominato `TitlesFragment`:

[![Aggiunta di un nuovo frammento al progetto TitlesFragment](walkthrough-images/02.png)](walkthrough-images/02.png#lightbox)

Dopo aver `TitlesFragment` è stato aggiunto, è necessario modificare la classe in modo che erediti da `Android.App.ListFragment`. `ListFragment` è un tipo di frammento specializzato che include funzionalità di elenco.
`TitlesFragment` verrà inoltre eseguire l'override `OnActivityCreated` (un altro metodo del ciclo di vita di frammento) e fornire un `Adapter` che `ListFragment` verrà utilizzato per popolare l'elenco:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
   base.OnActivityCreated(savedInstanceState);
   var adapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemChecked, Shakespeare.Titles);
   ListAdapter = adapter;
   if (savedInstanceState != null)
   {
       _currentPlayId = savedInstanceState.GetInt("current_play_id", 0);
   }
   var detailsFrame = Activity.FindViewById<View>(Resource.Id.details);
   _isDualPane = detailsFrame != null && detailsFrame.Visibility == ViewStates.Visible;
   if (_isDualPane)
   {
       ListView.ChoiceMode = (int) ChoiceMode.Single;
       ShowDetails(_currentPlayId);
   }
}
```

Come accennato in precedenza, l'applicazione dispone di due layout per `MainActivity`. Il codice in `OnActivityCreated` rileva la presenza del `FrameLayout` e determina quali file di layout è stato caricato. Se il `FrameLayout` esiste nel layout, quindi il `_isDualPane` flag è impostato su `true`. Il `_isDualPane` flag viene utilizzato in un' posizione nell'attività, in particolare il `ShowDetails` metodo. Il `ShowDetails` metodo verrà trattato in dettaglio più avanti.

`TitlesFragment` è un elenco e deve rispondere alle selezioni dell'utente nell'elenco. A tale scopo, `TitlesFragment` eseguirà l'override del metodo `OnListItemClick`. All'interno di `OnListItemClick`, un nuovo `DetailsFragment` verranno creati e visualizzati nel `FrameLayout`a livello di programmazione. Il codice relativo all'interno di `TitlesFragment` è:

```csharp
public override void OnListItemClick(ListView l, View v, int position, long id)
{
   ShowDetails(position);
}
private void ShowDetails(int playId)
{
   _currentPlayId = playId;
   if (_isDualPane)
   {
       // We can display everything in place with fragments.
       // Have the list highlight this item and show the data.
       ListView.SetItemChecked(playId, true);
       // Check what fragment is shown, replace if needed.
       var details = FragmentManager.FindFragmentById(Resource.Id.details) as DetailsFragment;
       if (details == null || details.ShownPlayId != playId)
       {
           // Make new fragment to show this selection.
           details = DetailsFragment.NewInstance(playId);
           // Execute a transaction, replacing any existing
           // fragment with this one inside the frame.
           var ft = FragmentManager.BeginTransaction();
           ft.Replace(Resource.Id.details, details);
           ft.SetTransition(FragmentTransaction.TransitFragmentFade);
           ft.Commit();
       }
   }
   else
   {
       // Otherwise we need to launch a new Activity to display
       // the dialog fragment with selected text.
       var intent = new Intent();
       intent.SetClass(Activity, typeof (DetailsActivity));
       intent.PutExtra("current_play_id", playId);
       StartActivity(intent);
   }
}
```

Il codice determina dal dispositivo come formattare e visualizzare l'offerta dalla riproduzione selezionato. Nel caso Tablet, di `_isDualPane` flag verrà impostato su `true`, e pertanto la virgoletta verrà visualizzata accanto al `TitlesFragment`. Se la riproduzione selezionato `id` non è già visualizzata, quindi un nuovo `DetailsFragment` viene creato e quindi caricati il `FrameLayout` sull'attività. Per altri dispositivi che non dispongono di una visualizzazione di grandi dimensioni &ndash; telefoni, ad esempio &ndash; `isDualPane` verrà impostato su `false` in modo nuovo `DetailsActivity` verrà avviato.


## <a name="5-create-the-detailsactivity"></a>5. Creare il DetailsActivity

Il `DetailsActivity` consente di visualizzare il `DetailsFragment` per i dispositivi più piccoli. Per vedere questo aspetto, innanzitutto verrà aggiunto una nuova attività al progetto denominato `DetailsActivity`. `DetailsActivity` è un'attività molto semplice. Crea ed esegue quindi ospitare un nuovo `DetailsFragment` per la riproduzione `id` che è stato inviato:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("current_play_id", 0);

       var details = DetailsFragment.NewInstance(index); // DetailsFragment.NewInstance is a factory method to create a Details Fragment
       var fragmentTransaction = FragmentManager.BeginTransaction();
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

Si noti che viene caricato alcun file di layout per `DetailsActivity`. In alternativa, `DetailsFragment` viene caricato nella visualizzazione dell'attività radice. Questa vista di primo livello con l'ID speciale `Android.Resource.Id.Content`. Un nuovo `DetailFragment` viene creato e quindi aggiunto alla visualizzazione radice all'interno di un `FragmentTransaction` creati tramite l'attività `FragmentManager`.


## <a name="6-create-the-detailsfragment"></a>6. Creare il DetailsFragment

A questo punto, aggiungere un altro frammento per l'applicazione denominata `DetailsFragment`. Questo frammento visualizzerà una virgoletta da riproduzione selezionato. Nel codice seguente viene illustrato l'intero `DetailsFragment`:

```csharp
internal class DetailsFragment : Fragment
{
   public static DetailsFragment NewInstance(int playId)
   {
       var detailsFrag = new DetailsFragment {Arguments = new Bundle()};
       detailsFrag.Arguments.PutInt("current_play_id", playId);
       return detailsFrag;
   }
   public int ShownPlayId
   {
       get { return Arguments.GetInt("current_play_id", 0); }
   }
   public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
   {
       if (container == null)
       {
           // Currently in a layout without a container, so no reason to create our view.
           return null;
       }
       var scroller = new ScrollView(Activity);
       var text = new TextView(Activity);
       var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
       text.SetPadding(padding, padding, padding, padding);
       text.TextSize = 24;
       text.Text = Shakespeare.Dialogue[ShownPlayId];
       scroller.AddView(text);
       return scroller;
   }
}
```

Affinché `DetailsFragment` per funzionare correttamente, l'indice di play selezionato in deve avere il `TitlesFragment`. Esistono diversi modi per fornire questo valore su `DetailsFragment`; in questo esempio, la riproduzione `Id` viene inserito in un Bundle e archiviati per la proprietà argomenti di un'istanza di Bundle di `DetailsFragment`. La proprietà `ShownPlayId` viene fornita per comodità &ndash; verrà utilizzato dalle istanze di `DetailsFragment` per recuperare tale valore dal Bundle di.

`OnCreateView` viene chiamato quando il frammento è necessario disegnare la relativa interfaccia utente e deve restituire un `Android.Views.View` oggetto. Nella maggior parte dei casi, si tratta di un `View` state alterate da un file di layout esistente. Nel caso dell'esempio precedente, il frammento verrà compilata a livello di codice la vista che verrà utilizzata per la visualizzazione.

La procedura è stata completata. Ora è stata creata un'applicazione che utilizza i frammenti per semplificare lo sviluppo in fattori di forma.

Nel [nella sezione successiva](supporting-pre-honeycomb.md), si intende estendere questa applicazione in modo che possano funzionare nei dispositivi pre-Android 3.0.

