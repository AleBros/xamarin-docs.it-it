---
title: Guida rapida di Xamarin.Forms Multiscreen
ms.topic: article
ms.prod: xamarin
ms.assetid: 255d93b9-518c-4e5d-a9cd-4dd8a7945a7f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: 5457cb3a80208311bac66a0232afd9cb4dc02c87
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinforms-multiscreen-quickstart"></a>Guida rapida di Xamarin.Forms Multiscreen

Questa guida rapida illustra come estendere l'applicazione Phoneword aggiungendo una seconda schermata per tenere traccia del registro chiamate per l'applicazione. L'applicazione finale è riportata di seguito:

[![](quickstart-images/intro-app-examples-sml.png "Applicazione Phoneword")](quickstart-images/intro-app-examples.png#lightbox "Applicazione Phoneword")

Estendere l'applicazione Phoneword come indicato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Visual Studio. Nella pagina iniziale fare clic su **Apri progetto...**  e nella finestra di dialogo **Apri progetto** selezionare il file della soluzione per il progetto Phoneword:

  ![](quickstart-images/vs/open-solution.png "Aprire il progetto")

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Phoneword** e selezionare **Aggiungi > Nuovo elemento...**:

  ![](quickstart-images/vs/add-new-item.png "Aggiungere un nuovo elemento")

1. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Elementi di Visual C# > Xamarin.Forms > Pagina contenuto**, assegnare al nuovo elemento il nome **CallHistoryPage** e fare clic sul pulsante **Aggiungi**. Verrà aggiunta una pagina denominata **CallHistoryPage** al progetto:

  ![](quickstart-images/vs/add-callhistorypage-class.png "Modelli di progetto Xamarin.Forms")

1. In **CallHistoryPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente. Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina:

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.CallHistoryPage"
                           Title="Call History">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
            </StackLayout>
        </ContentPage>

  Salvare le modifiche apportate a **CallHistoryPage.xaml** premendo **CTRL+S** e chiudere il file.

1. In **Esplora soluzioni** fare doppio clic su **App.xaml.cs** per aprirlo:

  ![](quickstart-images/vs/open-app-class.png "Aprire App.xaml.cs")

1. In **App.xaml.cs** importare lo spazio dei nomi `System.Collections.Generic`, aggiungere la dichiarazione della proprietà `PhoneNumbers`, inizializzare la proprietà nel costruttore `App` e inizializzare la proprietà [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) in modo che sia una [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). La raccolta `PhoneNumbers` verrà usata per archiviare un elenco di ogni numero di telefono convertito chiamato dall'applicazione:

        using System.Collections.Generic;
        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public static IList<string> PhoneNumbers { get; set; }

                public App()
                {
                    InitializeComponent();
                    PhoneNumbers = new List<string>();
                    MainPage = new NavigationPage(new MainPage());
                }
                ...
            }
        }

  Salvare le modifiche apportate a **App.xaml.cs** premendo **CTRL+S** e chiudere il file.

1. In **Esplora soluzioni** fare clic su **MainPage.xaml** per aprirlo:

  ![](quickstart-images/vs/open-mainpage-xaml.png "Aprire MainPage.xaml")

1. In **MainPage.xaml** aggiungere un controllo [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) alla fine del controllo [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Il pulsante verrà usato per passare alla pagina del registro chiamate:

        <StackLayout VerticalOptions="FillAndExpand"
                     HorizontalOptions="FillAndExpand"
                     Orientation="Vertical"
                     Spacing="15">
          ...
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
          <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
                  Clicked="OnCallHistory" />
        </StackLayout>

  Salvare le modifiche apportate a **MainPage.xaml** premendo **CTRL+S** e chiudere il file.

1. In **Esplora soluzioni** fare doppio clic su **MainPage.xaml.cs** per aprirlo:

  ![](quickstart-images/vs/open-mainpage-codebehind.png "Aprire MainPage.xaml.cs")

1. In **MainPage.xaml.cs** aggiungere il metodo gestore dell'evento `OnCallHistory` e modificare il metodo gestore dell'evento `OnCall` per aggiungere il numero di telefono convertito alla raccolta `App.PhoneNumbers` e abilitare l'elemento `callHistoryButton`, a condizione che la variabile `dialer` non sia `null`:

        using System;
        using Xamarin.Forms;

        namespace Phoneword
        {
            public partial class MainPage : ContentPage
            {
                ...

                async void OnCall(object sender, EventArgs e)
                {
                    ...
                    if (dialer != null) {
                        App.PhoneNumbers.Add (translatedNumber);
                        callHistoryButton.IsEnabled = true;
                        dialer.Dial (translatedNumber);
                    }
                    ...
                }

                async void OnCallHistory(object sender, EventArgs e)
                {
                    await Navigation.PushAsync (new CallHistoryPage ());
                }
            }
        }

  Salvare le modifiche apportate a **MainPage.xaml.cs** premendo **CTRL+S** e chiudere il file.

1. In Visual Studio selezionare la voce di menu **Compila > Compila soluzione** o premere **CTRL+MAIUSC+B**. L'applicazione verrà compilata e nella barra di stato di Visual Studio viene visualizzato un messaggio di operazione completata:

  ![](quickstart-images/vs/build-successful.png "Compilazione completata")

  In caso di errori, ripetere i passaggi precedenti e correggere gli errori fino a quando l'applicazione non viene compilata correttamente.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante Riproduci, per avviare l'applicazione:

  ![](quickstart-images/vs/start.png "Barra degli strumenti di Visual Studio")
  ![](quickstart-images/vs/phone-result-uwp.png "UWP applicazione Phoneword")

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Phoneword.Droid** e selezionare **Imposta come progetto di avvio**.
1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante Riproduci, per avviare l'applicazione all'interno di un emulatore Android.
1. Se si ha un dispositivo iOS e si soddisfano i requisiti di sistema Mac per lo sviluppo in Xamarin.Forms, usare una tecnica simile per distribuire l'app al dispositivo iOS. In alternativa, distribuire l'app nel [simulatore remoto per iOS](~/tools/ios-simulator.md).

  Nota: non tutti i simulatori supportano le chiamate telefoniche.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Visual Studio per Mac. Nella pagina iniziale fare clic su **Apri...**  e nella finestra di dialogo selezionare il file della soluzione per il progetto Phoneword:

  ![](quickstart-images/xs/open-solution.png "Aprire la soluzione")

1. Nel **riquadro della soluzione** selezionare il progetto **Phoneword**, fare clic con il pulsante destro del mouse e selezionare **Aggiungi > Nuovo file...**:

  ![](quickstart-images/xs/add-new-file.png "Aggiungere un nuovo file")

1. Nella finestra di dialogo **Nuovo file** selezionare **Forms > Forms ContentPage XAML**, assegnare al nuovo file il nome **CallHistoryPage** e fare clic sul pulsante **Nuovo**. Verrà aggiunta una pagina denominata **CallHistoryPage** al progetto:

  ![](quickstart-images/xs/add-callhistorypage-class.png "Aggiungere Forms ContentPage")

1. Nel **riquadro della soluzione** fare doppio clic su **CallHistoryPage.xaml** per aprirlo:

  ![](quickstart-images/xs/open-callhistorypage-xaml.png "Aprire CallHistoryPage.xaml")

1. In **CallHistoryPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente. Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina:

        <?xml version="1.0" encoding="UTF-8"?>
        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           x:Class="Phoneword.CallHistoryPage"
                           Title="Call History">
            <ContentPage.Padding>
                <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS" Value="20, 40, 20, 20" />
                    <On Platform="Android, WinPhone, Windows" Value="20" />
                </OnPlatform>
            </ContentPage.Padding>
            <StackLayout>
              <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
            </StackLayout>
        </ContentPage>      

  Salvare le modifiche apportate a **CallHistoryPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

1. Nel **riquadro della soluzione** fare doppio clic su **App.xaml.cs** per aprirlo:

  ![](quickstart-images/xs/open-app-class.png "Aprire App.xaml.cs")

1. In **App.xaml.cs** importare lo spazio dei nomi `System.Collections.Generic`, aggiungere la dichiarazione della proprietà `PhoneNumbers`, inizializzare la proprietà nel costruttore `App` e inizializzare la proprietà [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) in modo che sia una [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). La raccolta `PhoneNumbers` verrà usata per archiviare un elenco di ogni numero di telefono convertito chiamato dall'applicazione:

        using System.Collections.Generic;
        using Xamarin.Forms;
        using Xamarin.Forms.Xaml;

        [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
        namespace Phoneword
        {
            public partial class App : Application
            {
                public static IList<string> PhoneNumbers { get; set; }

                public App()
                {
                    InitializeComponent();
                    PhoneNumbers = new List<string>();
                    MainPage = new NavigationPage(new MainPage());
                }
                ...
            }
        }

  Salvare le modifiche apportate a **App.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

1. Nel **riquadro della soluzione** fare doppio clic su **MainPage.xaml** per aprirlo:

  ![](quickstart-images/xs/open-mainpage-xaml.png "Aprire MainPage.xaml")

1. In **MainPage.xaml** aggiungere un controllo [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) alla fine del controllo [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Il pulsante verrà usato per passare alla pagina del registro chiamate:

        <StackLayout VerticalOptions="FillAndExpand"
                     HorizontalOptions="FillAndExpand"
                     Orientation="Vertical"
                     Spacing="15">
          ...
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
          <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
                  Clicked="OnCallHistory" />
        </StackLayout>

  Salvare le modifiche apportate a **MainPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

1. Nel **riquadro della soluzione** fare doppio clic su **MainPage.xaml.cs** per aprirlo:

  ![](quickstart-images/xs/open-mainpage-codebehind.png "Aprire MainPage.xaml.cs")

1. In **MainPage.xaml.cs** aggiungere il metodo gestore dell'evento `OnCallHistory` e modificare il metodo gestore dell'evento `OnCall` per aggiungere il numero di telefono convertito alla raccolta `App.PhoneNumbers` e abilitare l'elemento `callHistoryButton`, a condizione che la variabile `dialer` non sia `null`:

        using System;
        using Xamarin.Forms;

        namespace Phoneword
        {
            public partial class MainPage : ContentPage
            {
                ...

                async void OnCall(object sender, EventArgs e)
                {
                    ...
                    if (dialer != null) {
                        App.PhoneNumbers.Add (translatedNumber);
                        callHistoryButton.IsEnabled = true;
                        dialer.Dial (translatedNumber);
                    }
                    ...
                }

                async void OnCallHistory(object sender, EventArgs e)
                {
                    await Navigation.PushAsync (new CallHistoryPage ());
                }
            }
        }

  Salvare le modifiche apportate a **MainPage.xaml.cs** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

1. In Visual Studio per Mac selezionare la voce di menu **Compila > Compila tutto** o premere **&#8984; + B**. L'applicazione verrà compilata e nella barra degli strumenti di Visual Studio per Mac viene visualizzato un messaggio di operazione completata:

  ![](quickstart-images/xs/build-successful.png "Compilazione completata")

  In caso di errori, ripetere i passaggi precedenti e correggere gli errori fino a quando l'applicazione non viene compilata correttamente.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante Riproduci, per avviare l'applicazione all'interno del simulatore iOS:

  ![](quickstart-images/xs/start.png "Barra degli strumenti di Visual Studio per Mac")
  ![](quickstart-images/xs/phone-result-ios.png "Simulatore iOS")

  Nota: il simulatore iOS non supporta le chiamate telefoniche.

1. Nel **riquadro della soluzione** selezionare il progetto **Phoneword.Droid**, fare clic con il pulsante destro del mouse e selezionare **Imposta come progetto di avvio**:

  ![](quickstart-images/xs/set-startup-project.png "Impostare come progetto di avvio")

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante Riproduci, per avviare l'applicazione all'interno di un emulatore Android:

  ![](quickstart-images/xs/phone-result-android.png "Emulatore Android")

  Nota: gli emulatori Android non supportano le chiamate telefoniche.

-----

L'applicazione Xamarin.Forms Multiscreen è stata completata. Nell'[argomento successivo](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/deepdive.md) di questa guida vengono esaminati i passaggi eseguiti in questa procedura dettagliata per comprendere la navigazione tra le pagine e il data binding con Xamarin.Forms.


## <a name="related-links"></a>Collegamenti correlati

- [Phoneword (sample)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/) (Phoneword - Esempio)
- [PhonewordMultiscreen (sample)](https://developer.xamarin.com/samples/xamarin-forms/PhonewordMultiscreen/) (PhonewordMultiscreen - Esempio)
