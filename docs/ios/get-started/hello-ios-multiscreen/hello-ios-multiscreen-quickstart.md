---
title: Hello, iOS Multiscreen
description: "In questa guida divisa in due parti l'applicazione Phoneword creata nella guida Hello, iOS viene espansa in modo da gestire una seconda schermata. Viene presentato anche lo schema progettuale MVC (Model-View-Controller), viene implementata la prima navigazione iOS e vengono illustrate in modo più approfondito la struttura e la funzionalità dell'applicazione iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/02/2016
ms.openlocfilehash: d4b7439448d5c25aacf991c89a188086c95a44ac
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="helloios-multiscreen-quickstart"></a>Guida introduttiva di Hello.iOS Multiscreen

In questa parte della procedura dettagliata viene aggiunta una seconda schermata all'applicazione Phoneword per consentire la visualizzazione di una cronologia dei numeri di telefono chiamati con l'app. L'applicazione finale avrà una seconda schermata contenente la cronologia delle chiamate, come illustrato nello screenshot seguente:

 [![](hello-ios-multiscreen-quickstart-images/00.png "L'applicazione finale avrà una seconda schermata contenente la cronologia delle chiamate, come illustrato in questo screenshot")](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

Nell'[approfondimento accluso alla guida](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md) viene rivista l'applicazione compilata e vengono analizzati l'architettura, la navigazione e altri concetti nuovi di iOS incontrati nel corso della procedura.

 <a name="Requirements" />

## <a name="requirements"></a>Requisiti

Questa guida inizia dal punto in cui è terminato il documento Hello, iOS e richiede il completamento della [guida rapida Hello, iOS](~/ios/get-started/hello-ios/index.md). La versione completa dell'app Phoneword può essere scaricata dall'[esempio di Hello, iOS](https://developer.xamarin.com/samples/monotouch/Hello_iOS/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="walkthrough"></a>Procedura dettagliata

In questa procedura dettagliata verrà aggiunta la schermata Call History all'applicazione **Phoneword**.


1. Aprire l'applicazione **Phoneword** in Visual Studio per Mac. Se necessario, è possibile scaricare l'applicazione Phoneword completata nella [procedura dettagliata Hello, iOS](~/ios/get-started/hello-ios/index.md) da [qui](https://developer.xamarin.com/samples/monotouch/Hello_iOS/).


2. Aprire il file **Main.storyboard** dal **riquadro della soluzione**:

  ![](hello-ios-multiscreen-quickstart-images/02new.png "Main.storyboard in iOS Designer")


3. Trascinare un **controller di spostamento** dalla **casella degli strumenti** all'area di progettazione. Può essere necessario eseguire lo zoom indietro per visualizzare tutti gli elementi nell'area di progettazione:

  ![](hello-ios-multiscreen-quickstart-images/03new.png "Trascinare un controller di spostamento dalla casella degli strumenti nell'area di progettazione")


4. Trascinare il **Sourceless Segue**, ovvero la freccia grigia a sinistra del controller di visualizzazione singolo, al **controller di spostamento** per modificare il punto iniziale dell'applicazione:

  ![](hello-ios-multiscreen-quickstart-images/04new.png "Trascinare il Sourceless Segue nel controller di navigazione per modificare il punto iniziale dell'applicazione")


5. Selezionare il **controller visualizzazione radice** esistente facendo clic sulla barra in basso e premere **Elimina** per rimuoverlo dall'area di progettazione.
Spostare la scena **Phoneword** accanto al **controller di spostamento**:

  ![](hello-ios-multiscreen-quickstart-images/05new.png "Spostare la scena Phoneword accanto al controller di spostamento")


6. Impostare **ViewController** come **controller visualizzazione radice** del controller di spostamento. Premere il tasto **CTRL** e fare clic all'interno del **controller di spostamento**. Dovrebbe apparire una linea blu. Quindi, continuando a tenere premuto il tasto **CTRL**, trascinare dal **controller di spostamento** alla scena **Phoneword** e rilasciare. Questa operazione si chiama _trascinamento con CTRL_:

 ![](hello-ios-multiscreen-quickstart-images/06.png "Trascinare dal controller di spostamento alla scena Phoneword e rilasciare")


7. Nel popover impostare il tipo di relazione su **Radice**:

  ![](hello-ios-multiscreen-quickstart-images/07new.png "Impostare la relazione su Radice")

  **ViewController** ora è il **controller visualizzazione radice del controller di spostamento**:

  ![](hello-ios-multiscreen-quickstart-images/08.png "ViewController ora è il controller visualizzazione radice del controller di spostamento")


8. Fare doppio clic sulla barra del **titolo** della schermata **Phoneword** e modificare il **titolo** in **Phoneword**:

  ![](hello-ios-multiscreen-quickstart-images/09.png "Modificare il titolo in "Phoneword"")


9. Trascinare un **pulsante** dalla **casella degli strumenti** e posizionarlo sotto il **pulsante di chiamata**. Trascinare i quadratini di ridimensionamento per rendere la larghezza del nuovo **pulsante** uguale a quella del **pulsante di chiamata**:

  ![](hello-ios-multiscreen-quickstart-images/10new.png "Rendere la larghezza del nuovo pulsante uguale a quella del pulsante di chiamata")


10. Nel **riquadro delle proprietà** modificare il **nome** del pulsante in **CallHistoryButton** e modificare il **titolo** in **Call History**:

  ![](hello-ios-multiscreen-quickstart-images/11new.png "Modificare il nome del pulsante in CallHistoryButton e modificare il titolo in Call History")


11. Creare la schermata **Call History**. Trascinare un **controller visualizzazione tabella** dalla **casella degli strumenti** nell'area di progettazione:

 ![](hello-ios-multiscreen-quickstart-images/12new.png "Trascinare un controller visualizzazione tabella nell'area di progettazione")


12. Selezionare quindi il **controller visualizzazione tabella** facendo clic sulla barra nera nella parte inferiore della scena. Nel **riquadro delle proprietà** modificare la classe del **controller visualizzazione tabella** in `CallHistoryController` e premere **INVIO**:

  ![](hello-ios-multiscreen-quickstart-images/13new.png "Modificare la classe del controller di visualizzazione tabella in CallHistoryController")

  La finestra di progettazione iOS genera una classe sottostante personalizzata denominata `CallHistoryController` per gestire la gerarchia delle visualizzazioni del contenuto di questa schermata.
  Il file **CallHistoryController.cs** viene visualizzato nel **riquadro della soluzione**:

  ![](hello-ios-multiscreen-quickstart-images/14new.png "Il file CallHistoryController.cs viene visualizzato nel riquadro della soluzione")


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


14. Creare un _elemento Segue_ (transizione) tra la scena **Phoneword** e la scena **Call History**.
  Nella scena **Phoneword** selezionare il **pulsante Call History** e trascinare premendo CTRL dal **pulsante** alla scena **Call History**:

  ![](hello-ios-multiscreen-quickstart-images/15.png "Premere CTRL e trascinare dal pulsante alla scena Call History")

  Selezionare **Visualizza** nel popover **Action Segue**

  iOS Designer aggiungerà un elemento Segue tra le due scene:

  ![](hello-ios-multiscreen-quickstart-images/17new.png "Elemento Segue tra le due scene")


15. Aggiungere un **titolo** al **controller visualizzazione tabella** selezionando la barra nera nella parte inferiore della scena e modificando il **titolo del controller visualizzazione** in **Call History** nel **riquadro delle proprietà**:

  ![](hello-ios-multiscreen-quickstart-images/18new.png "Modificare il titolo del controller visualizzazione in Call History nel riquadro delle proprietà")

16. Quando viene eseguita l'applicazione, il **pulsante Call History** apre la schermata **Call History**, ma la visualizzazione tabella sarà vuota perché non è presente un codice di cui tenere traccia e che visualizzi i numeri di telefono.

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

          // set the View Controller that’s powering the screen we’re
          // transitioning to

          var callHistoryContoller = segue.DestinationViewController as CallHistoryController;

          //set the Table View Controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryContoller != null)
          {
            callHistoryContoller.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```


È opportuno fare alcune considerazioni
  * La variabile `translatedNumber` è stata spostata dal metodo `ViewDidLoad` a una _variabile a livello di classe_.
  * Il codice **CallButton** è stato modificato per aggiungere i numeri di telefono composti all'elenco dei numeri di telefono chiamando `PhoneNumbers.Add(translatedNumber)`
  * È stato aggiunto il metodo `PrepareForSegue`

  Salvare e compilare l'applicazione per verificare che non ci siano errori.

20. Premere il pulsante di **avvio** per avviare l'applicazione all'interno del **simulatore iOS**:

  ![](hello-ios-multiscreen-quickstart-images/19.png "Premere il pulsante di avvio per avviare l'applicazione all'interno del simulatore iOS")


L'applicazione Xamarin.iOS Multiscreen è stata completata.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="walkthrough"></a>Procedura dettagliata

In questa procedura dettagliata verrà aggiunta la schermata Call History all'applicazione **Phoneword**.


1. Aprire l'applicazione **Phoneword** in Visual Studio. Se necessario, scaricare l'[applicazione Phoneword completata](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) dalla [procedura dettagliata Hello, iOS](~/ios/get-started/hello-ios/index.md). Tenere presente che è necessario connettersi a un [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per usare iOS Designer e il simulatore iOS.


2. Iniziare modificando l'interfaccia utente. Aprire il file **Main.storyboard** dal **riquadro della soluzione**, verificando che **Visualizza come** sia impostato su _iPhone 6_:

  ![](hello-ios-multiscreen-quickstart-images/image1.png "Main.storyboard in iOS Designer")


3. Trascinare un **controller di spostamento** dalla **casella degli strumenti** nell'area di progettazione:

  ![](hello-ios-multiscreen-quickstart-images/image2.png "Trascinare un controller di spostamento dalla casella degli strumenti nell'area di progettazione")


4. Trascinare il **Sourceless Segue**, ovvero la freccia grigia a sinistra della scena **Phoneword** dalla scena **Phoneword** al **controller di spostamenti** per modificare il punto iniziale dell'applicazione:

  ![](hello-ios-multiscreen-quickstart-images/image3.png "Trascinare il Sourceless Segue nel controller di navigazione per modificare il punto iniziale dell'applicazione")


5. Selezionare il **controller visualizzazione radice** facendo clic sulla barra nera e premere **Elimina** per rimuoverlo dall'area di progettazione.
  Spostare la scena **Phoneword** accanto al **controller di spostamento**:

  ![](hello-ios-multiscreen-quickstart-images/image4.png "Spostare la scena Phoneword accanto al controller di spostamento")


6. Impostare **ViewController** come `Root View Controller` del controller di spostamento. Premere il tasto **CTRL** e fare clic all'interno del **controller di spostamento**. Dovrebbe apparire una linea blu. Quindi, continuando a tenere premuto il tasto **CTRL**, trascinare dal **controller di spostamento** alla scena **Phoneword** e rilasciare. Questa operazione si chiama _trascinamento con CTRL_:

  ![](hello-ios-multiscreen-quickstart-images/image5.png "Trascinare dal controller di spostamento alla scena Phoneword e rilasciare")


7. Nel popover impostare il tipo di relazione su **Radice**:

  ![](hello-ios-multiscreen-quickstart-images/image6.png "Impostare la relazione su Radice")

  **ViewController** ora è il **controller visualizzazione radice del controller di spostamento.**


8. Fare doppio clic sulla barra del **titolo** della schermata **Phoneword** e modificare il **titolo** in **Phoneword**:

  ![](hello-ios-multiscreen-quickstart-images/image7.png "Modificare il titolo in Phoneword")


9. Trascinare un **pulsante** dalla **casella degli strumenti** e posizionarlo sotto il **pulsante di chiamata**. Trascinare i quadratini di ridimensionamento per rendere la larghezza del nuovo **pulsante** uguale a quella del **pulsante di chiamata**:

  ![](hello-ios-multiscreen-quickstart-images/image8.png "Rendere la larghezza del nuovo pulsante uguale a quella del pulsante di chiamata")


10. Nel **riquadro delle proprietà** modificare il **nome** del **pulsante** in `CallHistoryButton` e modificare il **titolo** in **Call History**:

  ![](hello-ios-multiscreen-quickstart-images/image9.png "Modificare il nome del pulsante in "CallHistoryButton" e modificare il titolo in "Call History"")


11. Creare la schermata **Call History**. Trascinare un **controller visualizzazione tabella** dalla **casella degli strumenti** nell'area di progettazione:

  ![](hello-ios-multiscreen-quickstart-images/image10.png "Trascinare un controller visualizzazione tabella nell'area di progettazione")


12. Selezionare il **controller visualizzazione tabella** facendo clic sulla barra nera nella parte inferiore della scena. Nel **riquadro delle proprietà** modificare la classe del **controller visualizzazione tabella** in `CallHistoryController` e premere **INVIO**:

  ![](hello-ios-multiscreen-quickstart-images/image11.png "Modificare la classe del controller di visualizzazione tabella in CallHistoryController")

  La finestra di progettazione iOS genera una classe sottostante personalizzata denominata `CallHistoryController` per gestire la gerarchia delle visualizzazioni del contenuto di questa schermata.
  Il file **CallHistoryController.cs** viene visualizzato nel **riquadro della soluzione**:

  ![](hello-ios-multiscreen-quickstart-images/image12.png "Il file CallHistoryController.cs viene visualizzato nel riquadro della soluzione")


13. Fare doppio clic sul file **CallHistoryController.cs** per aprirlo e sostituire il contenuto con il codice seguente:

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

  Salvare l'applicazione e compilarla per verificare che non siano presenti errori. Per il momento si possono ignorare gli avvisi relativi alla compilazione.


14. Creare un _elemento Segue_ (transizione) tra la scena **Phoneword** e la scena **Call History**.
  Nella **scena Phoneword** selezionare il **pulsante Call History** e **trascinare premendo CTRL** dal **pulsante** alla scena **Call History**:

  ![](hello-ios-multiscreen-quickstart-images/image13.png "Premere CTRL e trascinare dal pulsante alla scena Call History")

  Selezionare **Visualizza** nel popover **Action Segue**:

  ![](hello-ios-multiscreen-quickstart-images/image14.png "Selezionare Visualizza come tipo di elemento Segue")

  iOS Designer aggiungerà un elemento Segue tra le due scene:

  ![](hello-ios-multiscreen-quickstart-images/image15.png "Elemento Segue tra le due scene")


15. Aggiungere un **titolo** al **controller visualizzazione tabella** selezionando la barra nera nella parte inferiore della scena e modificando il **titolo del controller visualizzazione** in **Call History** nel **riquadro delle proprietà**:

  ![](hello-ios-multiscreen-quickstart-images/image16.png "Modificare il titolo del controller visualizzazione in Call History nel riquadro delle proprietà")


16. Quando viene eseguita l'applicazione, il **pulsante Call History** apre la schermata **Call History**, ma la visualizzazione tabella sarà vuota perché non è presente un codice di cui tenere traccia e che visualizzi i numeri di telefono.

  Questa app archivierà i numeri di telefono come un elenco di stringhe.

  Aggiungere una direttiva `using` per `System.Collections.Generic` nella parte superiore di **ViewController**:

        using System.Collections.Generic;

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

          // set the View Controller that’s powering the screen we’re
          // transitioning to

          var callHistoryContoller = segue.DestinationViewController as CallHistoryController;

          //set the Table View Controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryContoller != null)
          {
            callHistoryContoller.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

È opportuno fare alcune considerazioni
  * La variabile `translatedNumber` è stata spostata dal metodo `ViewDidLoad` a una _variabile a livello di classe_.
  * Il codice **CallButton** è stato modificato per aggiungere i numeri di telefono composti all'elenco dei numeri di telefono chiamando `PhoneNumbers.Add(translatedNumber)`
  * È stato aggiunto il metodo `PrepareForSegue`

  Salvare e compilare l'applicazione per verificare che non ci siano errori.

  Salvare e compilare l'applicazione per verificare che non ci siano errori.


20. Premere il pulsante di **avvio** per avviare l'applicazione all'interno del **simulatore iOS**:

  ![](hello-ios-multiscreen-quickstart-images/19.png "La prima schermata dell'app di esempio")


L'applicazione Xamarin.iOS Multiscreen è stata completata.


-----

L'app è ora in grado di gestire la navigazione usando gli elementi Storyboard Segue e il codice. È ora possibile analizzare gli strumenti e le competenze appena acquisite nell'[approfondimento su Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md).


## <a name="related-links"></a>Collegamenti correlati

- [Hello, iOS (sample)](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) (Hello, iOS - Esempio)
- [Linee guida dell'interfaccia umana iOS](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portale di provisioning iOS](https://developer.apple.com/ios/manage/overview/index.action)
