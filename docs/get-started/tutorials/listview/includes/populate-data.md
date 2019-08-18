---
ms.openlocfilehash: e03d0ada982cbf1d2954f4b677accc7ce7da793e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541486"
---
Un elemento [`ListView`](xref:Xamarin.Forms.ListView) viene popolato con dati mediante la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) che è di tipo `IEnumerable`. Il passaggio precedente ha popolato l'elemento `ListView` in XAML con una matrice di stringhe. Tuttavia, in genere un elemento `ListView` verrà popolato con dati di una raccolta, definita nel code-behind, che implementa `IEnumerable`.

In questo esercizio si modificherà il progetto **ListViewTutorial** per popolare l'elemento [`ListView`](xref:Xamarin.Forms.ListView) con i dati di una raccolta di oggetti archiviati in un elemento `List`.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **Esplora soluzioni**, nel progetto **ListViewTutorial**, aggiungere una classe denominata `Monkey` che contiene il codice seguente:

    ```csharp
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
        public string ImageUrl { get; set; }

        public override string ToString()
        {
            return Name;
        }
    }
    ```

    Questo codice definisce un oggetto `Monkey` che archivia un nome, una posizione e un URL di un'immagine che rappresenta la scimmia. Inoltre la classe esegue l'override del metodo `ToString` per restituire la proprietà `Name`.

1. In **Esplora soluzioni**, nel progetto **ListViewTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace ListViewTutorial
    {
        public partial class MainPage : ContentPage
        {
            public IList<Monkey> Monkeys { get; private set; }

            public MainPage()
            {
                InitializeComponent();

                Monkeys = new List<Monkey>();
                Monkeys.Add(new Monkey
                {
                    Name = "Baboon",
                    Location = "Africa & Asia",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Capuchin Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Blue Monkey",
                    Location = "Central and East Africa",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Squirrel Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Saimiri_sciureus-1_Luc_Viatour.jpg/220px-Saimiri_sciureus-1_Luc_Viatour.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Lion Tamarin",
                    Location = "Brazil",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Golden_lion_tamarin_portrait3.jpg/220px-Golden_lion_tamarin_portrait3.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Howler Monkey",
                    Location = "South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/Alouatta_guariba.jpg/200px-Alouatta_guariba.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Japanese Macaque",
                    Location = "Japan",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Macaca_fuscata_fuscata1.jpg/220px-Macaca_fuscata_fuscata1.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Mandrill",
                    Location = "Southern Cameroon, Gabon, Equatorial Guinea, and Congo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Mandrill_at_san_francisco_zoo.jpg/220px-Mandrill_at_san_francisco_zoo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Proboscis Monkey",
                    Location = "Borneo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Proboscis_Monkey_in_Borneo.jpg/250px-Proboscis_Monkey_in_Borneo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Red-shanked Douc",
                    Location = "Vietnam, Laos",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Portrait_of_a_Douc.jpg/159px-Portrait_of_a_Douc.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gray-shanked Douc",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cuc.Phuong.Primate.Rehab.center.jpg/320px-Cuc.Phuong.Primate.Rehab.center.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg/165px-Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Black Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/RhinopitecusBieti.jpg/320px-RhinopitecusBieti.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Tonkin Snub-nosed Monkey",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg/320px-Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Thomas's Langur",
                    Location = "Indonesia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Thomas%27s_langur_Presbytis_thomasi.jpg/142px-Thomas%27s_langur_Presbytis_thomasi.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Purple-faced Langur",
                    Location = "Sri Lanka",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Semnopithèque_blanchâtre_mâle.JPG/192px-Semnopithèque_blanchâtre_mâle.JPG"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gelada",
                    Location = "Ethiopia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Gelada-Pavian.jpg/320px-Gelada-Pavian.jpg"
                });

                BindingContext = this;
            }
        }
    }
    ```

    Questo codice definisce una proprietà `Monkeys` di tipo `IList<Monkey>` e la inizializza come elenco vuoto nel costruttore della classe. Gli oggetti `Monkey` vengono quindi aggiunti alla raccolta `Monkeys` e l'elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostato sull'oggetto `MainPage`. Per altre informazioni sulla proprietà `BindingContext`, vedere [Binding con un contesto di binding](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) nella guida [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) viene ereditata tramite la struttura ad albero visuale. Pertanto, dato che è stata impostata sull'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage), il valore viene ereditato dagli oggetti figlio di `ContentPage`, incluso l'elemento [`ListView`](xref:Xamarin.Forms.ListView).

1. In **MainPage.xaml** modificare la dichiarazione [`ListView`](xref:Xamarin.Forms.Image) per impostare la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) sulla raccolta `Monkeys`:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}" />
    ```

    Questo codice esegue l'associazione dei dati della proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) alla raccolta `Monkeys`. Al runtime l'elemento [`ListView`](xref:Xamarin.Forms.ListView) esaminerà il relativo elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) per la raccolta `Monkeys` e sarà popolato con i dati di questa raccolta. Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un elemento ListView popolato con dati di una raccolta in iOS e Android](../images/populate-data.png "Elemento ListView che mostra dati di una raccolta")](../images/populate-data-large.png#lightbox "Elemento ListView che mostra dati di una raccolta")

    L'elemento [`ListView`](xref:Xamarin.Forms.ListView) mostra la proprietà `Name` per ogni `Monkey` della raccolta `Monkeys`. Questo avviene perché, per impostazione predefinita, l'elemento `ListView` chiama il metodo `ToString` quando mostra gli oggetti di una raccolta, di cui è stato eseguito l'override nella classe `Monkey` per restituire il valore della proprietà `Name`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **riquadro della soluzione**, nel progetto **ListViewTutorial**, aggiungere una classe denominata `Monkey` che contiene il codice seguente:

    ```csharp
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
        public string ImageUrl { get; set; }

        public override string ToString()
        {
            return Name;
        }
    }
    ```

    Questo codice definisce un oggetto `Monkey` che archivia un nome, una posizione e un URL di un'immagine che rappresenta la scimmia. Inoltre la classe esegue l'override del metodo `ToString` per restituire la proprietà `Name`.

1. Nel **riquadro della soluzione**, nel progetto **ListViewTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace ListViewTutorial
    {
        public partial class MainPage : ContentPage
        {
            public IList<Monkey> Monkeys { get; private set; }

            public MainPage()
            {
                InitializeComponent();

                Monkeys = new List<Monkey>();
                Monkeys.Add(new Monkey
                {
                    Name = "Baboon",
                    Location = "Africa & Asia",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Capuchin Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Blue Monkey",
                    Location = "Central and East Africa",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Squirrel Monkey",
                    Location = "Central & South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Saimiri_sciureus-1_Luc_Viatour.jpg/220px-Saimiri_sciureus-1_Luc_Viatour.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Lion Tamarin",
                    Location = "Brazil",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Golden_lion_tamarin_portrait3.jpg/220px-Golden_lion_tamarin_portrait3.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Howler Monkey",
                    Location = "South America",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/Alouatta_guariba.jpg/200px-Alouatta_guariba.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Japanese Macaque",
                    Location = "Japan",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Macaca_fuscata_fuscata1.jpg/220px-Macaca_fuscata_fuscata1.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Mandrill",
                    Location = "Southern Cameroon, Gabon, Equatorial Guinea, and Congo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Mandrill_at_san_francisco_zoo.jpg/220px-Mandrill_at_san_francisco_zoo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Proboscis Monkey",
                    Location = "Borneo",
                    ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Proboscis_Monkey_in_Borneo.jpg/250px-Proboscis_Monkey_in_Borneo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Red-shanked Douc",
                    Location = "Vietnam, Laos",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Portrait_of_a_Douc.jpg/159px-Portrait_of_a_Douc.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gray-shanked Douc",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cuc.Phuong.Primate.Rehab.center.jpg/320px-Cuc.Phuong.Primate.Rehab.center.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg/165px-Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Black Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/RhinopitecusBieti.jpg/320px-RhinopitecusBieti.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Tonkin Snub-nosed Monkey",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg/320px-Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Thomas's Langur",
                    Location = "Indonesia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Thomas%27s_langur_Presbytis_thomasi.jpg/142px-Thomas%27s_langur_Presbytis_thomasi.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Purple-faced Langur",
                    Location = "Sri Lanka",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Semnopithèque_blanchâtre_mâle.JPG/192px-Semnopithèque_blanchâtre_mâle.JPG"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gelada",
                    Location = "Ethiopia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Gelada-Pavian.jpg/320px-Gelada-Pavian.jpg"
                });

                BindingContext = this;
            }
        }
    }
    ```

    Questo codice definisce una proprietà `Monkeys` di tipo `IList<Monkey>` e la inizializza come elenco vuoto nel costruttore della classe. Gli oggetti `Monkey` vengono quindi aggiunti alla raccolta `Monkeys` e l'elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostato sull'oggetto `MainPage`. Per altre informazioni sulla proprietà `BindingContext`, vedere [Binding con un contesto di binding](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) nella guida [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) viene ereditata tramite la struttura ad albero visuale. Pertanto, dato che è stata impostata sull'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage), il valore viene ereditato dagli oggetti figlio di `ContentPage`, incluso l'elemento [`ListView`](xref:Xamarin.Forms.ListView).

1. In **MainPage.xaml** modificare la dichiarazione [`ListView`](xref:Xamarin.Forms.Image) per impostare la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) sulla raccolta `Monkeys`:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}" />
    ```

    Questo codice esegue l'associazione dei dati della proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) alla raccolta `Monkeys`. Al runtime l'elemento [`ListView`](xref:Xamarin.Forms.ListView) esaminerà il relativo elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) per la raccolta `Monkeys` e sarà popolato con i dati di questa raccolta. Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un elemento ListView popolato con dati di una raccolta in iOS e Android](../images/populate-data.png "Elemento ListView che mostra dati di una raccolta")](../images/populate-data-large.png#lightbox "Elemento ListView che mostra dati di una raccolta")

    L'elemento [`ListView`](xref:Xamarin.Forms.ListView) mostra la proprietà `Name` per ogni `Monkey` della raccolta `Monkeys`. Questo avviene perché, per impostazione predefinita, l'elemento `ListView` chiama il metodo `ToString` quando mostra gli oggetti di una raccolta, di cui è stato eseguito l'override nella classe `Monkey` per restituire il valore della proprietà `Name`.
