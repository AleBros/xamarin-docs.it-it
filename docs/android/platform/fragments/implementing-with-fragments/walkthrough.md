---
title: Procedura dettagliata per i frammenti di Novell. Android-parte 1
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 4471f5ec199ef52a2dcb68ab85cc9a1209eb4802
ms.sourcegitcommit: 9a2a21974d35353c3765eb683ef2fd7161c1d94a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68329972"
---
# <a name="fragments-walkthrough-ndash-phone"></a>Frammenti del telefono &ndash; procedura dettagliata

Questa è la prima parte di una procedura dettagliata che creerà un'app Novell. Android destinata a un dispositivo Android con orientamento verticale. In questa procedura dettagliata viene illustrato come creare frammenti in Novell. Android e come aggiungerli a un esempio.

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Per questa app verranno create le classi seguenti:

1. `PlayQuoteFragment`&nbsp; Questo frammento visualizzerà una citazione di William Shakespeare. Verrà ospitato da `PlayQuoteActivity`.
1. `Shakespeare`&nbsp; Questa classe conterrà due matrici hardcoded come proprietà.
1. `TitlesFragment`&nbsp; In questo frammento verrà visualizzato un elenco di titoli di riproduzione scritti da William Shakespeare. Verrà ospitato da `MainActivity`.
1. `PlayQuoteActivity`avvierà il in risposta all'utente che seleziona una riproduzione. `TitlesFragment` &nbsp; `TitlesFragment` `PlayQuoteActivity`

## <a name="1-create-the-android-project"></a>1. Creare il progetto Android

Creare un nuovo progetto Novell. Android denominato **FragmentSample**.
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Creare un nuovo progetto Novell. Android](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Creazione di un nuovo progetto Novell. Android](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

Si consiglia di selezionare **lo sviluppo moderno** per questa procedura dettagliata.

Dopo aver creato il progetto, rinominare il file **layout/Main. aXML** in **layout/activity_main. aXML**.

-----

## <a name="2-add-the-data"></a>2. Aggiungere i dati

I dati per questa applicazione verranno archiviati in due matrici di stringhe hardcoded che sono proprietà di un nome `Shakespeare`di classe:

* `Shakespeare.Titles`&nbsp; Questa matrice conterrà un elenco di riproduzioni di William Shakespeare. Si tratta dell'origine dati per l' `TitlesFragment`oggetto.
* `Shakespeare.Dialogue`Questa matrice conterrà un elenco di virgolette da uno dei giochi contenuti in `Shakespeare.Titles`. &nbsp; Si tratta dell'origine dati per l' `PlayQuoteFragment`oggetto.

Aggiungere una nuova C# classe al progetto **FragmentSample** e denominarla **Shakespeare.cs**. All'interno di questo file creare una C# nuova classe `Shakespeare` denominata con il contenuto seguente

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3. Creare il PlayQuoteFragment

Il `PlayQuoteFragment` è un frammento Android che visualizzerà un'offerta per una riproduzione Shakespeare che è stata selezionata dall'utente in precedenza nell'applicazione. questo frammento non userà un file di layout Android, bensì creerà dinamicamente l'interfaccia utente. Aggiungere una nuova `Fragment` classe denominata `PlayQuoteFragment` al progetto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Aggiungere una nuova C# classe](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Aggiungere una nuova C# classe](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

Modificare quindi il codice per il frammento in modo simile al frammento di codice seguente:

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

Si tratta di un modello comune nelle app Android per fornire un metodo factory che creerà un'istanza di un frammento. In questo modo si garantisce che il frammento venga creato con i parametri necessari per un corretto funzionamento. In questa procedura dettagliata, l'app dovrebbe usare il `PlayQuoteFragment.NewInstance` metodo per creare un nuovo frammento ogni volta che viene selezionata un'offerta. Il `NewInstance` metodo accetta un solo parametro &ndash; nell'indice dell'offerta da visualizzare.

Il `OnCreateView` metodo verrà richiamato da Android quando è il momento di eseguire il rendering del frammento sullo schermo. Verrà restituito un oggetto Android `View` che è il frammento. Questo frammento non utilizza un file di layout per creare una vista. Al contrario, creerà a livello di codice la vista creando un'istanza  di un oggetto TextView per conservare l'offerta e visualizzerà tale widget in un **ScrollView**.

> [!NOTE]
> Le sottoclassi del frammento devono avere un costruttore predefinito pubblico senza parametri.

## <a name="4-create-the-playquoteactivity"></a>4. Creare il PlayQuoteActivity

I `PlayQuoteFragment`frammenti devono essere ospitati all'interno di un'attività, quindi questa app richiede un'attività che ospiterà. L'attività aggiungerà in modo dinamico il frammento al relativo layout in fase di esecuzione. Aggiungere una nuova attività all'applicazione e denominarla `PlayQuoteActivity`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Aggiungi attività Android al progetto](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Aggiungi attività Android al progetto](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

Modificare il codice in `PlayQuoteActivity`:

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

Quando `PlayQuoteActivity` viene creato, creerà un'istanza di un nuovo `PlayQuoteFragment` oggetto e caricherà il frammento nella visualizzazione radice nel contesto di un `FragmentTransaction`oggetto. Si noti che questa attività non carica un file di layout Android per la relativa interfaccia utente. Viene invece aggiunto un `PlayQuoteFragment` nuovo oggetto alla visualizzazione radice dell'applicazione. L'identificatore `Android.Resource.Id.Content` di risorsa viene usato per fare riferimento alla visualizzazione radice di un'attività senza conoscere l'identificatore specifico.

## <a name="5-create-titlesfragment"></a>5. Crea TitlesFragment

L' `TitlesFragment` oggetto creerà una sottoclasse di un frammento specializzato noto `ListFragment` come che incapsula la logica `ListView` per la visualizzazione di un oggetto in un frammento. Un `ListFragment` oggetto espone `ListAdapter` una `OnListItemClick` proprietà (utilizzata da `ListView`per visualizzare il contenuto) e un gestore eventi denominato che consente al frammento di rispondere ai clic su una riga visualizzata da. `ListView`

Per iniziare, aggiungere un nuovo frammento al progetto e denominarlo **TitlesFragment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Aggiungere il frammento Android al progetto](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Aggiungere il frammento Android al progetto](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

Modificare il codice all'interno del frammento:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
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
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Quando viene creata l'attività, Android richiama il `OnActivityCreated` metodo del frammento, ovvero la posizione in cui viene creato l' `ListView` adattatore di elenco per l'oggetto.  Il `ShowQuoteFromPlay` metodo avvierà un'istanza `PlayQuoteActivity` del per visualizzare le virgolette per la riproduzione selezionata.

## <a name="display-titlesfragment-in-mainactivity"></a>Visualizzare TitlesFragment in MainActivity

Il passaggio finale prevede la visualizzazione `TitlesFragment` all' `MainActivity`interno di. L'attività non carica dinamicamente il frammento. Al contrario, il frammento verrà caricato staticamente dichiarando il frammento nel file di layout dell' `fragment` attività usando un elemento. Il frammento da caricare viene identificato impostando `android:name` l'attributo sulla classe fragment (incluso lo spazio dei nomi del tipo). Per utilizzare `TitlesFragment` `FragmentSample.TitlesFragment`, ad esempio, l'oggettovieneimpostatosu.`android:name`

Modificare il file di layout **activity_main. aXML**, sostituendo il codice XML esistente con quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> L' `class` attributo è un sostituto valido `android:name`per. Non sono disponibili linee guida convenzionali sul modulo preferito. esistono molti esempi di codebase che utilizzeranno in modo `class` intercambiabile con `android:name`.

Non sono necessarie modifiche al codice per MainActivity. Il codice in tale classe dovrebbe essere molto simile al frammento di codice seguente:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>Eseguire l'app

Ora che il codice è completo, eseguire l'app in un dispositivo per verificarne l'azione.

[![Schermate dell'applicazione in esecuzione su un telefono.](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

La [parte 2 di questa procedura dettagliata](./walkthrough-landscape.md) optimtize questa applicazione per i dispositivi in esecuzione in modalità orizzontale.
