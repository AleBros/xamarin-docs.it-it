---
title: Hello, iOS - Guida introduttiva
description: Questa procedura dettagliata illustra come compilare una semplice applicazione Xamarin.iOS denominata Hello, iOS. Presenta inoltre gli strumenti e i concetti fondamentali che è necessario conoscere per compilare applicazioni Xamarin.iOS.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: D3868F3A-4EED-BDDF-45AA-665102C39634
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 5dcc37730008e6e39b96128bc1368f022daa2d06
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73023002"
---
# <a name="hello-ios--quickstart"></a>Hello, iOS - Guida introduttiva

Questa guida descrive come creare un'applicazione che converte un numero di telefono alfanumerico immesso dall'utente in un numero di telefono numerico e lo chiama. L'applicazione finale ha l'aspetto seguente:

 [![](hello-ios-quickstart-images/image1.png "The Hello.iOS Quickstart app")](hello-ios-quickstart-images/image1.png#lightbox)

## <a name="requirements"></a>Requisiti

Lo sviluppo di app iOS con Xamarin richiede:

- Mac che esegue macOS High Sierra (10.13) o versione successiva.
- Ultima versione di Xcode e iOS SDK installati [dall'App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) .

::: zone pivot="macos"

Xamarin.iOS è compatibile con la configurazione seguente:

- Versione più recente di Visual Studio per Mac con le specifiche indicate in precedenza.

Per istruzioni dettagliate sull'installazione, è disponibile la [guida all'installazione di Xamarin.iOS in Mac](~/ios/get-started/installation/mac.md)

::: zone-end
::: zone pivot="windows"

Xamarin.iOS è compatibile con la configurazione seguente:

- Versione più recente di Visual Studio 2019 o Visual Studio 2017, Community, Professional o Enterprise in Windows 10, associato a un host di compilazione Mac con le specifiche indicate in precedenza.

Per istruzioni dettagliate sull'installazione, è disponibile la [guida all'installazione di Xamarin.iOS in Windows](~/ios/get-started/installation/windows/index.md).

::: zone-end

Prima di iniziare, scaricare il [set di icone per app Xamarin](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true).

::: zone pivot="macos"

## <a name="visual-studio-for-mac-walkthrough"></a>Procedura dettagliata per Visual Studio per Mac

Questa procedura dettagliata descrive come creare un'applicazione, Phoneword, che converte un numero di telefono alfanumerico in un numero di telefono numerico.

1. Avviare Visual Studio per Mac dalla cartella **Applicazioni** o da **Spotlight**:

    ![](hello-ios-quickstart-images/image2new.png "The Launch screen")

    Nella schermata di avvio fare clic su **Nuovo progetto** per creare una nuova soluzione Xamarin.iOS:

    ![](hello-ios-quickstart-images/image3new.png "iOS solution")

2. Nella **finestra di dialogo Nuova soluzione** scegliere il modello **iOS > App > App visualizzazione singola**, verificando che sia selezionato il linguaggio C#. Fare clic su **Avanti**:

    ![](hello-ios-quickstart-images/image4new.png "Choose Single View Application")

3. Configurare l'app. Assegnarle il **nome** `Phoneword_iOS` e lasciare tutte le altre impostazioni predefinite. Fare clic su **Avanti**:

    ![](hello-ios-quickstart-images/image5new.png "Enter the app name")

4. Lasciare invariati i nomi del progetto e della soluzione. Scegliere il percorso del progetto o mantenere l'impostazione predefinita:

    ![](hello-ios-quickstart-images/image6new.png "Choose the location of the project")

5. Fare clic su **Crea** per creare la **Soluzione**.

6. Aprire il file **Main.storyboard** facendo doppio clic su di esso nel **riquadro della soluzione**. Ciò consente di creare un'interfaccia utente in modo visivo:

    ![](hello-ios-quickstart-images/image7new.png "The iOS Designer")

    Si noti che le _classi di dimensioni_ sono abilitate per impostazione predefinita. Per altre informazioni su di esse, fare riferimento alla guida [Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) (Storyboard unificati).

7. Nel **riquadro Casella degli strumenti** digitare "etichetta" nella barra di ricerca e trascinare un elemento **Etichetta** nell'area di progettazione (l'area al centro):

    ![](hello-ios-quickstart-images/image8new.png "Drag a Label onto the design surface the area in the center")

    > [!NOTE]
    > È possibile visualizzare il **riquadro delle proprietà** o la **casella degli strumenti** in qualsiasi momento passando a **Visualizza > Riquadri**.

8. Afferrare i punti di controllo dei *controlli di trascinamento* (i cerchi intorno al controllo) e ingrandire l'elemento:

    ![](hello-ios-quickstart-images/image9.png "Make the label wider")

9. Dopo aver selezionato l'elemento **Etichetta** nell'area di progettazione, usare il **riquadro delle proprietà** per modificare la proprietà **Testo** dell'elemento **Etichetta** in "Enter a Phoneword:" (Immettere una Phoneword)

    ![](hello-ios-quickstart-images/image10.png "Set the label to Enter a Phoneword")

10. Cercare "campo di testo" all'interno della casella degli strumenti e trascinare un **Campo di testo** dalla **casella degli strumenti** nell'area di progettazione e posizionarlo sotto l'elemento **Etichetta**. Regolare la larghezza fino a quando il **campo di testo** ha la stessa larghezza dell'etichetta : **Label**

    ![](hello-ios-quickstart-images/image12new.png "Make the Text Field the same width as the Label")

11. Dopo aver selezionato il **Campo di testo** nell'area di progettazione, modificare la proprietà **Nome**del **Campo di testo** nella sezione Identità del **riquadro Proprietà** in `PhoneNumberText` e modificare la proprietà **Testo** in "1-855-XAMARIN":

    ![](hello-ios-quickstart-images/image13new.png "Change the Title property to 1-855-XAMARIN")

12. Trascinare un oggetto **Pulsante** dalla **casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Campo di testo**. Regolare la larghezza in modo che il **pulsante** sia largo come il **campo di testo** e **l'etichetta**:

    ![](hello-ios-quickstart-images/image14new.png "Adjust the width so the Button is as wide as the Text Field and Label")

13. Dopo aver selezionato il **Pulsante** nell'area di progettazione, modificare la proprietà **Nome** nella sezione **Identità** del **riquadro delle proprietà** in `TranslateButton`. Modificare la proprietà **Titolo** in "Translate" (Converti):

    ![](hello-ios-quickstart-images/image15new.png "Change the Title property to Translate")

14. Ripetere i due passaggi precedenti e trascinare un oggetto **Pulsante** dalla **casella degli strumenti** all'area di progettazione e inserirlo sotto il primo **Pulsante**. Regolare la larghezza in modo che il **Pulsante** sia largo come il primo **Pulsante**:

    ![](hello-ios-quickstart-images/image16new.png "Adjust the width so the Button is as wide as the first Button")

15. Dopo aver selezionato il secondo **Pulsante** nell'area di progettazione, modificare la proprietà **Nome** nella sezione **Identità** del **riquadro delle proprietà** in `CallButton`. Modificare la proprietà **Titolo** in "Call" (Chiama):

    ![](hello-ios-quickstart-images/image17new.png "Change the Title property to Call")

    Salvare le modifiche passando a **File > Salva** o premendo **⌘ + s**.

16. È necessario aggiungere della logica all'app per convertire i numeri di telefono da alfanumerici a numerici. Aggiungere un nuovo file al progetto facendo clic con il pulsante destro del mouse sul progetto **Phoneword_iOS** nel **riquadro della soluzione** e scegliendo **Aggiungi > Nuovo file... ** o premendo **⌘ + n**:

    ![](hello-ios-quickstart-images/image18.png "Add a new file to the Project")

17. Nella finestra di dialogo **Nuovo file** selezionare **Generale > Classe vuota** e assegnare al nuovo file il nome `PhoneTranslator`:

    ![](hello-ios-quickstart-images/image19.png "Select Empty Class and name the new file PhoneTranslator")

18. Viene creata una nuova classe C# vuota. Rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword_iOS
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Salvare il file **PhoneTranslator.cs** e chiuderlo.

19. Aggiungere codice per associare l'interfaccia utente. A tale scopo, fare doppio clic su **ViewController.cs** nel **riquadro della soluzione** per aprirlo:

    ![](hello-ios-quickstart-images/image20new.png "Add code to wire up the user interface")

20. Iniziare associando `TranslateButton`. Nella classe **ViewController** trovare il metodo `ViewDidLoad` e aggiungere il codice seguente sotto la chiamata di `base.ViewDidLoad()`:

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(
            PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call ", UIControlState.Normal);
            CallButton.Enabled = false;
        } else {
            CallButton.SetTitle ("Call " + translatedNumber,
                UIControlState.Normal);
            CallButton.Enabled = true;
        }
    };
    ```

    Includere `using Phoneword_iOS;` se lo spazio dei nomi del file è diverso.

21. Aggiungere il codice relativo alla pressione dell'utente sul secondo pulsante, denominato `CallButton`. Inserire il codice seguente sotto il codice per `TranslateButton` e aggiungere `using Foundation;` all'inizio del file:

    ```csharp
        CallButton.TouchUpInside += (object sender, EventArgs e) => {
            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl ("tel:" + translatedNumber);

            // ...otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl (url)) {
                var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                PresentViewController (alert, true, null);
            }
        };
    ```

22. Salvare le modifiche e quindi compilare l'applicazione scegliendo **Compila > Compila tutto** o premendo il tasto **cancelletto B**.  Se l'applicazione viene compilata, verrà visualizzato un messaggio di operazione riuscita nella parte superiore dell'IDE:

    ![](hello-ios-quickstart-images/image21.png "A success message will appear at the top of the IDE")

    Se sono presenti errori, rivedere i passaggi precedenti e correggere eventuali errori fino a quando l'applicazione non viene compilata correttamente.

23. Testare infine l'applicazione nel **simulatore iOS**. Nella parte superiore sinistra dell'IDE scegliere **Debug** dal primo elenco a discesa e **iPhone XR iOS 12.0** (o un altro simulatore disponibile) dal secondo elenco a discesa e premere **Avvia** (il pulsante triangolare simile al pulsante Riproduci):

    ![](hello-ios-quickstart-images/image27.png "Select a simulator and press start")

    > [!NOTE]
    > Al momento, a causa di un requisito Apple, per compilare codice per un dispositivo o un simulatore potrebbe essere necessario un certificato di sviluppo o un'*identità di firma*. Per soddisfare questo requisito, seguire i passaggi nella [guida Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/manual-provisioning.md).

24. Verrà avviata l'applicazione all'interno del simulatore iOS:

    ![](hello-ios-quickstart-images/image28.png "The application running inside the iOS Simulator")

    Le chiamate telefoniche non sono supportate nel simulatore iOS. Verrà invece visualizzata una finestra di dialogo di avviso quando si tenta di effettuare una chiamata:

    ![](hello-ios-quickstart-images/image29.png "The alert dialog when trying to place a call")

::: zone-end
::: zone pivot="windows"

## <a name="visual-studio-walkthrough"></a>Procedura dettagliata in Visual Studio

Questa procedura dettagliata descrive come creare un'applicazione, Phoneword, che converte un numero di telefono alfanumerico in un numero di telefono numerico.

> [!NOTE]
> questa procedura dettagliata usa Visual Studio Enterprise 2017 in una macchina virtuale Windows 10. La configurazione usata può essere diversa da quella usata qui, purché i requisiti sopra riportati siano soddisfatti. Tenere presente tuttavia che alcune schermate potrebbero essere diverse.

> [!NOTE]
> Prima di procedere con questa procedura dettagliata, è necessario connettersi al Mac da Visual Studio. Xamarin.iOS, infatti, si basa su alcuni strumenti Apple per compilare e avviare iOS Designer e le applicazioni. Per eseguire la configurazione, seguire i passaggi nella guida [Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio dal menu **Start**:

    ![](hello-ios-quickstart-images/image001-.png "The Start screen")

    Creare una nuova soluzione Xamarin.iOS selezionando **File > Nuovo > Progetto > Visual C# > iPhone e iPad > App iOS (Xamarin)**:

    ![Selezionare il tipo di progetto App iOS (Xamarin)](hello-ios-quickstart-images/image002.w157.png "Selezionare il tipo di progetto App iOS (Xamarin)")

    Nella finestra di dialogo successiva selezionare il modello **App visualizzazione singola** e premere **OK** per creare il progetto:

    ![Modello di progetto Seleziona vista singola](hello-ios-quickstart-images/image002-2.w157.png "Modello di progetto Seleziona vista singola")

1. Verificare che l'icona Xamarin Mac Agent sulla barra degli strumenti sia verde.

    ![Verificare che l'icona Xamarin Mac Agent sulla barra degli strumenti sia verde](hello-ios-quickstart-images/vs-image4.png)

    Se non lo è, significa che non è disponibile la connessione all'host di compilazione Mac. Per connettersi, seguire i passaggi nella [guida alla configurazione](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Aprire il file **Main.storyboard** in iOS Designer facendo doppio clic su di esso in **Esplora soluzioni**:

    ![](hello-ios-quickstart-images/vs-image7.png "The iOS Designer")

1. Aprire la scheda **Casella degli strumenti**, digitare "etichetta" nella barra di ricerca e trascinare un elemento **Etichetta** nell'area di progettazione (l'area al centro):

    ![](hello-ios-quickstart-images/vs-image8.png "Drag a Label onto the design surface the area in the center")

1. Afferrare quindi i punti di controllo dei *controlli di trascinamento* e ingrandire l'elemento:

    ![](hello-ios-quickstart-images/vs-image9.png "Make the label wider")

1. Dopo aver selezionato l'elemento **Etichetta** nell'area di progettazione, usare la **finestra Proprietà** per modificare la proprietà **Testo** dell'elemento **Etichetta** in "Enter a Phoneword:" (Immettere una Phoneword)

    ![](hello-ios-quickstart-images/vs-image10.png "Change the Text property of the Label to `Enter a Phoneword`")

    > [!NOTE]
    > È possibile visualizzare le **proprietà** o la **casella degli strumenti** in qualsiasi momento passando al menu **Visualizza**.

1. Cercare "campo di testo" all'interno della casella degli strumenti e trascinare un **Campo di testo** dalla **casella degli strumenti** nell'area di progettazione e posizionarlo sotto l'elemento **Etichetta**. Regolare la larghezza fino a quando il **campo di testo** ha la stessa larghezza dell'etichetta : **Label**

    ![](hello-ios-quickstart-images/vs-image12.png "Adjust the width until the Text Field is the same width as the Label")

1. Dopo aver selezionato il **Campo di testo** nell'area di progettazione, modificare la proprietà **Nome**del **Campo di testo** nella sezione Identità di **Proprietà** in `PhoneNumberText` e modificare la proprietà **Testo** in "1-855-XAMARIN":

    ![](hello-ios-quickstart-images/vs-image13.png "Change the Text property to 1-855-XAMARIN")

1. Trascinare un oggetto **Pulsante** dalla **casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Campo di testo**. Regolare la larghezza in modo che il **pulsante** sia largo come il **campo di testo** e **l'etichetta**:

    ![](hello-ios-quickstart-images/vs-image14.png "Adjust the width so the Button is as wide as the Text Field and Label")

1. Dopo aver selezionato il **Pulsante** nell'area di progettazione, modificare la proprietà **Nome** nella sezione **Identità** di **Proprietà** in `TranslateButton`. Modificare la proprietà **Titolo** in "Translate" (Converti):

    ![](hello-ios-quickstart-images/vs-image15.png "Change the Title property to Translate")

1. Ripetere i due passaggi precedenti e trascinare un oggetto **Pulsante** dalla **casella degli strumenti** all'area di progettazione e inserirlo sotto il primo **Pulsante**. Regolare la larghezza in modo che il **Pulsante** sia largo come il primo **Pulsante**:

    ![](hello-ios-quickstart-images/vs-image16.png "Adjust the width so the Button is as wide as the first Button")

1. Dopo aver selezionato il secondo **Pulsante** nell'area di progettazione, modificare la proprietà **Nome** nella sezione **Identità** di **Proprietà** in `CallButton`. Modificare la proprietà **Titolo** in "Call" (Chiama):

    ![](hello-ios-quickstart-images/vs-image17.png "Change the Title property to Call")

    Salvare le modifiche passando a **File > Salva tutto** o premendo **Ctrl + s**.

1. Aggiungere codice per convertire i numeri di telefono da alfanumerici a numerici. A tale scopo, aggiungere prima un nuovo file al progetto facendo clic con il pulsante destro del mouse sul progetto **Phoneword** in **Esplora soluzioni** e scegliendo **Aggiungi > Nuovo elemento... ** o premendo **Ctrl + Maiusc + A**:

    ![](hello-ios-quickstart-images/vs-image18.png "Add some code to translate phone numbers from alphanumeric to numeric")

1. Nella finestra di dialogo **Aggiungi nuovo elemento** (fare clic con il pulsante destro del mouse sul progetto, scegliere Aggiungi > Nuovo elemento) selezionare **Apple > Classe** e denominare il nuovo file `PhoneTranslator`:

    ![](hello-ios-quickstart-images/vs-image19.w157.png "Add a new class named PhoneTranslator")

    > [!IMPORTANT]
    > Assicurarsi di selezionare il modello "Classe" con C# nell'icona. In caso contrario non è possibile fare riferimento a questa nuova classe.

1. Viene creata una nuova classe C#. Rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Salvare il file **PhoneTranslator.cs** e chiuderlo.

1. Fare doppio clic su **ViewController.cs** in **Esplora soluzioni** per aprirlo e aggiungere la logica per la gestione delle interazioni con i pulsanti:

    ![](hello-ios-quickstart-images/vs-image20.png "Logic added to handle interactions with the buttons")

1. Iniziare associando `TranslateButton`. Nella classe **ViewController** trovare il metodo `ViewDidLoad`. Aggiungere il codice seguente per il pulsante all'interno di `ViewDidLoad`, sotto la chiamata a `base.ViewDidLoad()`:

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {

        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call", UIControlState.Normal);
            CallButton.Enabled = false;
            }
        else {
            CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
            CallButton.Enabled = true;
            }
    };
    ```

    Includere `using Phoneword;` se lo spazio dei nomi del file è diverso.

1. Aggiungere il codice relativo alla pressione dell'utente sul secondo pulsante, denominato `CallButton`. Inserire il codice seguente sotto il codice per `TranslateButton` e aggiungere `using Foundation;` all'inizio del file:

    ```csharp
    CallButton.TouchUpInside += (object sender, EventArgs e) => {
        var url = new NSUrl ("tel:" + translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app,
            // otherwise show an alert dialog

        if (!UIApplication.SharedApplication.OpenUrl (url)) {
                        var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                        alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        PresentViewController (alert, true, null);
                    }
    };
    ```

1. Salvare le modifiche e quindi compilare l'applicazione scegliendo **Compila > Compila soluzione** o premendo CTRL , MAIUSC e **B**.  Se l'applicazione viene compilata, verrà visualizzato un messaggio di operazione riuscita nella parte inferiore dell'IDE:

    ![](hello-ios-quickstart-images/vs-image21.png "A success message will appear at the bottom of the IDE")

    Se sono presenti errori, rivedere i passaggi precedenti e correggere eventuali errori fino a quando l'applicazione non viene compilata correttamente.

1. Testare infine l'applicazione nel **simulatore iOS remoto**. Sulla barra degli strumenti dell'IDE scegliere **Debug** e **iPhone 8 Plus iOS x.x** dai menu a discesa e premere **Avvia** (il triangolo verde simile al pulsante Riproduci):

    ![](hello-ios-quickstart-images/vs-image27.png "Press Start")

1. Verrà avviata l'applicazione all'interno del simulatore iOS:

    ![](hello-ios-quickstart-images/vs-image28.png "The application running inside the iOS Simulator")

    Le chiamate telefoniche non sono supportate nel simulatore iOS. Verrà invece visualizzata una finestra di dialogo di avviso quando si tenta di effettuare una chiamata:

    ![](hello-ios-quickstart-images/vs-image29.png "An alert dialog will display when trying to place a call")

::: zone-end

La prima applicazione Xamarin.iOS è stata completata.

È ora possibile analizzare nel dettaglio gli strumenti e le competenze illustrate in questa guida in [Hello, iOS: approfondimenti](~/ios/get-started/hello-ios/hello-ios-deepdive.md).

## <a name="related-links"></a>Collegamenti correlati

- [Icone e immagini di avvio e dell'app Xamarin (esempio)](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true)
- [Hello, iOS (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Linee guida dell'interfaccia umana iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portale di provisioning di iOS](https://developer.apple.com/ios/manage/overview/index.action)
