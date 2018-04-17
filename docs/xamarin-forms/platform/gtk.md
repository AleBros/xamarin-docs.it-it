---
title: 'Installazione di piattaforma GTK #'
description: 'Xamarin. Forms include ora il supporto di anteprima per la piattaforma GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: a601e74cc274fd57bb2be9af3562b3a7290d7047
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="gtk-platform-setup"></a>Installazione di piattaforma GTK #

![Anteprima](~/media/shared/preview.png)

Xamarin. Forms dispone ora di supporto di anteprima per le app GTK #. GTK # è un toolkit dell'interfaccia utente grafica che si collega il toolkit GTK + e una varietà di librerie GNOME, che consente lo sviluppo di App di grafica GNONE completamente native utilizzando Mono e .NET. In questo articolo viene illustrato come aggiungere un progetto GTK # a una soluzione xamarin. Forms.

Prima di iniziare, creare una nuova soluzione xamarin. Forms o usare una soluzione xamarin. Forms esistente, ad esempio, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/).

> [!NOTE]
> Mentre questo articolo è incentrato sull'aggiunta di un'app GTK # a una soluzione xamarin. Forms in Visual Studio e VS2017 per Mac, può essere eseguita anche [MonoDevelop](http://www.monodevelop.com/) per Linux.

## <a name="adding-a-gtk-app"></a>Aggiunta di un'App GTK #

GTK # per macOS e Linux viene installato come parte del [Mono](http://www.mono-project.com/download/stable/). GTK # per .NET può essere installato in Windows con il [GTK # Installer](http://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Seguire queste istruzioni per aggiungere un'app GTK # che verrà eseguito sul desktop di Windows:

1. In Visual Studio 2017, fare clic sul nome della soluzione in **Esplora soluzioni** e scegliere **Aggiungi > Nuovo progetto...** .

2. Nel **nuovo progetto** finestra, l'istruzione select sinistro **Visual c#** e **Windows Desktop classico**. Nell'elenco dei tipi di progetto, scegliere **libreria di classi (.NET Framework)**e assicurarsi che il **Framework** elenco a discesa è impostata su un minimo di .NET Framework 4.7.

3. Digitare un nome per il progetto con una **GTK** estensione, ad esempio **GameOfLife.GTK**. Fare clic sul **esplorare** pulsante, selezionare la cartella che contiene la piattaforma di altri progetti, quindi premere **selezionare la cartella**. Questo viene inserito il progetto GTK nella stessa directory in altri progetti nella soluzione.

    ![Aggiungere un nuovo progetto GTK](gtk-images/win/add-new-project.png "aggiungere un nuovo progetto GTK")

    Premere il **OK** pulsante per creare il progetto.

4. Nel **Esplora soluzioni**, fare clic con il pulsante destro del nuovo progetto GTK e selezionare **Gestisci pacchetti NuGet**. Selezionare il **Sfoglia** scheda, fare clic sui **Includi versione provvisoria** casella di controllo per cercare **xamarin. Forms** 3.0 o versione successiva.

    ![Selezionare il pacchetto NuGet di xamarin. Forms](gtk-images/win/select-forms-nuget-package.png "selezionare il pacchetto NuGet di xamarin. Forms")

    Selezionare il pacchetto e fare clic sui **installare** pulsante.

5. Ora di ricerca per il **Xamarin.Forms.Platform.GTK** pacchetto 3.0 o versione successiva.

    ![Selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet](gtk-images/win/select-forms-platform-nuget-package.png "selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet")

    Selezionare il pacchetto e fare clic sui **installare** pulsante.

6. Nel **Esplora soluzioni**destro del mouse sul nome della soluzione e selezionare **Gestisci pacchetti NuGet per la soluzione**. Selezionare il **aggiornamento** scheda e il **xamarin. Forms** pacchetto. Selezionare tutti i progetti e aggiornarli alla stessa versione di xamarin. Forms utilizzata dal progetto GTK.

7. Nel **Esplora soluzioni**, fare clic su **riferimenti** nel progetto GTK. Nel **gestione riferimenti** finestra di dialogo, seleziona **progetti** a sinistra e controllare la casella di controllo accanto al progetto .NET Standard, libreria di classi Portabile o condiviso:

    ![Fare riferimento a progetto condiviso](gtk-images/win/reference-shared-project.png "fa riferimento al progetto condiviso")

8. Nel **gestione riferimenti** finestra di dialogo, premere il **Sfoglia** pulsante e selezionare il **C:\Program Files (x86)\GtkSharp\2.12\lib** cartella e selezionare il  **ATK sharp.dll**, **gdk sharp.dll**, **glade sharp.dll**, **glib sharp.dll**, **gtk-dotnet.dll**, **gtk sharp.dll** file.

    ![Fare riferimento a librerie GTK #](gtk-images/win/reference-gtk-libraries.png "fanno riferimento alle librerie GTK #")

    Premere il **OK** pulsante per aggiungere i riferimenti.

9. Nel progetto GTK rinominare **Class1.cs** alla **Program.cs**.

10. Nel progetto GTK, modificare il **Program.cs** file in modo che sia simile nel codice seguente:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Questo codice inizializza GTK # e xamarin. Forms, crea una finestra dell'applicazione ed esegue l'app.

11. Nel **Esplora soluzioni**, fare clic con il pulsante destro del progetto GTK e selezionare **proprietà**.

12. Nel **delle proprietà** finestra, seleziona il **applicazione** scheda e modificare il **tipo di Output** elenco a discesa per **applicazione Windows**.

    ![Modificare il tipo di output del progetto](gtk-images/win/change-project-output-type.png "modificare il tipo di output di progetto")

13. Nel **Esplora soluzioni**, fare clic sul progetto WPF e selezionare **imposta come progetto di avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![Gioco GTK # del ciclo di vita](gtk-images/win/gtk-gameoflife.png "GTK # gioco del ciclo di vita")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Seguire queste istruzioni per aggiungere un'app GTK # che verrà eseguito sul desktop Mac:

1. In Visual Studio per Mac, pulsante destro del mouse sulla soluzione xamarin. Forms e scegliere **Aggiungi > Aggiungi nuovo progetto...** .

2. Nel **nuovo progetto** finestra scegliere **altri > .NET > progetto 2.0 Gtk #** , quindi premere **Avanti**.

3. Digitare un nome per il progetto con una **GTK** estensione, ad esempio **GameOfLife.GTK**, quindi premere **crea**.

4. Nel **soluzione riempimento**, fare clic su **pacchetti > Aggiungi pacchetti...**  per il GTK il progetto e aggiungere il pacchetto NuGet di xamarin. Forms 3.0 versione non definitiva o versione successiva.

    ![Selezionare il pacchetto NuGet di xamarin. Forms](gtk-images/mac/select-forms-nuget-package.png "selezionare il pacchetto NuGet di xamarin. Forms")

5. Nel **soluzione riempimento**, fare clic su **pacchetti > Aggiungi pacchetti...**  per il GTK il progetto e aggiungere il pacchetto NuGet di versione non definitiva Xamarin.Forms.Platform.GTK 3.0 o versione successiva.

    ![Selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet](gtk-images/mac/select-forms-platform-nuget-package.png "selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet")

6. Aggiornamento di altri progetti della piattaforma per usare la stessa versione di xamarin. Forms usata dal progetto GTK.

7. Nel **soluzione riempimento**, fare clic su **riferimenti > Modifica riferimenti...**  per il GTK del progetto e aggiungere un riferimento al progetto xamarin. Forms (.NET Standard, libreria di classi Portabile o progetto condiviso).

    ![Fare riferimento a progetto condiviso](gtk-images/mac/reference-shared-project.png "fa riferimento al progetto condiviso")

8. Modificare il **Program.cs** file del progetto GTK in modo che non è simile al codice seguente:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Questo codice inizializza GTK # e xamarin. Forms, crea una finestra dell'applicazione ed esegue l'app.

9. Nel **soluzione riempimento**, fare clic sul progetto GTK e selezionare **imposta come progetto di avvio**.

10. In Visual Studio per la barra degli strumenti Mac, premere il **avviare** pulsante (il pulsante triangolare che è simile a un pulsante di riproduzione) per avviare l'app.

    ![Gioco GTK # del ciclo di vita](gtk-images/mac/gtk-gameoflife.png "GTK # gioco del ciclo di vita")

-----

## <a name="next-steps"></a>Passaggi successivi

### <a name="platform-specifics"></a>Specifiche della piattaforma

È possibile determinare quale piattaforma dell'applicazione di xamarin. Forms è alimentato da XAML o il codice. In questo modo è possibile modificare le caratteristiche di programma mentre è in esecuzione su GTK #. Nel codice, confrontare il valore di `Device.RuntimePlatform` con il `Device.GTK` costante (che è uguale alla stringa "GTK"). Se non esiste una corrispondenza, l'applicazione è in esecuzione su GTK #.

In XAML, è possibile utilizzare il `OnPlatform` tag per selezionare un valore di proprietà specifico per la piattaforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>Icona dell'applicazione

È possibile impostare l'icona dell'app all'avvio:

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>Themes

Sono disponibili un'ampia gamma di temi per GTK # e possono essere utilizzati da un'app xamarin. Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Moduli nativi

Form native consente xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivato pagine verranno usati da progetti nativi, compresi i progetti GTK #. Questo può essere eseguita tramite la creazione di un'istanza del [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivato pagina e la conversione nel native GTK # tipo utilizzando il `CreateContainer` metodo di estensione:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Per ulteriori informazioni sui moduli nativi, vedere [moduli nativi](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemi

Si tratta di un'anteprima, pertanto è opportuno prevedere che non siano pronti di produzione. Per lo stato corrente di implementazione, vedere [stato](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)e per i problemi noti correnti, vedere [problemi in sospeso e noti](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
