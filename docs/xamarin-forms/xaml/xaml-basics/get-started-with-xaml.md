---
title: Parte 1. Introduzione a XAML
description: In un' Xamarin.Forms applicazione XAML viene usato principalmente per definire il contenuto visivo di una pagina e funziona insieme a un file code-behind.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e38080fc9bc4ef0b74eb8c12c3a3f646c4888f53
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198073"
---
# <a name="part-1-getting-started-with-xaml"></a>Parte 1. Introduzione a XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_In un' Xamarin.Forms applicazione XAML viene usato principalmente per definire il contenuto visivo di una pagina e funziona insieme a un file code-behind C#._

Il file code-behind fornisce il supporto del codice per il markup. Insieme, questi due file contribuiscono a una nuova definizione di classe che include visualizzazioni figlio e inizializzazione di proprietà. All'interno del file XAML, alle classi e alle proprietà viene fatto riferimento con elementi e attributi XML e vengono stabiliti i collegamenti tra il markup e il codice.

## <a name="creating-the-solution"></a>Creazione della soluzione

Per iniziare a modificare il primo file XAML, usare Visual Studio o Visual Studio per Mac per creare una nuova Xamarin.Forms soluzione. Selezionare la scheda sotto corrispondente all'ambiente.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

In Windows avviare Visual Studio 2019 e nella finestra di avvio fare clic su **Crea un nuovo progetto** per creare un nuovo progetto:

![Nuova finestra della soluzione](get-started-with-xaml-images/win/new-solution-2019.png)

Nella finestra **Crea un nuovo progetto** selezionare **mobile** nell'elenco a discesa **tipo di progetto** , selezionare il modello **app mobile ( Xamarin.Forms )** e fare clic sul pulsante **Avanti** :

![Finestra nuovo progetto](get-started-with-xaml-images/win/new-project-2019.png)

Nella finestra **Configura nuovo progetto** impostare il **nome del progetto** su **XamlSamples** (o qualsiasi preferenza) e fare clic sul pulsante **Crea** .

Nella finestra di dialogo **nuova app multipiattaforma** fare clic su **blank**, quindi fare clic sul pulsante **OK** :

![Finestra di dialogo nuova app](get-started-with-xaml-images/win/new-cross-platform-app.png)

Nella soluzione vengono creati quattro progetti: **XamlSamples** .NET standard library, **XamlSamples. Android**, **XamlSamples. iOS**e la soluzione piattaforma UWP (Universal Windows Platform), **XamlSamples. UWP**.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac selezionare **File > nuova soluzione** dal menu. Nella finestra di dialogo **nuovo progetto** selezionare **app multipiattaforma >** a sinistra, quindi fare clic su **app Forms vuota** (*non* su **Forms app**) nell'elenco dei modelli:

![Finestra di dialogo nuovo progetto 1](get-started-with-xaml-images/mac/newprojectdialog1.png)

Fare clic su **Avanti**.

Nella finestra di dialogo successiva assegnare al progetto il nome **XamlSamples** (o qualsiasi altra preferenza). Verificare che sia selezionato il pulsante di opzione **usa .NET standard** :

![Finestra di dialogo nuovo progetto 2](get-started-with-xaml-images/mac/newprojectdialog2.png)

Fare clic su **Avanti**.

Nella finestra di dialogo seguente è possibile selezionare un percorso per il progetto:

![Finestra di dialogo nuovo progetto 3](get-started-with-xaml-images/mac/newprojectdialog3.png)

Premere **Crea**

Nella soluzione vengono creati tre progetti: **XamlSamples** .NET standard library, **XamlSamples. Android**e **XamlSamples. iOS**.

-----

Dopo aver creato la soluzione **XamlSamples** , è possibile testare l'ambiente di sviluppo selezionando i diversi progetti della piattaforma come progetto di avvio della soluzione e compilando e distribuendo l'applicazione semplice creata dal modello di progetto in emulatori Windows Phone o in dispositivi reali.

A meno che non sia necessario scrivere codice specifico per la piattaforma, il progetto Shared **XamlSamples** .NET Standard Library è il punto in cui si spenderanno praticamente tutti i tempi di programmazione. Questi articoli non si troveranno al di fuori del progetto.

### <a name="anatomy-of-a-xaml-file"></a>Anatomia di un file XAML

All'interno della **XamlSamples** .NET Standard Library è una coppia di file con i nomi seguenti:

- **App. XAML**, il file XAML; e
- **App.XAML.cs**, un file *code-behind* C# associato al file XAML.

È necessario fare clic sulla freccia accanto a **app. XAML** per visualizzare il file code-behind.

Sia **app. XAML** che **app.XAML.cs** contribuiscono a una classe denominata `App` che deriva da `Application` . La maggior parte delle altre classi con file XAML contribuisce a una classe che deriva da `ContentPage` . tali file usano XAML per definire il contenuto visivo di un'intera pagina. Questo vale per gli altri due file del progetto **XamlSamples** :

- **MainPage. XAML**, il file XAML; e
- **MainPage.XAML.cs**, il file code-behind C#.

Il file **MainPage. XAML** ha un aspetto simile al seguente (sebbene la formattazione potrebbe essere leggermente diversa):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!"
               VerticalOptions="Center"
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

Le due dichiarazioni dello spazio dei nomi XML ( `xmlns` ) fanno riferimento a URI, il primo sito Web di Novell e il secondo su Microsoft. Non preoccupare la verifica degli URI a cui puntano. Non c'è niente. Sono semplicemente URI di proprietà di Novell e Microsoft e funzionano fondamentalmente come identificatori di versione.

La prima dichiarazione dello spazio dei nomi XML significa che i tag definiti nel file XAML senza prefisso fanno riferimento alle classi in Xamarin.Forms , ad esempio `ContentPage` . La seconda dichiarazione dello spazio dei nomi definisce un prefisso `x` . Viene usato per diversi elementi e attributi intrinseci a XAML e che sono supportati da altre implementazioni di XAML. Tuttavia, questi elementi e attributi sono leggermente diversi a seconda dell'anno incorporato nell'URI. Xamarin.Formssupporta la specifica XAML 2009, ma non tutte.

La `local` dichiarazione dello spazio dei nomi consente di accedere ad altre classi dal progetto libreria .NET standard.

Alla fine del primo tag, il `x` prefisso viene usato per un attributo denominato `Class` . Poiché l'uso di questo `x` prefisso è praticamente universale per lo spazio dei nomi XAML, gli attributi XAML come `Class` sono quasi sempre definiti `x:Class` .

L' `x:Class` attributo specifica un nome di classe .NET completo, ovvero la `MainPage` classe nello `XamlSamples` spazio dei nomi. Ciò significa che questo file XAML definisce una nuova classe denominata `MainPage` nello `XamlSamples` spazio dei nomi che deriva da `ContentPage` , il tag in cui `x:Class` viene visualizzato l'attributo.

L' `x:Class` attributo può essere visualizzato solo nell'elemento radice di un file XAML per definire una classe C# derivata. Si tratta dell'unica nuova classe definita nel file XAML. Tutto il resto visualizzato nel file XAML viene invece semplicemente creato con istanze dalle classi esistenti e inizializzato.

Il file **MainPage.XAML.cs** è simile al seguente (eccetto le direttive inutilizzate `using` ):

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

La `MainPage` classe deriva da `ContentPage` , ma si noti la `partial` definizione della classe. Questo suggerisce che dovrebbe essere presente un'altra definizione di classe parziale per `MainPage` , ma dove si trova? E qual è il `InitializeComponent` Metodo?

Quando Visual Studio compila il progetto, analizza il file XAML per generare un file di codice C#. Se si osserva la directory **XamlSamples\XamlSamples\obj\Debug** , si troverà un file denominato **XamlSamples.MainPage.XAML.g.cs**. La "g" sta per essere generata. Si tratta dell'altra definizione di classe parziale di `MainPage` che contiene la definizione del `InitializeComponent` metodo chiamato dal `MainPage` costruttore. Queste due definizioni di classe parziali `MainPage` possono quindi essere compilate insieme. A seconda del fatto che XAML sia compilato o meno, il file XAML o un formato binario del file XAML è incorporato nel file eseguibile.

In fase di esecuzione, il codice nel progetto di piattaforma specifico chiama un `LoadApplication` metodo, passandogli una nuova istanza della `App` classe nella libreria .NET standard. Il `App` costruttore della classe crea un'istanza di `MainPage` . Il costruttore di tale classe chiama `InitializeComponent` , che quindi chiama il `LoadFromXaml` metodo che estrae il file XAML (o il relativo binario compilato) dalla libreria .NET standard. `LoadFromXaml`Inizializza tutti gli oggetti definiti nel file XAML, li connette tutti insieme nelle relazioni padre-figlio, associa i gestori eventi definiti nel codice agli eventi impostati nel file XAML e imposta l'albero risultante degli oggetti come contenuto della pagina.

Anche se in genere non è necessario impiegare molto tempo con i file di codice generati, a volte le eccezioni di runtime vengono generate nel codice dei file generati, pertanto è necessario conoscerle.

Quando si compila ed esegue questo programma, l' `Label` elemento viene visualizzato al centro della pagina come suggerito dal codice XAML:

[![Xamarin.FormsVisualizzazione predefinita](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

Per gli oggetti visivi più interessanti, è sufficiente XAML.

## <a name="adding-new-xaml-pages"></a>Aggiunta di nuove pagine XAML

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per aggiungere altre classi basate su XAML `ContentPage` al progetto, selezionare il progetto **XamlSamples** .NET standard library, fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo elemento**. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **elementi di Visual C# > Xamarin.Forms pagina contenuto >** (non **pagina contenuto (C#)**, che consente di creare una pagina di solo codice, o **visualizzazione contenuto**, che non è una pagina. Assegnare un nome alla pagina, ad esempio **HelloXamlPage**:

![Finestra di dialogo Aggiungi nuovo elemento](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per aggiungere altre classi basate su XAML `ContentPage` al progetto, selezionare il progetto **XamlSamples** .NET Standard Library e richiamare il **file > nuova** voce di menu file. Nella parte sinistra della finestra di dialogo **nuovo file** selezionare **moduli** a sinistra e **Forms ContentPage XAML** (non **Forms ContentPage**, che consente di creare una pagina di solo codice, o **visualizzazione contenuto**, che non è una pagina). Assegnare un nome alla pagina, ad esempio **HelloXamlPage**:

![Finestra di dialogo nuovo file](get-started-with-xaml-images/mac/newfiledialog.png)

-----

Vengono aggiunti due file al progetto, **HelloXamlPage. XAML** e il file code-behind **HelloXamlPage.XAML.cs**.

## <a name="setting-page-content"></a>Impostazione del contenuto della pagina

Modificare il file **HelloXamlPage. XAML** in modo che gli unici tag siano quelli `ContentPage` per `ContentPage.Content` e:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

I `ContentPage.Content` tag sono parte della sintassi univoca di XAML. Inizialmente, potrebbero sembrare un codice XML non valido, ma sono validi. Il periodo non è un carattere speciale in XML.

I `ContentPage.Content` tag vengono chiamati tag dell' *elemento proprietà* . `Content`è una proprietà di `ContentPage` e viene in genere impostata su una singola visualizzazione o un layout con visualizzazioni figlio. Normalmente le proprietà diventano attributi in XAML, ma sarebbe difficile impostare un `Content` attributo su un oggetto complesso. Per questo motivo, la proprietà viene espressa come elemento XML costituito dal nome della classe e dal nome della proprietà separati da un punto. `Content`A questo punto è possibile impostare la proprietà tra i `ContentPage.Content` tag, come segue:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

Si noti inoltre che `Title` nel tag radice è stato impostato un attributo.

A questo punto, la relazione tra le classi, le proprietà e il codice XML dovrebbe essere evidente: una Xamarin.Forms classe (ad esempio `ContentPage` o `Label` ) viene visualizzata nel file XAML come elemento XML. Le proprietà di tale classe, `Title` incluse `ContentPage` le proprietà su e Seven di `Label` , in genere vengono visualizzate come attributi XML.

Sono disponibili molti tasti di scelta rapida per impostare i valori di queste proprietà. Alcune proprietà sono tipi di dati di base: ad esempio, le `Title` `Text` proprietà e sono di tipo `String` , `Rotation` è di tipo `Double` e `IsVisible` (per `true` impostazione predefinita, è impostato qui solo per l'illustrazione) è di tipo `Boolean` .

La `HorizontalTextAlignment` proprietà è di tipo `TextAlignment` , che è un'enumerazione. Per una proprietà di qualsiasi tipo di enumerazione, è sufficiente specificare un nome di membro.

Per le proprietà di tipi più complessi, tuttavia, i convertitori vengono usati per l'analisi del codice XAML. Si tratta di classi in Xamarin.Forms che derivano da `TypeConverter` . Molte sono classi pubbliche, ma altre no. Per questo particolare file XAML, molte di queste classi svolgono un ruolo dietro le quinte:

- `LayoutOptionsConverter`per la `VerticalOptions` Proprietà
- `FontSizeConverter`per la `FontSize` Proprietà
- `ColorTypeConverter`per la `TextColor` Proprietà

Questi convertitori regolano la sintassi consentita delle impostazioni delle proprietà.

`ThicknessTypeConverter`Può gestire uno, due o quattro numeri separati da virgole. Se viene specificato un numero, si applica a tutti e quattro i lati. Con due numeri, il primo è il riempimento sinistro e destro e il secondo è superiore e inferiore. Quattro numeri sono nell'ordine a sinistra, in alto, a destra e in basso.

`LayoutOptionsConverter`Può convertire i nomi dei campi statici pubblici della `LayoutOptions` struttura in valori di tipo `LayoutOptions` .

`FontSizeConverter`Può gestire un `NamedSize` membro o una dimensione numerica del tipo di carattere.

`ColorTypeConverter`Accetta i nomi dei campi statici pubblici della `Color` struttura o dei valori RGB esadecimali, con o senza un canale alfa, preceduto da un simbolo di cancelletto (#). Di seguito è illustrata la sintassi senza un canale alfa:

 `TextColor="#rrggbb"`

Ognuna delle lettere minuscole è una cifra esadecimale. Ecco come viene incluso un canale alfa:

 `TextColor="#aarrggbb">`

Per il canale alfa, tenere presente che FF è completamente opaco e 00 è completamente trasparente.

Altri due formati consentono di specificare solo una singola cifra esadecimale per ogni canale:

 `TextColor="#rgb"` `TextColor="#argb"`

In questi casi, la cifra viene ripetuta per formare il valore. Ad esempio, #CF3 è il colore RGB CC-FF-33.

## <a name="page-navigation"></a>Navigazione tra pagine

Quando si esegue il programma **XamlSamples** , `MainPage` viene visualizzato. Per visualizzare il nuovo `HelloXamlPage` , è possibile impostarlo come nuova pagina di avvio nel file **app.XAML.cs** o passare alla nuova pagina da `MainPage` .

Per implementare la navigazione, modificare prima di tutto il codice nel costruttore **app.XAML.cs** in modo da `NavigationPage` creare un oggetto:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

Nel costruttore **MainPage.XAML.cs** è possibile creare un semplice `Button` e utilizzare il gestore eventi per passare a `HelloXamlPage` :

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

L'impostazione della `Content` proprietà della pagina sostituisce l'impostazione della `Content` proprietà nel file XAML. Quando si compila e si distribuisce la nuova versione di questo programma, sullo schermo viene visualizzato un pulsante. Premendo il pulsante si passa a `HelloXamlPage` . Di seguito è illustrata la pagina risultante in iPhone, Android e UWP:

[![Testo etichetta ruotato](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

È possibile tornare a `MainPage` usare il pulsante **< indietro** in iOS, usando la freccia sinistra nella parte superiore della pagina o nella parte inferiore del telefono in Android o usando la freccia sinistra nella parte superiore della pagina in Windows 10.

È possibile provare a usare XAML per diversi modi di eseguire il rendering di `Label` . Se è necessario incorporare caratteri Unicode nel testo, è possibile utilizzare la sintassi XML standard. Ad esempio, per inserire il saluto nelle virgolette intelligenti, usare:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Ecco come si presenta:

[![Testo etichetta ruotato con caratteri Unicode](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML e interazioni del codice

L'esempio **HelloXamlPage** contiene solo un singolo oggetto `Label` nella pagina, ma si tratta di un'operazione molto insolita. La maggior parte delle `ContentPage` derivazioni imposta la `Content` proprietà su un layout di qualche tipo, ad esempio `StackLayout` . La `Children` proprietà di `StackLayout` è definita come di tipo `IList<View>` , ma in realtà è un oggetto di tipo `ElementCollection<View>` e tale raccolta può essere popolata con più visualizzazioni o altri layout. In XAML, le relazioni padre-figlio vengono stabilite con la normale gerarchia XML. Di seguito è riportato un file XAML per una nuova pagina denominata **XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Il file XAML è sintatticamente completo ed è simile al seguente:

[![Più controlli in una pagina](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

Tuttavia, è probabile che questo programma risulti insufficiente dal punto di vista funzionale. Probabilmente l'oggetto `Slider` dovrebbe causare la `Label` visualizzazione del valore corrente da parte di e l'oggetto `Button` è probabilmente progettato per eseguire un'operazione all'interno del programma.

Come si vedrà nella [parte 4. Le nozioni di base sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), il processo di visualizzazione di un `Slider` valore tramite un oggetto `Label` possono essere gestite interamente in XAML con un data binding. Tuttavia, è utile vedere prima la soluzione di codice. Anche in questo caso, la gestione del `Button` clic richiede sicuramente il codice. Questo significa che il file code-behind per `XamlPlusCodePage` deve contenere gestori per l' `ValueChanged` evento di `Slider` e l' `Clicked` evento di `Button` . Aggiungere i seguenti elementi:

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

Questi gestori eventi non devono essere pubblici.

Tornando al file XAML, i `Slider` tag e `Button` devono includere gli attributi per gli `ValueChanged` eventi e `Clicked` che fanno riferimento a questi gestori:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

Si noti che l'assegnazione di un gestore a un evento ha la stessa sintassi dell'assegnazione di un valore a una proprietà.

Se il gestore per l' `ValueChanged` evento di `Slider` utilizzerà `Label` per visualizzare il valore corrente, il gestore deve fare riferimento a tale oggetto dal codice. `Label`Per è necessario un nome, specificato con l' `x:Name` attributo.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

Il `x` prefisso dell' `x:Name` attributo indica che questo attributo è intrinseco a XAML.

Il nome assegnato all' `x:Name` attributo ha le stesse regole dei nomi delle variabili C#. Ad esempio, deve iniziare con una lettera o un carattere di sottolineatura e non deve contenere spazi incorporati.

A questo punto `ValueChanged` , il gestore eventi può impostare `Label` per visualizzare il nuovo `Slider` valore. Il nuovo valore è disponibile dagli argomenti dell'evento:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

In alternativa, il gestore potrebbe ottenere l' `Slider` oggetto che genera questo evento dall' `sender` argomento e ottenere la `Value` proprietà da tale oggetto:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Quando si esegue il programma per la prima volta, `Label` non viene visualizzato il `Slider` valore perché l' `ValueChanged` evento non è stato ancora generato. Tuttavia, qualsiasi modifica di `Slider` causa la visualizzazione del valore:

[![Valore dispositivo di scorrimento visualizzato](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

Ora per il `Button` . Si simula una risposta a un `Clicked` evento visualizzando un avviso con il `Text` del pulsante. Il gestore eventi può eseguire in modo sicuro il cast dell' `sender` argomento a un oggetto `Button` e quindi accedere alle relative proprietà:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

Il metodo viene definito come `async` poiché il `DisplayAlert` metodo è asincrono e deve essere preceduto dall' `await` operatore, che restituisce quando il metodo viene completato. Poiché questo metodo ottiene l'oggetto che genera `Button` l'evento dall' `sender` argomento, è possibile utilizzare lo stesso gestore per più pulsanti.

Si è notato che un oggetto definito in XAML può generare un evento gestito nel file code-behind e che il file code-behind può accedere a un oggetto definito in XAML usando il nome assegnato con l' `x:Name` attributo. Questi sono i due modi fondamentali di interazione del codice e di XAML.

Altre informazioni dettagliate sul funzionamento di XAML possono essere raccolte esaminando il **file XamlPlusCode.XAML.g.cs**appena generato, che ora include qualsiasi nome assegnato a qualsiasi `x:Name` attributo come campo privato. Ecco una versione semplificata del file:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

La dichiarazione di questo campo consente di usare liberamente la variabile in qualsiasi punto del `XamlPlusCodePage` file di classe parziale nella giurisdizione. In fase di esecuzione, il campo viene assegnato dopo che il codice XAML è stato analizzato. Questo significa che il `valueLabel` campo è `null` quando il `XamlPlusCodePage` Costruttore inizia ma valido dopo la `InitializeComponent` chiamata a.

Quando `InitializeComponent` restituisce il controllo al costruttore, gli oggetti visivi della pagina sono stati costruiti come se fossero stati creati e inizializzati nel codice. Il file XAML non riproduce più alcun ruolo nella classe. È possibile modificare questi oggetti nella pagina in qualsiasi modo desiderato, ad esempio aggiungendo visualizzazioni a `StackLayout` oppure impostando la `Content` proprietà della pagina su un altro elemento. È possibile "scorrere l'albero" esaminando la `Content` proprietà della pagina e gli elementi nelle `Children` raccolte di layout. È possibile impostare le proprietà nelle viste a cui si accede in questo modo oppure assegnare i gestori eventi in modo dinamico.

È gratuito. Si tratta della pagina e XAML è solo uno strumento per la compilazione del contenuto.

## <a name="summary"></a>Summary

Con questa introduzione si è visto come un file XAML e un file di codice contribuiscono alla definizione di una classe e come interagiscono i file XAML e di codice. Tuttavia, XAML dispone anche di funzionalità sintattiche univoche che ne consentono l'uso in modo molto flessibile. È possibile iniziare a esplorarli nella [parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Dal data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
