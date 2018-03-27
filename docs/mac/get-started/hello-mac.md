---
title: Hello, Mac
description: Questa guida descrive i passaggi per creare per la prima volta un'app Xamarin.Mac e illustra la toolchain di sviluppo che include Visual Studio per Mac, Xcode e Interface Builder. Vengono inoltre descritti gli outlet e le azioni che espongono i comandi dell'interfaccia utente al codice e infine viene illustrato come compilare, eseguire e testare un'applicazione Xamarin.Mac.
ms.topic: article
ms.prod: xamarin
ms.assetid: 37D0E9E6-979B-7069-B3BE-C5F0AF99BA72
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/23/2017
ms.openlocfilehash: 635577bbc35d9e80147ecf7e1a59540099f85b9d
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="hello-mac"></a>Hello, Mac

Xamarin.Mac consente di sviluppare app Mac completamente native in C# e .NET usando le stesse librerie e gli stessi comandi di interfaccia OS X usati per lo sviluppo in *Objective-C* e *Xcode*. Poiché Xamarin.Mac si integra direttamente con Xcode, lo sviluppatore può usare _Interface Builder_ di Xcode per creare le interfacce utente di un'app oppure crearle direttamente nel codice C#.

Inoltre, poiché le applicazioni Xamarin.Mac sono scritte in C# e .NET, il codice back-end comune può essere condiviso con le app per dispositivi mobili Xamarin.iOS e Xamarin.Android offrendo un'esperienza nativa su ogni piattaforma.

Questo articolo descrive i concetti di base necessari per creare un'app Mac usando Xamarin.Mac, Visual Studio per Mac e Interface Builder di Xcode illustrando il processo di creazione di una semplice app **Hello, Mac** che conta il numero di volte in cui viene fatto clic su un pulsante:

[![](hello-mac-images/run02.png "Esempio di app Hello, Mac in esecuzione")](hello-mac-images/run02.png#lightbox)

Verranno descritti i concetti seguenti:

-  **Visual Studio per Mac**: introduzione a Visual Studio per Mac e alla creazione di applicazioni Xamarin.Mac.
-  **Anatomia di un'applicazione Xamarin.Mac**: descrizione degli elementi che costituiscono un'applicazione Xamarin.Mac.
-  **Interface Builder di Xcode**: come usare Interface Builder di Xcode per definire l'interfaccia utente di un'app.
-  **Outlet e azioni**: come usare outlet e azioni per collegare i comandi nell'interfaccia utente.
-  **Distribuzione/Test**: come eseguire e testare un'app Xamarin.Mac.


<a name="Requirements" />

## <a name="requirements"></a>Requisiti

Per sviluppare un'applicazione macOS con Xamarin.Mac è necessario quanto segue:

- Un computer Mac che esegue macOS Yosemite(10.10) o versione successiva.
- Xcode 7 e versioni successive (sebbene sia consigliabile installare la versione stabile più recente disponibile nell'[App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)).
- Ultima versione di Xamarin.Mac e Visual Studio per Mac.

Per eseguire le applicazioni Mac create con Xamarin.Mac sono necessari i requisiti di sistema seguenti:

- Computer Mac che esegue Mac OS X 10.7 o versione successiva.

<a name="Starting_a_new_Xamarin.Mac_App_in_Xamarin_Studio" />

## <a name="starting-a-new-xamarinmac-app-in-visual-studio-for-mac"></a>Creazione di una nuova app Xamarin.Mac in Visual Studio per Mac

Come affermato in precedenza, questa guida descrive i passaggi per creare un'app Mac chiamata `Hello_Mac` che aggiunge un singolo pulsante e un'etichetta alla finestra principale. Quando viene fatto clic sul pulsante, l'etichetta visualizza il numero di volte in cui è stato fatto clic.

Per iniziare, eseguire le operazioni seguenti:

1. Avviare Visual Studio per Mac:

    [![](hello-mac-images/setup01.png "Interfaccia principale di Visual Studio per Mac")](hello-mac-images/setup01.png#lightbox)

2. Fare clic sul collegamento **Nuova soluzione...** nell'angolo superiore sinistro della schermata per aprire la finestra di dialogo **Nuovo progetto**:

    [![](hello-mac-images/setup03.png "Creazione di una nuova soluzione in Visual Studio per Mac")](hello-mac-images/setup02.png#lightbox)

3. Selezionare **Mac** > **App** > **App Cocoa** e fare clic sul pulsante **Avanti**:

    [![](hello-mac-images/setup03.png "Selezione di un'app Cocoa")](hello-mac-images/setup03.png#lightbox)

4. Immettere `Hello_Mac` come **Nome app** e mantenere gli altri valori predefiniti. Fare clic su **Avanti**:

    [![](hello-mac-images/setup05.png "Impostazione del nome dell'app")](hello-mac-images/setup05.png#lightbox)

4. Durante la creazione di una soluzione che dovrà contenere più progetti, è possibile che lo sviluppatore voglia impostare un **Nome della soluzione** diverso. In questo esempio, tuttavia, mantenere l'impostazione predefinita di un nome corrispondente al **Nome progetto**:

    [![](hello-mac-images/setup04.png "Verifica dei dettagli della nuova soluzione")](hello-mac-images/setup04.png#lightbox)

5. Fare clic sul pulsante **Crea**.

Visual Studio per Mac crea la nuova app Xamarin.Mac e visualizza i file predefiniti che vengono aggiunti alla soluzione dell'app:

 [![](hello-mac-images/project01.png "Visualizzazione predefinita della nuova soluzione")](hello-mac-images/project01.png#lightbox)

Visual Studio per Mac usa **Soluzioni** e **Progetti** nello stesso modo in cui vengono usati in Visual Studio. Una soluzione è un contenitore che può includere uno o più progetti. I progetti possono includere applicazioni, librerie di supporto, applicazioni di test e così via. In questo caso, Visual Studio per Mac ha creato automaticamente una soluzione e un progetto applicazione.

Lo sviluppatore può creare uno o più progetti libreria di codice contenenti codice comune condiviso. I progetti libreria possono essere usati dal progetto app o condivisi con altri progetti app Xamarin.Mac (o Xamarin.iOS e Xamarin.Android in base al tipo di codice) allo stesso modo di un'applicazione .NET standard.

<a name="The_Project" />

## <a name="anatomy-of-a-xamarinmac-application"></a>Anatomia di un'applicazione Xamarin.Mac

Se si ha familiarità con la programmazione iOS, è possibile osservare diverse analogie. Poiché iOS infatti usa il framework CocoaTouch, una versione ridotta di Cocoa, usato da Mac, numerosi concetti corrispondono.

Osservare i file nel progetto:

-   `Main.cs`: contiene il punto di ingresso principale dell'app. All'avvio l'app contiene la prima classe e il primo metodo eseguiti.
-   `AppDelegate.cs`: questo file contiene la classe principale dell'app responsabile dell'ascolto degli eventi del sistema operativo.
-   `Info.plist`: questo file contiene le proprietà dell'app, ad esempio il nome dell'applicazione, le icone e così via.
-   `Entitlements.plist`: questo file contiene gli entitlement per l'app e consente l'accesso ad esempio al supporto Sandboxing e iCloud.
-  `Main.storyboard`: definisce l'interfaccia utente (finestre e menu) di un'app e imposta il layout delle interconnessioni tra le finestre tramite Segue. Gli storyboard sono file XML che contengono la definizione delle visualizzazioni (elementi dell'interfaccia utente). Questo file può essere creato e gestito tramite Interface Builder in Xcode.
-   `ViewController.cs`: controller della finestra principale. I controller sono descritti nel dettaglio in un altro articolo. Per ora è sufficiente considerare un controller come il motore principale di una visualizzazione specifica.
-   `ViewController.designer.cs`: questo file contiene il codice plumbing che consente l'integrazione con l'interfaccia utente della schermata principale.

Le sezioni seguenti descrivono brevemente alcuni di questi file. I file sono descritti nel dettaglio più avanti, ma è consigliabile comprenderne ora le nozioni di base.

<a name="Main_cs" />

### <a name="maincs"></a>Main.cs

Il file **Main.cs** è molto semplice. Contiene un metodo `Main` statico che crea una nuova istanza dell'app Xamarin.Mac e passa il nome della classe che gestirà gli eventi del sistema operativo, in questo caso la classe `AppDelegate`:

```csharp
using System;
using System.Drawing;
using Foundation;
using AppKit;
using ObjCRuntime;

namespace Hello_Mac
{
        class MainClass
        {
                static void Main (string[] args)
                {
                        NSApplication.Init ();
                        NSApplication.Main (args);
                }
        }
}
```

<a name="AppDelegate_cs" />

### <a name="appdelegatecs"></a>AppDelegate.cs

Il file `AppDelegate.cs` contiene una classe `AppDelegate` che è responsabile della creazione delle finestre e dell'ascolto degli eventi del sistema operativo:

```csharp
using AppKit;
using Foundation;

namespace Hello_Mac
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

Se lo sviluppatore non ha già creato un'app iOS in precedenza, questo codice non risulterà famigliare sebbene sia piuttosto semplice.

Il metodo `DidFinishLaunching` viene eseguito dopo la creazione dell'istanza dell'app ed è responsabile dell'effettiva creazione della finestra dell'app e dell'avvio del processo di inserimento della visualizzazione nella finestra.

Il metodo `WillTerminate` viene chiamato quando l'utente o il sistema ha creato un'istanza di un arresto dell'app. Lo sviluppatore deve usare questo metodo per finalizzare l'app prima della chiusura, ad esempio per salvare le preferenze dell'utente o le dimensioni e la posizione delle finestre.

<a name="ViewController_cs" />

### <a name="viewcontrollercs"></a>ViewController.cs

Cocoa e per derivazione CocoaTouch usano il modello *MVC* (Model View Controller). La dichiarazione `ViewController` rappresenta l'oggetto che controlla la finestra dell'app. In genere, per ogni finestra creata, e per numerosi altri elementi all'interno delle finestre, è disponibile un controller che è responsabile del ciclo di vita della finestra, ad esempio dell'apertura, dell'aggiunta di nuove visualizzazioni (controlli) e così via.

La classe `ViewController` è il controller della finestra principale. Ciò significa che è responsabile del ciclo di vita della finestra principale. Il controller seguente è descritto più avanti nel dettaglio:

```csharp
using System;

using AppKit;
using Foundation;

namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }
    }
}
```

<a name="ViewController_Designer_cs" />

### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

Il file di progettazione per la classe della finestra principale è attualmente vuoto ma verrà popolato automaticamente da Visual Studio per Mac quando viene creata l'interfaccia utente con Interface Builder all'interno di Xcode:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac to store outlets and
// actions made in the UI designer. If it is removed, they will be lost.
// Manual changes to this file may not be handled correctly.
//
using Foundation;

namespace Hello_Mac
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

Lo sviluppatore in genere non si occupa dei file di progettazione che vengono gestiti automaticamente da Visual Studio per Mac e forniscono il codice plumbing dei requisiti che consente l'accesso ai controlli aggiunti a qualsiasi finestra o visualizzazione dell'app.

Dopo aver creato il progetto app Xamarin.Mac e aver acquisito le nozioni di base sui suoi componenti, passare a Xcode per creare l'interfaccia utente usando Interface Builder.

<a name="Info_plist" />

### <a name="infoplist"></a>Info. plist

Il file `Info.plist` contiene informazioni sull'app Xamarin.Mac, ad esempio **Nome** e **Identificatore del bundle**:

[![](hello-mac-images/infoplist01.png "Editor plist di Visual Studio per Mac")](hello-mac-images/infoplist01.png#lightbox)

Il file definisce anche lo _Storyboard_ che verrà usato per visualizzare l'interfaccia utente per l'app Xamarin.Mac nella casella a discesa **Interfaccia principale**. Nel caso dell'esempio precedente `Main` nella casella a discesa si riferisce a `Main.storyboard` nell'albero di origine del progetto in **Esplora soluzioni**. Definisce anche le icone dell'app specificando il *Catalogo asset* che le contiene, in questo caso AppIcons.

### <a name="entitlementsplist"></a>Entitlements.plist

Il file `Entitlements.plist` dell'app controlla gli entitlement dell'app Xamarin.Mac, ad esempio **Sandboxing** e **iCloud**:

[![](hello-mac-images/entitlements01.png "Editor degli entitlement di Visual Studio per Mac")](hello-mac-images/entitlements01.png#lightbox)

Per l'esempio Hello World non sono necessari entitlement. La sezione seguente illustra come usare Interface Builder di Xcode per modificare il file `Main.storyboard` e definire l'interfaccia utente dell'app Xamarin.Mac.

<a name="Introduction_to_Xcode_and_Interface_Builder" />

## <a name="introduction-to-xcode-and-interface-builder"></a>Introduzione a Xcode e Interface Builder

Apple ha creato uno strumento chiamato Interface Builder incluso in Xcode che consente agli sviluppatori di creare visivamente un'interfaccia utente in una finestra di progettazione. Xamarin.Mac si integra facilmente con Interface Builder consentendo di creare un'interfaccia utente con gli stessi strumenti degli utenti di Objective-C.

Per iniziare, fare doppio clic sul file `Main.storyboard` in **Esplora soluzioni** per aprirlo e modificarlo in Xcode e Interface Builder:

[![](hello-mac-images/xcode01.png "File Main.storyboard in Esplora soluzioni")](hello-mac-images/xcode01.png#lightbox)

Viene avviato Xcode come illustrato di seguito:

[![](hello-mac-images/xcode02.png "Visualizzazione predefinita di Interface Builder di Xcode")](hello-mac-images/xcode02.png#lightbox)

Prima di iniziare la progettazione dell'interfaccia, leggere una rapida panoramica di Xcode per conoscere le funzioni principali che verranno usate.

> [!NOTE]
> Lo sviluppatore non deve necessariamente usare Xcode e Interface Builder per creare l'interfaccia utente per un'app Xamarin.Mac che può essere creata direttamente dal codice C#. Per ragioni di semplicità, nella parte rimanente di questa esercitazione verrà usato Interface Builder per creare l'interfaccia utente.


<a name="Components_of_Xcode" />

### <a name="components-of-xcode"></a>Componenti di Xcode

Quando si apre un file `.storyboard` in Xcode da Visual Studio per Mac, il file si apre con **Project Navigator** (Selezione progetto) a sinistra, **Interface Hierarchy** (Gerarchia interfacce) e **Interface Editor** (Editor interfaccia) al centro e la sezione **Properties & Utilities** (Proprietà e utilità) a destra:

[![](hello-mac-images/xcode03.png "Sezioni di Interface Builder in Xcode")](hello-mac-images/xcode03.png#lightbox)

Di seguito è descritta ognuna di queste funzioni di Xcode e viene illustrato come usarle per creare l'interfaccia di un'app Xamarin.Mac.

<a name="Project_Navigation" />

### <a name="project-navigation"></a>Project Navigator (Navigazione progetto)

Quando si apre un file `.storyboard` per la modifica in Xcode, Visual Studio per Mac crea un *file di progetto Xcode* in background per la comunicazione delle modifiche da Visual Studio per Mac a Xcode e viceversa. Successivamente, quando lo sviluppatore torna a Visual Studio per Mac da Xcode, Visual Studio per Mac sincronizza tutte le modifiche apportate al progetto con il progetto Xamarin.Mac.

La sezione **Project Navigation** (Navigazione progetto) consente allo sviluppatore di navigare in tutti i file del progetto Xcode _shim_ . Lo sviluppatore è in genere interessato solo ai file `.storyboard` nell'elenco, ad esempio `Main.storyboard`.

<a name="Interface_Hierarchy" />

### <a name="interface-hierarchy"></a>Interface Hierarchy (Gerarchia interfacce)

La sezione **Interface Hierarchy** (Gerarchia interfacce) consente allo sviluppatore di accedere facilmente a diverse proprietà chiave dell'interfaccia utente, ad esempio a **Placeholders** (Segnaposto) e a **Window** (Finestra). Questa sezione può essere usata per accedere ai singoli elementi (visualizzazioni) che costituiscono l'interfaccia utente e per modificare la modalità di annidamento degli elementi trascinandoli all'interno della gerarchia.

<a name="Interface_Editor" />

### <a name="interface-editor"></a>Interface Editor (Editor interfaccia)

La sezione **Interface Editor** (Editor interfaccia) visualizza la superficie su cui viene disposta graficamente l'interfaccia utente. Trascinare gli elementi dalla sezione **Library** (Libreria) della sezione **Properties & Utilities** (Proprietà e utilità) per creare il progetto. Gli elementi dell'interfaccia utente (visualizzazioni) inseriti nell'area di progettazione vengono aggiunti alla sezione **Interface Hierarchy** (Gerarchia interfacce) in modo che vengano visualizzati in **Interface Editor** (Editor interfaccia).

<a name="Properties_Utilities" />

### <a name="properties--utilities"></a>Properties & Utilities (Proprietà e utilità)

La sezione **Properties & Utilities** (Proprietà e utilità) è divisa in due sezioni principali, **Properties** (Proprietà) e **Library** (Libreria):

[![](hello-mac-images/xcode04.png "Controllo delle proprietà")](hello-mac-images/xcode04.png#lightbox)

Inizialmente questa sezione è quasi vuota. Tuttavia, se lo sviluppatore seleziona un elemento in **Interface Editor** (Editor interfaccia) o **Interface Hierarchy** (Gerarchia interfacce), la sezione **Properties** (proprietà) viene popolata con le informazioni relative all'elemento e alle proprietà che possono essere modificate.

Nella sezione **Properties** (Proprietà) sono incluse otto *schede di controllo*, come mostra la figura seguente:

[![](hello-mac-images/xcode05.png "Panoramica di tutti i controlli")](hello-mac-images/xcode05.png#lightbox)

<a name="Properties_Utility_Types" />

### <a name="properties--utility-types"></a>Tipi di proprietà e utilità

Da sinistra a destra, queste schede sono:

-   **File Inspector** (Controllo file): visualizza le informazioni sui file, ad esempio il nome file e il percorso del file Xib in fase di modifica.
-   **Quick Help** (Guida rapida): questa scheda offre una guida contestuale in base all'elemento selezionato in Xcode.
-   **Identity Inspector** (Controllo identità): offre informazioni sul comando o la visualizzazione selezionata.
-   **Attributes Inspector** (Controllo attributi): consente allo sviluppatore di personalizzare diversi attributi del comando o della visualizzazione selezionata.
-   **Size Inspector** (Controllo dimensioni): consente allo sviluppatore di controllare il comportamento relativo alle dimensioni e al ridimensionamento del comando o della visualizzazione selezionata.
-   **Connections Inspector** (Controllo connessioni): visualizza le connessioni **Outlet** e **Action** (Azione) dei comandi selezionati. Outlet e azioni sono descritti più avanti in dettaglio.
-   **Bindings Inspector** (Controllo associazioni): consente allo sviluppatore di configurare i comandi in modo che i valori vengano associati automaticamente ai modelli di dati.
-   **View Effects Inspector** (Controllo effetti visualizzazioni): consente allo sviluppatore di specificare gli effetti nei comandi, ad esempio le animazioni.

Usare la sezione **Library** (Libreria) per cercare i comandi e gli oggetti da inserire nell'area di progettazione per creare graficamente l'interfaccia utente:

[![](hello-mac-images/xcode06.png "Controllo Library (Libreria) di Xcode")](hello-mac-images/xcode06.png#lightbox)

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Dopo aver acquisito le nozioni di base sull'IDE di Xcode e Interface Builder, lo sviluppatore può procedere alla creazione dell'interfaccia utente per la visualizzazione principale.

Eseguire le operazioni seguenti:

1. In Xcode trascinare **Push Button** (Pulsante Push) da **Library Section** (Sezione librerie):

    [![](hello-mac-images/xcode07.png "Selezione di NSButton da Library Inspector")](hello-mac-images/xcode07.png#lightbox)

2. Trascinare il pulsante in **View** (visualizzazione) in **Window Controller** (Controller finestre) in **Interface Editor** (Editor interfaccia):

    [![](hello-mac-images/xcode08.png "Aggiunta di un pulsante alla progettazione dell'interfaccia")](hello-mac-images/xcode08.png#lightbox)

3. Fare clic sulla proprietà **Title** (Titolo) in **Attribute Inspector** (Controllo attributi) e modificare il titolo del pulsante in `Click Me`:

    [![](hello-mac-images/xcode09.png "Impostazione delle proprietà del pulsante")](hello-mac-images/xcode09.png#lightbox)

4. Trascinare **Label** (Etichetta) da **Library Section** (Sezione librerie):

    [![](hello-mac-images/xcode10.png "Selezione di un'etichetta dal controllo librerie")](hello-mac-images/xcode10.png#lightbox)

5. Trascinare l'etichetta in **Window** (Finestra) accanto al pulsante in **Interface Editor** (Editor interfaccia):

    [![](hello-mac-images/xcode11.png "Aggiunta di un'etichetta alla progettazione dell'interfaccia")](hello-mac-images/xcode11.png#lightbox)

6. Selezionare il quadratino a destra sull'etichetta e trascinarlo fino a quando non è vicino al bordo della finestra:

    [![](hello-mac-images/xcode12.png "Ridimensionamento dell'etichetta")](hello-mac-images/xcode12.png#lightbox)

7. Selezionare il pulsante aggiunto in **Interface Editor** (Editor interfaccia) e fare clic sull'icona **Constraints Editor** (Editor vincoli) nella parte inferiore della finestra:

    [![](hello-mac-images/xcode13.png "Aggiunta di vincoli al pulsante")](hello-mac-images/xcode13.png#lightbox)

8. Nella parte superiore dell'editor fare clic sui **cursori a I rossi** nella parte superiore e sinistra. Se vengono modificate le dimensioni della finestra, il pulsante viene mantenuto nella stessa posizione nell'angolo superiore sinistro della schermata.

9. Selezionare le caselle **Height** (Altezza) e **Width** (larghezza) e usare le dimensioni predefinite. In questo modo il pulsante mantiene le stesse dimensioni quando si ridimensiona la finestra.

10. Fare clic sul pulsante **Add 4 Constraints** (Aggiungi 4 vincoli) per aggiungere i vincoli e chiudere l'editor.

11. Selezionare l'etichetta e fare di nuovo clic sull'icona **Constraints Editor** (Editor vincoli):

    [![](hello-mac-images/xcode14.png "Aggiunta di vincoli all'etichetta")](hello-mac-images/xcode14.png#lightbox)

12. Se si fa clic sui **cursori a I rossi** nella parte superiore, a destra e a sinistra di **Constraints Editor** (Editor vincoli), l'etichetta viene bloccata alle relative posizioni X e Y e viene ingrandita e ridotta quando vengono modificate le dimensioni della finestra nell'applicazione in esecuzione.

13. Selezionare nuovamente la casella **Height** (Altezza) e usare la dimensione predefinita, quindi fare clic sul pulsante **Add 4 Constraints** (Aggiungi 4 vincoli) per aggiungere i vincoli e chiudere l'editor.

14. Salvare le modifiche apportate all'interfaccia utente.

Durante il ridimensionamento e lo spostamenti dei comandi, si noti che Interface Builder offre suggerimenti utili basati su [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Linee guida dell'interfaccia umana OS X). Queste linee guida aiutano lo sviluppatore a creare app di qualità elevata con un aspetto famigliare per gli utenti Mac.

Osservare la sezione **Interface Hierarchy** (Gerarchia interfacce) per verificare come appaiono il layout e la gerarchia degli elementi che costituiscono l'interfaccia utente:

[![](hello-mac-images/xcode15.png "Selezione di un elemento in Interface Hierarchy (Gerarchia interfacce)")](hello-mac-images/xcode15.png#lightbox)

In questa sezione lo sviluppatore può selezionare gli elementi da modificare o trascinare per riordinare gli elementi dell'interfaccia utente. Ad esempio, se un elemento dell'interfaccia utente è stato coperto da un altro elemento, è possibile trascinarlo nella parte inferiore dell'elenco per impostarlo come elemento in primo piano nella finestra.

Dopo aver creato l'interfaccia utente, lo sviluppatore deve esporre gli elementi dell'interfaccia in modo che Xamarin.Mac possa accedere e interagire con essi nel codice C#. La sezione successiva, **Outlet e azioni**, descrive come eseguire questa operazione.

<a name="Outlets_and_Actions" />

### <a name="outlets-and-actions"></a>Outlet e azioni

Che cosa sono gli **outlet** e le **azioni**? Nella programmazione dell'interfaccia utente .NET tradizionale, i comandi dell'interfaccia utente vengono esposti automaticamente come proprietà quando vengono aggiunti. In Mac ciò non avviene, la semplice aggiunta di un comando in una visualizzazione non lo rende accessibile al codice. Lo sviluppatore deve esporre esplicitamente l'elemento dell'interfaccia utente al codice. A tale scopo, Apple offre due opzioni:

-   **Outlet**: gli outlet sono analoghi alle proprietà. Se lo sviluppatore collega un comando a un outlet, il comando viene esposto al codice tramite una proprietà per consentire di eseguire operazioni come collegare gestori di eventi, chiamare metodi e così via.
-   **Azioni**: le azioni sono analoghe al modello di comando in WPF. Ad esempio, quando viene eseguita un'azione su un comando, come il clic su un pulsante, il comando chiama automaticamente un metodo nel codice. Le azioni sono un'opzione pratica e potente poiché lo sviluppatore può collegare numerosi comandi alla stessa azione.

In Xcode gli **outlet** e le **azioni** vengono aggiunti direttamente nel codice tramite *trascinamento*. In particolare, ciò significa che per creare un **outlet** o un'**azione**, lo sviluppatore sceglie un comando a cui aggiungere un **outlet** o un'**azione**, tiene premuto il tasto **CTRL** sulla tastiera e trascina il comando direttamente nel codice.

Per gli sviluppatori Xamarin.Mac, ciò significa che lo sviluppatore trascina i file stub Objective-C che corrispondono al file C# nella posizione in cui creare l'**outlet** o l'**azione**. Visual Studio per Mac ha creato un file denominato `ViewController.h` come parte del progetto Xcode shim generato per l'uso di Interface Builder:

[![](hello-mac-images/xcode16.png "Visualizzazione dell'origine in Xcode")](hello-mac-images/xcode16.png#lightbox)

Questo file stub `.h` esegue il mirroring di `ViewController.designer.cs` che viene aggiunto automaticamente al progetto Xamarin.Mac quando viene creato un nuovo `NSWindow`. Questo file viene usato per sincronizzare le modifiche effettuate da Interface Builder e rappresenta la posizione in cui vengono creati gli **outlet** e le **azioni** in modo che gli elementi dell'interfaccia utente vengano esposti al codice C#.

<a name="Adding_an_Outlet" />

#### <a name="adding-an-outlet"></a>Aggiunta di un outlet

Dopo aver acquisito le nozioni di base su **outlet** e **azioni**,  creare un **outlet** per esporre l'etichetta creata al codice C#.

Eseguire le operazioni seguenti:

1. In Xcode nell'angolo superiore destro della schermata fare clic sul pulsante **Double Circle** (Doppio cerchio) per aprire **Assistant Editor** (Editor assistente):

    [![](hello-mac-images/outlet01.png "Visualizzazione di Assistant Editor (Editor assistente)")](hello-mac-images/outlet01.png#lightbox)

2. Xcode passa in una modalità di visualizzazione suddivisa con **Interface Editor** (Editor interfacce) da un lato e **Code Editor** (Editor codice) dall'altro.

3. Si noti che Xcode ha selezionato automaticamente il file **ViewController.m** in **Code Editor** (Editor codice) che non è la selezione corretta. Come indicato nella sezione precedente in cui sono stati descritti gli **outlet** e le **azioni**, è necessario che sia selezionato il file **ViewController.h**.

4. Nella parte superiore di **Code Editor** (Editor codice) fare clic su **Automatic Link** (Collegamento automatico) e selezionare il file `ViewController.h`:

    [![](hello-mac-images/outlet02.png "Selezione del file corretto")](hello-mac-images/outlet02.png#lightbox)

5. Xcode dovrebbe ora avere il file selezionato corretto:

    [![](hello-mac-images/outlet03.png "Visualizzazione del file ViewController.h")](hello-mac-images/outlet03.png#lightbox)

6. **L'ultimo passaggio è molto importante.** Se non viene selezionato il file corretto, lo sviluppatore non sarà in grado di creare gli **outlet** e le **azioni** oppure verrà eseguita l'esposizione a una classe errata in C#.

7. In **Interface Editor** (Editor interfaccia) tenere premuto il tasto **CTRL** sulla tastiera e fare clic e trascinare l'etichetta creata nell'editor di codice sotto il codice `@interface ViewController : NSViewController {}`:

    [![](hello-mac-images/outlet04.png "Trascinamento per la creazione di un outlet")](hello-mac-images/outlet04.png#lightbox)

8. Viene visualizzata una finestra di dialogo. Lasciare **Connection** (Connessione) impostata su **Outlet** e immettere `ClickedLabel` per **Name** (Nome):

    [![](hello-mac-images/outlet05.png "Definizione dell'outlet")](hello-mac-images/outlet05.png#lightbox)

9. Fare clic sul pulsante **Connect** (Connetti) per creare l'**outlet**:

    [![](hello-mac-images/outlet06.png "Visualizzazione dell'outlet finale")](hello-mac-images/outlet06.png#lightbox)

10. Salvare le modifiche apportate al file.

<a name="Adding_an_Action" />

#### <a name="adding-an-action"></a>Aggiunta di un'azione

Esporre quindi il pulsante al codice C#. Come per l'etichetta precedente, lo sviluppatore può collegare il pulsante a un **Outlet**. Poiché si vuole solo rispondere al clic sul pulsante, usare un'**Azione**.

Eseguire le operazioni seguenti:

1. Assicurarsi che in Xcode sia ancora attivo **Assistant Editor** (Editor assistente) e che il file **ViewController.h** sia visibile in **Code Editor** (Editor codice).
2. In **Interface Editor** (Editor interfaccia) tenere premuto il tasto **CTRL** sulla tastiera e fare clic e trascinare il pulsante creato in precedenza nell'editor di codice sotto il codice `@property (assign) IBOutlet NSTextField *ClickedLabel;`:

    [![](hello-mac-images/action01.png "Trascinamento per la creazione di un'azione")](hello-mac-images/action01.png#lightbox)

3. Impostare **Connection** (Connessione) sul tipo **Action** (Azione):

    [![](hello-mac-images/action02.png "Definizione di un'azione")](hello-mac-images/action02.png#lightbox)

4. Immettere `ClickedButton` in **Name** (Nome):

    [![](hello-mac-images/action03.png "Assegnazione di un nome alla nuova azione")](hello-mac-images/action03.png#lightbox)

5. Fare clic sul pulsante **Connect** (Connetti) per creare l'**azione**:

    [![](hello-mac-images/action04.png "Visualizzazione dell'azione finale")](hello-mac-images/action04.png#lightbox)

6. Salvare le modifiche apportate al file.

Dopo aver collegato l'interfaccia utente e averla esposta al codice C#, tornare a Visual Studio per Mac che sincronizzerà le modifiche apportate in Xcode e Interface Builder.

> [!NOTE]
> La creazione dell'interfaccia utente e degli **outlet** e delle **azioni** di questa prima app potrebbe essere sembrata un'operazione lunga, ma sono stati introdotti numerosi nuovi concetti ed è stata fornita una lunga descrizione delle nuove funzionalità. Una maggior familiarità con Interface Builder consentirà di creare l'interfaccia e gli **outlet** e le **azioni** in pochi minuti.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizzazione delle modifiche con Xcode

Quando lo sviluppatore torna a Visual Studio per Mac da Xcode, tutte le modifiche apportate in xCode verranno automaticamente sincronizzate con il progetto Xamarin.Mac.

Selezionare **ViewController.designer.cs** in **Esplora soluzioni** per visualizzare in che modo l'**outlet** e l'**azione** sono stati collegati nel codice C#:

[![](hello-mac-images/sync01.png "Sincronizzazione delle modifiche con Xcode")](hello-mac-images/sync01.png#lightbox)

Si noti come le due definizioni nel file **ViewController.designer.cs**:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Sono allineate con le definizioni nel file `ViewController.h` in Xcode:

```csharp
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Visual Studio per Mac controlla le modifiche apportate al file **.h** e le sincronizza automaticamente nel file **.designer.cs** corrispondente per esporle all'app. Si noti che poiché **ViewController.designer.cs** è una classe parziale, Visual Studio per Mac non deve modificare **ViewController.cs** sovrascrivendo eventuali modifiche apportate alla classe dallo sviluppatore.

In genere lo sviluppatore non ha mai la necessità di aprire il file **ViewController.designer.cs**. Il file è stato descritto esclusivamente per scopi didattici.

> [!NOTE]
> Nella maggior parte dei casi, Visual Studio per Mac rileva automaticamente le modifiche apportate in Xcode e le sincronizza con il progetto Xamarin.Mac. Se la sincronizzazione non viene eseguita automaticamente, tornare a Xcode e quindi di nuovo in Visual Studio per Mac. Questa operazione avvia solitamente un ciclo di sincronizzazione.

<a name="Writing_the_Code" />

## <a name="writing-the-code"></a>Scrittura del codice

Dopo aver creato l'interfaccia utente e aver esposto gli elementi dell'interfaccia al codice tramite **outlet** e **azioni**, è possibile procedere alla scrittura del codice per attivare il programma.

Per questa app di esempio, ogni volta che viene fatto clic sul primo pulsante, l'etichetta verrà aggiornata per indicare il numero di volte in cui è stato fatto clic sul pulsante. A tale scopo, aprire il file `ViewController.cs` per la modifica facendo doppio clic sul file in **Esplora soluzioni**:

[![](hello-mac-images/code01.png "Visualizzazione del file ViewController.cs in Visual Studio per Mac")](hello-mac-images/code01.png#lightbox)

Innanzitutto, creare una variabile a livello di classe nella classe `ViewController` per tenere traccia del numero di clic eseguiti. Modificare la definizione della classe nel modo seguente:

```csharp
namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

Successivamente, nella stessa classe (`ViewController`) eseguire l'override del metodo `ViewDidLoad` e aggiungere codice per impostare il messaggio iniziale per l'etichetta:

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Usare `ViewDidLoad` anziché un altro metodo come `Initialize` poiché `ViewDidLoad` viene chiamato *dopo* che il sistema operativo ha caricato e creato un'istanza dell'interfaccia utente dal file **.storyboard**. Se lo sviluppatore tenta di accedere all'etichetta prima che il caricamento e la creazione dell'istanza del file **.storyboard** siano stati completati, viene visualizzato un errore `NullReferenceException` poiché l'etichetta non è ancora stata creata.

Successivamente, aggiungere il codice per rispondere al clic dell'utente sul pulsante. Aggiungere il metodo parziale seguente alla classe `ViewController`:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Questo codice si collega all'**azione** creata in Xcode e Interface Builder e verrà chiamato ogni volta che l'utente fa clic sul pulsante.

<a name="Testing_the_Application" />

## <a name="testing-the-application"></a>Verifica dell'applicazione

È ora possibile compilare ed eseguire l'app per assicurarsi che venga eseguita come previsto. Lo sviluppatore può compilare ed eseguire l'app in un unico passaggio oppure compilare l'app senza eseguirla.

Ogni volta che viene compilata un'app, lo sviluppatore può scegliere il tipo di build:

-   **Debug**: la build di debug viene compilata in un file **.app** (applicazione) con metadati aggiuntivi che consentono allo sviluppatore di eseguire il debug mentre l'app è in esecuzione.
-   **Rilascio**: anche la build di rilascio crea un file **.app** ma poiché il file non include le informazioni di debug risulta più piccolo e viene eseguito più rapidamente.

Lo sviluppatore può selezionare il tipo di build dal **Selettore configurazione** nell'angolo superiore sinistro della schermata di Visual Studio per Mac:

[![](hello-mac-images/run01.png "Selezione di una build di debug")](hello-mac-images/run01.png#lightbox)

<a name="Building_the_Application" />

## <a name="building-the-application"></a>Compilazione dell'applicazione

Nel caso dell'esempio, poiché si vuole creare solo una build di debug, assicurarsi che sia selezionata l'opzione **Debug**. Compilare l'app premendo **⌘B** oppure scegliendo **Compila tutto** dal menu **Compila**.

Se non si sono verificati errori, viene visualizzato il messaggio **Compilazione riuscita** nella barra di stato di Visual Studio per Mac. Se si sono verificati errori, rivedere il progetto e assicurarsi che siano stati eseguiti correttamente i passaggi descritti. Iniziare verificando che il codice in Xcode e in Visual Studio per Mac corrisponda al codice dell'esercitazione.

<a name="Running_the_Application" />

## <a name="running-the-application"></a>Esecuzione dell'applicazione

È possibile eseguire l'app in tre modi:

-  Premere **⌘+INVIO**.
-  Scegliere **Debug** dal menu **Esegui**.
-  Fare clic sul pulsante **Esegui** nella barra degli strumenti di Visual Studio per Mac (sopra **Esplora soluzioni**).

Se non è già stata compilata, l'app verrà compilata e verrà avviata in modalità di debug con la finestra di interfaccia principale visualizzata:

[![](hello-mac-images/run02.png "Esecuzione dell'applicazione")](hello-mac-images/run02.png#lightbox)

Se viene fatto clic sul pulsante alcune volte, l'etichetta dovrà essere aggiornata con il conteggio:

[![](hello-mac-images/run03.png "Visualizzazione dei risultati dei clic sul pulsante")](hello-mac-images/run03.png#lightbox)

<a name="Where_to_Next" />

## <a name="where-to-next"></a>Articoli successivi

Con le nozioni di base sull'uso di un'applicazione Xamarin.Mac, vedere i documenti seguenti per un approfondimento:

- [Introduction to Storyboards](~/mac/platform/storyboards/index.md) (Introduzione agli storyboard): questo articolo offre un'introduzione all'uso degli storyboard in un'app Xamarin.Mac. Viene descritto come creare e gestire l'interfaccia utente dell'app usando gli storyboard e Interface Builder di Xcode.
- [Windows](~/mac/user-interface/window.md) (Finestre): questo articolo descrive l'uso di finestre e pannelli in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le finestre e i pannelli in Xcode e Interface Builder, caricare le finestre e i pannelli da file con estensione xib, usare le finestre e rispondere alle finestre nel codice C#.
- [Dialogs](~/mac/user-interface/dialog.md) (Finestra di dialogo): questo articolo descrive l'uso delle finestre di dialogo e delle finestre modali in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le finestre modali in Xcode e Interface builder, usare le finestre di dialogo standard, visualizzare e rispondere alle finestre nel codice C#.
- [Alerts](~/mac/user-interface/alert.md) (Avvisi): questo articolo descrive l'uso degli avvisi in un'applicazione Xamarin.Mac. Viene descritto come creare e visualizzare gli avvisi dal codice C# e rispondere agli avvisi.
- [Menus](~/mac/user-interface/menu.md) (Menu): i menu vengono usate in diverse parti dell'interfaccia utente di un'applicazione Mac; dal menu principale dell'applicazione nella parte superiore della schermata ai popup e ai menu contestuali che possono essere visualizzati in qualsiasi posizione all'interno di una finestra. I menu sono parte integrante dell'esperienza utente di un'applicazione Mac. Questo articolo descrive l'uso dei menu Cocoa in un'applicazione Xamarin.Mac.
- [Toolbars](~/mac/user-interface/toolbar.md) (Barre degli strumenti): questo articolo descrive l'uso delle barre degli strumenti in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le barre degli strumenti in Xcode e Interface Builder, esporre gli elementi della barra degli strumenti al codice usando outlet e azioni, abilitare e disabilitare gli elementi della barra degli strumenti e rispondere agli elementi della barra degli strumenti nel codice C#.
- [Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabella): questo articolo descrive l'uso delle visualizzazioni tabella in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le visualizzazioni tabella in Xcode e Interface Builder, esporre gli elementi della visualizzazione tabella al codice usando outlet e azioni, popolare gli elementi della tabella e rispondere agli elementi della visualizzazione tabella nel codice C#.
- [Outline Views](~/mac/user-interface/outline-view.md) (Visualizzazioni Struttura): questo articolo descrive l'uso delle visualizzazioni Struttura in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le visualizzazioni Struttura in Xcode e Interface Builder, esporre gli elementi della visualizzazione Struttura al codice usando outlet e azioni, popolare gli elementi della struttura e rispondere agli elementi della visualizzazione Struttura nel codice C#.
- [Source Lists](~/mac/user-interface/source-list.md) (Elenchi di origine): questo articolo descrive l'uso degli elenchi di origine in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire gli elenchi di origine in Xcode e Interface Builder, esporre gli elementi degli elenchi di origine al codice usando outlet e azioni, popolare gli elementi degli elenchi di origine e rispondere agli elementi degli elenchi di origine nel codice C#.
- [Collection Views](~/mac/user-interface/collection-view.md) (Visualizzazioni raccolta): questo articolo descrive l'uso delle visualizzazioni raccolta in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le visualizzazioni raccolta in Xcode e Interface Builder, esporre gli elementi della visualizzazione raccolta al codice usando outlet e azioni, popolare le visualizzazioni raccolta e rispondere alle visualizzazioni raccolta nel codice C#.
- [Working with Images](~/mac/app-fundamentals/image.md) (Uso delle immagini): questo articolo descrive l'uso delle immagini e delle icone in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le immagini necessarie per creare l'icona di un'app e usare le immagini nel codice C# e in Interface Builder di Xcode.

Si consiglia anche di vedere la [galleria di esempi Mac](https://developer.xamarin.com/samples/mac/all/) che include una vasta gamma di codici pronti all'uso utili per sviluppare rapidamente un progetto Xamarin.Mac.

Per un esempio di un'app Xamarin.Mac completa che include molte delle funzioni che gli utenti si aspettano di trovare in un tipica applicazione Mac, vedere l'[app di esempio SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto i componenti di base di un'app Xamarin.Mac standard. È stata illustrata la creazione di una nuova app in Visual Studio per Mac, la progettazione dell'interfaccia utente in Xcode e Interface Builder, l'esposizione degli elementi dell'interfaccia utente al codice C# tramite **outlet** e **azioni**, l'aggiunta di codice per gli elementi dell'interfaccia utente e infine la compilazione e il test di un'app Xamarin.Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac (sample)](https://developer.xamarin.com/samples/mac/Hello_Mac/) (Hello, Mac - Esempio)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
