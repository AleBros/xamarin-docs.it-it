---
ms.openlocfilehash: 16ceaba572ca932777bb366d9f7c58f6dcb24f70
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841457"
---
La sottoclasse [`Application`](xref:Xamarin.Forms.Application) ha un dizionario statico [`Properties`](xref:Xamarin.Forms.Application.Properties) che può essere usato per archiviare dati fra le modifiche di stato del ciclo di vita. Il dizionario usa una chiave `string` e archivia un valore `object`. Il dizionario viene salvato automaticamente sul dispositivo e non è ripopolato quando l'applicazione viene riavviata.

> [!IMPORTANT]
> Il dizionario [`Properties`](xref:Xamarin.Forms.Application.Properties) può serializzare solo i tipi primitivi per l'archiviazione.

In questo esercizio si modificherà l'applicazione in modo da rendere permanente il testo da un elemento [`Entry`](xref:Xamarin.Forms.Entry) durante l'elaborazione in background e ripristinare il testo nell'elemento `Entry` quando l'applicazione viene riavviata.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **Esplora soluzioni**, nel progetto **AppLifecycleTutorial**, espandere **App.xaml** e fare doppio clic su **App.xaml.cs** per aprirlo. In **App.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Questo codice definisce una proprietà `DisplayText` e una costante `displayText`. Quando l'applicazione viene messa in background o terminata, l'override del metodo `OnSleep` aggiunge il valore della proprietà `DisplayText` al dizionario [`Properties`](xref:Xamarin.Forms.Application.Properties), in base alla chiave `displayText`. Quindi, all'avvio dell'applicazione, a condizione che il dizionario `Properties` contenga la chiave `displayText`, il valore della chiave viene ripristinato alla proprietà `DisplayText`.

    > [!IMPORTANT]
    > Verificare sempre la presenza della chiave nel dizionario [`Properties`](xref:Xamarin.Forms.Application.Properties) prima di accedervi, in modo da evitare errori imprevisti.

    Non è necessario ripristinare i dati dal dizionario [`Properties`](xref:Xamarin.Forms.Application.Properties) nell'overload del metodo `OnResume`. Questo perché, quando un'applicazione è messa in background, l'applicazione stessa e il suo stato sono ancora in memoria.

1. In **Esplora soluzioni**, nel progetto **AppLifecycleTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Entry`](xref:Xamarin.Forms.Entry) in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) specifica il testo segnaposto mostrato alla prima visualizzazione dell'elemento `Entry` e un gestore eventi denominato `OnEntryCompleted` viene registrato con l'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed). Inoltre, per `Entry` viene specificato un nome con l'attributo `x:Name`. Ciò consente al file code-behind di accedere all'oggetto `Entry` usando il nome assegnato a esso.

1. In **Esplora soluzioni**, nel progetto **AppLifecycleTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere un override per il metodo `OnAppearing` e il gestore eventi `OnEntryCompleted` alla classe:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    Il metodo `OnAppearing` recupera il valore della proprietà `App.DisplayText` e lo imposta come valore della proprietà [`Text`](xref:Xamarin.Forms.Entry.Text) dell'elemento [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > L'override del metodo `OnAppearing` viene eseguito dopo che è stato eseguito il layout dell'elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage), ma subito prima che diventi visibile. Pertanto, questo è un buon momento per impostare il contenuto delle viste Xamarin.Forms.

    Quando il testo viene completato nell'elemento [`Entry`](xref:Xamarin.Forms.Entry), con il tasto INVIO, viene eseguito il metodo `OnEntryCompleted` e il testo `Entry` è archiviato nella proprietà `App.DisplayText`.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto.

    Immettere testo nell'elemento [`Entry`](xref:Xamarin.Forms.Entry) e premere il tasto INVIO. Quindi mettere l'applicazione in background toccando il pulsante Home per chiamare il metodo `OnSleep`.

    Infine avviare nuovamente l'applicazione da Visual Studio e il testo immesso in precedenza nell'elemento [`Entry`](xref:Xamarin.Forms.Entry) verrà ripristinato:

    [![Screenshot di un elemento Entry la cui proprietà Text è resa permanente tra le modifiche di stato del ciclo di vita, in iOS e Android](../images/persist-data.png "Elemento Entry la cui proprietà Text è resa permanente fra le modifiche di stato del ciclo di vita")](../images/persist-data-large.png#lightbox "Elemento Entry la cui proprietà Text è resa permanente fra le modifiche di stato del ciclo di vita")

    Per altre informazioni su come rendere permanenti i dati nel dizionario delle proprietà, vedere [Dizionario delle proprietà](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) nella guida [Classe App di Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **riquadro della soluzione**, nel progetto **AppLifecycleTutorial**, espandere **App.xaml** e fare doppio clic su **App.xaml.cs** per aprirlo. In **App.xaml.cs** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Questo codice definisce una proprietà `DisplayText` e una costante `displayText`. Quando l'applicazione viene messa in background o terminata, l'override del metodo `OnSleep` aggiunge il valore della proprietà `DisplayText` al dizionario [`Properties`](xref:Xamarin.Forms.Application.Properties), in base alla chiave `displayText`. Quindi, all'avvio dell'applicazione, a condizione che il dizionario `Properties` contenga la chiave `displayText`, il valore della chiave viene ripristinato alla proprietà `DisplayText`.

    > [!IMPORTANT]
    > Verificare sempre la presenza della chiave nel dizionario [`Properties`](xref:Xamarin.Forms.Application.Properties) prima di accedervi, in modo da evitare errori imprevisti.

    Non è necessario ripristinare i dati dal dizionario [`Properties`](xref:Xamarin.Forms.Application.Properties) nell'overload del metodo `OnResume`. Questo perché, quando un'applicazione è messa in background, l'applicazione stessa e il suo stato sono ancora in memoria.

1. Nel **riquadro della soluzione**, nel progetto **AppLifecycleTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Entry`](xref:Xamarin.Forms.Entry) in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) specifica il testo segnaposto mostrato alla prima visualizzazione dell'elemento `Entry` e un gestore eventi denominato `OnEntryCompleted` viene registrato con l'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed). Inoltre, per `Entry` viene specificato un nome con l'attributo `x:Name`. Ciò consente al file code-behind di accedere all'oggetto `Entry` usando il nome assegnato a esso.

1. Nel **riquadro della soluzione**, nel progetto **AppLifecycleTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere un override per il metodo `OnAppearing` e il gestore eventi `OnEntryCompleted` alla classe:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    Il metodo `OnAppearing` recupera il valore della proprietà `App.DisplayText` e lo imposta come valore della proprietà [`Text`](xref:Xamarin.Forms.Entry.Text) dell'elemento [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > L'override del metodo `OnAppearing` viene eseguito dopo che è stato eseguito il layout dell'elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage), ma subito prima che diventi visibile. Pertanto, questo è un buon momento per impostare il contenuto delle viste Xamarin.Forms.

    Quando il testo viene completato nell'elemento [`Entry`](xref:Xamarin.Forms.Entry), con il tasto INVIO, viene eseguito il metodo `OnEntryCompleted` e il testo `Entry` è archiviato nella proprietà `App.DisplayText`.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto.

    Immettere testo nell'elemento [`Entry`](xref:Xamarin.Forms.Entry) e premere il tasto INVIO. Quindi mettere l'applicazione in background toccando il pulsante Home per chiamare il metodo `OnSleep`.

    Infine avviare nuovamente l'applicazione da Visual Studio per Mac e il testo immesso in precedenza nell'elemento [`Entry`](xref:Xamarin.Forms.Entry) verrà ripristinato:

    [![Screenshot di un elemento Entry la cui proprietà Text è resa permanente tra le modifiche di stato del ciclo di vita, in iOS e Android](../images/persist-data.png "Elemento Entry la cui proprietà Text è resa permanente fra le modifiche di stato del ciclo di vita")](../images/persist-data-large.png#lightbox "Elemento Entry la cui proprietà Text è resa permanente fra le modifiche di stato del ciclo di vita")

    Per altre informazioni su come rendere permanenti i dati nel dizionario delle proprietà, vedere [Dizionario delle proprietà](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) nella guida [Classe App di Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).
