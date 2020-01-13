---
title: ViewPager con frammenti
description: ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente di swipeable con ViewPager usando i frammenti come pagine di dati.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d0fdfa44ce6caa0c5f0e0aa2eed6406e606eacc4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029026"
---
# <a name="viewpager-with-fragments"></a>ViewPager con frammenti

_ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente di swipeable con ViewPager usando i frammenti come pagine di dati._

## <a name="overview"></a>Panoramica

`ViewPager` viene spesso usato in combinazione con i frammenti in modo da semplificare la gestione del ciclo di vita di ogni pagina nel `ViewPager`. In questa procedura dettagliata viene usato `ViewPager` per creare un'app denominata **FlashCardPager** che presenta una serie di problemi matematici sulle schede flash. Ogni scheda flash viene implementata come frammento. L'utente scorre verso sinistra e verso destra attraverso le schede flash e tocca un problema matematico per rivelare la risposta. Questa app crea un'istanza di `Fragment` per ogni scheda Flash e implementa un adapter derivato da `FragmentPagerAdapter`. In [viewpager e nelle visualizzazioni](~/android/user-interface/controls/view-pager/viewpager-and-views.md)la maggior parte del lavoro è stata eseguita nei metodi `MainActivity` ciclo di vita. In **FlashCardPager**, la maggior parte del lavoro verrà eseguita da un `Fragment` in uno dei relativi metodi del ciclo di vita. 

Questa guida non illustra le nozioni di base dei frammenti &ndash; se non si ha ancora familiarità con i frammenti in Xamarin.Android, vedere [frammenti](~/android/platform/fragments/index.md) che consentono di iniziare a usare i frammenti. 

## <a name="start-an-app-project"></a>Avviare un progetto di app

Creare un nuovo progetto Android denominato **FlashCardPager**. Avviare quindi Gestione pacchetti NuGet (per altre informazioni sull'installazione dei pacchetti NuGet, vedere [procedura dettagliata: inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Trovare e installare il pacchetto **Xamarin.Android. support. v4** come illustrato in [viewpager e viste](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 

## <a name="add-an-example-data-source"></a>Aggiungere un'origine dati di esempio

In **FlashCardPager**l'origine dati è un mazzo di schede flash rappresentato dalla classe `FlashCardDeck`; Questa origine dati fornisce il `ViewPager` con il contenuto dell'elemento. `FlashCardDeck` contiene una raccolta pronta di problemi matematici e risposte. Il costruttore `FlashCardDeck` non richiede argomenti: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La raccolta di schede flash nella `FlashCardDeck` è organizzata in modo che ogni scheda flash possa accedere a un indicizzatore. La riga di codice seguente, ad esempio, recupera il quarto problema di smart card nel mazzo: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Questa riga di codice recupera la risposta corrispondente al problema precedente:

```csharp
string answer = flashCardDeck[3].Answer;
```

Poiché i dettagli di implementazione di `FlashCardDeck` non sono rilevanti per comprendere `ViewPager`, il codice `FlashCardDeck` non è elencato qui.
Il codice sorgente da `FlashCardDeck` è disponibile in [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Scaricare questo file di origine (oppure copiare e incollare il codice in un nuovo file **FlashCardDeck.cs** ) e aggiungerlo al progetto.

## <a name="create-a-viewpager-layout"></a>Creare un layout ViewPager

Aprire **risorse/layout/Main. aXML** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

Questo XML definisce un `ViewPager` che occupa l'intero schermo. Si noti che è necessario usare il nome completo **Android. support. v4. View. viewpager** perché `ViewPager` è incluso in una libreria di supporto. `ViewPager` è disponibile solo dalla [libreria del supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). non è disponibile nel Android SDK.

## <a name="set-up-viewpager"></a>Configurare ViewPager

Modificare **MainActivity.cs** e aggiungere le seguenti istruzioni `using`:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Modificare la dichiarazione della classe `MainActivity` in modo che derivi da `FragmentActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` deriva da`FragmentActivity` (anziché da `Activity`) perché `FragmentActivity` sa come gestire il supporto dei frammenti. Sostituire il metodo `OnCreate` con il codice seguente: 

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

1. Imposta la visualizzazione dalla risorsa di layout **Main. aXML** .

2. Recupera un riferimento al `ViewPager` dal layout.

3. Crea un'istanza di un nuovo `FlashCardDeck` come origine dati.

Quando si compila ed esegue questo codice, viene visualizzata una schermata simile alla seguente: 

[![screenshot dell'app FlashCardPager con ViewPager vuoto](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

A questo punto, il `ViewPager` è vuoto perché mancano i frammenti usati per popolare il `ViewPager`e non è presente un adattatore per la creazione di questi frammenti dai dati in **FlashCardDeck**. 

Nelle sezioni seguenti viene creato un `FlashCardFragment` per implementare la funzionalità di ogni scheda Flash e viene creata una `FragmentPagerAdapter` per connettere la `ViewPager` ai frammenti creati dai dati nel `FlashCardDeck`. 

## <a name="create-the-fragment"></a>Creare il frammento

Ogni scheda flash verrà gestita da un frammento di interfaccia utente denominato `FlashCardFragment`. nella visualizzazione di `FlashCardFragment`verranno visualizzate le informazioni contenute con una singola scheda flash. Ogni istanza di `FlashCardFragment` sarà ospitata dall'`ViewPager`. 
la vista di `FlashCardFragment`sarà costituita da un `TextView` in cui viene visualizzato il testo del problema relativo alla scheda flash. Questa vista consente di implementare un gestore eventi che usa un `Toast` per visualizzare la risposta quando l'utente tocca la domanda di memoria flash. 

### <a name="create-the-flashcardfragment-layout"></a>Creare il layout di FlashCardFragment

Prima che `FlashCardFragment` possa essere implementato, è necessario definire il layout. Questo layout è un layout del contenitore di frammenti per un singolo frammento. Aggiungere un nuovo layout Android a **risorse/layout** denominato **flashcard_layout. aXML**. Aprire **Resources/layout/flashcard_layout. aXML** e sostituirne il contenuto con il codice seguente: 

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

Questo layout definisce un singolo frammento di scheda Flash; ogni frammento è costituito da un `TextView` che visualizza un problema matematico usando un tipo di carattere grande (100sp). Questo testo viene centrato verticalmente e orizzontalmente sulla scheda flash. 

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

Questo codice elimina la definizione di `Fragment` essenziale che verrà usata per visualizzare una scheda flash. Si noti che `FlashCardFragment` deriva dalla versione della libreria di supporto di `Fragment` definita in `Android.Support.V4.App.Fragment`. Il costruttore è vuoto, in modo che il metodo factory `newInstance` venga usato per creare un nuovo `FlashCardFragment` invece di un costruttore. 

Il metodo del ciclo di vita `OnCreateView` crea e configura l'`TextView`. Consente di ingrandire il layout per il `TextView` del frammento e restituisce il `TextView` inflated al chiamante. `LayoutInflater` e `ViewGroup` vengono passati al `OnCreateView` in modo che sia possibile gonfiare il layout. Il bundle di `savedInstanceState` contiene i dati utilizzati da `OnCreateView` per ricreare il `TextView` da uno stato salvato. 

La visualizzazione del frammento viene inflat in modo esplicito dalla chiamata a `inflater.Inflate`. L'argomento `container` è l'elemento padre della visualizzazione e il flag di `false` indica all'INFLATER di evitare di aggiungere la visualizzazione inflat all'elemento padre della visualizzazione, che verrà aggiunta quando `ViewPager` il metodo `GetItem` dell'adapter più avanti in questa procedura dettagliata. 

### <a name="add-state-code-to-flashcardfragment"></a>Aggiungere il codice di stato a FlashCardFragment

Analogamente a un'attività, un frammento ha un `Bundle` usato per salvare e recuperare il proprio stato. In **FlashCardPager**questa `Bundle` viene utilizzata per salvare il testo della domanda e della risposta per la scheda Flash associata. In **FlashCardFragment.cs**aggiungere le chiavi di `Bundle` seguenti all'inizio della definizione della classe `FlashCardFragment`: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modificare il metodo factory `newInstance` in modo che crei un oggetto `Bundle` e usi le chiavi indicate sopra per archiviare il testo della domanda e della risposta passato nel frammento dopo che ne è stata creata un'istanza: 

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

Modificare il metodo del ciclo di vita del frammento `OnCreateView` recuperare queste informazioni dal bundle passato e caricare il testo della domanda nel `TextBox`: 

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

La variabile `answer` non viene utilizzata in questo caso, ma verrà utilizzata in un secondo momento quando il codice del gestore eventi viene aggiunto a questo file. 

## <a name="create-the-adapter"></a>Creare l'adapter

`ViewPager` utilizza un oggetto controller di adapter che risiede tra l'`ViewPager` e l'origine dati (vedere l'illustrazione nell'articolo dell' [Adapter](~/android/user-interface/controls/view-pager/index.md#adapter) viewpager). Per accedere a questi dati, `ViewPager` necessario fornire un adapter personalizzato derivato da `PagerAdapter`. Poiché in questo esempio vengono utilizzati frammenti, viene utilizzato un `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` deriva da `PagerAdapter`. 
`FragmentPagerAdapter` rappresenta ogni pagina come `Fragment` mantenuta in modo permanente in Gestione frammenti per il periodo di tempo in cui l'utente può tornare alla pagina. Quando l'utente scorre le pagine del `ViewPager`, il `FragmentPagerAdapter` estrae le informazioni dall'origine dati e le usa per creare `Fragment`per la visualizzazione del `ViewPager`. 

Quando si implementa una `FragmentPagerAdapter`, è necessario eseguire l'override di quanto segue:

- **Conteggio** &ndash; proprietà di sola lettura che restituisce il numero di visualizzazioni (pagine) disponibili.

- **GetItem** &ndash; restituisce il frammento da visualizzare per la pagina specificata.

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

Questo codice esclude l'implementazione di `FragmentPagerAdapter` essenziale. Nelle sezioni seguenti, ognuno di questi metodi viene sostituito con codice funzionante. Lo scopo del costruttore è passare Gestione frammenti al costruttore della classe base del `FlashCardDeckAdapter`. 

### <a name="implement-the-adapter-constructor"></a>Implementare il costruttore dell'adattatore

Quando l'app crea un'istanza del `FlashCardDeckAdapter`, fornisce un riferimento a gestione frammenti e a una `FlashCardDeck`di cui è stata creata un'istanza. Aggiungere la variabile membro seguente all'inizio della classe `FlashCardDeckAdapter` in **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Aggiungere la riga di codice seguente al costruttore `FlashCardDeckAdapter`: 

```csharp
this.flashCardDeck = flashCards;
```

Questa riga di codice archivia l'istanza `FlashCardDeck` che verrà utilizzata dall'`FlashCardDeckAdapter`. 

### <a name="implement-count"></a>Numero di implementazioni

L'implementazione del `Count` è relativamente semplice: restituisce il numero di schede flash nel deck della scheda flash. Sostituisci `Count` con il seguente codice: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```

La proprietà `NumCards` di `FlashCardDeck` restituisce il numero di schede Flash (numero di frammenti) nel set di dati. 

### <a name="implement-getitem"></a>Implementa GetItem

Il metodo `GetItem` restituisce il frammento associato alla posizione specificata. Quando viene chiamato `GetItem` per una posizione nel deck della scheda Flash, viene restituito un `FlashCardFragment` configurato per visualizzare il problema della scheda flash in tale posizione. Sostituire il metodo `GetItem` con il codice seguente: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Questo codice esegue le operazioni seguenti:

1. Cerca la stringa del problema matematico nella `FlashCardDeck` Deck per la posizione specificata. 

2. Cerca la stringa di risposta nel `FlashCardDeck` Deck per la posizione specificata. 

3. Chiama il metodo factory `FlashCardFragment` `newInstance`, passando le stringhe di risposta e il problema della scheda flash. 

4. Crea e restituisce un nuovo `Fragment` di schede flash che contiene il testo della domanda e della risposta per tale posizione. 

Quando il `ViewPager` esegue il rendering del `Fragment` in `position`, Visualizza il `TextBox` contenente la stringa di problema matematico che risiede in `position` nel deck della scheda flash. 

## <a name="add-the-adapter-to-the-viewpager"></a>Aggiungere l'adapter a ViewPager

Ora che la `FlashCardDeckAdapter` è implementata, è possibile aggiungerla al `ViewPager`. In **MainActivity.cs**aggiungere la riga di codice seguente alla fine del metodo `OnCreate`:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Questo codice crea un'istanza della `FlashCardDeckAdapter`, passando la `SupportFragmentManager` nel primo argomento. (La proprietà `SupportFragmentManager` di FragmentActivity viene utilizzata per ottenere un riferimento al &ndash; `FragmentManager` per ulteriori informazioni sul `FragmentManager`, vedere [gestione dei frammenti](~/android/platform/fragments/managing-fragments.md)). 

L'implementazione di base è ora completa &ndash; compilare ed eseguire l'app.
Nella schermata successiva verrà visualizzata la prima immagine del deck della scheda Flash, come illustrato nella schermata successiva. Scorri verso sinistra per visualizzare altre schede Flash, quindi scorri a destra per tornare al mazzo di carte Flash:

[![screenshot di esempio dell'app FlashCardPager senza indicatori di cercapersone](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>Aggiungi un indicatore cercapersone

Questa implementazione minima di `ViewPager` Visualizza ogni scheda flash del deck, ma non fornisce alcuna indicazione sul punto in cui l'utente si trova all'interno del mazzo. Il passaggio successivo consiste nell'aggiungere un `PagerTabStrip`. Il `PagerTabStrip` informa l'utente del numero di problema visualizzato e fornisce il contesto di navigazione visualizzando un hint delle schede Flash precedenti e successive. 

Aprire **risorse/layout/Main. aXML** e aggiungere un `PagerTabStrip` al layout:

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

Quando si compila ed esegue l'app, il `PagerTabStrip` vuoto visualizzato nella parte superiore di ogni scheda Flash: 

[![primo piano di PagerTabStrip senza testo](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)

### <a name="display-a-title"></a>Visualizza un titolo

Per aggiungere un titolo a ogni scheda della pagina, implementare il `GetPageTitleFormatted` metodo nell'adapter. `ViewPager` chiama `GetPageTitleFormatted` (se implementato) per ottenere la stringa del titolo che descrive la pagina in corrispondenza della posizione specificata. Aggiungere il metodo seguente alla classe `FlashCardDeckAdapter` in **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Questo codice converte la posizione del deck della scheda flash in un numero di problema. La stringa risultante viene convertita in una `String` Java restituita al `ViewPager`. Quando si esegue l'app con questo nuovo metodo, ogni pagina Visualizza il numero di problema nella `PagerTabStrip`: 

[![screenshot di FlashCardPager con il numero di problema visualizzato al di sopra di ogni pagina](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

È possibile scorrere in avanti e indietro per visualizzare il numero di problema nel deck della scheda Flash visualizzato nella parte superiore di ogni scheda flash. 

## <a name="handle-user-input"></a>Gestisci input utente

**FlashCardPager** presenta una serie di schede flash basate su frammenti in una `ViewPager`, ma non ha ancora un modo per rivelare la risposta a ogni problema. In questa sezione viene aggiunto un gestore eventi al `FlashCardFragment` per visualizzare la risposta quando l'utente tocca il testo del problema relativo alla scheda flash. 

Aprire **FlashCardFragment.cs** e aggiungere il codice seguente alla fine del metodo `OnCreateView` immediatamente prima che la visualizzazione venga restituita al chiamante: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Questo `Click` gestore eventi Visualizza la risposta in un avviso popup che viene visualizzato quando l'utente tocca il `TextBox`. La variabile `answer` è stata inizializzata in precedenza quando le informazioni sullo stato sono state lette dal bundle passato al `OnCreateView`. Compilare ed eseguire l'app, quindi toccare il testo del problema su ogni scheda flash per visualizzare la risposta: 

[![screenshot dei popup delle app FlashCardPager quando viene toccato un problema matematico](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

Il **FlashCardPager** presentato in questa procedura dettagliata usa un `MainActivity` derivato da `FragmentActivity`, ma è anche possibile derivare `MainActivity` da `AppCompatActivity` (che fornisce anche il supporto per la gestione dei frammenti). Per visualizzare un esempio di `AppCompatActivity`, vedere [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) nella raccolta di esempi.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato fornito un esempio dettagliato di come creare un'app di base basata su `ViewPager`usando `Fragment`. È stata presentata un'origine dati di esempio contenente domande e risposte con le schede Flash, un layout `ViewPager` per visualizzare le schede flash e una sottoclasse `FragmentPagerAdapter` che connette il `ViewPager` all'origine dati. Per consentire all'utente di spostarsi tra le schede Flash, sono state incluse istruzioni che spiegano come aggiungere un `PagerTabStrip` per visualizzare il numero di problema nella parte superiore di ogni pagina. Infine, è stato aggiunto il codice di gestione degli eventi per visualizzare la risposta quando l'utente tocca un problema relativo a una scheda flash. 

## <a name="related-links"></a>Collegamenti correlati

- [FlashCardPager (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
