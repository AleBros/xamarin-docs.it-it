---
title: 'Installazione della piattaforma GTK #'
description: 'Novell. Forms ora dispone del supporto in anteprima per la piattaforma GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: cbc3bceffacd9669c1e2e667faadc2939fd4aa1f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005919"
---
# <a name="gtk-platform-setup"></a>Installazione della piattaforma GTK #

![Anteprima](~/media/shared/preview.png)

Novell. Forms ora include il supporto in anteprima per le app GTK #. GTK # è un toolkit di interfaccia utente grafica che collega il toolkit GTK + e un'ampia gamma di librerie GNOME, consentendo lo sviluppo di app grafiche GNOME completamente native usando Mono e .NET. Questo articolo illustra come aggiungere un progetto GTK # a una soluzione Novell. Forms.

> [!IMPORTANT]
> Il supporto di Novell. Forms per GTK # è fornito dalla community. Per ulteriori informazioni, vedere [supporto della piattaforma Novell. Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Prima di iniziare, creare una nuova soluzione Novell. Forms oppure usare una soluzione Novell. Forms esistente, ad esempio [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife).

> [!NOTE]
> Sebbene questo articolo sia incentrato sull'aggiunta di un'app GTK # a una soluzione Novell. Forms in VS2017 e Visual Studio per Mac, può essere eseguita anche in [MonoDevelop](https://www.monodevelop.com/) per Linux.

## <a name="adding-a-gtk-app"></a>Aggiunta di un'app GTK #

GTK # per macOS e Linux è installato come parte di [mono](https://www.mono-project.com/download/stable/). GTK # per .NET può essere installato in Windows con il [programma di installazione di GTK #](https://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Seguire queste istruzioni per aggiungere un'app GTK # che viene eseguita nel desktop di Windows:

1. In Visual Studio 2019, fare clic con il pulsante destro del mouse sul nome della soluzione in **Esplora soluzioni** e scegliere **Aggiungi > nuovo progetto.**

2. Nella finestra **nuovo progetto** , a sinistra, selezionare **Visual C#**  e **desktop classico di Windows**. Nell'elenco dei tipi di progetto scegliere **libreria di classi (.NET Framework)** e assicurarsi che l'elenco a discesa **Framework** sia impostato su un valore minimo di .NET Framework 4,7.

3. Digitare un nome per il progetto con un'estensione **GTK** , ad esempio **GameOfLife. GTK**. Fare clic sul pulsante **Sfoglia** , selezionare la cartella contenente gli altri progetti della piattaforma e premere **Seleziona cartella**. Il progetto GTK verrà inserito nella stessa directory degli altri progetti della soluzione.

    ![Aggiungere un nuovo progetto GTK](gtk-images/win/add-new-project.png "Aggiungere un nuovo progetto GTK")

    Premere il pulsante **OK** per creare il progetto.

4. Nel **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nuovo progetto GTK e scegliere **Gestisci pacchetti NuGet**. Selezionare la scheda **Sfoglia** e cercare **Novell. Forms** 3,0 o versione successiva.

    ![Selezionare il pacchetto NuGet Novell. Forms](gtk-images/win/select-forms-nuget-package.png "Selezionare il pacchetto NuGet Novell. Forms")

    Selezionare il pacchetto e fare clic sul pulsante **Installa** .

5. A questo punto, cercare il pacchetto **Novell. Forms. Platform. GTK** 3,0 o versione successiva.

    ![Selezionare il pacchetto NuGet Novell. Forms. Platform. GTK](gtk-images/win/select-forms-platform-nuget-package.png "Selezionare il pacchetto NuGet Novell. Forms. Platform. GTK")

    Selezionare il pacchetto e fare clic sul pulsante **Installa** .

6. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome della soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**. Selezionare la scheda **aggiornamento** e il pacchetto **Novell. Forms** . Selezionare tutti i progetti e aggiornarli alla stessa versione di Novell. Forms usata dal progetto GTK.

7. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse su **riferimenti** nel progetto GTK. Nella finestra di dialogo **Gestione riferimenti** selezionare **progetti** a sinistra e selezionare la casella di controllo accanto al progetto .NET standard o condiviso:

    ![Fare riferimento al progetto condiviso](gtk-images/win/reference-shared-project.png "Fare riferimento al progetto condiviso")

8. Nella finestra di dialogo **Gestione riferimenti** premere il pulsante **Sfoglia** e passare alla cartella **c:\Programmi (x86) \GtkSharp\2.12\lib** e selezionare **ATK-Sharp. dll**, **gdk-sharp. dll**, **Glade-Sharp. dll**,  **file GLib-Sharp.** dll, **gtk-dotnet. dll**, **GTK-Sharp. dll** .

    ![Riferimenti alle librerie GTK #](gtk-images/win/reference-gtk-libraries.png "Riferimenti alle librerie GTK #")

    Premere il pulsante **OK** per aggiungere i riferimenti.

9. Nel progetto GTK rinominare **Class1.cs** in **Program.cs**.

10. Nel progetto GTK modificare il file **Program.cs** in modo che sia simile al codice seguente:

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

    Questo codice inizializza GTK # e Novell. Forms, crea una finestra dell'applicazione ed esegue l'app.

11. Nel **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto GTK e scegliere **Proprietà**.

12. Nella finestra **Proprietà** selezionare la scheda **applicazione** e modificare l'elenco a discesa **tipo di output** in **applicazione Windows**.

    ![Modificare il tipo di output del progetto](gtk-images/win/change-project-output-type.png "Modificare il tipo di output del progetto")

13. Nel **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto GTK e selezionare **Imposta come progetto di avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![Gioco della vita in GTK #](gtk-images/win/gtk-gameoflife.png "Gioco della vita in GTK #")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Seguire queste istruzioni per aggiungere un'app GTK # che viene eseguita sul desktop Mac:

1. In Visual Studio per Mac fare clic con il pulsante destro del mouse sulla soluzione Novell. Forms e scegliere **aggiungi > Aggiungi nuovo progetto.**

2. Nella finestra **nuovo progetto** scegliere **altri > .net > progetto GTK # 2,0** e fare clic su **Avanti**.

3. Digitare un nome per il progetto con un'estensione **GTK** , ad esempio **GameOfLife. GTK**, quindi fare clic su **Crea**.

4. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse su **pacchetti > Aggiungi pacchetti..** . per il progetto GTK e aggiungere il pacchetto NuGet novell. Forms 3,0 o versione successiva.

    ![Selezionare il pacchetto NuGet Novell. Forms](gtk-images/mac/select-forms-nuget-package.png "Selezionare il pacchetto NuGet Novell. Forms")

5. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse su **pacchetti > Aggiungi pacchetti..** . per il progetto GTK e aggiungere il pacchetto NuGet Novell. Forms. Platform. GTK 3,0 o versione successiva.

    ![Selezionare il pacchetto NuGet Novell. Forms. Platform. GTK](gtk-images/mac/select-forms-platform-nuget-package.png "Selezionare il pacchetto NuGet Novell. Forms. Platform. GTK")

6. Aggiornare gli altri progetti della piattaforma in modo che usino la stessa versione di Novell. Forms usata dal progetto GTK.

7. Nella **riquadro della soluzione**fare clic con il pulsante destro del mouse su **References > Edit References...** per il progetto GTK e aggiungere un riferimento al progetto Novell. Forms (.NET standard o progetto condiviso).

    ![Fare riferimento al progetto condiviso](gtk-images/mac/reference-shared-project.png "Fare riferimento al progetto condiviso")

8. Modificare il file **Program.cs** del progetto GTK in modo che sia simile al codice seguente:

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

    Questo codice inizializza GTK # e Novell. Forms, crea una finestra dell'applicazione ed esegue l'app.

9. Nel **riquadro della soluzione**fare clic con il pulsante destro del mouse sul progetto GTK e selezionare **Imposta come progetto di avvio**.

10. Nella barra degli strumenti Visual Studio per Mac fare clic sul pulsante **Avvia** (il pulsante triangolare simile a un pulsante Riproduci) per avviare l'app.

    ![Gioco della vita in GTK #](gtk-images/mac/gtk-gameoflife.png "Gioco della vita in GTK #")

-----

## <a name="next-steps"></a>Passaggi successivi

### <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

È possibile determinare la piattaforma in cui è in esecuzione l'applicazione Novell. Forms da XAML o dal codice. In questo modo è possibile modificare le caratteristiche del programma quando viene eseguito in GTK #. Nel codice confrontare il valore di `Device.RuntimePlatform` con la costante `Device.GTK` (che corrisponde alla stringa "GTK"). Se esiste una corrispondenza, l'applicazione è in esecuzione in GTK #.

In XAML è possibile usare il tag `OnPlatform` per selezionare un valore della proprietà specifico per la piattaforma:

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

Per GTK # è disponibile un'ampia gamma di temi che possono essere usati da un'app Novell. Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Form nativi

I moduli nativi consentono a Novell. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)pagine derivate da essere utilizzate da progetti nativi, inclusi i progetti GTK #. Questa operazione può essere eseguita creando un'istanza della pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)e trasformandola nel tipo GTK # nativo usando il metodo di estensione `CreateContainer`:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Per ulteriori informazioni sui moduli nativi, vedere [form nativi](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemi

Si tratta di un'anteprima, quindi è necessario aspettarsi che non tutti gli elementi siano pronti per la produzione. Per lo stato di implementazione corrente, vedere [stato](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)e per i problemi noti correnti, vedere [in sospeso & problemi noti](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
