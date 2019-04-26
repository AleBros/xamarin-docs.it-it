---
title: 'Installazione di piattaforma GTK #'
description: 'Xamarin. Forms include ora il supporto di anteprima per la piattaforma di GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 7e9bfa841db9f0a76f762bab22050377830d85de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61171657"
---
# <a name="gtk-platform-setup"></a>Installazione di piattaforma GTK #

![Anteprima](~/media/shared/preview.png)

Xamarin. Forms dispone ora di supporto in anteprima per le app di GTK #. GTK # è un toolkit dell'interfaccia utente grafica che si collega il toolkit di GTK + e un'ampia gamma di librerie GNOME, consentendo lo sviluppo di GNOME completamente native App grafiche con Mono e .NET. Questo articolo illustra come aggiungere un progetto GTK # a una soluzione xamarin. Forms.

Prima di iniziare, creare una nuova soluzione xamarin. Forms o usare una soluzione xamarin. Forms esistente, ad esempio, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/).

> [!NOTE]
> Questo articolo è incentrato sull'aggiunta di un'app di GTK # a una soluzione xamarin. Forms in Visual Studio 2017 e Visual Studio per Mac, può essere eseguita anche [MonoDevelop](http://www.monodevelop.com/) per Linux.

## <a name="adding-a-gtk-app"></a>Aggiunta di un'App GTK #

GTK # per macOS e Linux viene installato come parte della [Mono](https://www.mono-project.com/download/stable/). GTK # per .NET può essere installato in Windows con il [programma di installazione di GTK #](https://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Seguire queste istruzioni per aggiungere un'app di GTK # che verrà eseguito sul desktop di Windows:

1. In Visual Studio 2019, fare doppio clic sul nome della soluzione in **Esplora soluzioni** e scegliere **Aggiungi > Nuovo progetto...** .

2. Nel **nuovo progetto** finestra, l'istruzione select a sinistra **Visual c#** e **Desktop classico di Windows**. Nell'elenco dei tipi di progetto, scegliere **libreria di classi (.NET Framework)** e assicurarsi che le **Framework** elenco a discesa è impostata su un minimo di .NET Framework 4.7.

3. Digitare un nome per il progetto con un **GTK** estensione, ad esempio **GameOfLife.GTK**. Fare clic sui **esplorare** pulsante, selezionare la cartella che contiene l'altra piattaforma progetti, quindi premere **Seleziona cartella**. Il progetto GTK verrà impostato nella stessa directory in altri progetti nella soluzione.

    ![Aggiungere un nuovo progetto GTK](gtk-images/win/add-new-project.png "aggiungere un nuovo progetto GTK")

    Premere il **OK** pulsante per creare il progetto.

4. Nel **Esplora soluzioni**, fare clic con il pulsante destro del nuovo progetto GTK e selezionare **Gestisci pacchetti NuGet**. Selezionare il **esplorare** scheda e cercare **xamarin. Forms** 3.0 o versione successiva.

    ![Selezionare il pacchetto NuGet di xamarin. Forms](gtk-images/win/select-forms-nuget-package.png "selezionare il pacchetto NuGet di xamarin. Forms")

    Selezionare il pacchetto e scegliere il **installare** pulsante.

5. Cercare ora il **Xamarin.Forms.Platform.GTK** pacchetto 3.0 o versione successiva.

    ![Selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet](gtk-images/win/select-forms-platform-nuget-package.png "selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet")

    Selezionare il pacchetto e scegliere il **installare** pulsante.

6. Nel **Esplora soluzioni**, fare clic sul nome della soluzione e selezionare **Gestisci pacchetti NuGet per la soluzione**. Selezionare il **Update** scheda e il **xamarin. Forms** pacchetto. Selezionare tutti i progetti e aggiornarli per la stessa versione di xamarin. Forms come quelle utilizzate dal progetto GTK.

7. Nel **Esplora soluzioni**, fare clic su **riferimenti** nel progetto GTK. Nel **gestione riferimenti** finestra di dialogo, seleziona **progetti** a sinistra, quindi controllare la casella di controllo accanto al progetto .NET Standard o condiviso:

    ![Fare riferimento al progetto condiviso](gtk-images/win/reference-shared-project.png "fanno riferimento a progetto condiviso")

8. Nel **gestione riferimenti** finestra di dialogo, premere il **Sfoglia** pulsante e passare al **C:\Program Files (x86)\GtkSharp\2.12\lib** cartella e selezionare il  **ATK sharp.dll**, **gdk sharp.dll**, **glade sharp.dll**, **glib sharp.dll**, **gtk-dotnet.dll**, **gtk-sharp.dll** file.

    ![Fare riferimento alle librerie GTK #](gtk-images/win/reference-gtk-libraries.png "fanno riferimento alle librerie GTK #")

    Premere il **OK** pulsante per aggiungere i riferimenti.

9. Nel progetto GTK, rinominare **Class1.cs** al **Program.cs**.

10. Nel progetto GTK, modificare il **Program.cs** file in modo che risulti simile al seguente:

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

    ![Modificare il tipo di output del progetto](gtk-images/win/change-project-output-type.png "modificare il tipo di output progetto")

13. Nel **Esplora soluzioni**, fare clic sul progetto GTK e selezionare **imposta come progetto di avvio**. Premere F5 per eseguire il programma con il debugger di Visual Studio sul desktop di Windows:

    ![Gioco di GTK # del ciclo di vita](gtk-images/win/gtk-gameoflife.png "GTK # gioco del ciclo di vita")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Seguire queste istruzioni per aggiungere un'app di GTK # che verrà eseguito sul desktop Mac:

1. In Visual Studio per Mac, fare clic sulla soluzione xamarin. Forms e scegliere **Aggiungi > Aggiungi nuovo progetto...** .

2. Nel **nuovo progetto** finestra scegliere **altri > .NET > Gtk # 2.0 progetto** , quindi premere **Avanti**.

3. Digitare un nome per il progetto con un **GTK** estensione, ad esempio **GameOfLife.GTK**, quindi premere **Create**.

4. Nel **riquadro della soluzione**, fare clic su **pacchetti > Aggiungi pacchetti...**  per GTK il progetto e aggiungere il pacchetto NuGet di xamarin. Forms 3.0 versioni non definitive o versioni successive.

    ![Selezionare il pacchetto NuGet di xamarin. Forms](gtk-images/mac/select-forms-nuget-package.png "selezionare il pacchetto NuGet di xamarin. Forms")

5. Nel **riquadro della soluzione**, fare clic su **pacchetti > Aggiungi pacchetti...**  per GTK il progetto e aggiungere il pacchetto NuGet di versioni non definitive Xamarin.Forms.Platform.GTK 3.0 o versione successiva.

    ![Selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet](gtk-images/mac/select-forms-platform-nuget-package.png "selezionare il pacchetto Xamarin.Forms.Platform.GTK NuGet")

6. Aggiornare gli altri progetti della piattaforma per usare la stessa versione di xamarin. Forms come usati dal progetto GTK.

7. Nel **riquadro della soluzione**, fare clic su **riferimenti > Modifica riferimenti...**  per GTK il progetto e aggiungere un riferimento al progetto xamarin. Forms (.NET Standard o progetti condivisi).

    ![Fare riferimento al progetto condiviso](gtk-images/mac/reference-shared-project.png "fanno riferimento a progetto condiviso")

8. Modificare il **Program.cs** file del progetto GTK in modo che sia simile al seguente codice:

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

9. Nel **riquadro della soluzione**, fare clic sul progetto GTK e selezionare **imposta come progetto di avvio**.

10. In Visual Studio per la barra degli strumenti Mac, premere il **avviare** pulsante (il pulsante triangolare simile al pulsante Riproduci) per avviare l'app.

    ![Gioco di GTK # del ciclo di vita](gtk-images/mac/gtk-gameoflife.png "GTK # gioco del ciclo di vita")

-----

## <a name="next-steps"></a>Passaggi successivi

### <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

È possibile determinare in quale piattaforma l'applicazione xamarin. Forms è alimentato da XAML o codice. In questo modo è possibile modificare le caratteristiche di programma in esecuzione in GTK #. Nel codice, confrontare il valore della `Device.RuntimePlatform` con il `Device.GTK` costante (che è uguale alla stringa "GTK"). Se non esiste una corrispondenza, l'applicazione viene eseguita in GTK #.

In XAML, è possibile usare il `OnPlatform` tag per selezionare un valore di proprietà specifico della piattaforma:

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

Sono disponibili una vasta gamma di temi per GTK # e possono essere usati da un'app xamarin. Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Form nativi

Form nativi consente a xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagine deve essere utilizzato da progetti nativi, inclusi i progetti di GTK #. A questo scopo tramite la creazione di un'istanza del [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina inferiori e convertendolo nel native GTK # tipo utilizzando il `CreateContainer` metodo di estensione:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Per altre informazioni sui moduli nativi, vedere [form nativi](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemi

Si tratta di un'anteprima, pertanto è opportuno prevedere che non siano pronte per la produzione. Per lo stato corrente di implementazione, vedere [lo stato](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)e per i problemi noti correnti, vedere [problemi in sospeso & noti](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
