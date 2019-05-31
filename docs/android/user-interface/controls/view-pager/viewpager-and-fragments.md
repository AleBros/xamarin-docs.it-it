---
title: ViewPager con frammenti
description: ViewPager è un gestore di layout che consente di implementare l'esplorazione gestuali. Navigazione gestuali consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente swipeable con ViewPager, uso dei frammenti come le pagine di dati.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 748443352c106fbad88f8eda895cde097ce14a45
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394702"
---
# <a name="viewpager-with-fragments"></a>ViewPager con frammenti

_ViewPager è un gestore di layout che consente di implementare l'esplorazione gestuali. Navigazione gestuali consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente swipeable con ViewPager, uso dei frammenti come le pagine di dati._

 
## <a name="overview"></a>Panoramica

`ViewPager` viene spesso usato in combinazione con frammenti in modo che risulti più facile da gestire il ciclo di vita di ogni pagina la `ViewPager`. In questa procedura dettagliata `ViewPager` viene usato per creare un'app denominata **FlashCardPager** che presenta una serie di problemi matematici per le schede flash. Ogni scheda flash viene implementato come un frammento. L'utente passa a sinistra e destra nelle schede flash e tocca un problema di matematica per visualizzare la relativa risposta. Questa app crea un `Fragment` istanza per ogni scheda flash e implementa un adattatore derivato da `FragmentPagerAdapter`. Nelle [Viewpager e viste](~/android/user-interface/controls/view-pager/viewpager-and-views.md), la maggior parte del lavoro è stata eseguita `MainActivity` metodi del ciclo di vita. Nelle **FlashCardPager**, la maggior parte del lavoro viene eseguita da un `Fragment` in uno dei relativi metodi del ciclo di vita. 

Questa Guida non illustra le nozioni di base di frammenti &ndash; se non ha ancora familiarità con frammenti in xamarin. Android, vedere [frammenti](~/android/platform/fragments/index.md) che consentono di iniziare con frammenti. 



## <a name="start-an-app-project"></a>Avviare un progetto di App

Creare un nuovo progetto Android chiamato **FlashCardPager**. Successivamente, avviare Gestione pacchetti NuGet (per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: Inserimento di pacchetto NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Trovare e installare il **Xamarin.Android.Support.v4** del pacchetto come descritto in [insieme Viewpager e visualizzazioni](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Aggiungere un'origine dati di esempio

Nelle **FlashCardPager**, l'origine dati è un mazzo di carte flash rappresentato dal `FlashCardDeck` classe; i dati di origine fornisce il `ViewPager` con contenuto dell'elemento. `FlashCardDeck` contiene una raccolta predefinita di problemi matematici e risposte. Il `FlashCardDeck` costruttore non richiede argomenti: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La raccolta di schede flash in `FlashCardDeck` è organizzata in modo che ogni scheda sono accessibili da un indicizzatore. Ad esempio, la riga di codice seguente recupera il problema quarto flash carta del mazzo: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Questa riga di codice recupera la risposta corrispondente al problema precedente:

```csharp
string answer = flashCardDeck[3].Answer;
```

Poiché i dettagli di implementazione `FlashCardDeck` non sono rilevanti per comprendere `ViewPager`, il `FlashCardDeck` codice non è elencato qui.
Il codice sorgente `FlashCardDeck` è disponibile all'indirizzo [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Scaricare questo file di origine (o copiare e incollare il codice in una nuova **FlashCardDeck.cs** file) e aggiungerlo al progetto.



## <a name="create-a-viewpager-layout"></a>Creare un Layout ViewPager

Aprire **Resources/layout/Main.axml** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

Questo XML definisce un `ViewPager` che occupa l'intera schermata. Si noti che è necessario utilizzare il nome completo **android.support.v4.view.ViewPager** perché `ViewPager` viene compresso in una libreria di supporto. `ViewPager` è disponibile solo il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); non è disponibile in Android SDK.


## <a name="set-up-viewpager"></a>Configurare ViewPager

Modificare **MainActivity.cs** e aggiungere quanto segue `using` istruzioni:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Modifica il `MainActivity` dichiarazione di classe in modo che derivi da `FragmentActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` è derivato da`FragmentActivity` (anziché `Activity`) perché `FragmentActivity` sappia come gestire il supporto dei frammenti. Sostituire il metodo `OnCreate` con il codice seguente: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

Questo codice esegue le operazioni seguenti:

1.  Imposta la visualizzazione dal **Main. axml** risorsa del layout.

2.  Recupera un riferimento di `ViewPager` dal layout.

3.  Crea un'istanza di un nuovo `FlashCardDeck` come origine dati.

Quando si compila e si esegue questo codice, verrà visualizzato un display che simile allo screenshot seguente: 

[![Schermata di FlashCardPager app con ViewPager vuoto](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

A questo punto, il `ViewPager` è vuoto perché mancano i frammenti che vengono usati popolare il `ViewPager`, e un adapter è assente per la creazione di questi frammenti dai dati nei **FlashCardDeck**. 

Nelle sezioni seguenti, una `FlashCardFragment` è creare per implementare le funzionalità di ogni scheda, flash e un `FragmentPagerAdapter` viene creato per connettersi il `ViewPager` ai frammenti creati dai dati nel `FlashCardDeck`. 



## <a name="create-the-fragment"></a>Creare il frammento

Ogni scheda flash verranno gestito da un frammento dell'interfaccia utente denominato `FlashCardFragment`. `FlashCardFragment`della vista sono riportati le informazioni contenute con una singola scheda. Ogni istanza del `FlashCardFragment` sarà ospitata dal `ViewPager`. 
`FlashCardFragment`della visualizzazione sarà costituito da un `TextView` che visualizza il testo di problema scheda flash. In questa vista implementerà un gestore eventi che usa un `Toast` per visualizzare la risposta quando l'utente tocca la domanda di scheda. 



### <a name="create-the-flashcardfragment-layout"></a>Creare il Layout FlashCardFragment

Prima di `FlashCardFragment` possono essere implementate, bensì deve essere definito. Questo layout è un layout di contenitore di frammenti per un singolo frammento. Aggiungere un nuovo layout Android che **risorse/layout** chiamato **flashcard_layout.axml**. Aprire **Resources/layout/flashcard_layout.axml** e sostituirne il contenuto con il codice seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

Questo layout definisce un frammento di singola scheda di flash; ogni frammento è costituito da un `TextView` che consente di visualizzare un problema di matematica usando un tipo di carattere di grandi dimensioni (100sp). Questo testo è centrato orizzontalmente e verticalmente nella scheda flash. 



### <a name="create-the-initial-flashcardfragment-class"></a>Creare la classe FlashCardFragment iniziale

Aggiungere un nuovo file denominato **FlashCardFragment.cs** e sostituirne il contenuto con il codice seguente:

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

Questo codice associa automaticamente l'essenziale `Fragment` definizione che verrà usato per visualizzare una scheda. Si noti che `FlashCardFragment` deriva dalla versione di libreria di supporto di `Fragment` definito nella `Android.Support.V4.App.Fragment`. Il costruttore è vuoto, in modo che il `newInstance` metodo factory viene usato per creare un nuovo `FlashCardFragment` invece di un costruttore. 

Il `OnCreateView` metodo del ciclo di vita crea e configura il `TextView`. Incrementa il layout per il frammento `TextView` e restituisce il gonfiato `TextView` al chiamante. `LayoutInflater` e `ViewGroup` vengono passati al `OnCreateView` in modo che può aumentare significativamente il layout. Il `savedInstanceState` bundle contiene i dati che `OnCreateView` utilizzato per ricreare il `TextView` da uno stato salvato. 

Visualizzazione del frammento viene ingrandita in modo esplicito tramite la chiamata di `inflater.Inflate`. Il `container` argomento è il padre della vista e il `false` flag indica inflater occorre evitare l'aggiunta della visualizzazione ingrandita al padre della visualizzazione (verrà aggiunto quando `ViewPager` chiamata dell'adapter `GetItem` più avanti in questo metodo procedura dettagliata). 



### <a name="add-state-code-to-flashcardfragment"></a>Aggiungere il codice di stato a FlashCardFragment

Come un'attività, dispone di un frammento un `Bundle` viene utilizzata per salvare e recuperare il relativo stato. Nelle **FlashCardPager**, questo `Bundle` viene usato per salvare la domanda e risposta testo per la scheda associata. In **FlashCardFragment.cs**, aggiungere il codice seguente `Bundle` chiavi in cima il `FlashCardFragment` definizione della classe: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modificare il `newInstance` metodo factory in modo che venga creato un `Bundle` dell'oggetto e Usa le chiavi precedenti per archiviare la domanda passata e rispondere nel frammento di testo dopo che ne viene creata un'istanza: 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

Modificare il metodo del ciclo di vita di frammento `OnCreateView` per recuperare queste informazioni dal Bundle di passato e caricare il testo della domanda nel `TextBox`: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

Il `answer` variabile non viene usata qui, ma verrà utilizzato in un secondo momento quando il codice del gestore eventi viene aggiunto a questo file. 


## <a name="create-the-adapter"></a>Creare l'Adapter

`ViewPager` Usa un oggetto controller di adapter che risiede tra le `ViewPager` e l'origine dati (vedere la figura nel ViewPager [Adapter](~/android/user-interface/controls/view-pager/index.md#adapter) articolo). Per accedere a questi dati, `ViewPager` richiede di specificare un adapter personalizzato derivato da `PagerAdapter`. Poiché questo esempio Usa frammenti, Usa un' `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` è derivato da `PagerAdapter`. 
`FragmentPagerAdapter` rappresenta ogni pagina come un `Fragment` che viene mantenuto in Gestione frammenti di in modo permanente fino a quando l'utente può tornare alla pagina. Come le tessere magnetiche utente tramite pagine del `ViewPager`, il `FragmentPagerAdapter` estrae le informazioni dall'origine dati e lo usa per creare `Fragment`s per il `ViewPager` da visualizzare. 

Quando si implementa un `FragmentPagerAdapter`, è necessario eseguire l'override di quanto segue:

-   **Conteggio** &ndash; proprietà di sola lettura che restituisce il numero di visualizzazioni (pagine) disponibili.

-   **GetItem** &ndash; restituisce il frammento da visualizzare per la pagina specificata.

Aggiungere un nuovo file denominato **FlashCardDeckAdapter.cs** e sostituirne il contenuto con il codice seguente:

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

Questo codice associa automaticamente l'essenziale `FragmentPagerAdapter` implementazione. Nelle sezioni seguenti, ognuno di questi metodi viene sostituito con codice di lavoro. Lo scopo del costruttore consiste nel passare il gestore di frammento per il `FlashCardDeckAdapter`del costruttore della classe base. 



### <a name="implement-the-adapter-constructor"></a>Implementare il costruttore dell'adattatore

Quando l'app crea un'istanza di `FlashCardDeckAdapter`, fornisce un riferimento a un'istanza e la gestione di frammento `FlashCardDeck`. Aggiungere la variabile membro seguente all'inizio del `FlashCardDeckAdapter` classe **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Aggiungere la seguente riga di codice per il `FlashCardDeckAdapter` costruttore: 

```csharp
this.flashCardDeck = flashCards;
```

La seguente riga di codice consente di memorizzare il `FlashCardDeck` dell'istanza che il `FlashCardDeckAdapter` userà. 



### <a name="implement-count"></a>Conteggio di implementare

Il `Count` implementazione è abbastanza semplice: restituisce il numero di schede flash nel mazzo di carte flash. Sostituisci `Count` con il seguente codice: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


Il `NumCards` proprietà di `FlashCardDeck` restituisce il numero di schede flash (numero di frammenti) nel set di dati. 



### <a name="implement-getitem"></a>Implementare GetItem

Il `GetItem` metodo restituisce il frammento associato alla posizione specificata. Quando `GetItem` viene chiamato per una posizione nel mazzo di carte flash, viene restituito un `FlashCardFragment` configurato per visualizzare il problema di smart card flash in tale posizione. Sostituire il metodo `GetItem` con il codice seguente: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Questo codice esegue le operazioni seguenti:

1.  Cerca la stringa di problemi matematici nel `FlashCardDeck` mazzo relativo alla posizione specificata. 

2.  Cerca la stringa di risposta nel `FlashCardDeck` mazzo relativo alla posizione specificata. 

3.  Chiama il `FlashCardFragment` metodo factory `newInstance`, passando le stringhe di problema e la risposta di scheda. 

4.  Crea e restituisce una nuova scheda `Fragment` che contiene il testo di domande e risposte per tale posizione. 

Quando il `ViewPager` esegue il rendering il `Fragment` alla `position`, viene visualizzato il `TextBox` contenente la stringa di problema matematica che risiede in `position` nel mazzo di carte flash. 



## <a name="add-the-adapter-to-the-viewpager"></a>Aggiungere l'Adapter per il ViewPager

Ora che il `FlashCardDeckAdapter` viene implementato, è possibile aggiungerla al `ViewPager`. Nelle **MainActivity.cs**, aggiungere la riga di codice seguente alla fine del `OnCreate` metodo:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Questo codice crea un'istanza di `FlashCardDeckAdapter`, passando il `SupportFragmentManager` nel primo argomento. (Il `SupportFragmentManager` proprietà di FragmentActivity viene usata per ottenere un riferimento al `FragmentManager` &ndash; per altre informazioni sul `FragmentManager`, vedere [Gestione frammenti](~/android/platform/fragments/managing-fragments.md).) 

L'implementazione di base è stata completata &ndash; compilare ed eseguire l'app.
Dovrebbe essere la prima immagine del mazzo di carte flash visualizzati sullo schermo, come illustrato a sinistra nel prossimo screenshot. Scorrere verso sinistra per visualizzare altre schede flash, quindi scorrere rapidamente per spostarsi all'indietro il mazzo di carte flash:

[![Screenshot di esempio di app FlashCardPager senza indicatori di cercapersone](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Aggiungere un indicatore di cercapersone

Questo minimo `ViewPager` implementazione consente di visualizzare ogni scheda del mazzo, ma è non presente alcuna indicazione per capire dove l'utente è all'interno del mazzo. Il passaggio successivo consiste nell'aggiungere un `PagerTabStrip`. Il `PagerTabStrip` informa l'utente sulla quale problema numero viene visualizzato e fornisce il contesto di navigazione visualizzando un hint per le schede flash precedenti e successivi. 

Aprire **Resources/layout/Main.axml** e aggiungere un `PagerTabStrip` al layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

  <android.support.v4.view.PagerTabStrip
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:layout_gravity="top"
      android:paddingBottom="10dp"
      android:paddingTop="10dp"
      android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

Quando si compila e si esegue l'app, si dovrebbe essere vuoto `PagerTabStrip` visualizzato nella parte superiore di ogni scheda flash: 

[![Primo piano di PagerTabStrip senza testo](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Visualizzare un titolo

Per aggiungere un titolo per ogni scheda della pagina, implementare il `GetPageTitleFormatted` metodo nell'adapter. `ViewPager` le chiamate `GetPageTitleFormatted` (se è implementato) per ottenere la stringa del titolo che descrive la pagina in corrispondenza della posizione specificata. Aggiungere il metodo seguente per il `FlashCardDeckAdapter` classe **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Questo codice converte la posizione nel mazzo di carte flash in un numero di problemi. La stringa risultante viene convertita in un linguaggio `String` che viene restituito al `ViewPager`. Quando si esegue l'app con questo nuovo metodo, ogni pagina Visualizza il numero di problemi nel `PagerTabStrip`: 

[![Screenshot di FlashCardPager con il numero di problema visualizzato di sopra di ogni pagina](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

È possibile scorrere avanti e indietro per visualizzare il numero di problemi nel mazzo di carte flash che viene visualizzato nella parte superiore di ogni scheda flash. 



## <a name="handle-user-input"></a>Gestire l'Input dell'utente

**FlashCardPager** presenta una serie di schede in base al frammento in un `ViewPager`, ma non dispone ancora di un modo per fornire la risposta per ogni problema. In questa sezione viene aggiunto un gestore eventi per il `FlashCardFragment` per visualizzare la risposta quando l'utente tocca il testo di problema scheda flash. 

Aprire **FlashCardFragment.cs** e aggiungere il codice seguente alla fine del `OnCreateView` metodo solo prima della visualizzazione viene restituita al chiamante: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Ciò `Click` gestore dell'evento Visualizza la risposta in un tipo avviso popup che viene visualizzato quando l'utente tocca il `TextBox`. Il `answer` variabile è stata inizializzata in precedenza quando è stato letto le informazioni sullo stato dal Bundle di cui è stato passato a `OnCreateView`. Creare ed eseguire l'app, quindi toccare il testo del problema in ogni scheda per visualizzare la risposta: 

[![Screenshot di FlashCardPager app avvisi popup quando viene toccato problema math](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

Il **FlashCardPager** presentati in questa procedura dettagliata Usa un `MainActivity` derivato da `FragmentActivity`, ma è anche possibile derivare `MainActivity` da `AppCompatActivity` (che fornisce inoltre supporto per la gestione di frammenti). Per visualizzare un `AppCompatActivity` esempio, vedere [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/) nella raccolta di esempio. 



## <a name="summary"></a>Riepilogo

Questa procedura dettagliata è fornito un esempio dettagliato di come compilare una semplice `ViewPager`-basato su app con `Fragment`s. Presentata un'origine dati di esempio contenente scheda domande e risposte, un `ViewPager` layout per visualizzare le schede flash e una `FragmentPagerAdapter` sottoclasse che si connette il `ViewPager` all'origine dati. Per consentire all'utente di spostarsi tra le schede flash, sono state incluse le istruzioni che illustrano come aggiungere un `PagerTabStrip` per visualizzare il numero di problemi nella parte superiore di ogni pagina. Infine, è stato aggiunto codice di gestione eventi per visualizzare la risposta quando l'utente tocca un problema di scheda. 



## <a name="related-links"></a>Collegamenti correlati

- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
