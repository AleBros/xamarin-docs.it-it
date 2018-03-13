---
title: ViewPager con frammenti
description: "ViewPager è un gestore di layout che consente di implementare gestuali navigazione. Navigazione gestuali consente all'utente Scorri rapidamente verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente swipeable con ViewPager, utilizzare i frammenti come le pagine di dati."
ms.topic: article
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: cd71617cce209ef0127023f69c2b503fee031e43
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="viewpager-with-fragments"></a>ViewPager con frammenti

_ViewPager è un gestore di layout che consente di implementare gestuali navigazione. Navigazione gestuali consente all'utente Scorri rapidamente verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente swipeable con ViewPager, utilizzare i frammenti come le pagine di dati._

 
## <a name="overview"></a>Panoramica

`ViewPager` viene spesso usato in combinazione con frammenti in modo che sia più facile da gestire il ciclo di vita di ogni pagina di `ViewPager`. In questa procedura dettagliata, `ViewPager` viene utilizzato per creare una chiamata di un'app **FlashCardPager** che visualizza una serie di problemi di matematica in schede. Ogni scheda viene implementato come un frammento. L'utente passa a sinistra e destra tramite le schede flash e tocca a un problema di matematica per visualizzare la relativa risposta. Questa applicazione crea un `Fragment` istanza per ogni scheda e implementa un adattatore derivato dalla `FragmentPagerAdapter`. In [Viewpager e viste](~/android/user-interface/controls/view-pager/viewpager-and-views.md), la maggior parte delle operazioni eseguite in `MainActivity` metodi del ciclo di vita. In **FlashCardPager**, la maggior parte delle operazioni viene eseguita da un `Fragment` in uno dei relativi metodi del ciclo di vita. 

Questa Guida non comprende le nozioni di base di frammenti &ndash; se si non ha ancora familiarità con i frammenti di xamarin, vedere [frammenti](~/android/platform/fragments/index.md) consentono di iniziare a utilizzare frammenti. 



## <a name="start-an-app-project"></a>Avviare un progetto di App

Creare un nuovo progetto Android denominato **FlashCardPager**. Successivamente, avviare Gestione pacchetti NuGet (per ulteriori informazioni sull'installazione dei pacchetti NuGet, vedere [procedura dettagliata: inclusi un NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Trovare e installare il **Xamarin.Android.Support.v4** pacchetto come descritto in [Viewpager e viste](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Aggiungere un'origine dati di esempio

In **FlashCardPager**, l'origine dati è un ponte di schede flash rappresentato dal `FlashCardDeck` classe; i dati di origine fornisce il `ViewPager` con il contenuto dell'elemento. `FlashCardDeck` contiene una raccolta predefinita di problemi di matematica e risposte. Il `FlashCardDeck` costruttore non richiede argomenti: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La raccolta di schede in `FlashCardDeck` è organizzata in modo che ogni scheda è possibile accedere tramite un indicizzatore. La riga di codice seguente recupera ad esempio, il problema scheda quarto del mazzo: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Questa riga di codice recupera la risposta corrispondente al problema precedente:

```csharp
string answer = flashCardDeck[3].Answer;
```

Perché i dettagli di implementazione di `FlashCardDeck` non sono rilevanti per conoscere `ViewPager`, `FlashCardDeck` codice non è elencato di seguito.
Il codice sorgente da `FlashCardDeck` è disponibile all'indirizzo [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Scaricare questo file di origine (oppure copiare e incollare il codice in un nuovo **FlashCardDeck.cs** file) e aggiungerlo al progetto.



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

Questo codice XML definisce un `ViewPager` che occupa l'intero schermo. Si noti che è necessario utilizzare il nome completo **android.support.v4.view.ViewPager** perché `ViewPager` viene compresso in una libreria di supporto. `ViewPager` è disponibile solo dal [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); non è disponibile in Android SDK.


## <a name="set-up-viewpager"></a>Impostare ViewPager

Modifica **Mainactivity** e aggiungere le seguenti `using` istruzioni:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Modifica il `MainActivity` dichiarazione di classe in modo che deriva da `AppCompatActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` derivato da`FragmentActivity` (anziché `Activity`) perché `FragmentActivity` sia in grado di gestire il supporto dei frammenti. Sostituire il metodo `OnCreate` con il codice seguente: 

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

1.  Imposta la visualizzazione di **Main. axml** risorse di layout.

2.  Recupera un riferimento di `ViewPager` dal layout.

3.  Crea un nuovo `FlashCardDeck` come origine dati.

Quando si compila e si esegue questo codice, si dovrebbe essere una visualizzazione simile nella schermata seguente: 

[![App schermata di FlashCardPager con ViewPager vuoto](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

A questo punto, il `ViewPager` è vuoto perché mancano i frammenti che vengono utilizzati popolare il `ViewPager`, e un adapter è assente per la creazione di questi frammenti dai dati **FlashCardDeck**. 

Nelle sezioni seguenti, un `FlashCardFragment` creato per implementare la funzionalità di ogni scheda e un `FragmentPagerAdapter` viene creato per collegare il `ViewPager` ai frammenti creati dai dati di `FlashCardDeck`. 



## <a name="create-the-fragment"></a>Creare il frammento

Ogni scheda verranno gestito da un frammento dell'interfaccia utente chiamato `FlashCardFragment`. `FlashCardFragment`della vista verrà visualizzate le informazioni contenute con una singola scheda. Ogni istanza di `FlashCardFragment` sarà ospitata dal `ViewPager`. 
`FlashCardFragment`della visualizzazione sarà costituito da un `TextView` che visualizza il testo di problema scheda flash. Questa vista verrà implementato un gestore eventi che utilizza un `Toast` per visualizzare la risposta quando l'utente digita la domanda di scheda. 



### <a name="create-the-flashcardfragment-layout"></a>Creare il Layout FlashCardFragment

Prima di `FlashCardFragment` può essere implementato, il layout deve essere definito. Questo layout è un contenitore frammento per un singolo frammento. Aggiungere un nuovo layout Android **risorse/layout** chiamato **flashcard_layout.axml**. Aprire **Resources/layout/flashcard_layout.axml** e sostituirne il contenuto con il codice seguente: 

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

Consente di definire un frammento singola scheda; ogni frammento è costituita da un `TextView` che consente di visualizzare un problema di matematica usando un tipo di carattere di grandi dimensioni (100sp). Questo testo viene centrato verticalmente e orizzontalmente nella scheda flash. 



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

Questo codice viene generato automaticamente essenziale `Fragment` definizione che verrà utilizzato per visualizzare una scheda. Si noti che `FlashCardFragment` è derivato dalla versione della libreria di supporto di `Fragment` definito in `Android.Support.V4.App.Fragment`. Il costruttore è vuoto, in modo che il `newInstance` metodo factory viene utilizzata per creare un nuovo `FlashCardFragment` anziché un costruttore. 

Il `OnCreateView` del ciclo di vita metodo crea e configura il `TextView`. Incrementa il layout per il frammento `TextView` e restituisce l'ingrandita `TextView` al chiamante. `LayoutInflater` e `ViewGroup` vengono passati a `OnCreateView` in modo che può aumentare significativamente il layout. Il `savedInstanceState` bundle contiene dati che `OnCreateView` viene usato per ricreare il `TextView` da uno stato salvato. 

Visualizzazione del frammento viene ingrandita in modo esplicito dalla chiamata a `inflater.Inflate`. Il `container` argomento è il padre della vista e `false` flag indica l'Inflater è stato di non aggiungere la visualizzazione ingrandita padre della vista (verrà aggiunto quando `ViewPager` chiamata dell'adapter `GetItem` metodo più avanti in questo procedura dettagliata). 



### <a name="add-state-code-to-flashcardfragment"></a>Aggiungere il codice di stato FlashCardFragment

Ad esempio un'attività, un frammento ha un `Bundle` viene utilizzata per salvare e recuperare il relativo stato. In **FlashCardPager**, questo `Bundle` viene utilizzato per salvare la domanda e risposta testo per la scheda associata. In **FlashCardFragment.cs**, aggiungere il seguente `Bundle` chiavi in cima al `FlashCardFragment` definizione di classe: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modificare il `newInstance` metodo factory che crea un `Bundle` dell'oggetto e utilizza le chiavi precedenti per archiviare la domanda passata e rispondere nel frammento di testo dopo che viene creata un'istanza: 

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

Modificare il metodo del ciclo di vita di frammento `OnCreateView` per recuperare le informazioni dal Bundle di passato e caricare il testo della domanda nel `TextBox`: 

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

Il `answer` variabile non viene usata qui, ma e verrà utilizzato in un secondo momento quando il codice del gestore eventi viene aggiunto a questo file. 


## <a name="create-the-adapter"></a>Creazione dell'adattatore

`ViewPager` viene utilizzato un oggetto controller adapter che si trova tra il `ViewPager` e l'origine dati (vedere la figura del ViewPager [Adapter](~/android/user-interface/controls/view-pager/index.md#adapter) articolo). Per accedere ai dati, `ViewPager` è necessario fornire un adapter personalizzato derivato da `PagerAdapter`. Poiché in questo esempio utilizza frammenti, viene utilizzato un `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` è derivato da `PagerAdapter`. 
`FragmentPagerAdapter` ogni pagina come rappresenta un `Fragment` che viene mantenuto il responsabile di frammento in modo permanente fino a quando l'utente può tornare alla pagina. Come le tessere magnetiche utente tra le pagine del `ViewPager`, `FragmentPagerAdapter` estrae le informazioni dall'origine dati e viene utilizzato per creare `Fragment`s per il `ViewPager` da visualizzare. 

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

Questo codice viene generato automaticamente essenziale `FragmentPagerAdapter` implementazione. Nelle sezioni seguenti, ognuno di questi metodi viene sostituito con codice di lavoro. Lo scopo del costruttore consiste nel passare per la Gestione frammenti di `FlashCardDeckAdapter`del costruttore della classe base. 



### <a name="implement-the-adapter-constructor"></a>Implementare il costruttore dell'adattatore

Quando l'applicazione crea un'istanza di `FlashCardDeckAdapter`, fornisce un riferimento per la gestione di frammento e un'istanza `FlashCardDeck`. Aggiungere la variabile membro seguente all'inizio del `FlashCardDeckAdapter` classe **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Aggiungere la seguente riga di codice per il `FlashCardDeckAdapter` costruttore: 

```csharp
this.flashCardDeck = flashCards;
```

Questa riga di codice consente di memorizzare il `FlashCardDeck` che l'istanza di `FlashCardDeckAdapter` utilizzerà. 



### <a name="implement-count"></a>Conteggio di implementazione

Il `Count` implementazione è relativamente semplice: restituisce il numero di schede flash del mazzo scheda flash. Sostituisci `Count` con il seguente codice: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


Il `NumCards` proprietà `FlashCardDeck` restituisce il numero di schede flash (numero di frammenti) nel set di dati. 



### <a name="implement-getitem"></a>Implementare GetItem

Il `GetItem` metodo restituisce il frammento associato alla posizione specificata. Quando `GetItem` viene chiamato per una posizione del mazzo scheda flash, viene restituito un `FlashCardFragment` configurato per visualizzare il problema della scheda in tale posizione. Sostituire il metodo `GetItem` con il codice seguente: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Questo codice esegue le operazioni seguenti:

1.  Cerca la stringa problema matematico di `FlashCardDeck` ponte per la posizione specificata. 

2.  Cerca la stringa di risposta nel `FlashCardDeck` ponte per la posizione specificata. 

3.  Chiamate di `FlashCardFragment` metodo factory `newInstance`, passando le stringhe di problema e la risposta di scheda. 

4.  Crea e restituisce una nuova scheda `Fragment` che contiene il testo della domanda e risposta per tale posizione. 

Quando il `ViewPager` esegue il rendering di `Fragment` in `position`, viene visualizzato il `TextBox` che contiene la stringa di matematica problema che si trovano in `position` del mazzo scheda flash. 



## <a name="add-the-adapter-to-the-viewpager"></a>Aggiungere l'Adapter per il ViewPager

Ora che il `FlashCardDeckAdapter` è implementato, è necessario aggiungerlo per il `ViewPager`. In **Mainactivity**, aggiungere la riga di codice seguente alla fine del `OnCreate` metodo:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Questo codice crea un'istanza di `FlashCardDeckAdapter`, passando il `SupportFragmentManager` nel primo argomento. (Il `SupportFragmentManager` proprietà di FragmentActivity viene utilizzata per ottenere un riferimento al `FragmentManager` &ndash; per ulteriori informazioni sul `FragmentManager`, vedere [Gestione frammenti](~/android/platform/fragments/managing-fragments.md).) 

Implementazione di base è stata completata &ndash; compilare ed eseguire l'app.
Dovrebbe essere la prima immagine del ponte scheda vengono visualizzati sullo schermo, come mostrato nella schermata successiva a sinistra. Scorri rapidamente verso sinistra per visualizzare altre schede, quindi scorrere rapidamente per riportare le carte del mazzo scheda:

[![Schermate di esempio di app FlashCardPager senza gli indicatori di cercapersone](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Aggiungere un indicatore di cercapersone

Questo minimo `ViewPager` implementazione visualizza ogni scheda del mazzo, ma non offre alcuna indicazione per capire dove l'utente è all'interno di presentazione. Il passaggio successivo consiste nell'aggiungere un `PagerTabStrip`. Il `PagerTabStrip` informa l'utente, come ad esempio quale problema numero viene visualizzato e fornisce il contesto di esplorazione tramite la visualizzazione di un hint per le schede flash precedenti e successivi. 

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

Quando si compila e si esegue l'app, si dovrebbe essere vuota `PagerTabStrip` visualizzati nella parte superiore di ogni scheda: 

[![Primo piano PagerTabStrip senza testo](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Visualizzare un titolo

Per aggiungere un titolo per ogni scheda della pagina, implementare il `GetPageTitleFormatted` metodo nell'adapter. `ViewPager` chiamate `GetPageTitleFormatted` (Se implementato) per ottenere la stringa del titolo che descrive la pagina in corrispondenza della posizione specificata. Aggiungere il metodo seguente per il `FlashCardDeckAdapter` classe **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Questo codice converte la posizione del mazzo scheda numero un problema. La stringa risultante viene convertita in un linguaggio `String` restituito per il `ViewPager`. Quando si esegue l'app con questo nuovo metodo, ogni pagina Visualizza il numero del problema nel `PagerTabStrip`: 

[![Schermate di FlashCardPager con il numero di problema visualizzato di sopra di ogni pagina](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

È possibile scorrere avanti e indietro per visualizzare il numero del problema del mazzo scheda viene visualizzata nella parte superiore di ogni scheda. 



## <a name="handle-user-input"></a>Gestire l'Input utente

**FlashCardPager** presenta una serie di schede basate sul frammento di in un `ViewPager`, ma non dispone ancora di un modo per fornire la risposta per ciascun problema. In questa sezione viene aggiunto un gestore eventi per il `FlashCardFragment` per visualizzare la risposta quando l'utente tocca sul testo problema scheda flash. 

Aprire **FlashCardFragment.cs** e aggiungere il codice seguente alla fine del `OnCreateView` metodo subito prima della visualizzazione viene restituita al chiamante: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Questo `Click` gestore eventi visualizza la risposta in un tipo di avviso popup che viene visualizzato quando l'utente tocca il `TextBox`. Il `answer` variabile è stata inizializzata in precedenza quando le informazioni sullo stato è stato letto dal Bundle di cui è stato passato a `OnCreateView`. Creare e quindi eseguire l'app, quindi toccare il testo del problema in ogni scheda per visualizzare la risposta: 

[![Schermate di FlashCardPager app avvisi popup quando verrà scelti problema matematico](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

Il **FlashCardPager** presentati in questa procedura dettagliata viene utilizzato un `MainActivity` derivato da `FragmentActivity`, ma è anche possibile derivare `MainActivity` da `AppCompatActivity` (che fornisce inoltre supporto per la gestione di frammenti). Per visualizzare un `AppCompatActivity` esempio, vedere [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/) nella raccolta di esempio. 



## <a name="summary"></a>Riepilogo

Questa procedura dettagliata è fornito un esempio dettagliato di come compilare un basic `ViewPager`-app con `Fragment`s. Presentata un'origine dati di esempio contenente scheda domande e risposte, un `ViewPager` layout per visualizzare le schede flash e un `FragmentPagerAdapter` sottoclasse che si connette il `ViewPager` all'origine dati. Per consentire all'utente di spostarsi tra le schede flash, sono incluse le istruzioni che illustrano come aggiungere un `PagerTabStrip` per visualizzare il numero del problema nella parte superiore di ogni pagina. Infine, codice di gestione degli eventi è stato aggiunto per visualizzare la risposta quando l'utente tocca un problema di scheda. 



## <a name="related-links"></a>Collegamenti correlati

- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
