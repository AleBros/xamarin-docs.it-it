---
title: Xamarin. Android Fragments Walkthrough - parte 1
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 984e72f2b3ee11bcc0902663893509e5687fc099
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119345"
---
# <a name="fragments-walkthrough-ndash-phone"></a>Procedura dettagliata frammenti &ndash; telefono

Questa è la prima parte di una procedura dettagliata in cui verrà creata un'app xamarin. Android destinata a un dispositivo Android con orientamento verticale. Questa procedura dettagliata verrà illustrato come creare frammenti in xamarin. Android e come aggiungerli a un campione.

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Le classi seguenti verranno create per questa app:

1. `PlayQuoteFragment` &nbsp; Questo frammento verrà visualizzato un'offerta da un gioco da William Shakespeare. Questa verrà ospitata da `PlayQuoteActivity`.
1. `Shakespeare` &nbsp; Questa classe conterrà due matrici hardcoded come proprietà.
1. `TitlesFragment` &nbsp; Questo frammento verrà visualizzato un elenco dei titoli di riproduzione che sono stati scritti dal William Shakespeare. Questa verrà ospitata da `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` verrà avviata il `PlayQuoteActivity` in risposta all'utente la selezione di un gioco in `TitlesFragment`.

## <a name="1-create-the-android-project"></a>1. Creare il progetto Android

Creare un nuovo progetto xamarin. Android chiamato **FragmentSample**.
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Creare un nuovo progetto xamarin. Android](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Crea un nuovo progetto xamarin. Android](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

È consigliabile selezionare **sviluppo moderno** per questa procedura dettagliata.

Dopo aver creato il progetto, rinominare il file **layout/Main.axml** al **layout/activity_main.axml**.

-----

## <a name="2-add-the-data"></a>2. Aggiungere i dati

I dati per questa applicazione verranno archiviati in due matrici di stringhe hardcoded che sono proprietà di un nome di classe `Shakespeare`:

* `Shakespeare.Titles` &nbsp; La matrice conterrà un elenco di riproduzione da William Shakespeare. Questo è l'origine dati per il `TitlesFragment`.
* `Shakespeare.Dialogue` &nbsp; La matrice conterrà un elenco delle offerte da uno della riproduzione del contenuto in `Shakespeare.Titles`. Questo è l'origine dati per il `PlayQuoteFragment`.

Aggiungere un nuovo C# classe per il **FragmentSample** del progetto e denominarlo **Shakespeare.cs**. All'interno del file, creare un nuovo oggetto C# classe denominata `Shakespeare` con il contenuto seguente

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

Il `PlayQuoteFragment` è un frammento di Android che verrà visualizzato un'offerta per un gioco di Shakespeare che è stato selezionato dall'utente in precedenza nell'applicazione, questo frammento non userà un file di layout Android, al contrario, verrà creato in modo dinamico l'interfaccia utente. Aggiungere un nuovo `Fragment` classe denominata `PlayQuoteFragment` al progetto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Aggiungere un nuovo C# classe](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Aggiungere un nuovo C# classe](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

Quindi, modificare il codice per il frammento simile a quella di questo frammento di codice:

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

È un modello comune nelle App Android per fornire un metodo factory che verrà creata un'istanza di un frammento. Ciò garantisce che il frammento verrà creato con i parametri necessari per il funzionamento corretto. In questa procedura dettagliata, è previsto che l'app per usare il `PlayQuoteFragment.NewInstance` metodo per creare un nuovo frammento ogni volta che un'offerta sia selezionata. Il `NewInstance` metodo utilizzerà un solo parametro &ndash; l'indice dell'azione da visualizzare.

Il `OnCreateView` metodo verrà richiamato da Android quando si è pronti per eseguire il rendering nel frammento sullo schermo. Verrà restituito un Android `View` oggetto, ovvero il frammento. Questo frammento non utilizza un file di layout per creare una vista. Al contrario, a livello di codice creerà la vista creando un **TextView** per contenere l'offerta e visualizzerà widget in un **ScrollView**.

> [!NOTE]
> Le sottoclassi di frammento devono avere un costruttore predefinito pubblico senza parametri.

## <a name="4-create-the-playquoteactivity"></a>4. Creare il PlayQuoteActivity

I frammenti devono essere ospitati in un'attività, in modo che questa app richiede un'attività che ospiterà il `PlayQuoteFragment`. L'attività in modo dinamico aggiungerà il frammento di layout in fase di esecuzione. Aggiungere una nuova attività per l'applicazione e denominarla `PlayQuoteActivity`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Aggiungere attività Android al progetto](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Aggiungere attività Android al progetto](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

Modificare il codice nella `PlayQuoteActivity`:

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

Quando `PlayQuoteActivity` viene creato, creerà un'istanza di un nuovo `PlayQuoteFragment` e il carico di tale frammento nella relativa visualizzazione di primo livello nel contesto di un `FragmentTransaction`. Si noti che questa attività non viene caricato un file di layout Android per la propria interfaccia utente. Al contrario, un nuovo `PlayQuoteFragment` viene aggiunta alla visualizzazione radice dell'applicazione. L'identificatore della risorsa `Android.Resource.Id.Content` viene utilizzato per fare riferimento alla visualizzazione radice di un'attività senza conoscere il relativo identificatore specifico.

## <a name="5-create-titlesfragment"></a>5. Creare TitlesFragment

Il `TitlesFragment` crea una sottoclasse di un frammento specializzato noto come un `ListFragment` che incapsula la logica per la visualizzazione di un `ListView` in un frammento. Oggetto `ListFragment` espone una `ListAdapter` proprietà (usato dal `ListView` per visualizzarne il contenuto) e un gestore eventi denominato `OnListItemClick` che consente il frammento di rispondere al clic del mouse su una riga che viene visualizzato il `ListView`.

Per iniziare, aggiungere un nuovo frammento al progetto e denominarla **TitlesFragment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Aggiungere Android frammento al progetto](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Aggiungere Android frammento al progetto](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

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

Quando viene creata l'attività Android Richiama il `OnActivityCreated` metodo del frammento; qui l'adapter di elenco per il `ListView` viene creato.  Il `ShowQuoteFromPlay` metodo avvia un'istanza di `PlayQuoteActivity` per visualizzare l'offerta per la riproduzione selezionato.

## <a name="display-titlesfragment-in-mainactivity"></a>Visualizzare TitlesFragment MainActivity

Il passaggio finale consiste nella visualizzazione `TitlesFragment` all'interno di `MainActivity`. L'attività non viene caricato in modo dinamico il frammento. Invece il frammento verrà essere caricato in modo statico, dichiararla nel file di layout dell'attività usando un `fragment` elemento. Il frammento da caricare viene identificato tramite l'impostazione di `android:name` attributo alla classe del frammento (incluso lo spazio dei nomi del tipo). Ad esempio, per usare la `TitlesFragment`, quindi `android:name` verrebbe impostato `FragmentSample.TitlesFragment`.

Modificare il file di layout **activity_mail.axml**, sostituendo il documento XML esistente con il codice seguente:

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
> Il `class` attributo è un sostituto valido per `android:name`. Non vi è alcuna indicazione formali nel quale forma si desidera utilizzare, ci sono molti esempi di base di codice che useranno `class` in modo intercambiabile con `android:name`.

Sono non disponibili modifiche al codice necessarie per MainActivity. Il codice in tale classe deve essere molto simile a questo frammento di codice:

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

Ora che il codice è completato, eseguire l'app in un dispositivo per vederlo in azione.

[![Screenshot dell'applicazione in esecuzione su un telefono.](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[Parte 2 di questa procedura dettagliata](./walkthrough-landscape.md) verrà optimtize questa applicazione per i dispositivi in esecuzione in modalità orizzontale.
