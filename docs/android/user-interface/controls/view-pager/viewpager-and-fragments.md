---
title: ViewPager con frammenti
description: ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente di swipeable con ViewPager usando i frammenti come pagine di dati.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 90bffc2360654f571728f76810f144e702a81e57
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646094"
---
# <a name="viewpager-with-fragments"></a>ViewPager con frammenti

_ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente di swipeable con ViewPager usando i frammenti come pagine di dati._

 
## <a name="overview"></a>Panoramica

`ViewPager`viene spesso usato in combinazione con i frammenti in modo da semplificare la gestione del ciclo di vita di ogni pagina in `ViewPager`. In questa procedura dettagliata `ViewPager` viene usato per creare un'app denominata **FlashCardPager** che presenta una serie di problemi matematici sulle schede flash. Ogni scheda flash viene implementata come frammento. L'utente scorre verso sinistra e verso destra attraverso le schede flash e tocca un problema matematico per rivelare la risposta. Questa app crea un' `Fragment` istanza per ogni scheda Flash e implementa un adapter derivato da `FragmentPagerAdapter`. In [viewpager e nelle visualizzazioni](~/android/user-interface/controls/view-pager/viewpager-and-views.md)la maggior parte del lavoro è stata `MainActivity` eseguita nei metodi del ciclo di vita. In **FlashCardPager**, la maggior parte del lavoro verrà eseguita da un `Fragment` in uno dei relativi metodi del ciclo di vita. 

Questa guida non illustra le nozioni di base dei &ndash; frammenti se non si ha ancora familiarità con i frammenti in Novell. Android, vedere [frammenti](~/android/platform/fragments/index.md) che consentono di iniziare a usare i frammenti. 



## <a name="start-an-app-project"></a>Avviare un progetto di app

Creare un nuovo progetto Android denominato **FlashCardPager**. Avviare quindi Gestione pacchetti NuGet (per altre informazioni sull'installazione dei pacchetti NuGet, vedere [procedura dettagliata: Inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). Trovare e installare il pacchetto **Novell. Android. support. v4** come illustrato in [viewpager e viste](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Aggiungere un'origine dati di esempio

In **FlashCardPager**, l'origine dati è un mazzo di schede flash rappresentato dalla `FlashCardDeck` classe. questa origine dati fornisce l'oggetto con il contenuto dell' `ViewPager` elemento. `FlashCardDeck`contiene una raccolta pronta di problemi matematici e risposte. Il `FlashCardDeck` costruttore non richiede argomenti: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La raccolta di schede flash in `FlashCardDeck` è organizzata in modo che ogni scheda flash possa accedere a un indicizzatore. La riga di codice seguente, ad esempio, recupera il quarto problema di smart card nel mazzo: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Questa riga di codice recupera la risposta corrispondente al problema precedente:

```csharp
string answer = flashCardDeck[3].Answer;
```

Poiché i dettagli di implementazione `FlashCardDeck` di non sono rilevanti per `ViewPager`la comprensione `FlashCardDeck` , il codice non è elencato qui.
Il codice sorgente di `FlashCardDeck` è disponibile in [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
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

Questo XML definisce un `ViewPager` oggetto che occupa l'intera schermata. Si noti che è necessario usare il nome completo **Android. support. v4. View. viewpager** perché `ViewPager` è incluso in una libreria di supporto. `ViewPager`è disponibile solo dalla [libreria del supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). non è disponibile nel Android SDK.


## <a name="set-up-viewpager"></a>Configurare ViewPager

Modificare **MainActivity.cs** e aggiungere le istruzioni `using` seguenti:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Modificare la `MainActivity` dichiarazione della classe in modo che derivi da `FragmentActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity`è derivato da`FragmentActivity` ( `Activity`anziché) perché `FragmentActivity` sa come gestire il supporto dei frammenti. Sostituire il metodo `OnCreate` con il codice seguente: 

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

1.  Imposta la visualizzazione dalla risorsa di layout **Main. aXML** .

2.  Recupera un riferimento a `ViewPager` dal layout.

3.  Crea un'istanza di `FlashCardDeck` un nuovo oggetto come origine dati.

Quando si compila ed esegue questo codice, viene visualizzata una schermata simile alla seguente: 

[![Screenshot dell'app FlashCardPager con ViewPager vuoto](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

A questo punto, `ViewPager` è vuoto perché mancano i frammenti usati per `ViewPager`popolare e non è presente un adattatore per la creazione di questi frammenti dai dati in **FlashCardDeck**. 

Nelle sezioni seguenti viene creato un `FlashCardFragment` oggetto per implementare la funzionalità di ogni scheda Flash e viene creato un `FragmentPagerAdapter` oggetto per connettere l' `ViewPager` oggetto ai frammenti creati dai dati in `FlashCardDeck`. 



## <a name="create-the-fragment"></a>Creare il frammento

Ogni scheda flash verrà gestita da un frammento di interfaccia `FlashCardFragment`utente denominato. `FlashCardFragment`verranno visualizzate le informazioni contenute con una singola scheda flash. Ogni istanza di `FlashCardFragment` verrà ospitata `ViewPager`da. 
`FlashCardFragment`la visualizzazione è costituita da `TextView` un oggetto che Visualizza il testo del problema relativo alla scheda flash. Questa vista implementa un gestore eventi che usa un oggetto `Toast` per visualizzare la risposta quando l'utente tocca la domanda di memoria flash. 



### <a name="create-the-flashcardfragment-layout"></a>Creare il layout di FlashCardFragment

Prima `FlashCardFragment` di poter implementare, è necessario definire il layout. Questo layout è un layout del contenitore di frammenti per un singolo frammento. Aggiungere un nuovo layout Android a **risorse/layout** denominato **flashcard_layout. aXML**. Aprire **Resources/layout/flashcard_layout. aXML** e sostituirne il contenuto con il codice seguente: 

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

Questo layout definisce un singolo frammento di scheda Flash; ogni frammento è costituito da `TextView` un che visualizza un problema matematico usando un tipo di carattere grande (100sp). Questo testo viene centrato verticalmente e orizzontalmente sulla scheda flash. 



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

Questo codice elimina la definizione essenziale `Fragment` che verrà usata per visualizzare una scheda flash. Si noti `FlashCardFragment` che è derivato dalla versione della libreria di `Fragment` supporto di `Android.Support.V4.App.Fragment`definita in. Il costruttore è vuoto, in modo `newInstance` che il metodo factory venga usato per creare `FlashCardFragment` un nuovo oggetto anziché un costruttore. 

Il `OnCreateView` metodo Lifecycle crea e configura il `TextView`. Consente di ingrandire il layout per l'oggetto `TextView` del frammento e restituisce l'oggetto inflatto al chiamante. `TextView` `LayoutInflater`e `ViewGroup` vengono passati a `OnCreateView` in modo che sia possibile gonfiare il layout. Il `savedInstanceState` bundle contiene i `OnCreateView` dati utilizzati da per ricreare `TextView` l'oggetto da uno stato salvato. 

La visualizzazione del frammento viene inflat in modo esplicito dalla chiamata `inflater.Inflate`a. L' `container` argomento è l'elemento padre della visualizzazione e il `false` flag indica all'INFLATER di evitare di aggiungere la visualizzazione inflat all'elemento padre della visualizzazione (verrà aggiunta quando `ViewPager` la chiamata al `GetItem` metodo dell'adapter più avanti in questo procedura dettagliata). 



### <a name="add-state-code-to-flashcardfragment"></a>Aggiungere il codice di stato a FlashCardFragment

Analogamente a un'attività, un frammento ha un oggetto `Bundle` che usa per salvare e recuperare il proprio stato. In **FlashCardPager**, `Bundle` viene usato per salvare il testo della domanda e della risposta per la scheda Flash associata. In **FlashCardFragment.cs**aggiungere le chiavi seguenti `Bundle` all'inizio della definizione della `FlashCardFragment` classe: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modificare il `newInstance` metodo factory in modo che crei un `Bundle` oggetto e usi le chiavi sopra indicate per archiviare il testo della domanda e della risposta passato nel frammento dopo che ne è stata creata un'istanza: 

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

Modificare il metodo `OnCreateView` del ciclo di vita del frammento per recuperare queste informazioni dal bundle passato e caricare il testo della `TextBox`domanda in: 

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

La `answer` variabile non viene utilizzata in questo caso, ma verrà utilizzata in un secondo momento quando il codice del gestore eventi viene aggiunto a questo file. 


## <a name="create-the-adapter"></a>Creare l'adapter

`ViewPager`Usa un oggetto controller di adapter che risiede tra `ViewPager` e l'origine dati (vedere l'illustrazione nell'articolo dell' [Adapter](~/android/user-interface/controls/view-pager/index.md#adapter) viewpager). Per accedere a questi dati `ViewPager` , è necessario fornire un adapter personalizzato derivato da `PagerAdapter`. Poiché in questo esempio vengono utilizzati i frammenti, viene `FragmentPagerAdapter` utilizzato un oggetto `PagerAdapter` &ndash; `FragmentPagerAdapter` derivato da. 
`FragmentPagerAdapter`rappresenta ogni pagina come `Fragment` che viene mantenuta in modo permanente in Gestione frammenti per tutto il tempo in cui l'utente può tornare alla pagina. Quando l'utente `ViewPager`scorre le pagine del `FragmentPagerAdapter` , estrae le informazioni dall'origine dati e le usa per creare `Fragment`le `ViewPager` istanze di da visualizzare. 

Quando si implementa un `FragmentPagerAdapter`, è necessario eseguire l'override di quanto segue:

-   **Numero** di &ndash; Proprietà di sola lettura che restituisce il numero di visualizzazioni (pagine) disponibili.

-   **GetItem** &ndash; Restituisce il frammento da visualizzare per la pagina specificata.

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

Questo codice esclude l'implementazione essenziale `FragmentPagerAdapter` . Nelle sezioni seguenti, ognuno di questi metodi viene sostituito con codice funzionante. Lo scopo del costruttore è passare Gestione frammenti al `FlashCardDeckAdapter`costruttore della classe di base. 



### <a name="implement-the-adapter-constructor"></a>Implementare il costruttore dell'adattatore

Quando l'app crea un'istanza `FlashCardDeckAdapter`di, fornisce un riferimento a gestione frammenti e ne viene `FlashCardDeck`creata un'istanza. Aggiungere la variabile membro seguente all'inizio della `FlashCardDeckAdapter` classe in **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Aggiungere la seguente riga di codice al `FlashCardDeckAdapter` Costruttore: 

```csharp
this.flashCardDeck = flashCards;
```

Questa riga di codice archivia l' `FlashCardDeck` istanza di `FlashCardDeckAdapter` che verrà utilizzata da. 



### <a name="implement-count"></a>Numero di implementazioni

L' `Count` implementazione è relativamente semplice: restituisce il numero di schede flash nel deck della scheda flash. Sostituisci `Count` con il seguente codice: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


La `NumCards` proprietà di `FlashCardDeck` restituisce il numero di schede Flash (numero di frammenti) nel set di dati. 



### <a name="implement-getitem"></a>Implementa GetItem

Il `GetItem` metodo restituisce il frammento associato alla posizione specificata. Quando `GetItem` viene chiamato per una posizione nel deck della scheda Flash, restituisce un oggetto `FlashCardFragment` configurato per visualizzare il problema della scheda flash in tale posizione. Sostituire il metodo `GetItem` con il codice seguente: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Questo codice esegue le operazioni seguenti:

1.  Cerca la stringa del problema matematico nel `FlashCardDeck` Deck per la posizione specificata. 

2.  Cerca la stringa di risposta nel `FlashCardDeck` Deck per la posizione specificata. 

3.  Chiama il `FlashCardFragment` metodo `newInstance`Factory, passando le stringhe di risposta e il problema della scheda flash. 

4.  Crea e restituisce una nuova scheda `Fragment` Flash contenente il testo della domanda e della risposta per tale posizione. 

Quando l' `ViewPager` oggetto esegue il `Fragment` rendering `position`di in, viene `TextBox` visualizzato l'oggetto che contiene la stringa del `position` problema matematico che risiede nel deck della scheda flash. 



## <a name="add-the-adapter-to-the-viewpager"></a>Aggiungere l'adapter a ViewPager

Ora che `FlashCardDeckAdapter` è implementato, è possibile aggiungerlo `ViewPager`a. In **MainActivity.cs**aggiungere la seguente riga di codice alla fine del `OnCreate` metodo:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Questo codice crea un'istanza `FlashCardDeckAdapter`di, passando `SupportFragmentManager` in nel primo argomento. (La `SupportFragmentManager` proprietà di FragmentActivity viene utilizzata per ottenere un riferimento &ndash; `FragmentManager` a per ulteriori informazioni su `FragmentManager`, vedere [gestione dei frammenti](~/android/platform/fragments/managing-fragments.md)). 

L'implementazione di base è ora &ndash; completare la compilazione ed eseguire l'app.
Nella schermata successiva verrà visualizzata la prima immagine del deck della scheda Flash, come illustrato nella schermata successiva. Scorri verso sinistra per visualizzare altre schede Flash, quindi scorri a destra per tornare al mazzo di carte Flash:

[![Screenshot di esempio dell'app FlashCardPager senza indicatori di cercapersone](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Aggiungi un indicatore cercapersone

Questa implementazione `ViewPager` minima Visualizza ogni scheda Flash nel mazzo, ma non fornisce alcuna indicazione sul punto in cui l'utente si trova all'interno del mazzo. Il passaggio successivo consiste nell'aggiungere un `PagerTabStrip`. `PagerTabStrip` Informa l'utente del numero di problema visualizzato e fornisce il contesto di navigazione visualizzando un hint delle schede Flash precedenti e successive. 

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

Quando si compila e si esegue l'app, il vuoto `PagerTabStrip` visualizzato nella parte superiore di ogni scheda flash è visibile: 

[![Primo piano di PagerTabStrip senza testo](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Visualizza un titolo

Per aggiungere un titolo a ogni scheda della pagina, implementare `GetPageTitleFormatted` il metodo nell'adapter. `ViewPager`chiama `GetPageTitleFormatted` (se implementato) per ottenere la stringa del titolo che descrive la pagina in corrispondenza della posizione specificata. Aggiungere il metodo seguente alla `FlashCardDeckAdapter` classe in **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Questo codice converte la posizione del deck della scheda flash in un numero di problema. La stringa risultante viene convertita in un `String` oggetto Java restituito `ViewPager`a. Quando si esegue l'app con questo nuovo metodo, ogni pagina Visualizza il numero di problema in `PagerTabStrip`: 

[![Screenshot di FlashCardPager con il numero di problema visualizzato al di sopra di ogni pagina](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

È possibile scorrere in avanti e indietro per visualizzare il numero di problema nel deck della scheda Flash visualizzato nella parte superiore di ogni scheda flash. 



## <a name="handle-user-input"></a>Gestisci input utente

**FlashCardPager** presenta una serie di schede flash basate su frammenti in un `ViewPager`, ma non dispone ancora di un modo per rivelare la risposta a ogni problema. In questa sezione viene aggiunto un gestore eventi a `FlashCardFragment` per visualizzare la risposta quando l'utente tocca il testo del problema relativo alla scheda flash. 

Aprire **FlashCardFragment.cs** e aggiungere il codice seguente alla fine del `OnCreateView` metodo appena prima che la visualizzazione venga restituita al chiamante: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Questo `Click` gestore eventi Visualizza la risposta in un avviso popup che viene visualizzato quando l'utente `TextBox`tocca. La `answer` variabile è stata inizializzata in precedenza quando le informazioni sullo stato sono state lette `OnCreateView`dal bundle passato a. Compilare ed eseguire l'app, quindi toccare il testo del problema su ogni scheda flash per visualizzare la risposta: 

[![Screenshot dei popup dell'app FlashCardPager quando viene toccato un problema matematico](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

Il **FlashCardPager** presentato in questa procedura dettagliata USA `MainActivity` una derivata `FragmentActivity`da `MainActivity` , ma è anche possibile derivare da `AppCompatActivity` , che fornisce anche il supporto per la gestione dei frammenti. Per visualizzare un `AppCompatActivity` esempio, vedere [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) nella raccolta di esempi.



## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato fornito un esempio dettagliato di come creare un'app basata `ViewPager`su base usando. `Fragment` È stata presentata un'origine dati di esempio contenente domande e risposte con le `ViewPager` schede Flash, un layout per visualizzare le schede `FragmentPagerAdapter` Flash e una sottoclasse che connette l'oggetto `ViewPager` all'origine dati. Per consentire all'utente di spostarsi tra le schede Flash, sono state incluse istruzioni che spiegano come `PagerTabStrip` aggiungere un oggetto per visualizzare il numero di problema nella parte superiore di ogni pagina. Infine, è stato aggiunto il codice di gestione degli eventi per visualizzare la risposta quando l'utente tocca un problema relativo a una scheda flash. 



## <a name="related-links"></a>Collegamenti correlati

- [FlashCardPager (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
