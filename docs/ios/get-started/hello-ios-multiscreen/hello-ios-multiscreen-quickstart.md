---
title: Hello, iOS Multiscreen - Guida introduttiva
description: Questo documento illustra come espandere l'applicazione di esempio Phoneword per aggiungere una seconda schermata, descrivendo lo schema MVC (Model, View, Controller), la navigazione in iOS e altri concetti di sviluppo di app iOS fondamentali.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: c89a3616bfa239ba919ae9750082bcef48c9f890
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73023364"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Hello, iOS Multiscreen - Guida introduttiva

In questa parte della procedura dettagliata viene aggiunta una seconda schermata all'applicazione Phoneword per consentire la visualizzazione di una cronologia dei numeri di telefono chiamati con l'app. L'applicazione finale avrà una seconda schermata contenente la cronologia delle chiamate, come illustrato nello screenshot seguente:

[![](hello-ios-multiscreen-quickstart-images/00.png "The final application will have a second screen that displays the call history, as illustrated by this screenshot")](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

Nell'[approfondimento accluso alla guida](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md) viene rivista l'applicazione compilata e vengono analizzati l'architettura, la navigazione e altri concetti nuovi di iOS incontrati nel corso della procedura.

## <a name="requirements"></a>Requisiti

Questa guida inizia dal punto in cui è terminato il documento Hello, iOS e richiede il completamento della [guida rapida Hello, iOS](~/ios/get-started/hello-ios/index.md). Scaricare la versione completa dell'app Phoneword dall'[esempio di Hello, iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>Procedura dettagliata in macOS

In questa procedura dettagliata verrà aggiunta la schermata Call History all'applicazione **Phoneword**.

1. Aprire l'applicazione **Phoneword** in Visual Studio per Mac. Se necessario, l'applicazione Phoneword completata dalla guida [dettagliata Hello, iOS](~/ios/get-started/hello-ios/index.md) può essere scaricata da [qui](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

2. Aprire il file **Main.storyboard** dal **riquadro della soluzione**:

    ![](hello-ios-multiscreen-quickstart-images/02new.png "The Main.storyboard in the iOS Designer")

3. Trascinare un controller di navigazione dalla **casella degli strumenti** nell'area di progettazione (potrebbe essere necessario eseguire lo zoom indietro per adattarli tutti nell'area di progettazione:Drag a navigation **controller** from the Toolbox onto the design surface (You might need to zoom out to fit these all on the design surface!):

    ![](hello-ios-multiscreen-quickstart-images/03new.png "Drag a navigation controller from the Toolbox onto the design surface")

4. Trascinare il **segue senza origine** (ovvero la freccia grigia a sinistra del controller di visualizzazione singola) nel controller di **navigazione** per modificare il punto iniziale dell'applicazione:

    ![](hello-ios-multiscreen-quickstart-images/04new.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. Selezionare il controller di **visualizzazione radice** esistente facendo clic sulla barra inferiore e premere **CANC** per rimuoverlo dall'area di progettazione.
Quindi, spostare la scena **Phoneword** accanto al **controller di navigazione**:

    ![](hello-ios-multiscreen-quickstart-images/05new.png "Move the Phoneword scene next to the navigation controller")

6. Impostare **ViewController** come controller di **visualizzazione radice**del controller di spostamento. Premere il tasto **Ctrl** e fare clic all'interno del **controller di navigazione**. Dovrebbe apparire una linea blu. Quindi, tenendo premuto il **tasto Ctrl,** trascinare dal controller di **navigazione** alla scena **Phoneword** e rilasciare. Questa operazione si chiama _trascinamento con CTRL_:

    ![](hello-ios-multiscreen-quickstart-images/06.png "Drag from the navigation controller to the Phoneword scene and release")

7. Nel popover impostare il tipo di relazione su **Radice**:

    ![](hello-ios-multiscreen-quickstart-images/07new.png "Setting the relationship to Root")

    **Il ViewController** è ora controller di visualizzazione radice del controller di spostamento:The ViewController is now the **navigation controller's Root view controller:**

    ![](hello-ios-multiscreen-quickstart-images/08.png "The ViewController is now the navigation controllers Root view controller")

8. Fare doppio clic sulla barra del **titolo** della schermata **Phoneword** e modificare il **titolo** in **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/09.png "Change the Title to 'Phoneword'")

9. Trascinare un **pulsante** dalla **casella degli strumenti** e posizionarlo sotto il **pulsante di chiamata**. Trascinare i quadratini di ridimensionamento per rendere la larghezza del nuovo **pulsante** uguale a quella del **pulsante di chiamata**:

    ![](hello-ios-multiscreen-quickstart-images/10new.png "Make the new Button the same width as the Call Button")

10. Nel **riquadro**proprietà modificare il **nome** del pulsante in **CallHistoryButton** e il **titolo** in **Cronologia chiamate**:

    ![](hello-ios-multiscreen-quickstart-images/11new.png "Change the Name of the Button to CallHistoryButton and change the Title to Call History")

11. Creare la schermata **Call History**. Dalla **Casella degli strumenti**trascinare un controller della **visualizzazione tabella** nell'area di progettazione:

    ![](hello-ios-multiscreen-quickstart-images/12new.png "Drag a table view controller onto the design surface")

12. Selezionare quindi il controller della **vista tabella** facendo clic sulla barra nera nella parte inferiore della scena. Nel **riquadro proprietà**, modificare la classe `CallHistoryController` del controller della visualizzazione **tabella** e premere **INVIO**:

    ![](hello-ios-multiscreen-quickstart-images/13new.png "Change the table view controllers class to CallHistoryController")

    iOS Designer genera una classe sottostante personalizzata denominata `CallHistoryController` per gestire la gerarchia delle visualizzazioni del contenuto di questa schermata. Il file **CallHistoryController.cs** viene visualizzato nel **riquadro della soluzione**:

    ![](hello-ios-multiscreen-quickstart-images/14new.png "The CallHistoryController.cs file in the Solution Pad")

13. Fare doppio clic sul file **CallHistoryController.cs** per aprirlo e sostituire il contenuto con il codice seguente:
    
    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword_iOS
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<string> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Salvare l'applicazione (**⌘ + s**) e compilarla (**⌘ + b**) per verificare che non siano presenti errori.

14. Creare un _segue_ (transizione) tra la scena **Phoneword** e la scena **Cronologia chiamate.**
  Nella **scena Phoneword**, selezionare il **pulsante Cronologia chiamate** e premere CTRL e trascinare dal **pulsante** alla scena **Cronologia chiamate:**

    ![](hello-ios-multiscreen-quickstart-images/15.png "Ctrl-drag from the Button to the Call History scene")

    Dal popover **Action Segue,** selezionare **Mostra**

    iOS Designer aggiungerà un elemento Segue tra le due scene:

    ![](hello-ios-multiscreen-quickstart-images/17new.png "The Segue between the two scenes")

15. Aggiungere un **titolo** al controller di **visualizzazione tabella** selezionando la barra nera nella parte inferiore della scena e modificando il titolo del controller di **visualizzazione** in **Cronologia chiamate** nel **riquadro Proprietà**:

    ![](hello-ios-multiscreen-quickstart-images/18new.png "Change the view controller title to Call History in the Properties Pad")

16. Quando viene eseguita l'applicazione, il **pulsante Registro chiamate** apre la schermata **Registro chiamate**, ma la visualizzazione tabella sarà vuota perché non è presente un codice di cui tenere traccia e che visualizzi i numeri di telefono.

    Questa app archivierà i numeri di telefono come un elenco di stringhe.

    Aggiungere `using` una `System.Collections.Generic` direttiva per nella parte superiore di **ViewController**:

    ```csharp
    using System.Collections.Generic;
    ```

17. Modificare la classe `ViewController` usando il codice seguente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    È opportuno fare alcune considerazioni:

    - La variabile `translatedNumber` è stata spostata dal metodo `ViewDidLoad` a una _variabile a livello di classe_.
    - Il codice **CallButton** è stato modificato per aggiungere i numeri di telefono composti all'elenco dei numeri di telefono chiamando `PhoneNumbers.Add(translatedNumber)`.
    - È stato aggiunto il metodo `PrepareForSegue`.

    Salvare e compilare l'applicazione per verificare che non ci siano errori.

18. Premere il pulsante **Start** per avviare l'applicazione all'interno del **simulatore iOS**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "Press the Start button to launch the application inside the iOS Simulator")

L'applicazione Xamarin.iOS Multiscreen è stata completata.

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Procedura dettagliata su Windows

In questa procedura dettagliata verrà aggiunta la schermata Call History all'applicazione **Phoneword**.

1. Aprire l'applicazione **Phoneword** in Visual Studio. Se necessario, scaricare l'[applicazione Phoneword completata](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) dalla [procedura dettagliata Hello, iOS](~/ios/get-started/hello-ios/index.md). Tenere presente che è necessario connettersi a un [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per usare iOS Designer e il simulatore iOS.

2. Iniziare modificando l'interfaccia utente. Aprire il file **Main.storyboard** dal **riquadro della soluzione**, verificando che **Visualizza come** sia impostato su _iPhone 6_:

    ![](hello-ios-multiscreen-quickstart-images/image1.png "The Main.storyboard in the iOS Designer")

3. Trascinare un controller di navigazione dalla **casella degli strumenti** nell'area di progettazione:Drag a navigation **controller** from the Toolbox onto the design surface:

    ![](hello-ios-multiscreen-quickstart-images/image2.png "Drag a navigation controller from the Toolbox onto the design surface")

4. Trascinare il **Segue senza origine** (ovvero la freccia grigia a sinistra della scena **Phoneword)** dalla scena **Phoneword** al controller di **navigazione** per modificare il punto di partenza dell'applicazione:

    ![](hello-ios-multiscreen-quickstart-images/image3.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. Selezionare il **controller di visualizzazione radice** facendo clic sulla barra nera e premere **CANC** per rimuoverlo dall'area di progettazione.
  Quindi, spostare la scena **Phoneword** accanto al **controller di navigazione**:

    ![](hello-ios-multiscreen-quickstart-images/image4.png "Move the Phoneword scene next to the navigation controller")

6. Impostare il **ViewController** come controller di visualizzazione radice del controller di navigazione. Premere il tasto **CTRL** e fare clic all'interno del **controller di spostamento**. Dovrebbe apparire una linea blu. Quindi, tenendo premuto il **tasto Ctrl,** trascinare dal controller di **navigazione** alla scena **Phoneword** e rilasciare. Questa operazione si chiama _trascinamento con CTRL_:

    ![](hello-ios-multiscreen-quickstart-images/image5.png "Drag from the navigation controller to the Phoneword scene and release")

7. Nel popover impostare il tipo di relazione su **Radice**:

    ![](hello-ios-multiscreen-quickstart-images/image6.png "Set the relationship to Root")

    Il **ViewController** è ora il controller di visualizzazione radice del controller di **spostamento.**

8. Fare doppio clic sulla barra del **titolo** della schermata **Phoneword** e modificare il **titolo** in **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/image7.png "Change the Title to Phoneword")

9. Trascinare un **pulsante** dalla **casella degli strumenti** e posizionarlo sotto il **pulsante di chiamata**. Trascinare i quadratini di ridimensionamento per rendere la larghezza del nuovo **pulsante** uguale a quella del **pulsante di chiamata**:

    ![](hello-ios-multiscreen-quickstart-images/image8.png "Make the new Button the same width as the Call Button")

10. Nel **riquadro delle proprietà** modificare il **nome** del **pulsante** in `CallHistoryButton` e modificare il **titolo** in **Call History**:

    ![](hello-ios-multiscreen-quickstart-images/image9.png "Change the Name of the Button to 'CallHistoryButton' and the Title to 'Call History'")

11. Creare la schermata **Call History**. Dalla **Casella degli strumenti**trascinare un controller della **visualizzazione tabella** nell'area di progettazione:

    ![](hello-ios-multiscreen-quickstart-images/image10.png "Drag a table view controller onto the design surface")

12. Selezionare il controller della **vista tabella** facendo clic sulla barra nera nella parte inferiore della scena. In **Esplora proprietà**modificare la classe **del controller del controller della visualizzazione tabella** `CallHistoryController` e premere **INVIO:**

    ![](hello-ios-multiscreen-quickstart-images/image11.png "Change the table view controllers class to CallHistoryController")

    iOS Designer genera una classe sottostante personalizzata denominata `CallHistoryController` per gestire la gerarchia delle visualizzazioni del contenuto di questa schermata. Il file **CallHistoryController.cs** viene visualizzato nel **riquadro della soluzione**:

    ![](hello-ios-multiscreen-quickstart-images/image12.png "The CallHistoryController.cs file in the Solution Explorer")

13. Fare doppio clic sul file **CallHistoryController.cs** per aprirlo e sostituire il contenuto con il codice seguente:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<String> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                // Returns the number of rows in each section of the table
                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Salvare l'applicazione e compilarla per verificare che non siano presenti errori. Per il momento si possono ignorare gli avvisi relativi alla compilazione.

14. Creare un _segue_ (transizione) tra la scena **Phoneword** e la scena **Cronologia chiamate.**
  Nella **scena Phoneword**, selezionare il **pulsante Cronologia chiamate** e **premere CTRL e trascinare** dal **pulsante** alla scena **Cronologia chiamate:**

    ![](hello-ios-multiscreen-quickstart-images/image13.png "Ctrl-drag from the Button to the Call History scene")

    Selezionare **Visualizza** nel popover **Action Segue**:

    ![](hello-ios-multiscreen-quickstart-images/image14.png "Select Show as the segue type")

    iOS Designer aggiungerà un elemento Segue tra le due scene:

    ![](hello-ios-multiscreen-quickstart-images/image15.png "The Segue between the two scenes")

15. Aggiungere un **titolo** al controller di **visualizzazione tabella** selezionando la barra nera nella parte inferiore della scena e modificando il controller di visualizzazione **> Titolo** alla cronologia delle **chiamate** in **Esplora proprietà**:

    ![](hello-ios-multiscreen-quickstart-images/image16.png "Change the view controller Title to Call History")

16. Quando viene eseguita l'applicazione, il **pulsante Registro chiamate** apre la schermata **Registro chiamate**, ma la visualizzazione tabella sarà vuota perché non è presente un codice di cui tenere traccia e che visualizzi i numeri di telefono.

    Questa app archivierà i numeri di telefono come un elenco di stringhe.

    Aggiungere una direttiva `using` per `System.Collections.Generic` nella parte superiore di **ViewController**:

    ```csharp
    using System.Collections.Generic;
    ```

17. Modificare la classe `ViewController` usando il codice seguente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    È opportuno fare alcune considerazioni
    - La variabile `translatedNumber` è stata spostata dal metodo `ViewDidLoad` a una _variabile a livello di classe_.
    - Il codice **CallButton** è stato modificato per aggiungere i numeri di telefono composti all'elenco dei numeri di telefono chiamando `PhoneNumbers.Add(translatedNumber)`
    - È stato aggiunto il metodo `PrepareForSegue`

    Salvare e compilare l'applicazione per verificare che non ci siano errori.

    Salvare e compilare l'applicazione per verificare che non ci siano errori.

18. Premere il pulsante di **avvio** per avviare l'applicazione all'interno del **simulatore iOS**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "The first screen of the sample app")

L'applicazione Xamarin.iOS Multiscreen è stata completata.

::: zone-end

L'app è ora in grado di gestire la navigazione usando gli elementi Storyboard Segue e il codice. È ora possibile analizzare gli strumenti e le competenze appena acquisite nell'[approfondimento su Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md).

## <a name="related-links"></a>Collegamenti correlati

- [Hello, iOS (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Linee guida dell'interfaccia umana iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portale di provisioning di iOS](https://developer.apple.com/ios/manage/overview/index.action)
