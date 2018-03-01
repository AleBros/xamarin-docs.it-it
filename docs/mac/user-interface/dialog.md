---
title: Finestre di dialogo
description: In questo articolo viene descritto l'utilizzo con le finestre di dialogo e finestre modali in un'applicazione Xamarin.Mac. Vengono descritte la creazione di finestre modali in Xcode e interfaccia generatore, utilizzo di finestre di dialogo standard e l'interazione con questi controlli nel codice c#.
ms.topic: article
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 9b65e870fae0074726d0bdd46d9eecbe99240e98
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="dialogs"></a>Finestre di dialogo

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere allo stesso finestre di dialogo e finestre modali che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire le finestre modali (o, facoltativamente, crearli direttamente nel codice c#).

Una finestra di dialogo viene visualizzato in risposta a un'azione dell'utente e in genere fornisce agli utenti di modi di completare l'azione. Una finestra di dialogo richiede una risposta da parte dell'utente prima di chiuderla.

Windows può essere utilizzato in uno stato non modale (ad esempio un editor di testo che può avere più documenti aperti contemporaneamente) o modale (ad esempio una finestra di dialogo di esportazione che deve essere chiusa prima di continua l'applicazione).

[ ![](dialog-images/dialog03.png "La finestra di dialogo Apri")](dialog-images/dialog03.png)

In questo articolo verranno descritte le nozioni di base dell'utilizzo di finestre di dialogo e finestre modali in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Introduzione alle finestre di dialogo

Una finestra di dialogo viene visualizzato in risposta a un'azione dell'utente (ad esempio, il salvataggio di un file) e fornisce un modo per gli utenti di completare tale operazione. Una finestra di dialogo richiede una risposta da parte dell'utente prima di chiuderla.

In base a Apple, sono disponibili tre modalità per presentare una finestra di dialogo:

- **Documento modale** -finestra di dialogo modale di un documento impedisce all'utente di apportare altre modifiche all'interno di un determinato documento fino a quando non viene chiuso.
- **App modale** : un'App di finestra di dialogo modale impedisce all'utente di interagire con l'applicazione fino a quando non viene chiuso.
- **Non modale** finestra di dialogo non modale consente agli utenti di modificare le impostazioni nella finestra di dialogo durante ancora l'interazione con la finestra del documento.

### <a name="modal-window"></a>Finestra modale

Qualsiasi standard `NSWindow` può essere utilizzato come una finestra di dialogo personalizzata e visualizzato come form modale:

[ ![](dialog-images/modal01.png "Una finestra modale di esempio")](dialog-images/modal01.png)

### <a name="document-modal-dialog-sheets"></a>Finestre delle finestre di dialogo modali documento

Oggetto _foglio_ è una finestra di dialogo modale che è collegato a una finestra del documento specificato, impedendo agli utenti di interagire con la finestra fino a quando non sono per chiudere la finestra di dialogo. Un foglio è collegato alla finestra da cui emerge e solo un foglio di lavoro può essere aperto per una finestra in qualsiasi momento.

[ ![](dialog-images/sheet08.png "Un esempio di foglio modale")](dialog-images/sheet08.png)

### <a name="preferences-windows"></a>Preferenze Windows

Una finestra Preferenze è una finestra di dialogo non modale che contiene le impostazioni dell'applicazione che l'utente vengono modificati raramente. Preferenze di Windows includono spesso una barra degli strumenti che consente all'utente di passare tra diversi gruppi di impostazioni:

[ ![](dialog-images/dialog02.png "Una finestra delle preferenze di esempio")](dialog-images/dialog02.png)

### <a name="open-dialog"></a>Finestra di dialogo aprire

La finestra di dialogo aprire concede agli utenti un modo coerenza per trovare e aprire un elemento in un'applicazione:

[ ![](dialog-images/dialog03.png "Una finestra di dialogo Apri")](dialog-images/dialog03.png)


### <a name="print-and-page-setup-dialogs"></a>Stampa e le finestre di dialogo Imposta pagina

Pagina Installazione finestre di dialogo che è possibile visualizzare in modo che gli utenti possono avere una stampa coerenza esperienza in tutte le applicazioni che utilizzano e macOS fornisce stampa standard.

La finestra di dialogo di stampa può essere visualizzata come entrambi libera mobile finestra di dialogo:

[ ![](dialog-images/print01.png "Una finestra di dialogo Stampa")](dialog-images/print01.png)

In alternativa, possono essere visualizzato come un foglio di:

[ ![](dialog-images/print02.png "Un foglio di stampato")](dialog-images/print02.png)

Finestra di dialogo Imposta pagina può essere visualizzata come entrambi libera mobile finestra di dialogo:

[ ![](dialog-images/print03.png "Una finestra di dialogo Imposta pagina")](dialog-images/print03.png)

In alternativa, possono essere visualizzato come un foglio di:

[ ![](dialog-images/print04.png "Un foglio di programma di installazione di pagina")](dialog-images/print04.png)

### <a name="save-dialogs"></a>Salvare le finestre di dialogo

La finestra di dialogo Salva offre agli utenti un modo coerenza per salvare un elemento in un'applicazione. La finestra di dialogo Salva dispone di due stati: **minima** (noto anche come compressa):

[ ![](dialog-images/save01.png "Una finestra di dialogo Salva")](dialog-images/save01.png)

E **espansa** stato:

[ ![](dialog-images/save02.png "Finestra di dialogo Salva espanso")](dialog-images/save02.png)

Il **minimo** Salva finestra di dialogo può anche essere visualizzato come un foglio di:

[ ![](dialog-images/save03.png "Un numero minimo di salvataggio foglio")](dialog-images/save03.png)

Che può essere il **espansa** Salva finestra di dialogo:

[ ![](dialog-images/save04.png "Salvare un espanso foglio")](dialog-images/save04.png)

Per ulteriori informazioni, vedere il [finestre di dialogo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) sezione di Apple [linee guida dell'interfaccia umana di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Aggiunta di una finestra modale a un progetto

A parte la finestra principale del documento, un'applicazione Xamarin.Mac potrebbe essere necessario visualizzare altri tipi di windows per l'utente, ad esempio le preferenze o pannelli di controllo.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **Esplora**, aprire il `Main.storyboard` file per la modifica in Generatore del Xcode di interfaccia.
2. Trascinare una nuova **View Controller** nell'area di progettazione:

    [ ![](dialog-images/new01.png "Selezione di un Controller di visualizzazione dalla libreria")](dialog-images/new01.png)
3. Nel **controllo di identità**, immettere `CustomDialogController` per il **nome classe**: 

    [ ![](dialog-images/new02.png "L'impostazione del nome di classe")](dialog-images/new02.png)
4. Tornare a Visual Studio per Mac, consentire la sincronizzazione con Xcode e creare il `CustomDialogController.h` file.
5. Tornare a Xcode e progettare l'interfaccia: 

    [ ![](dialog-images/new03.png "Progettazione dell'interfaccia utente in Xcode")](dialog-images/new03.png)
6. Creare un **definire modale** dalla finestra principale dell'app per il nuovo Controller di visualizzazione mediante il trascinamento di controllo dall'elemento dell'interfaccia utente che verrà aperta la finestra di dialogo alla finestra della finestra di dialogo. Assegnare il **identificatore** `ModalSegue`: 

    [ ![](dialog-images/new06.png "Segue un modale")](dialog-images/new06.png)
6. Rete per qualsiasi **azioni** e **prese**: 

    [ ![](dialog-images/new04.png "Configurazione di un'azione")](dialog-images/new04.png)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Rendere il `CustomDialogController.cs` file aspetto simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Questo codice espone alcune proprietà per impostare il titolo e la descrizione della finestra di dialogo e alcuni eventi per rispondere alla finestra di dialogo viene annullata o accettata.

Successivamente, modificare il `ViewController.cs` file, eseguire l'override di `PrepareForSegue` metodo e renderlo simile al seguente:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

Questo codice inizializza segue che è stato definito in Generatore del Xcode di interfaccia per la finestra di dialogo e imposta il titolo e descrizione. Gestisce inoltre la scelta che rende l'utente nella finestra di dialogo.

È possibile eseguire l'applicazione e visualizzare la finestra di dialogo personalizzata:

[ ![](dialog-images/new05.png "Una finestra di dialogo di esempio")](dialog-images/new05.png)

Per ulteriori informazioni sull'utilizzo di windows in un'applicazione Xamarin.Mac, consultare il nostro [utilizzo di finestre](~/mac/user-interface/window.md) documentazione.

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Creazione di un foglio di lavoro personalizzato

Oggetto _foglio_ è una finestra di dialogo modale che è collegato a una finestra del documento specificato, impedendo agli utenti di interagire con la finestra fino a quando non sono per chiudere la finestra di dialogo. Un foglio è collegato alla finestra da cui emerge e solo un foglio di lavoro può essere aperto per una finestra in qualsiasi momento. 

Per creare un foglio personalizzato in Xamarin.Mac, è possibile eseguire le operazioni seguenti:

1. Nel **Esplora**, aprire il `Main.storyboard` file per la modifica in Generatore del Xcode di interfaccia.
2. Trascinare una nuova **View Controller** nell'area di progettazione:

    [ ![](dialog-images/new01.png "Selezione di un Controller di visualizzazione dalla libreria")](dialog-images/new01.png)
2. Progettare l'interfaccia utente:

    [ ![](dialog-images/sheet01.png "La progettazione dell'interfaccia utente")](dialog-images/sheet01.png)
3. Creare un **foglio definire** dalla finestra di principale per il nuovo Controller di visualizzazione: 

    [ ![](dialog-images/sheet02.png "Selezione del tipo segue foglio")](dialog-images/sheet02.png)
4. Nel **controllo di identità**, denominare il Controller di visualizzazione **classe** `SheetViewController`: 

    [ ![](dialog-images/sheet03.png "L'impostazione del nome di classe")](dialog-images/sheet03.png)
5. Definire le esigenze **prese** e **azioni**: 

    [ ![](dialog-images/sheet04.png "Definire i punti vendita e le azioni necessarie")](dialog-images/sheet04.png)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.

Successivamente, modificare il `SheetViewController.cs` file e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Successivamente, modificare il `ViewController.cs` file, modificare il `PrepareForSegue` metodo e renderlo simile al seguente:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

Se si esegue l'applicazione e aprire la finestra, questo verrà collegato alla finestra:

[ ![](dialog-images/sheet08.png "Un esempio di foglio")](dialog-images/sheet08.png)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Creazione di una finestra di dialogo Preferenze

Prima è la visualizzazione delle preferenze nel generatore di interfaccia il layout, è necessario aggiungere un tipo segue personalizzato per gestire le preferenze per la disattivazione. Aggiungere una nuova classe al progetto e chiamarlo `ReplaceViewSeque `. Modificare la classe e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

Con la segue personalizzati creati, è possibile aggiungere una nuova finestra in Generatore del Xcode di interfaccia per gestire il nostro preferenze.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **Esplora**, aprire il `Main.storyboard` file per la modifica in Generatore del Xcode di interfaccia.
2. Trascinare una nuova **finestra Controller** nell'area di progettazione:

    [ ![](dialog-images/pref01.png "Selezionare un Controller di finestra dalla libreria")](dialog-images/pref01.png)
3. Disporre la finestra vicino il **barra dei Menu** progettazione:

    [ ![](dialog-images/pref02.png "Aggiunta della nuova finestra")](dialog-images/pref02.png)
4. Poiché vi saranno le schede nella pagina delle preferenze, creare copie del Controller di visualizzazione associata:

    [ ![](dialog-images/pref03.png "Aggiunta di controller di visualizzazione necessarie")](dialog-images/pref03.png)
5. Trascinare una nuova **Controller della barra degli strumenti** dal **libreria**:

    [ ![](dialog-images/pref04.png "Selezionare un Controller della barra degli strumenti dalla libreria")](dialog-images/pref04.png)
6. E rilasciarla nella finestra nell'area di progettazione:

    [ ![](dialog-images/pref05.png "Aggiunge un nuovo Controller della barra degli strumenti")](dialog-images/pref05.png)
7. La progettazione della barra degli strumenti layout:

    [ ![](dialog-images/pref06.png "La barra degli strumenti di layout")](dialog-images/pref06.png)
8. CTRL + clic e trascinare da ogni **pulsante della barra degli strumenti** per le visualizzazioni create in precedenza. Selezionare un **personalizzato** definire tipo:

    [ ![](dialog-images/pref07.png "L'impostazione del tipo segue")](dialog-images/pref07.png)
9. Selezionare il nuovo Segue e impostare il **classe** a `ReplaceViewSegue`:

    [ ![](dialog-images/pref08.png "L'impostazione della classe segue")](dialog-images/pref08.png)
10. Nel **Menubar progettazione** nell'area di progettazione, dal Menu dell'applicazione selezionare **preferenze...** , CTRL + clic e trascinare la finestra Preferenze per creare un **Mostra** definire:

    [ ![](dialog-images/pref09.png "L'impostazione del tipo segue")](dialog-images/pref09.png)
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.

Se si esegue il codice e selezionare il **preferenze...**  dal **Menu applicazione**, verrà visualizzata la finestra:

[ ![](dialog-images/pref10.png "Una finestra Preferenze di esempio")](dialog-images/pref10.png)

Per ulteriori informazioni sull'uso di Windows e le barre degli strumenti, vedere il nostro [Windows](~/mac/user-interface/window.md) e [barre degli strumenti](~/mac/user-interface/toolbar.md) documentazione.

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Salvataggio e caricamento preferenze

In un tipico macOS App, quando l'utente modifica le preferenze dell'utente dell'applicazione, le modifiche vengono salvate automaticamente. Il modo più semplice per gestire questo problema in un'app Xamarin.Mac, consiste nel creare un'unica classe per gestire tutte le preferenze dell'utente e condividere a livello di sistema.

In primo luogo, aggiungere un nuovo `AppPreferences` classe al progetto e di ereditare da `NSObject`. Sono progettate per utilizzare le preferenze [Data Binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md) che rendono il processo di creazione e gestione la preferenza di form molto più semplice. Poiché le preferenze saranno costituito da una piccola quantità di tipi di dati semplice, usare il `NSUserDefaults` per archiviare e recuperare i valori.

Modificare il `AppPreferences.cs` file e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLangauge")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLangauge", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLangauge");
                SaveInt ("DefaultLangauge", value, true);
                DidChangeValue ("DefaultLangauge");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

Questa classe contiene alcune routine di supporto, ad esempio `SaveInt`, `LoadInt`, `SaveColor`, `LoadColor`, e così via per lavorare con `NSUserDefaults` più semplice. Inoltre, dato che `NSUserDefaults` non dispone di un metodo incorporato per gestire `NSColors`, `NSColorToHexString` e `NSColorFromHexString` vengono utilizzati metodi per convertire i colori per le stringhe esadecimali basata sul web (`#RRGGBBAA` dove `AA` è la trasparenza alfa) che può essere facilmente archiviati e recuperati.

Nel `AppDelegate.cs` file, creare un'istanza di **AppPreferences** oggetto che verrà utilizzato a livello di app:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>Le preferenze per le visualizzazioni delle preferenze di cablaggio

Successivamente, connettersi classe preferenza elementi dell'interfaccia utente della finestra delle preferenze e le viste create in precedenza. In Generatore di interfaccia, selezionare un Controller di visualizzazione delle preferenze e passare il **controllo di identità**, creare una classe personalizzata per il controller: 

[ ![](dialog-images/prefs12.png "Il controllo di identità")](dialog-images/prefs12.png)

Tornare a Visual Studio per Mac sincronizzare le modifiche e aprire la classe appena creata per la modifica. Impostare la classe simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Si noti che questa classe ha svolto due cose qui: è un helper `App` proprietà per facilitare l'accesso di **AppDelegate** più semplice. In secondo luogo, il `Preferences` proprietà espone globale **AppPreferences** classe per l'associazione dati con controlli dell'interfaccia utente inserito in questa vista.

Successivamente, fare doppio clic il file dello Storyboard per aprire nuovamente nel generatore di interfaccia (e visualizzare le modifiche appena apportate in precedenza). Trascinare i controlli dell'interfaccia utente necessari per compilare l'interfaccia preferenze nella visualizzazione. Per ogni controllo, passare il **controllo associazione** e associare le singole proprietà del **AppPreference** classe:

[ ![](dialog-images/prefs13.png "Il controllo con associazione")](dialog-images/prefs13.png)

Ripetere i passaggi precedenti per tutti i pannelli (View Controller) e proprietà preferenza necessari.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>L'applicazione delle preferenze viene modificato in tutte le finestre aperte

Come descritto in precedenza, in un tipico macOS App, quando l'utente modifica le preferenze dell'utente dell'App, tali modifiche vengono salvati automaticamente e applicato a qualsiasi finestra l'utente potrebbe avere aperto nell'applicazione.

Un'attenta pianificazione e progettazione di windows e le preferenze dell'app consentirà di questo processo si verifica in modo uniforme e trasparente per l'utente finale, con una minima quantità di lavoro di codifica.

Per qualsiasi finestra che utilizzeranno le preferenze di App, aggiungere la seguente proprietà di supporto per il Controller di visualizzazione del contenuto per facilitare l'accesso il nostro **AppDelegate** più semplice:

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

Successivamente, aggiungere una classe per configurare il comportamento in base alle preferenze dell'utente o il contenuto:

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

È necessario chiamare il metodo di configurazione prima apertura di finestra per assicurarsi che sia conforme alle preferenze dell'utente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Successivamente, modificare il `AppDelegate.cs` file e aggiungere il metodo seguente per applicare le preferenze di modifica a tutte le finestre aperte:

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

Successivamente, aggiungere un `PreferenceWindowDelegate` classe al progetto e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWidowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWidowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

In questo modo le modifiche delle preferenze da inviare a tutte le finestre aperte quando la preferenza di finestra viene chiusa.

Infine, modificare il Controller di finestra Preferenze e aggiungere il delegato creato in precedenza:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWidowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Con tutte queste modifiche sul posto, se l'utente modifica le preferenze dell'App e chiude la finestra di preferenza, le modifiche verranno applicate a tutte le finestre aperte:

[ ![](dialog-images/prefs14.png "Una finestra Preferenze di esempio")](dialog-images/prefs14.png)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Finestra di dialogo aprire

La finestra di dialogo aprire offre agli utenti un modo coerenza per trovare e aprire un elemento in un'applicazione. Per visualizzare una finestra di dialogo Apri in un'applicazione Xamarin.Mac, utilizzare il codice seguente:

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

Nel codice precedente, si sta aprendo una nuova finestra del documento per visualizzare il contenuto del file. È necessario sostituire il codice con la funzionalità è richiesta dall'applicazione.

Le proprietà seguenti sono disponibili quando si lavora con un `NSOpenPanel`:

- **CanChooseFiles** - se `true` l'utente può selezionare i file.
- **CanChooseDirectories** - se `true` l'utente può selezionare una directory.
- **AllowsMultipleSelection** - se `true` l'utente può selezionare più di un file alla volta.
- **ResolveAliases** - se `true` selezionando e alias, risolve tale nome di percorso del file originale.
- **AllowedFileTypes** -è una matrice di stringhe di tipi di file che l'utente può selezionare come un'estensione di uno o _UTI_. Il valore predefinito è `null`, che consente a qualsiasi file da aprire.

Il `RunModal ()` metodo consente di visualizzare la finestra di dialogo Apri e consentire all'utente di selezionare i file o directory (come specificato dalle proprietà) e restituisce `1` se l'utente fa clic il **aprire** pulsante.

Finestra di dialogo Apri restituisce selezionati file o directory dell'utente come una matrice di URL nel `URL` proprietà.

Se si esegue il programma e selezionare il **Apri...**  articolo dal **File** visualizzazione del menu, le operazioni seguenti: 

[ ![](dialog-images/dialog03.png "La finestra di dialogo Apri")](dialog-images/dialog03.png)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>La stampa e le finestre di dialogo Imposta pagina

Pagina Installazione finestre di dialogo che è possibile visualizzare in modo che gli utenti possono avere una stampa coerenza esperienza in tutte le applicazioni che utilizzano e macOS fornisce stampa standard.

Il codice seguente mostra la finestra di dialogo Stampa standard:

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

Se si imposta la `ShowPrintAsSheet` proprietà `false`, eseguire l'applicazione e visualizzare la finestra di dialogo di stampa, verrà visualizzato quanto segue:

[ ![](dialog-images/print01.png "Una finestra di dialogo Stampa")](dialog-images/print01.png)

Se impostato il `ShowPrintAsSheet` proprietà `true`, eseguire l'applicazione e visualizzare la finestra di dialogo di stampa, verrà visualizzato quanto segue:

[ ![](dialog-images/print02.png "Un foglio di stampato")](dialog-images/print02.png)

Il seguente codice visualizzerà la finestra di dialogo di Layout di pagina:

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

Se si imposta la `ShowPrintAsSheet` proprietà `false`, eseguire l'applicazione e visualizzare la finestra di dialogo di layout di stampa, verrà visualizzato quanto segue:

[ ![](dialog-images/print03.png "Una finestra di dialogo Imposta pagina")](dialog-images/print03.png)

Se impostato il `ShowPrintAsSheet` proprietà `true`, eseguire l'applicazione e visualizzare la finestra di dialogo di layout di stampa, verrà visualizzato quanto segue:

[ ![](dialog-images/print04.png "Un foglio di programma di installazione di pagina")](dialog-images/print04.png)

Per ulteriori informazioni sull'utilizzo di stampa e le finestre di dialogo programma di installazione di pagina, vedere Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092), [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) e [Introduzione alla stampa](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1) documentazione.

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>Salva finestra di dialogo

La finestra di dialogo Salva offre agli utenti un modo coerenza per salvare un elemento in un'applicazione.

Il codice seguente mostra la finestra di dialogo Salva standard:

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

Il `AllowedFileTypes` proprietà è una matrice di tipi di file che l'utente può selezionare per salvare il file come stringhe. Il tipo di file possa essere specificato come un'estensione o _UTI_. Il valore predefinito è `null`, che consente di qualsiasi tipo di file da utilizzare.

Se si imposta la `ShowSaveAsSheet` proprietà `false`, eseguire l'applicazione e selezionare **Salva con nome...**  dal **File** menu, verrà visualizzato quanto segue:

[ ![](dialog-images/save01.png "Salvataggio la finestra di dialogo")](dialog-images/save01.png)

L'utente è possibile espandere la finestra di dialogo:

[ ![](dialog-images/save02.png "La finestra di dialogo di salvare un espanso")](dialog-images/save02.png)

Se si imposta la `ShowSaveAsSheet` proprietà `true`, eseguire l'applicazione e selezionare **Salva con nome...**  dal **File** menu, verrà visualizzato quanto segue:

[ ![](dialog-images/save03.png "Salvataggio foglio")](dialog-images/save03.png)

L'utente è possibile espandere la finestra di dialogo:

[ ![](dialog-images/save04.png "Salvare un espanso foglio")](dialog-images/save04.png)

Per ulteriori informazioni su come utilizzare la finestra di dialogo Salva, vedere Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di finestre modali, fogli e del sistema standard, finestre di dialogo in un'applicazione Xamarin.Mac un'analisi approfondita. È stato illustrato i diversi tipi e finestre modali, finestre e finestre di dialogo, viene utilizzato come creare e gestire le finestre modali e le finestre in Xcode del generatore di interfaccia e sull'utilizzo di finestre modali, finestre e finestre di dialogo nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menu](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Barre degli strumenti](~/mac/user-interface/toolbar.md)
- [Linee guida sull'interfaccia umane di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Introduzione agli elenchi](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Introduzione alla stampa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
