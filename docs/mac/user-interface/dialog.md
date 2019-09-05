---
title: Finestre di dialogo in Novell. Mac
description: Questo articolo illustra l'uso di finestre di dialogo e finestre modali in un'applicazione Novell. Mac. Descrive la creazione di finestre modali in Xcode e Interface Builder, l'uso di finestre di dialogo standard e l'interazione con questi C# controlli nel codice.
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 723f751b93af304c7d70fb1250dbda2f0220e77e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292209"
---
# <a name="dialogs-in-xamarinmac"></a>Finestre di dialogo in Novell. Mac

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere alle stesse finestre di dialogo e finestre modali che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Novell. Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire le finestre modali o, facoltativamente, crearle direttamente C# nel codice.

Viene visualizzata una finestra di dialogo in risposta a un'azione dell'utente che in genere consente agli utenti di completare l'azione. Una finestra di dialogo richiede una risposta da parte dell'utente prima che possa essere chiusa.

È possibile utilizzare Windows in uno stato non modale, ad esempio un editor di testo che può disporre di più documenti aperti contemporaneamente, o modale, ad esempio una finestra di dialogo di esportazione che deve essere rilasciata prima che l'applicazione possa continuare.

[![](dialog-images/dialog03.png "Finestra di dialogo Apri")](dialog-images/dialog03.png#lightbox)

In questo articolo verranno illustrate le nozioni di base per l'uso di finestre di dialogo e finestre modali in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` i comandi e `Export` usati per collegare le C# classi a. Oggetti Objective-C ed elementi dell'interfaccia utente.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Introduzione alle finestre di dialogo

Viene visualizzata una finestra di dialogo in risposta a un'azione dell'utente, ad esempio il salvataggio di un file, e viene fornito un modo per consentire agli utenti di completare l'azione. Una finestra di dialogo richiede una risposta da parte dell'utente prima che possa essere chiusa.

Secondo Apple, esistono tre modi per presentare una finestra di dialogo:

- **Modale del documento** : una finestra di dialogo modale del documento impedisce all'utente di eseguire altre operazioni all'interno di un determinato documento fino a quando non viene rilasciata.
- **App modale** : una finestra di dialogo modale per le app impedisce all'utente di interagire con l'applicazione fino a quando non viene rilasciata.
- Non **modale** Una finestra di dialogo non modale consente agli utenti di modificare le impostazioni nella finestra di dialogo continuando a interagire con la finestra del documento.

### <a name="modal-window"></a>Finestra modale

Qualsiasi standard `NSWindow` può essere utilizzato come finestra di dialogo personalizzata visualizzandolo modale:

[![](dialog-images/modal01.png "Finestra modale di esempio")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Finestre di dialogo modali del documento

Un _foglio_ è una finestra di dialogo modale collegata a una determinata finestra del documento, che impedisce agli utenti di interagire con la finestra fino a quando non chiude la finestra di dialogo. Un foglio viene collegato alla finestra da cui emerge ed è possibile aprire un solo foglio per una finestra in qualsiasi momento.

[![](dialog-images/sheet08.png "Foglio modale di esempio")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Finestre di preferenze

Una finestra di preferenze è una finestra di dialogo non modale che contiene le impostazioni dell'applicazione modificate raramente dall'utente. Le finestre di preferenze includono spesso una barra degli strumenti che consente all'utente di spostarsi tra diversi gruppi di impostazioni:

[![](dialog-images/dialog02.png "Finestra delle preferenze di esempio")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Apri finestra di dialogo

La finestra di dialogo Apri fornisce agli utenti un modo coerente per trovare e aprire un elemento in un'applicazione:

[![](dialog-images/dialog03.png "Finestra di dialogo Apri")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>Finestre di dialogo di stampa e impostazione pagina

macOS fornisce finestre di dialogo di stampa e configurazione della pagina standard che possono essere visualizzate dall'applicazione, in modo che gli utenti possano avere un'esperienza di stampa coerente in ogni applicazione utilizzata.

La finestra di dialogo stampa può essere visualizzata come una finestra di dialogo a virgola mobile gratuita:

[![](dialog-images/print01.png "Finestra di dialogo Stampa")](dialog-images/print01.png#lightbox)

Oppure può essere visualizzato come foglio:

[![](dialog-images/print02.png "Un foglio di stampa")](dialog-images/print02.png#lightbox)

La finestra di dialogo Imposta pagina può essere visualizzata come una finestra di dialogo a virgola mobile gratuita:

[![](dialog-images/print03.png "Finestra di dialogo Imposta pagina")](dialog-images/print03.png#lightbox)

Oppure può essere visualizzato come foglio:

[![](dialog-images/print04.png "Foglio di impostazione della pagina")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Salva finestre di dialogo

La finestra di dialogo Salva consente agli utenti di salvare in modo coerente un elemento in un'applicazione. Nella finestra di dialogo Salva sono presenti due stati: **Minima** (anche noto come compresso):

[![](dialog-images/save01.png "Finestra di dialogo Salva")](dialog-images/save01.png#lightbox)

E lo stato **espanso** :

[![](dialog-images/save02.png "Finestra di dialogo Salva espansa")](dialog-images/save02.png#lightbox)

La finestra di dialogo Salva **minima** può essere visualizzata anche come foglio:

[![](dialog-images/save03.png "Un foglio di salvataggio minimo")](dialog-images/save03.png#lightbox)

Come è possibile la finestra di dialogo Salva **espansa** :

[![](dialog-images/save04.png "Un foglio di salvataggio espanso")](dialog-images/save04.png#lightbox)

Per altre informazioni, vedere la sezione [Dialogs](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) of Apple ' s [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Aggiunta di una finestra modale a un progetto

A parte la finestra del documento principale, un'applicazione Novell. Mac potrebbe dover visualizzare altri tipi di finestre per l'utente, ad esempio le preferenze o i pannelli di controllo.

Per aggiungere una nuova finestra, procedere come segue:

1. Nella **Esplora soluzioni**aprire il file per `Main.storyboard` la modifica nella Interface Builder di Xcode.
2. Trascinare un nuovo **controller di visualizzazione** nella area di progettazione:

    [![](dialog-images/new01.png "Selezione di un controller di visualizzazione dalla libreria")](dialog-images/new01.png#lightbox)
3. In **Identity Inspector**immettere `CustomDialogController` per il nome della **classe**: 

    [![](dialog-images/new02.png "Impostazione del nome della classe")](dialog-images/new02.png#lightbox)
4. Tornare a Visual Studio per Mac, consentirne la sincronizzazione con Xcode e creare il `CustomDialogController.h` file.
5. Tornare a Xcode e progettare l'interfaccia: 

    [![](dialog-images/new03.png "Progettazione dell'interfaccia utente in Xcode")](dialog-images/new03.png#lightbox)
6. Creare un **segue modale** dalla finestra principale dell'app al nuovo controller di visualizzazione tramite il trascinamento del controllo dall'elemento dell'interfaccia utente che apre la finestra di dialogo alla finestra della finestra di dialogo. Assegnare l' **identificatore** `ModalSegue`: 

    [![](dialog-images/new06.png "Un segue modale")](dialog-images/new06.png#lightbox)
7. Cablare eventuali **azioni** e **Outlet**: 

    [![](dialog-images/new04.png "Configurazione di un'azione")](dialog-images/new04.png#lightbox)
8. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Fare in `CustomDialogController.cs` modo che il file abbia un aspetto simile al seguente:

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

Questo codice espone alcune proprietà per impostare il titolo e la descrizione della finestra di dialogo e alcuni eventi per reagire alla finestra di dialogo annullata o accettata.

Modificare quindi il `ViewController.cs` file, eseguire l'override `PrepareForSegue` del metodo e renderlo simile al seguente:

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

Questo codice inizializza il segue definito nel Interface Builder di Xcode nella finestra di dialogo e imposta il titolo e la descrizione. Consente inoltre di gestire la scelta effettuata dall'utente nella finestra di dialogo.

È possibile eseguire l'applicazione e visualizzare la finestra di dialogo personalizzata:

[![](dialog-images/new05.png "Finestra di dialogo di esempio")](dialog-images/new05.png#lightbox)

Per ulteriori informazioni sull'utilizzo di Windows in un'applicazione Novell. Mac, consultare la documentazione relativa all'utilizzo [di Windows](~/mac/user-interface/window.md) .

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Creazione di un foglio personalizzato

Un _foglio_ è una finestra di dialogo modale collegata a una determinata finestra del documento, che impedisce agli utenti di interagire con la finestra fino a quando non chiude la finestra di dialogo. Un foglio viene collegato alla finestra da cui emerge ed è possibile aprire un solo foglio per una finestra in qualsiasi momento. 

Per creare un foglio personalizzato in Novell. Mac, procedere come segue:

1. Nella **Esplora soluzioni**aprire il file per `Main.storyboard` la modifica nella Interface Builder di Xcode.
2. Trascinare un nuovo **controller di visualizzazione** nella area di progettazione:

    [![](dialog-images/new01.png "Selezione di un controller di visualizzazione dalla libreria")](dialog-images/new01.png#lightbox)
3. Progettare l'interfaccia utente:

    [![](dialog-images/sheet01.png "Progettazione dell'interfaccia utente")](dialog-images/sheet01.png#lightbox)
4. Creare un **foglio segue** dalla finestra principale al nuovo controller di visualizzazione: 

    [![](dialog-images/sheet02.png "Selezione del tipo di segue del foglio")](dialog-images/sheet02.png#lightbox)
5. Nel **controllo di identità**assegnare un nome alla **classe** `SheetViewController`del controller di visualizzazione: 

    [![](dialog-images/sheet03.png "Impostazione del nome della classe")](dialog-images/sheet03.png#lightbox)
6. Definire gli **Outlet** e le **azioni**necessari: 

    [![](dialog-images/sheet04.png "Definizione degli Outlet e delle azioni necessari")](dialog-images/sheet04.png#lightbox)
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

Modificare quindi il `ViewController.cs` file, modificare il `PrepareForSegue` metodo e renderlo simile al seguente:

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

Se si esegue l'applicazione e si apre il foglio, questo verrà collegato alla finestra:

[![](dialog-images/sheet08.png "Un foglio di esempio")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Creazione di una finestra di dialogo di preferenze

Prima di disporre la visualizzazione delle preferenze in Interface Builder, è necessario aggiungere un tipo di segue personalizzato per gestire il cambio delle preferenze. Aggiungere una nuova classe al progetto e chiamarla `ReplaceViewSeque`. Modificare la classe e renderla simile alla seguente:

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

Con il segue personalizzato creato, è possibile aggiungere una nuova finestra nella Interface Builder di Xcode per gestire le proprie preferenze.

Per aggiungere una nuova finestra, procedere come segue:

1. Nella **Esplora soluzioni**aprire il file per `Main.storyboard` la modifica nella Interface Builder di Xcode.
2. Trascinare un nuovo **controller della finestra** nella area di progettazione:

    [![](dialog-images/pref01.png "Selezionare un controller di finestra dalla libreria")](dialog-images/pref01.png#lightbox)
3. Disporre la finestra accanto alla finestra di progettazione della **barra dei menu** :

    [![](dialog-images/pref02.png "Aggiunta della nuova finestra")](dialog-images/pref02.png#lightbox)
4. Creare copie del controller di visualizzazione collegato perché saranno presenti schede nella visualizzazione delle preferenze:

    [![](dialog-images/pref03.png "Aggiunta dei controller di visualizzazione richiesti")](dialog-images/pref03.png#lightbox)
5. Trascinare un nuovo **controller della barra degli strumenti** dalla **libreria**:

    [![](dialog-images/pref04.png "Selezionare un controller della barra degli strumenti dalla libreria")](dialog-images/pref04.png#lightbox)
6. E rilasciarlo nella finestra nella Area di progettazione:

    [![](dialog-images/pref05.png "Aggiunta di un nuovo controller della barra degli strumenti")](dialog-images/pref05.png#lightbox)
7. Layout della struttura della barra degli strumenti:

    [![](dialog-images/pref06.png "Layout della barra degli strumenti")](dialog-images/pref06.png#lightbox)
8. CTRL: fare clic e trascinare da ogni **pulsante della barra degli strumenti** per visualizzare le visualizzazioni create in precedenza. Selezionare un tipo di segue **personalizzato** :

    [![](dialog-images/pref07.png "Impostazione del tipo segue")](dialog-images/pref07.png#lightbox)
9. Selezionare il nuovo segue e impostare la **classe** su `ReplaceViewSegue`:

    [![](dialog-images/pref08.png "Impostazione della classe segue")](dialog-images/pref08.png#lightbox)
10. Nella finestra di **progettazione dei menu** nella area di progettazione scegliere **Preferenze**dal menu applicazione, quindi fare clic sul pulsante destro del mouse e trascinare nella finestra Preferenze per creare un segue di **visualizzazione** :

    [![](dialog-images/pref09.png "Impostazione del tipo segue")](dialog-images/pref09.png#lightbox)
11. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione.

Se eseguiamo il codice e selezioni le **Preferenze...** dal **menu applicazione**, verrà visualizzata la finestra:

[![](dialog-images/pref10.png "Finestra delle preferenze di esempio")](dialog-images/pref10.png#lightbox)

Per ulteriori informazioni sull'utilizzo di finestre e barre degli strumenti, vedere la documentazione di [Windows](~/mac/user-interface/window.md) e delle [barre degli strumenti](~/mac/user-interface/toolbar.md) .

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Salvataggio e caricamento delle preferenze

In una tipica app macOS, quando l'utente apporta modifiche a tutte le preferenze dell'utente dell'app, le modifiche vengono salvate automaticamente. Il modo più semplice per gestire questo problema in un'app Novell. Mac consiste nel creare una singola classe per gestire tutte le preferenze dell'utente e condividerle a livello di sistema.

In primo luogo, aggiungere `AppPreferences` una nuova classe al progetto ed ereditare da. `NSObject` Le preferenze saranno progettate per usare il [Data Binding e la codifica chiave-valore,](~/mac/app-fundamentals/databinding.md) che rende molto più semplice il processo di creazione e gestione dei moduli delle preferenze. Poiché le preferenze sono costituite da una piccola quantità di tipi di dati semplici, utilizzare il incorporato `NSUserDefaults` per archiviare e recuperare i valori.

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

Questa classe contiene alcune routine di supporto `SaveInt` `SaveColor`, ad esempio, `LoadInt`, `NSUserDefaults` , `LoadColor`e così via, per semplificare l'utilizzo di. Inoltre, poiché `NSUserDefaults` non dispone di un metodo incorporato per gestire `NSColors`, i `NSColorToHexString` metodi e `NSColorFromHexString` vengono utilizzati per convertire i colori in stringhe esadecimali basate sul Web (`#RRGGBBAA` dove `AA` è la trasparenza alfa) che può essere archiviazione e recupero semplici.

Nel file creare un'istanza dell'oggetto AppPreferences che verrà usato a livello di app: `AppDelegate.cs`

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

### <a name="wiring-preferences-to-preference-views"></a>Preferenze di cablaggio per le visualizzazioni delle preferenze

Connettere quindi la classe di preferenza agli elementi dell'interfaccia utente nella finestra delle preferenze e le visualizzazioni create in precedenza. In Interface Builder selezionare un controller di visualizzazione delle preferenze e passare a **Identity Inspector**, quindi creare una classe personalizzata per il controller: 

[![](dialog-images/prefs12.png "Controllo di identità")](dialog-images/prefs12.png#lightbox)

Tornare a Visual Studio per Mac per sincronizzare le modifiche e aprire la classe appena creata per la modifica. Rendere la classe simile alla seguente:

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

Si noti che questa classe ha eseguito due operazioni: Per prima cosa, esiste una `App` proprietà helper che semplifica l'accesso a **AppDelegate** . In secondo luogo `Preferences` , la proprietà espone la classe **AppPreferences** globale per data binding con tutti i controlli dell'interfaccia utente posizionati in questa visualizzazione.

Fare quindi doppio clic sul file dello storyboard per riaprirlo in Interface Builder (per visualizzare le modifiche appena apportate sopra). Trascinare i controlli dell'interfaccia utente necessari per compilare l'interfaccia di preferenze nella visualizzazione. Per ogni controllo, passare al controllo **Binding** e associare le singole proprietà della classe **AppPreference** :

[![](dialog-images/prefs13.png "Controllo Binding")](dialog-images/prefs13.png#lightbox)

Ripetere i passaggi precedenti per tutti i pannelli (controller di visualizzazione) e le proprietà delle preferenze necessarie.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>Applicazione delle modifiche di preferenza a tutte le finestre aperte

Come indicato in precedenza, in una tipica app macOS, quando l'utente apporta modifiche a una delle preferenze dell'utente dell'app, queste modifiche vengono salvate automaticamente e applicate a qualsiasi finestra che l'utente potrebbe avere aperto nell'applicazione.

Un'attenta pianificazione e progettazione delle preferenze dell'app e di Windows consentirà a questo processo di funzionare in modo semplice e trasparente per l'utente finale, con una quantità minima di codice.

Per qualsiasi finestra che utilizzerà le preferenze dell'app, aggiungere la seguente proprietà helper al relativo controller di visualizzazione del contenuto per semplificare l'accesso ai **AppDelegate** :

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

Aggiungere quindi una classe per configurare il contenuto o il comportamento in base alle preferenze dell'utente:

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

È necessario chiamare il metodo di configurazione quando la finestra viene aperta per la prima volta per assicurarsi che sia conforme alle preferenze dell'utente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Modificare quindi il `AppDelegate.cs` file e aggiungere il metodo seguente per applicare tutte le modifiche alle preferenze a tutte le finestre aperte:

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

Aggiungere quindi una `PreferenceWindowDelegate` classe al progetto e renderla simile alla seguente:

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

Questa operazione causerà l'invio di qualsiasi preferenza a tutte le finestre aperte alla chiusura della finestra delle preferenze.

Infine, modificare il controller della finestra delle preferenze e aggiungere il delegato creato in precedenza:

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

Dopo aver apportato tutte le modifiche, se l'utente modifica le preferenze dell'app e chiude la finestra delle preferenze, le modifiche verranno applicate a tutte le finestre aperte:

[![](dialog-images/prefs14.png "Finestra delle preferenze di esempio")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Finestra di dialogo Apri

La finestra di dialogo Apri fornisce agli utenti un modo coerente per trovare e aprire un elemento in un'applicazione. Per visualizzare una finestra di dialogo aperta in un'applicazione Novell. Mac, usare il codice seguente:

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

Nel codice sopra riportato, viene aperta una nuova finestra del documento per visualizzare il contenuto del file. È necessario sostituire questo codice con la funzionalità richiesta dall'applicazione.

Quando si utilizza un oggetto `NSOpenPanel`, sono disponibili le proprietà seguenti:

- **CanChooseFiles** : se `true` l'utente può selezionare i file.
- **CanChooseDirectories** : se `true` l'utente può selezionare le directory.
- **AllowsMultipleSelection** : se `true` l'utente può selezionare più di un file alla volta.
- **ResolveAliases** : se `true` si seleziona e alias, lo risolve nel percorso del file originale.
- **AllowedFileTypes** : matrice di stringhe di tipi di file che l'utente può selezionare come estensione o _uti_. Il valore predefinito è `null`, che consente l'apertura di qualsiasi file.

Il `RunModal ()` metodo Visualizza la finestra di dialogo Apri e consente all'utente di selezionare file o directory (come specificato dalle proprietà) e `1` restituisce se l'utente fa clic sul pulsante **Apri** .

La finestra di dialogo Apri restituisce i file o le directory selezionati dell'utente come matrice di URL `URL` nella proprietà.

Se si esegue il programma e si seleziona l'elemento **Apri...** dal menu **file** , viene visualizzato quanto segue: 

[![](dialog-images/dialog03.png "Finestra di dialogo Apri")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>Finestre di dialogo di stampa e impostazione pagina

macOS fornisce finestre di dialogo di stampa e configurazione della pagina standard che possono essere visualizzate dall'applicazione, in modo che gli utenti possano avere un'esperienza di stampa coerente in ogni applicazione utilizzata.

Nel codice seguente viene visualizzata la finestra di dialogo stampa standard:

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

Se si imposta la `ShowPrintAsSheet` proprietà su `false`, eseguire l'applicazione e visualizzare la finestra di dialogo Stampa, verrà visualizzato quanto segue:

[![](dialog-images/print01.png "Finestra di dialogo Stampa")](dialog-images/print01.png#lightbox)

Se si imposta `ShowPrintAsSheet` la proprietà `true`su, eseguire l'applicazione e visualizzare la finestra di dialogo Stampa, verrà visualizzato quanto segue:

[![](dialog-images/print02.png "Un foglio di stampa")](dialog-images/print02.png#lightbox)

Nel codice seguente viene visualizzata la finestra di dialogo layout pagina:

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

Se si imposta la `ShowPrintAsSheet` proprietà su `false`, eseguire l'applicazione e visualizzare la finestra di dialogo layout di stampa, verrà visualizzato quanto segue:

[![](dialog-images/print03.png "Finestra di dialogo Imposta pagina")](dialog-images/print03.png#lightbox)

Se si imposta `ShowPrintAsSheet` la proprietà `true`su, eseguire l'applicazione e visualizzare la finestra di dialogo layout di stampa, verrà visualizzato quanto segue:

[![](dialog-images/print04.png "Foglio di impostazione della pagina")](dialog-images/print04.png#lightbox)

Per ulteriori informazioni sull'utilizzo delle finestre di dialogo di stampa e di impostazione della pagina, vedere la documentazione di Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092), [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) e [Introduzione alla stampa](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1) .

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>Finestra di dialogo Salva

La finestra di dialogo Salva consente agli utenti di salvare in modo coerente un elemento in un'applicazione.

Nel codice seguente viene visualizzata la finestra di dialogo Salva standard:

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

La `AllowedFileTypes` proprietà è una matrice di stringhe di tipi di file che l'utente può selezionare per salvare il file. Il tipo di file può essere specificato come estensione o _uti_. Il valore predefinito è `null`, che consente l'utilizzo di qualsiasi tipo di file.

Se si imposta la `ShowSaveAsSheet` proprietà su `false`, eseguire l'applicazione e selezionare **Salva con nome** dal menu **file** . verrà visualizzato quanto segue:

[![](dialog-images/save01.png "Finestra di dialogo Salva")](dialog-images/save01.png#lightbox)

L'utente può espandere la finestra di dialogo:

[![](dialog-images/save02.png "Finestra di dialogo Salva espansa")](dialog-images/save02.png#lightbox)

Se si imposta la `ShowSaveAsSheet` proprietà su `true`, eseguire l'applicazione e selezionare **Salva con nome** dal menu **file** . verrà visualizzato quanto segue:

[![](dialog-images/save03.png "Un foglio di salvataggio")](dialog-images/save03.png#lightbox)

L'utente può espandere la finestra di dialogo:

[![](dialog-images/save04.png "Un foglio di salvataggio espanso")](dialog-images/save04.png#lightbox)

Per ulteriori informazioni sull'utilizzo della finestra di dialogo Salva, consultare la documentazione di Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) .

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso di finestre modali, fogli e finestre di dialogo di sistema standard in un'applicazione Novell. Mac. Sono stati osservati i diversi tipi e usi di finestre modali, fogli e finestre di dialogo, come creare e gestire finestre e fogli modali in Interface Builder di Xcode e come usare finestre, fogli e finestre di dialogo modali C# nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menu](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Barre degli strumenti](~/mac/user-interface/toolbar.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Introduzione ai fogli](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Introduzione alla stampa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
