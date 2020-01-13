---
title: Parte 1. Introduzione a XAML
description: In un'applicazione Xamarin.Forms XAML viene utilizzato principalmente per definire il contenuto di una pagina e funziona insieme a un file code-behind visual.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
ms.openlocfilehash: a35a3750957073168bca4903e1cef79ed53a3de9
ms.sourcegitcommit: bb6a103de620386187a08151a4cdc29a4dc53a10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679968"
---
# <a name="part-1-getting-started-with-xaml"></a>Parte 1. Introduzione a XAML

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_In un'applicazione Xamarin.Forms XAML viene usato principalmente per definire il contenuto di una pagina visual e interagisce con un C# file code-behind._

Il file code-behind fornisce supporto di codice per il markup. Insieme, questi due file contribuiscono a una nuova definizione di classe che include visualizzazioni figlio e l'inizializzazione della proprietà. All'interno del file XAML, classi e proprietà viene fatto riferimento con elementi e attributi XML e vengono stabiliti i collegamenti tra il markup e codice.

## <a name="creating-the-solution"></a>Creazione della soluzione

Per iniziare la modifica del primo file XAML, usare Visual Studio o Visual Studio per Mac per creare una nuova soluzione Xamarin.Forms. (Selezionare la scheda di seguito corrispondenti all'ambiente).

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In Windows avviare Visual Studio 2019 e nella finestra di avvio fare clic su **Crea un nuovo progetto** per creare un nuovo progetto:

![Nuova finestra della soluzione](get-started-with-xaml-images/win/new-solution-2019.png)

Nella finestra **Crea un nuovo progetto** selezionare **mobile** nell'elenco a discesa **tipo di progetto** , selezionare il modello **app per dispositivi mobili (Xamarin.Forms)** e fare clic sul pulsante **Avanti** :

![Finestra nuovo progetto](get-started-with-xaml-images/win/new-project-2019.png)

Nella finestra **Configura nuovo progetto** impostare il **nome del progetto** su **XamlSamples** (o qualsiasi preferenza) e fare clic sul pulsante **Crea** .

Nella finestra di dialogo **nuova app multipiattaforma** fare clic su **blank**, quindi fare clic sul pulsante **OK** :

![Finestra di dialogo nuova app](get-started-with-xaml-images/win/new-cross-platform-app.png)

Nella soluzione vengono creati quattro progetti: il **XamlSamples** libreria .NET Standard, **XamlSamples.Android**, **XamlSamples.iOS**e la piattaforma Windows universale soluzione **XamlSamples.UWP**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac, selezionare **File > nuova soluzione** dal menu di scelta. Nel **nuovo progetto** finestra di dialogo, seleziona **multipiattaforma > App** a sinistra, e **App Forms vuota** (*non* **App Forms** ) dall'elenco di modelli:

![Finestra di dialogo nuovo progetto 1](get-started-with-xaml-images/mac/newprojectdialog1.png)

Premere **successivo**.

Nella finestra di dialogo successiva, assegnare al progetto un nome di **XamlSamples** (o secondo le proprie preferenze). Assicurarsi che il **uso di .NET Standard** pulsante di opzione selezionato:

![Finestra di dialogo nuovo progetto 2](get-started-with-xaml-images/mac/newprojectdialog2.png)

Premere **successivo**.

Nella finestra di dialogo seguente, è possibile selezionare un percorso per il progetto:

![Finestra di dialogo nuovo progetto 3](get-started-with-xaml-images/mac/newprojectdialog3.png)

Premere **creare**

Vengono creati tre progetti nella soluzione: il **XamlSamples** libreria .NET Standard **XamlSamples.Android**, e **XamlSamples.iOS**.

-----

Dopo aver creato il **XamlSamples** soluzione, è possibile testare l'ambiente di sviluppo selezionando i vari progetti di piattaforma come progetto di avvio della soluzione e la compilazione e distribuzione dell'applicazione semplice creato da il modello di progetto in emulatori phone o dispositivi reali.

A meno che non devi scrivere codice specifico della piattaforma, condiviso **XamlSamples** progetto di libreria .NET Standard è in cui si prevede di passare praticamente tutto il tempo di programmazione. Questi articoli non verranno avventurarsi di fuori di tale progetto.

### <a name="anatomy-of-a-xaml-file"></a>Anatomia di un File XAML

All'interno di **XamlSamples** libreria .NET Standard è una coppia di file con i nomi seguenti:

- **App**, il file XAML; e
- **App.Xaml.cs**, un C# *code-behind* file associato al file XAML.

È necessario fare clic sulla freccia accanto a **app** per visualizzare il file code-behind.

Entrambe **app. XAML** e **App.xaml.cs** contribuiscono a una classe denominata `App` che deriva da `Application`. La maggior parte delle altre classi con i file XAML contribuiscono a una classe che deriva da `ContentPage`; tali file usano XAML per definire il contenuto di un'intera pagina visual. Questo vale per gli altri due file nei **XamlSamples** progetto:

- **MainPage. XAML**, il file XAML; e
- **MainPage.xaml.cs**, il C# file code-behind.

Il **MainPage. XAML** file avrà un aspetto simile al seguente (anche se la formattazione potrebbero essere leggermente diversa):

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

Le due dichiarazioni dello spazio`xmlns`dei nomi XML () fanno riferimento a URI, il primo sito Web di Novell e il secondo su Microsoft. Non ci preoccupiamo verifica quali tali scegliere gli URI. Non è presente alcun elemento. Sono semplicemente gli URI di proprietà da Xamarin e Microsoft, e in sostanza funzionano come identificatori di versione.

La prima dichiarazione dello spazio dei nomi XML significa che i tag definiti all'interno del file XAML senza il prefisso si riferisce alle classi in Xamarin.Forms, ad esempio `ContentPage`. La seconda dichiarazione dello spazio dei nomi definisce un prefisso di `x`. Questa opzione viene utilizzata per diversi elementi e attributi intrinseci XAML stesso e che sono supportati da altre implementazioni di XAML. Tuttavia, questi elementi e attributi sono leggermente diversi a seconda dell'anno incorporato nell'URI. Xamarin.Forms supporta la specifica di XAML 2009, ma non tutte.

Il `local` dichiarazione dello spazio dei nomi consente di accedere alle altre classi dal progetto della libreria .NET Standard.

Alla fine del primo tag, il `x` prefisso usato per un attributo denominato `Class`. Poiché l'uso di questo `x` prefisso è praticamente universale per lo spazio dei nomi XAML, gli attributi XAML, ad esempio `Class` sono quasi sempre detti `x:Class`.

Il `x:Class` attributo specifica un nome di classe .NET completo: il `MainPage` classe la `XamlSamples` dello spazio dei nomi. Ciò significa che questo file XAML definisce una nuova classe denominata `MainPage` nella `XamlSamples` dello spazio dei nomi che deriva da `ContentPage`, ovvero il tag in cui il `x:Class` attributo viene visualizzato.

Il `x:Class` attributo può essere visualizzato solo nell'elemento radice di un file XAML per definire un oggetto derivato C# classe. Si tratta della classe solo nuovi definita nel file XAML. Tutti gli elementi che viene visualizzato nel file XAML viene invece è sufficiente creare un'istanza da classi esistenti e inizializzato.

Il **MainPage.xaml.cs** file avrà un aspetto simile al seguente (a parte inutilizzata `using` direttive):

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

Il `MainPage` deriva dalla classe `ContentPage`, ma si noti il `partial` definizione di classe. Ne consegue che non vi sarà un'altra definizione di classe parziale per `MainPage`, ma in cui è? E che cosa si tratta `InitializeComponent` metodo?

Quando Visual Studio compila il progetto, analizza il file XAML per generare un C# file di codice. Se si osserva il **XamlSamples\XamlSamples\obj\Debug** directory, è possibile trovare un file denominato **XamlSamples.MainPage.xaml.g.cs**. 'g' è l'acronimo per generati. Questo è l'altra definizione di classe parziale `MainPage` che contiene la definizione del `InitializeComponent` chiamato dal metodo di `MainPage` costruttore. Questi due parziale `MainPage` le definizioni di classe possono quindi essere compilate contemporaneamente. A seconda del fatto che il XAML viene compilata o non, il file XAML o un formato binario del file XAML incorporato nel file eseguibile.

In fase di esecuzione di codice nelle chiamate progetto particolare piattaforma in un `LoadApplication` , passando a esso una nuova istanza del `App` classe nella libreria .NET Standard. Il `App` crea un'istanza di costruttore di classe `MainPage`. Chiama il costruttore della classe `InitializeComponent`, che a sua volta chiama il `LoadFromXaml` metodo che consente di estrarre il file XAML (o il file binario compilato) dalla libreria .NET Standard. `LoadFromXaml` Inizializza tutti gli oggetti definiti nel file XAML, li connette tutti gli elementi in relazioni padre-figlio, gestori di eventi definiti nel codice per gli eventi impostato nel file XAML e imposta l'albero degli oggetti risultante come contenuto della pagina.

Sebbene in genere non occorre dedicare molto tempo con file di codice generati, in alcuni casi runtime vengono generate eccezioni nel codice nel file generati, è necessario acquisire familiarità con le.

Quando si compila e si esegue questo programma, il `Label` elemento viene visualizzato al centro della pagina, come suggerisce il XAML:

[![Visualizzazione predefinita di Xamarin.Forms](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

Per gli oggetti visivi più interessanti, tutto quello che serve è più interessanti di XAML.

## <a name="adding-new-xaml-pages"></a>Aggiunta di nuove pagine XAML

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per aggiungere altre classi `ContentPage` basate su XAML al progetto, selezionare il progetto libreria .NET Standard **XamlSamples** , fare clic con il pulsante destro del mouse e scegliere **Aggiungi > nuovo elemento**. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **elementi C# visivi > Xamarin.Forms > pagina contenuto** (non **paginaC#contenuto ()** , che crea una pagina di solo codice o una **visualizzazione contenuto**, che non è una pagina. Assegnare, ad esempio, un nome, la pagina **HelloXamlPage**:

![Finestra di dialogo Aggiungi nuovo elemento](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per aggiungere altre XAML basato `ContentPage` classi al progetto, selezionare il **XamlSamples** libreria .NET Standard di progetto e richiamare il **File > Nuovo File** voce di menu. A sinistra del **nuovo File** finestra di dialogo, seleziona **Forms** a sinistra, e **Forms ContentPage Xaml** (non **elemento Forms ContentPage**, ovvero Crea una pagina di solo codice, o **visualizzazione contenuto**, che non si tratta di una pagina). Assegnare, ad esempio, un nome, la pagina **HelloXamlPage**:

![Finestra di dialogo nuovo file](get-started-with-xaml-images/mac/newfiledialog.png)

-----

Due file vengono aggiunti al progetto **HelloXamlPage.xaml** e il file code-behind **HelloXamlPage.xaml.cs**.

## <a name="setting-page-content"></a>Contenuto della pagina impostazione

Modificare il **HelloXamlPage.xaml** del file in modo che i tag unici sono quelli relativi `ContentPage` e `ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

Il `ContentPage.Content` tag fanno parte della sintassi di XAML univoca. Inizialmente, sembrano potrebbe essere XML non valido, ma lo sono. Il periodo non è un carattere speciale nel codice XML.

Il `ContentPage.Content` tag vengono chiamati *property (elemento)* tag. `Content` è una proprietà di `ContentPage`e in genere è impostato su una singola visualizzazione o un layout con visualizzazioni figlio. In genere proprietà diventano attributi in XAML, ma sarebbe difficile impostare un `Content` attributo a un oggetto complesso. Per questo motivo, la proprietà viene espressa come elemento XML multicontenitore costituita dal nome della classe e il nome di proprietà separati da un punto. A questo punto il `Content` proprietà può essere compreso tra il `ContentPage.Content` tag, simile al seguente:

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

Si noti inoltre che un `Title` attributo è stato impostato nel tag radice.

A questo punto, la relazione tra le classi, le proprietà e il codice XML dovrebbe essere evidente: Una classe Xamarin.Forms, ad `ContentPage` esempio `Label`o, viene visualizzata nel file XAML come elemento XML. Le proprietà di tale classe, inclusi `Title` sul `ContentPage` e sette proprietà di `Label`, ovvero in genere vengono visualizzati come attributi XML.

Molti tasti di scelta rapida disponibili per impostare i valori di queste proprietà. Alcune proprietà sono tipi di dati di base: Ad esempio, le `Title` proprietà `Text` e sono di `String`tipo, `Rotation` è di tipo `Double`e `IsVisible` (che è `true` per impostazione predefinita ed è impostato qui solo per illustrazione) è di tipo `Boolean`.

Il `HorizontalTextAlignment` proprietà è di tipo `TextAlignment`, che costituisce un'enumerazione. Per una proprietà di qualsiasi tipo di enumerazione, è sufficiente specificare è un nome di membro.

Per le proprietà dei tipi più complessi, tuttavia, convertitori di tipi vengono usate per l'analisi di XAML. Si tratta di classi in Xamarin.Forms che derivano da `TypeConverter`. Molti sono classi pubbliche, ma non lo sono. Per questo file XAML, alcune di queste classi svolgono un ruolo dietro le quinte:

- `LayoutOptionsConverter` per il `VerticalOptions` proprietà
- `FontSizeConverter` per il `FontSize` proprietà
- `ColorTypeConverter` per il `TextColor` proprietà

I convertitori di tipi controllano la sintassi delle impostazioni delle proprietà consentita.

Il `ThicknessTypeConverter` può gestire uno, due o quattro numeri separati da virgole. Se viene fornito un numero, si applica a tutti e quattro i lati. Con due numeri, il primo è left e right spaziatura interna e il secondo è superiore e inferiore. Quattro numeri sono in ordine da sinistra, superiore, destro e inferiore.

Il `LayoutOptionsConverter` possibile convertire i nomi dei campi statici pubblici del `LayoutOptions` struttura per i valori di tipo `LayoutOptions`.

Il `FontSizeConverter` può gestire un `NamedSize` membro o una dimensione del carattere numerico.

Il `ColorTypeConverter` accetta i nomi dei campi statici pubblici del `Color` struttura o valori RGB esadecimali, con o senza un canale alfa, preceduto da un simbolo di cancelletto (#). Di seguito è riportata la sintassi senza canale alfa:

 `TextColor="#rrggbb"`

Ognuna delle lettere poco è una cifra esadecimale. Ecco come viene incluso un canale alfa:

 `TextColor="#aarrggbb">`

Per il canale alfa, tenere presente che è completamente opaco FF e 00 è completamente trasparente.

Due altri formati consentono di specificare solo una singola cifra esadecimale per ogni canale:

 `TextColor="#rgb"` `TextColor="#argb"`

In questi casi, la cifra viene ripetuta in modo da formare il valore. Ad esempio, & CF3 è il colore RGB CC-FF-33.

## <a name="page-navigation"></a>Navigazione tra le pagine

Quando si esegue la **XamlSamples** programma, il `MainPage` viene visualizzato. Per vedere le nuove `HelloXamlPage` è possibile impostare che come l'avvio di nuovo nella pagina la **App.xaml.cs** file oppure passare alla nuova pagina da `MainPage`.

Per implementare l'esplorazione, modificare prima di tutto il codice nel **App.xaml.cs** costruttore in modo che un `NavigationPage` oggetto viene creato:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

Nel **MainPage.xaml.cs** costruttore, è possibile creare una semplice `Button` e utilizzare il gestore eventi per passare a `HelloXamlPage`:

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

Impostando il `Content` proprietà della pagina sostituisce l'impostazione del `Content` proprietà nel file XAML. Quando si compila e distribuisce la nuova versione di questo programma, viene visualizzato un pulsante sullo schermo. Ci si passa a `HelloXamlPage`. Ecco la pagina risultante su iPhone, Android e UWP:

[![Testo etichetta ruotato](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

È possibile tornare `MainPage` usando il **< nuovamente** pulsante su iOS, con la freccia a sinistra nella parte superiore della pagina o nella parte inferiore del telefono su Android o con la freccia a sinistra nella parte superiore della pagina in Windows 10.

È possibile sperimentare con il XAML per diversi modi per eseguire il rendering di `Label`. Se è necessario per incorporare caratteri Unicode in testo, è possibile usare la sintassi XML standard. Ad esempio, per inserire il messaggio di saluto in virgolette inglesi, usare:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Ecco l'aspetto:

[![Testo etichetta ruotato con caratteri Unicode](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>Le interazioni tra codice e XAML

Il **HelloXamlPage** esempio contiene una sola `Label` nella pagina, ma è molto insolita. La maggior parte degli `ContentPage` derivati set il `Content` ordinare proprietà a un layout di alcuni, ad esempio un `StackLayout`. Il `Children` proprietà del `StackLayout` viene definito come di tipo `IList<View>` ma è in realtà un oggetto di tipo `ElementCollection<View>`, e che l'insieme può essere popolato con più viste o altri layout. In XAML, queste relazioni padre-figlio vengono stabilite con gerarchia XML normale. Ecco un file XAML per una nuova pagina denominata **XamlPlusCodePage**:

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

Questo file XAML è sintatticamente completato ed ecco l'aspetto:

[![Più controlli in una pagina](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

Tuttavia, probabile si consideri questo programma per essere funzionalmente inadeguati. Forse il `Slider` dovrebbe per causare le `Label` per visualizzare il valore corrente e il `Button` probabilmente consente di eseguire un'operazione all'interno del programma.

Come si vedrà [parte 4. Nozioni fondamentali sull'associazione di dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), il processo di visualizzazione di un `Slider` valore utilizzando un `Label` può essere gestita interamente in XAML con un'associazione dati. Ma è utile visualizzare la soluzione di codice prima di tutto. Anche in questo caso, la gestione di `Button` fare clic su codice è senza dubbio necessaria. Ciò significa che il file code-behind per `XamlPlusCodePage` deve contenere i gestori per il `ValueChanged` eventi delle `Slider` e il `Clicked` eventi del `Button`. È possibile aggiungerli:

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

Questi gestori eventi non sono necessario essere pubblici.

Indietro nel file XAML, il `Slider` e `Button` tag è necessario includere gli attributi per il `ValueChanged` e `Clicked` che fanno riferimento a questi gestori eventi:

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

Si noti che l'assegnazione di un gestore a un evento ha la stessa sintassi di assegnazione di un valore a una proprietà.

Se il gestore per il `ValueChanged` eventi del `Slider` utilizzerà il `Label` per visualizzare il valore corrente, il gestore deve fare riferimento a tale oggetto dal codice. Il `Label` richiede un nome, che viene specificato con il `x:Name` attributo.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

Il `x` prefisso di `x:Name` attributo indica che questo attributo è intrinseco XAML.

Il nome assegnato per il `x:Name` attributo ha le stesse regole C# i nomi delle variabili. Ad esempio, deve iniziare con una lettera o un carattere di sottolineatura e non contenere spazi incorporati.

A questo punto il `ValueChanged` gestore eventi può impostare il `Label` per visualizzare il nuovo `Slider` valore. Il nuovo valore è disponibile dagli argomenti dell'evento:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

In alternativa, il gestore di è stato possibile ottenere il `Slider` oggetto che genera l'evento dal `sender` argomento e ottenere il `Value` proprietà rispetto a quello:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Alla prima esecuzione del programma, il `Label` non viene visualizzata la `Slider` valore perché il `ValueChanged` ancora non è stato generato l'evento. Ma qualsiasi modifica del `Slider` fa sì che il valore da visualizzare:

[![Valore dispositivo di scorrimento visualizzato](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

Ora per il `Button`. È possibile simulare una risposta a un `Clicked` evento visualizzando un avviso con il `Text` del pulsante. Il gestore eventi può eseguire il cast sicuro il `sender` argomento per un `Button` e quindi accedere alle relative proprietà:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

Il metodo viene definito come `async` perché il `DisplayAlert` metodo è asincrono e deve far precedere il `await` operatore, che restituisce al completamento del metodo. Poiché questo metodo consente di ottenere il `Button` che genera l'evento dal `sender` argomento, lo stesso gestore può essere usato per più pulsanti.

Si è visto che un oggetto definito in XAML può generare un evento che viene gestito nel file code-behind e che il file code-behind può accedere a un oggetto definito in XAML usando il nome assegnato a esso con il `x:Name` attributo. Si tratta di due modi fondamentali che l'interazione di codice e XAML.

Alcune informazioni aggiuntive nel modo in cui è possibile ricavare XAML works esaminando appena generato **file XamlPlusCode.xaml.g.cs**, che ora include un nome assegnato a qualsiasi `x:Name` attributo come un campo privato. Di seguito è una versione semplificata di tale file:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

La dichiarazione di questo campo permette alla variabile da usare liberamente in qualsiasi punto all'interno di `XamlPlusCodePage` file di classe parziale nella giurisdizione dell'utente. In fase di esecuzione è assegnato il campo dopo che il XAML è stato analizzato. Ciò significa che il `valueLabel` campo viene `null` quando il `XamlPlusCodePage` costruttore inizia ma validi dopo `InitializeComponent` viene chiamato.

Dopo aver `InitializeComponent` restituisce il controllo torna al costruttore, gli oggetti visivi della pagina siano stati creati come se fosse stata creata un'istanza e inizializzate nel codice. Il file XAML non è più svolge alcun ruolo nella classe. È possibile modificare questi oggetti nella pagina in qualsiasi modo desiderato, ad esempio, aggiungendo le visualizzazioni per il `StackLayout`, impostazione o il `Content` proprietà della pagina per qualcos'altro interamente. È possibile "percorrere la struttura ad albero" esaminando le `Content` proprietà della pagina e gli elementi nel `Children` raccolte di layout. È possibile impostare le proprietà delle viste accessibile in questo modo, o assegnare in modo dinamico i gestori eventi a essi.

È possibile. È la pagina e XAML è solo uno strumento per compilare il relativo contenuto.

## <a name="summary"></a>Riepilogo

Con questa introduzione, si è visto come un file XAML e un file di codice contribuiscono a una definizione di classe e come interagiscono i file XAML e codice. Ma XAML ha anche univoca sintattiche diverse funzionalità che consentono di poter essere utilizzato in modo molto flessibile. È possibile iniziare a esplorare questi elementi nel [parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
