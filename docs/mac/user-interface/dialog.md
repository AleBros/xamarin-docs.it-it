---
title: Finestre di dialogo in xamarin. Mac
description: Questo articolo descrive l'uso delle finestre di dialogo e finestre modali in un'applicazione xamarin. Mac. Descrive la creazione di finestre modali in Xcode e Interface builder, utilizzo di finestre di dialogo standard e l'interazione con questi controlli nel codice c#.
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f53c1afd7c3f835bff2aae507b9937067bde4bec
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865742"
---
# <a name="dialogs-in-xamarinmac"></a>Finestre di dialogo in xamarin. Mac

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare le stesse finestre di dialogo e modale di Windows che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire il Windows modale (oppure crearle direttamente nel codice c#).

Una finestra di dialogo viene visualizzata in risposta a un'azione dell'utente e in genere fornisce agli utenti di modi possano completare l'azione. Una finestra di dialogo richiede una risposta da parte dell'utente prima di chiuderla.

Windows può essere usato in uno stato non modale (ad esempio un editor di testo che è possibile avere più documenti aperti in una sola volta) o modale (ad esempio, una finestra di dialogo di esportazione che deve essere chiusa prima che l'applicazione può continuare).

[![](dialog-images/dialog03.png "La finestra di dialogo Apri")](dialog-images/dialog03.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di finestre di dialogo e Windows modali in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Introduzione alle finestre di dialogo

Una finestra di dialogo viene visualizzata in risposta a un'azione dell'utente (ad esempio, il salvataggio di un file) e fornisce un modo per gli utenti completare l'operazione. Una finestra di dialogo richiede una risposta da parte dell'utente prima di chiuderla.

In base a Apple, esistono tre modi per presentare una finestra di dialogo:

- **Documentare modale** -finestra di dialogo modale di un documento impedisce all'utente di qualunque altra all'interno di un determinato documento fino a quando non viene chiuso.
- **App modale** : un'App finestra di dialogo modale impedisce all'utente di interagire con l'applicazione fino a quando non viene chiuso.
- **Non modale** finestra di dialogo non modale consente agli utenti di modificare le impostazioni nella finestra di dialogo durante comunque l'interazione con la finestra del documento.

### <a name="modal-window"></a>Finestra modale

Qualsiasi standard `NSWindow` può essere utilizzato come una finestra di dialogo personalizzata visualizzando come modale:

[![](dialog-images/modal01.png "Una finestra modale di esempio")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Fogli di finestra di dialogo modale di documento

Oggetto _foglio_ è una finestra di dialogo modale che è collegato a una finestra del documento specificato, impedendo agli utenti di interagire con la finestra fino a quando non sono per chiudere la finestra di dialogo. Un foglio è collegato alla finestra da cui emerge e un solo foglio può essere aperto per una finestra in qualsiasi momento.

[![](dialog-images/sheet08.png "Un esempio di foglio modale")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Windows le preferenze

Una finestra Preferenze è una finestra di dialogo non modale che contiene le impostazioni dell'applicazione che l'utente vengono modificati raramente. Le preferenze di Windows include spesso una barra degli strumenti che consente all'utente di spostarsi tra i diversi gruppi di impostazioni:

[![](dialog-images/dialog02.png "Una finestra delle preferenze di esempio")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Finestra di dialogo aprire

La finestra di dialogo aprire offre agli utenti un modo coerente per trovare e aprire un elemento in un'applicazione:

[![](dialog-images/dialog03.png "Una finestra di dialogo Apri")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>Stampa e le finestre di dialogo Imposta pagina

macOS fornisce stampa standard e pagina di installazione di finestre di dialogo che è possibile visualizzare in modo che gli utenti possono avere una stampa coerente esperienza in tutte le applicazioni che usano.

La finestra di dialogo di stampa può essere visualizzato come entrambi una finestra di dialogo gratuitamente a virgola mobile:

[![](dialog-images/print01.png "Una finestra di dialogo Stampa")](dialog-images/print01.png#lightbox)

In alternativa, può essere visualizzato come un foglio di:

[![](dialog-images/print02.png "Un foglio di stampa")](dialog-images/print02.png#lightbox)

Finestra di dialogo Imposta pagina possono essere visualizzato come entrambi una finestra di dialogo gratuitamente a virgola mobile:

[![](dialog-images/print03.png "Una finestra di dialogo Imposta pagina")](dialog-images/print03.png#lightbox)

In alternativa, può essere visualizzato come un foglio di:

[![](dialog-images/print04.png "Un foglio di programma di installazione di pagina")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Salvare le finestre di dialogo

La finestra di dialogo Salva offre agli utenti un modo coerente per salvare un elemento in un'applicazione. La finestra di dialogo Salva dispone di due stati: **Minimo** (noto anche come compressa):

[![](dialog-images/save01.png "Una finestra di dialogo Salva")](dialog-images/save01.png#lightbox)

E il **espansa** stato:

[![](dialog-images/save02.png "Finestra di dialogo Salva espanso")](dialog-images/save02.png#lightbox)

Il **minimo** finestra di dialogo Salva inoltre possono essere visualizzato come un foglio di:

[![](dialog-images/save03.png "Un minimo di salvataggio foglio")](dialog-images/save03.png#lightbox)

Come può il **espansa** Salva finestra di dialogo:

[![](dialog-images/save04.png "Salvare un espanso foglio")](dialog-images/save04.png#lightbox)

Per altre informazioni, vedere la [Dialogs](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Aggiunta di una finestra modale a un progetto

A parte la finestra del documento principale, un'applicazione xamarin. Mac potrebbe essere necessario visualizzare altri tipi di windows per l'utente, ad esempio le preferenze o i pannelli di controllo.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, aprire il `Main.storyboard` file per la modifica in Interface Builder di Xcode.
2. Trascinare una nuova **Controller visualizzazione** nell'area di progettazione:

    [![](dialog-images/new01.png "Quando si seleziona un Controller di visualizzazione dalla libreria")](dialog-images/new01.png#lightbox)
3. Nel **Identity Inspector**, immettere `CustomDialogController` per il **nome classe**: 

    [![](dialog-images/new02.png "Impostazione del nome di classe")](dialog-images/new02.png#lightbox)
4. Tornare a Visual Studio per Mac, consentire la sincronizzazione con Xcode e creare il `CustomDialogController.h` file.
5. Tornare a Xcode e progettare l'interfaccia: 

    [![](dialog-images/new03.png "Progettazione dell'interfaccia utente in Xcode")](dialog-images/new03.png#lightbox)
6. Creare un **Segue modale** dalla finestra principale dell'app nel nuovo controller di visualizzazione mediante il trascinamento di controllo dall'elemento dell'interfaccia utente che verrà aperta la finestra di dialogo alla finestra della finestra di dialogo. Assegnare il **Identifier** `ModalSegue`: 

    [![](dialog-images/new06.png "Un elemento segue modale")](dialog-images/new06.png#lightbox)
7. Wireup eventuali **azioni** e **Outlet**: 

    [![](dialog-images/new04.png "Configurazione di un'azione")](dialog-images/new04.png#lightbox)
8. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

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

Questo codice espone alcuni eventi per rispondere alla finestra di dialogo viene annullata o accettato e alcune proprietà per impostare il titolo e la descrizione della finestra di dialogo.

Successivamente, modificare il `ViewController.cs` file, eseguire l'override di `PrepareForSegue` (metodo) e renderlo simile al seguente:

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

Questo codice inizializza l'elemento segue è definiti in Interface Builder di Xcode a nostra finestra di dialogo e consente di impostare il titolo e descrizione. Gestisce inoltre la scelta che rende l'utente nella finestra di dialogo.

È possibile eseguire l'applicazione e visualizzare la finestra di dialogo personalizzata:

[![](dialog-images/new05.png "Una finestra di dialogo di esempio")](dialog-images/new05.png#lightbox)

Per altre informazioni sull'uso di windows in un'applicazione xamarin. Mac, vedere la [utilizzo di Windows](~/mac/user-interface/window.md) documentazione.

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Creazione di un foglio di lavoro personalizzato

Oggetto _foglio_ è una finestra di dialogo modale che è collegato a una finestra del documento specificato, impedendo agli utenti di interagire con la finestra fino a quando non sono per chiudere la finestra di dialogo. Un foglio è collegato alla finestra da cui emerge e un solo foglio può essere aperto per una finestra in qualsiasi momento. 

Per creare un foglio personalizzata in xamarin. Mac, è possibile eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, aprire il `Main.storyboard` file per la modifica in Interface Builder di Xcode.
2. Trascinare una nuova **Controller visualizzazione** nell'area di progettazione:

    [![](dialog-images/new01.png "Quando si seleziona un Controller di visualizzazione dalla libreria")](dialog-images/new01.png#lightbox)
3. Progettare l'interfaccia utente:

    [![](dialog-images/sheet01.png "La progettazione dell'interfaccia utente")](dialog-images/sheet01.png#lightbox)
4. Creare un **foglio Segue** dalla finestra del principale per il nuovo Controller di visualizzazione: 

    [![](dialog-images/sheet02.png "Selezione del tipo di elemento segue foglio")](dialog-images/sheet02.png#lightbox)
5. Nel **Identity Inspector**, assegnare un nome del Controller visualizzazione **classe** `SheetViewController`: 

    [![](dialog-images/sheet03.png "Impostazione del nome di classe")](dialog-images/sheet03.png#lightbox)
6. Definire alcuna necessità **Outlet** e **azioni**: 

    [![](dialog-images/sheet04.png "La definizione di Outlet e azioni necessari")](dialog-images/sheet04.png#lightbox)
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.

Modificare quindi il `SheetViewController.cs` file e renderlo simile al seguente:

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

Successivamente, modificare il `ViewController.cs` file, modificare il `PrepareForSegue` (metodo) e renderlo simile al seguente:

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

Se è eseguire l'applicazione e aprire la finestra, si verrà collegato alla finestra:

[![](dialog-images/sheet08.png "Un foglio di esempio")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Creazione di una finestra di dialogo Preferenze

Prima è il layout di visualizzazione delle preferenze in Interface Builder, è necessario aggiungere un tipo di elemento segue personalizzata per gestire le preferenze per la disattivazione. Aggiungere una nuova classe al progetto e denominarlo `ReplaceViewSeque`. Modificare la classe e renderlo simile al seguente:

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

L'elemento segue personalizzato creato, è possibile aggiungere una nuova finestra in Interface Builder di Xcode per gestire il preferenze.

Per aggiungere una nuova finestra, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, aprire il `Main.storyboard` file per la modifica in Interface Builder di Xcode.
2. Trascinare una nuova **Controller di finestra** nell'area di progettazione:

    [![](dialog-images/pref01.png "Selezionare un Controller di finestra dalla libreria")](dialog-images/pref01.png#lightbox)
3. Disporre la finestra vicino i **barra dei Menu** progettazione:

    [![](dialog-images/pref02.png "Aggiunta la nuova finestra")](dialog-images/pref02.png#lightbox)
4. Creare copie dei Controller di visualizzazione associato saranno le schede nella visualizzazione di preferenza:

    [![](dialog-images/pref03.png "Aggiunta di controller di visualizzazione richiesto")](dialog-images/pref03.png#lightbox)
5. Trascinare una nuova **Controller di barra degli strumenti** dalle **libreria**:

    [![](dialog-images/pref04.png "Selezionare un Controller di barra degli strumenti dalla raccolta")](dialog-images/pref04.png#lightbox)
6. E rilasciarlo nella finestra nell'area di progettazione:

    [![](dialog-images/pref05.png "Aggiunge un nuovo Controller di barra degli strumenti")](dialog-images/pref05.png#lightbox)
7. La progettazione della barra degli strumenti layout:

    [![](dialog-images/pref06.png "Barra degli strumenti layout")](dialog-images/pref06.png#lightbox)
8. CTRL + clic e trascinare da ognuna **pulsante sulla barra degli strumenti** alle visualizzazioni creato in precedenza. Selezionare una **Custom** tipo di elemento segue:

    [![](dialog-images/pref07.png "Impostare il tipo di elemento segue")](dialog-images/pref07.png#lightbox)
9. Selezionare il nuovo elemento Segue e impostare il **classe** a `ReplaceViewSegue`:

    [![](dialog-images/pref08.png "L'impostazione della classe di elemento segue")](dialog-images/pref08.png#lightbox)
10. Nel **sulla barra dei menu Progettazione** nell'area di progettazione, dal Menu dell'applicazione selezionare **preferenze...** , fare CTRL + clic e trascinare la finestra Preferenze per creare un **mostrano** elemento segue:

    [![](dialog-images/pref09.png "Impostare il tipo di elemento segue")](dialog-images/pref09.png#lightbox)
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.

Se si esegue il codice e selezionare il **preferenze...**  dal **Menu applicazione**, verrà visualizzata la finestra:

[![](dialog-images/pref10.png "Una finestra Preferenze di esempio")](dialog-images/pref10.png#lightbox)

Per altre informazioni sull'uso di Windows e le barre degli strumenti, vedere la [Windows](~/mac/user-interface/window.md) e [barre degli strumenti](~/mac/user-interface/toolbar.md) documentazione.

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Salvataggio e caricamento delle preferenze

In macOS tipiche App, quando l'utente apporta modifiche a una delle preferenze dell'utente dell'App, tali modifiche vengono salvate automaticamente. Il modo più semplice per gestire questo problema in un'app xamarin. Mac, consiste nel creare un'unica classe per gestire tutte le preferenze dell'utente e condividerlo a livello di sistema.

In primo luogo, aggiungere una nuova `AppPreferences` classe al progetto e di ereditare `NSObject`. Verranno progettate le preferenze per utilizzare [Data Binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md) che rendono il processo di creazione e la preferenza di gestione basata su form molto più semplice. Poiché le preferenze costituito da una piccola quantità di tipi di dati semplice, usare incorporato `NSUserDefaults` per archiviare e recuperare i valori.

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
        [Export("DefaultLanguage")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLanguage", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLanguage");
                SaveInt ("DefaultLanguage", value, true);
                DidChangeValue ("DefaultLanguage");
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

Questa classe contiene alcune routine di supporto, ad esempio `SaveInt`, `LoadInt`, `SaveColor`, `LoadColor`, e così via per agevolare l'uso con `NSUserDefaults` più semplice. Inoltre, dato `NSUserDefaults` non ha un metodo integrato per gestire `NSColors`, il `NSColorToHexString` e `NSColorFromHexString` metodi vengono usati per convertire i colori in stringhe esadecimali basata sul web (`#RRGGBBAA` dove `AA` è la trasparenza alfa) che può essere facilmente archiviati e recuperati.

Nel `AppDelegate.cs` file, creare un'istanza del **AppPreferences** oggetto che verrà utilizzato a livello di app:

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

### <a name="wiring-preferences-to-preference-views"></a>Collegare le preferenze per le visualizzazioni di preferenza

Successivamente, connettersi classe preferenza agli elementi dell'interfaccia utente nella finestra di preferenze e le visualizzazioni create in precedenza. In Interface Builder, selezionare un Controller di visualizzazione delle preferenze e passare per il **Identity Inspector**, creare una classe personalizzata per il controller: 

[![](dialog-images/prefs12.png "Identity Inspector")](dialog-images/prefs12.png#lightbox)

Tornare a Visual Studio per Mac sincronizzare le modifiche e aprire la classe appena creata per la modifica. Rendere la classe simile alla seguente:

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

Si noti che questa classe ha svolto due cose qui: In primo luogo, è un helper `App` proprietà per facilitare l'accesso di **AppDelegate** più semplice. Secondo, il `Preferences` proprietà espone globale **AppPreferences** classe per il data binding con tutti i controlli dell'interfaccia utente inseriti in questa vista.

Successivamente, fare doppio clic sul file di Storyboard per aprirla nuovamente in Interface Builder (e visualizzare le modifiche appena apportate di sopra). Trascinare tutti i controlli dell'interfaccia utente necessari per compilare l'interfaccia preferenze nella visualizzazione. Per ogni controllo, passare al **associazione Inspector** ed eseguire l'associazione per le singole proprietà delle **AppPreference** classe:

[![](dialog-images/prefs13.png "Il controllo di associazione")](dialog-images/prefs13.png#lightbox)

Ripetere i passaggi precedenti per tutti i pannelli (controller di visualizzazione) e alle proprietà di preferenza è richiesto.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>Applicazione delle preferenze viene modificato in tutti i Windows

Come indicato in precedenza, in un tipico macOS App, quando l'utente apporta modifiche a una delle preferenze dell'utente dell'App, tali modifiche vengono salvate automaticamente e applicate a tutte le finestre l'utente potrebbe essere aperto nell'applicazione.

Un'attenta pianificazione e progettazione di windows e le preferenze dell'app consentirà questo processo si verifica in modo uniforme e trasparente per l'utente finale, con una quantità minima di lavoro di codifica.

Per qualsiasi finestra che utilizzerà le preferenze di App, aggiungere la seguente proprietà helper al relativo contenuto Controller di visualizzazione per rendere l'accesso ai nostri **AppDelegate** più semplice:

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

È necessario chiamare il metodo di configurazione quando la finestra viene aperta prima di tutto per assicurarsi che sia conforme alle preferenze dell'utente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Modificare quindi il `AppDelegate.cs` file e aggiungere il metodo seguente per applicare le preferenze di modifica a tutte le finestre aperte:

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
    public class PreferenceWindowDelegate : NSWindowDelegate
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
        public PreferenceWindowDelegate (NSWindow window)
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

In questo modo tutte le modifiche delle preferenze da inviare al Windows Apri tutti quando la preferenza di finestra viene chiusa.

Infine, modificare il Controller di finestra di preferenza e aggiungere il delegato creato in precedenza:

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
            Window.Delegate = new PreferenceWindowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Con tutte queste modifiche sul posto, se l'utente modifica le preferenze dell'App e chiude la finestra di preferenza, le modifiche verranno applicate a tutti i Windows aperta:

[![](dialog-images/prefs14.png "Una finestra Preferenze di esempio")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Finestra di dialogo aprire

La finestra di dialogo aprire offre agli utenti un modo coerente per trovare e aprire un elemento in un'applicazione. Per visualizzare una finestra di dialogo Apri in un'applicazione xamarin. Mac, usare il codice seguente:

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

Nel codice precedente, apriremo una nuova finestra del documento da visualizzare il contenuto del file. È necessario sostituire questo codice con la funzionalità è richiesta dall'applicazione.

Le proprietà seguenti sono disponibili quando si lavora con un `NSOpenPanel`:

- **CanChooseFiles** - se `true` l'utente può selezionare i file.
- **CanChooseDirectories** - se `true` l'utente può selezionare le directory.
- **AllowsMultipleSelection** - se `true` l'utente può selezionare più di un file alla volta.
- **ResolveAliases** - se `true` selezionando e alias, risolve tale percorso del file originale.
- **AllowedFileTypes** -è una matrice di stringhe dei tipi di file che l'utente può selezionare come un'estensione di uno o _UTI_. Il valore predefinito è `null`, che consente a qualsiasi file da aprire.

Il `RunModal ()` metodo consente di visualizzare la finestra di dialogo Apri e consentire all'utente di selezionare i file o directory (come specificato dalle proprietà) e restituisce `1` se l'utente fa clic il **Open** pulsante.

Finestra di dialogo Apri restituisce i file selezionati o le directory dell'utente come una matrice di URL nel `URL` proprietà.

Se si esegue il programma e selezionare il **aprire...**  articolo dal **File** viene visualizzato dal menu di seguito: 

[![](dialog-images/dialog03.png "La finestra di dialogo Apri")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>La stampa e le finestre di dialogo Imposta pagina

macOS fornisce stampa standard e pagina di installazione di finestre di dialogo che è possibile visualizzare in modo che gli utenti possono avere una stampa coerente esperienza in tutte le applicazioni che usano.

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

Se si impostano i `ShowPrintAsSheet` proprietà `false`, eseguire l'applicazione e visualizzare la finestra di dialogo Stampa, verrà visualizzato quanto segue:

[![](dialog-images/print01.png "Una finestra di dialogo Stampa")](dialog-images/print01.png#lightbox)

Se impostare il `ShowPrintAsSheet` proprietà `true`, eseguire l'applicazione e visualizzare la finestra di dialogo Stampa, verrà visualizzato quanto segue:

[![](dialog-images/print02.png "Un foglio di stampa")](dialog-images/print02.png#lightbox)

Il codice seguente visualizzerà la finestra di dialogo di Layout di pagina:

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

Se si impostano i `ShowPrintAsSheet` proprietà `false`, eseguire l'applicazione e visualizzare la finestra di dialogo di layout di stampa, verrà visualizzato quanto segue:

[![](dialog-images/print03.png "Una finestra di dialogo Imposta pagina")](dialog-images/print03.png#lightbox)

Se impostare il `ShowPrintAsSheet` proprietà `true`, eseguire l'applicazione e visualizzare la finestra di dialogo di layout di stampa, verrà visualizzato quanto segue:

[![](dialog-images/print04.png "Un foglio di programma di installazione di pagina")](dialog-images/print04.png#lightbox)

Per altre informazioni sull'utilizzo di stampa e pagina finestre di dialogo di installazione, vedere di Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092), [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) e [Introduzione alla stampa](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1) documentazione.

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>Il salvataggio della finestra

La finestra di dialogo Salva offre agli utenti un modo coerente per salvare un elemento in un'applicazione.

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

Il `AllowedFileTypes` proprietà è una matrice di tipi di file che l'utente può scegliere di salvare il file come stringhe. Il tipo di file può essere specificato come un'estensione o _UTI_. Il valore predefinito è `null`, che consente a qualsiasi tipo di file da utilizzare.

Se si impostano i `ShowSaveAsSheet` proprietà `false`, eseguire l'applicazione e selezionare **Salva con nome...**  dal **File** menu, verrà visualizzato quanto segue:

[![](dialog-images/save01.png "Salvataggio nella finestra di dialogo")](dialog-images/save01.png#lightbox)

L'utente può espandere la finestra di dialogo:

[![](dialog-images/save02.png "Finestra di dialogo Salva espanso")](dialog-images/save02.png#lightbox)

Se si impostano i `ShowSaveAsSheet` proprietà `true`, eseguire l'applicazione e selezionare **Salva con nome...**  dal **File** menu, verrà visualizzato quanto segue:

[![](dialog-images/save03.png "Salvataggio foglio")](dialog-images/save03.png#lightbox)

L'utente può espandere la finestra di dialogo:

[![](dialog-images/save04.png "Salvare un espanso foglio")](dialog-images/save04.png#lightbox)

Per altre informazioni su come utilizzare la finestra di dialogo Salva, vedi di Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo di Windows modale, fogli e di sistema standard, finestre di dialogo in un'applicazione xamarin. Mac. Abbiamo visto i diversi tipi e utilizzi di modale di Windows, finestre e finestre di dialogo, come creare e gestire i fogli in Xcode e Windows modale di Interface Builder e come lavorare con Windows modale, fogli di stile e finestre di dialogo nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menu](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Barre degli strumenti](~/mac/user-interface/toolbar.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Introduzione ai fogli](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Introduzione alla stampa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
