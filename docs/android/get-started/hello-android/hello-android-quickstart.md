---
title: 'Hello, Android: guida introduttiva'
description: "In questa guida divisa in due parti è possibile creare un'applicazione Xamarin.Android per la prima volta usando Visual Studio o Visual Studio per Mac e comprendere le nozioni di base dello sviluppo di applicazioni Android con Xamarin. Verranno descritti gli strumenti, i concetti e i passaggi necessari per creare e distribuire un'applicazione Xamarin.Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 58197ae9f7c05ab9a838555ba5ab496550a16bce
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="hello-android-quickstart"></a>Hello, Android: guida introduttiva

_In questa guida divisa in due parti è possibile creare un'applicazione Xamarin.Android per la prima volta usando Visual Studio o Visual Studio per Mac e comprendere le nozioni di base dello sviluppo di applicazioni Android con Xamarin. Verranno descritti gli strumenti, i concetti e i passaggi necessari per creare e distribuire un'applicazione Xamarin.Android._

## <a name="hello-android-quickstart"></a>Hello, Android: guida introduttiva

In questa guida verrà creata un'applicazione che converte un numero di telefono alfanumerico immesso dall'utente in un numero di telefono numerico e visualizza il numero di telefono numerico all'utente. L'applicazione finale ha l'aspetto seguente:

[![Screenshot dell'app completata](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)


## <a name="requirements"></a>Requisiti

Per seguire questa procedura dettagliata è necessario quanto segue:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Windows 7 o versione successiva.

-   Visual Studio 2015 Professional o versione successiva.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

-   Ultima versione di Visual Studio per Mac.

-   OS X Yosemite o versione successiva.

-----

In questa procedura si presuppone che l'ultima versione di Xamarin.Android sia installata e in esecuzione sulla piattaforma desiderata. Per istruzioni sull'istallazione di Xamarin.Android, vedere le guide relative all'[installazione di Xamarin.Android](~/android/get-started/installation/index.md).
Prima di iniziare, scaricare e decomprimere le [icone delle app e le schermate di avvio Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true).

## <a name="configuring-emulators"></a>Configurazione degli emulatori

Se si usa l'emulatore di Android SDK di Google, è consigliabile configurare l'emulatore per l'uso dell'accelerazione hardware. Le istruzioni per configurare l'accelerazione hardware sono disponibili in [Hardware Acceleration](~/android/get-started/installation/android-emulator/hardware-acceleration.md) (Accelerazione hardware).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se si usa l'emulatore Android di Visual Studio, è necessario abilitare Hyper-V nel computer. Per altre informazioni sulla configurazione dell'emulatore Android di Visual Studio, vedere [System Requirements for the Visual Studio Emulator for Android](https://msdn.microsoft.com/en-us/library/mt228280.aspx) (Requisiti di sistema per l'emulatore Android di Visual Studio).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

-----

## <a name="walkthrough"></a>Procedura dettagliata

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Avviare Visual Studio.  Fare clic su **File > Nuovo > Progetto** per creare un nuovo progetto.

Nella finestra di dialogo **Nuovo progetto** fare clic sul modello **App vuota (Android)**.
Assegnare il nome `Phoneword` al nuovo progetto. Fare clic su **OK** per creare il nuovo progetto:

[![Nuovo progetto Phoneword](hello-android-quickstart-images/vs/02-new-project-name-sml.png)](hello-android-quickstart-images/vs/02-new-project-name.png#lightbox)

### <a name="creating-the-layout"></a>Creazione del layout

Dopo aver creato il nuovo progetto, espandere la cartella **Risorse** e quindi la cartella di **layout** in **Esplora soluzioni**.
Fare doppio clic su **Main.axml** per aprirlo in Android Designer. Questo è il file di layout per la schermata dell'app:

[![Aprire Main.axml](hello-android-quickstart-images/vs/04-open-layout-sml.png)](hello-android-quickstart-images/vs/04-open-layout.png#lightbox)

Dalla **Casella degli strumenti** (l'area a sinistra) immettere `text` nel campo di ricerca e trascinare un widget **Testo (grande)** nell'area di progettazione (l'area al centro):

[![Aggiungere widget di testo di grandi dimensioni](hello-android-quickstart-images/vs/04-large-text-sml.png)](hello-android-quickstart-images/vs/04-large-text.png#lightbox)

Con l'opzione **Testo (grande)** selezionata nell'area di progettazione, usare il riquadro **Proprietà** per modificare la proprietà `text` del widget **Testo (grande)** in `Enter a Phoneword:` come illustrato di seguito:

[![Impostare le proprietà di un testo di grandi dimensioni](hello-android-quickstart-images/vs/05-enter-a-phoneword-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword.png#lightbox)

Trascinare un widget **Testo normale** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Testo (grande)**:

[![Aggiungere un widget Testo normale](hello-android-quickstart-images/vs/06-plain-text-sml.png)](hello-android-quickstart-images/vs/06-plain-text.png#lightbox)

Con il widget **Testo normale** selezionato nell'area di progettazione, usare il riquadro **Proprietà** per modificare la proprietà `id` del widget **Testo normale** in `@+id/PhoneNumberText` e modificare la proprietà `text` in `1-855-XAMARIN`:

[![Impostare le proprietà del testo normale](hello-android-quickstart-images/vs/07-add-properties-sml.png)](hello-android-quickstart-images/vs/07-add-properties.png#lightbox)

Trascinare un **Pulsante** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Testo normale**:

[![Trascinare il pulsante di traduzione nell'area di progettazione](hello-android-quickstart-images/vs/08-drag-button-sml.png)](hello-android-quickstart-images/vs/08-drag-button.png#lightbox)

Con il **Pulsante** selezionato nell'area di progettazione, usare il riquadro **Proprietà** per modificare la proprietà `id` del **Pulsante** in `@+id/TranslateButton` e modificare la proprietà `text` in `Translate`:

[![Impostare le proprietà del pulsante di traduzione](hello-android-quickstart-images/vs/09-translate-button-sml.png)](hello-android-quickstart-images/vs/09-translate-button.png#lightbox)

Trascinare un oggetto **TextView** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Pulsante**. Impostare la proprietà `id` dell'oggetto **TextView** su `@+id/TranslatedPhoneWord` e modificare `text` in una stringa vuota:

[![Impostare le proprietà nella visualizzazione di testo.](hello-android-quickstart-images/vs/10-textview-properties-sml.png)](hello-android-quickstart-images/vs/10-textview-properties.png#lightbox)    

Salvare premendo **CTRL+S**.

### <a name="writing-translation-code"></a>Scrittura del codice di conversione

Il passaggio successivo consiste nell'aggiungere codice per convertire i numeri di telefono da alfanumerici a numerici. Aggiungere un nuovo file al progetto facendo clic con il pulsante destro del mouse sul progetto **Phoneword** nel riquadro **Esplora soluzioni** e scegliendo **Aggiungi > Nuovo elemento...**  come illustrato di seguito:

[![Aggiungere un nuovo elemento](hello-android-quickstart-images/vs/12-add-new-item-sml.png)](hello-android-quickstart-images/vs/12-add-new-item.png#lightbox)

Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Visual C# > Codice** e assegnare al nuovo file di codice il nome **PhoneTranslator.cs**:

[![Aggiungere PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-sml.png)](hello-android-quickstart-images/vs/14-add-class.png#lightbox)

Viene creata una nuova classe C# vuota. Inserire il codice seguente nel file:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
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
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Salvare le modifiche al file **PhoneTranslator.cs** facendo clic su **File > Salva** (o premendo **CTRL+S**), quindi chiudere il file.

### <a name="wiring-up-the-interface"></a>Associazione dell'interfaccia

Il passaggio successivo consiste nell'aggiungere codice per collegare l'interfaccia utente tramite l'inserimento di codice sottostante nella classe `MainActivity`. Iniziare collegando il pulsante **Traduci**. Nella classe `MainActivity` cercare il metodo `OnCreate`. Il passaggio successivo consiste nell'aggiungere il codice del pulsante all'interno di `OnCreate`, sotto le chiamate `base.OnCreate(bundle)` e `SetContentView
(Resource.Layout.Main)`. In primo luogo, modificare il codice del modello in modo che il metodo `OnCreate` sia simile al seguente:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // New code will go here
        }
    }
}
```

Ottenere un riferimento ai controlli che sono stati creati nel file di layout con Android Designer. Aggiungere il codice seguente all'interno del metodo `OnCreate` dopo la chiamata a `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Aggiungere codice che risponde quando l'utente preme il pulsante **Traduci**.
Aggiungere il codice seguente al metodo `OnCreate` (dopo le righe aggiunte nel passaggio precedente):

```csharp
// Add code to translate number
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    string translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Salvare selezionando **File > Salva tutto** (o premendo **CTRL-MAIUSC-S**) e compilare l'applicazione selezionando **Compila > Ricompila soluzione** (o premendo **CTRL-MAIUSC-B**). 

Se sono presenti errori, rivedere i passaggi precedenti e correggere eventuali errori fino a quando l'applicazione non viene compilata correttamente. Se si verifica un errore di compilazione, ad esempio _La risorsa non esiste nel contesto corrente_, verificare che il nome dello spazio dei nomi in **MainActivity.cs** corrisponda al nome del progetto (`Phoneword`) e quindi ricompilare completamente la soluzione. Se si verificano ancora errori di compilazione, verificare di aver installato gli ultimi aggiornamenti di Xamarin.Android.

### <a name="setting-the-label-and-app-icon"></a>Impostazione dell'etichetta e dell'icona dell'app

L'applicazione &ndash; è ora funzionante ed è possibile finalizzarla. In **MainActivity.cs** modificare `Label` per `MainActivity`. Android visualizza `Label` nella parte superiore della schermata per indicare agli utenti la loro posizione all'interno dell'applicazione.
Nella parte superiore della classe `MainActivity` modificare `Label` in `Phone Word` come illustrato di seguito:

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

A questo punto è possibile impostare l'icona dell'applicazione. Per impostazione predefinita, Visual Studio offre un'icona predefinita per il progetto. Eliminare questi file dall'applicazione e sostituirli con un'icona diversa. Espandere la cartella **Risorse** nel **riquadro della soluzione**. Si noti che sono presenti cinque cartelle che iniziano con **mipmap -** e che ogni cartella contiene un singolo file **Icon.png**:

[![Cartelle mipmap- e file Icon.png](hello-android-quickstart-images/vs/21-mipmap-folders-sml.png)](hello-android-quickstart-images/vs/21-mipmap-folders.png#lightbox)

È necessario eliminare ognuno di questi file di icona dal progetto. Fare clic con il pulsante destro su ogni file **Icon.png** e selezionare **Elimina** dal menu di scelta rapida:
   
[![Eliminare il file Icon.png predefinito](hello-android-quickstart-images/vs/21-delete-icon-sml.png)](hello-android-quickstart-images/vs/21-delete-icon.png#lightbox)
   
Fare clic sul pulsante **Elimina** nella finestra di dialogo.

Quindi, scaricare e decomprimere il [set di icone di app Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Il file zip contiene le icone per l'applicazione. Ogni icona è visivamente identica ma a risoluzioni diverse ne viene eseguito il rendering correttamente in dispositivi diversi con densità dello schermo diverse.  Il set di file deve essere copiato nel progetto Xamarin.Android. In Visual Studio in **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **mipmap-hdpi** e selezionare **Aggiungi > Elementi esistenti**:

[![Aggiungere file](hello-android-quickstart-images/vs/22-add-files-sml.png)](hello-android-quickstart-images/vs/22-add-files.png#lightbox)

Dalla finestra di dialogo di selezione passare alla directory delle icone delle app Xamarin decompressa e aprire la cartella **mipmap-hdpi**. Selezionare **Icon.png** e fare clic su **Aggiungi**.

Ripetere questi passaggi per ogni cartella **mipmap-** fino a quando le cartelle delle icone delle app Xamarin **mipmap-** non vengono copiate nelle cartelle **mipmap-** corrispondenti nel progetto **Phoneword**.

Dopo aver copiato tutte le icone nel progetto Xamarin.Android, aprire la finestra di dialogo **Opzioni progetto** facendo clic con il pulsante destro del mouse sul progetto nel **riquadro della soluzione**. Selezionare **Compila > Applicazione Android** e selezionare **@mipmap/icon** dalla casella combinata **Icona applicazione**:

[![Impostazione dell'icona di progetto](hello-android-quickstart-images/vs/25-set-project-icon-sml.png)](hello-android-quickstart-images/vs/25-set-project-icon.png#lightbox)

### <a name="running-the-app"></a>Esecuzione dell'app

Infine, testare l'applicazione eseguendola su un dispositivo Android o un emulatore e convertendo Phoneword:

[![Screenshot dell'app completata](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Avviare Visual Studio per Mac dalla cartella **Applicazioni** o da **Spotlight**. 

Fare clic su **Nuova soluzione...** per creare un nuovo progetto.

Nela finestra di dialogo **Scegli un modello per il nuovo progetto** fare clic su **Android > App** e selezionare il modello **App Android**. Scegliere **Avanti**.

[![Scegliere il modello App Android](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

Nella finestra di dialogo **Configura l'app Android** assegnare alla nuova app il nome `Phoneword` e fare clic su **Avanti**.

[![Configurare l'app Android](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

Nella finestra di dialogo **Configura nuova app Android** lasciare i nomi della soluzione e del progetto impostati su `Phoneword` e fare clic su **Crea** per creare il progetto.

### <a name="creating-the-layout"></a>Creazione del layout

Dopo aver creato il nuovo progetto, espandere la cartella **Risorse** e quindi la cartella di **layout** nel riquadro della **soluzione**.
Fare doppio clic su **Main.axml** per aprirlo in Android Designer. Questo è il file di layout della schermata visualizzata in Android Designer:

[![Aprire Main.axml](hello-android-quickstart-images/xs/05-open-layout-sml.png)](hello-android-quickstart-images/xs/05-open-layout.png#lightbox)

Selezionare **Hello World, Click Me!** **Pulsante** nell'area di progettazione e premere il tasto **Canc** per rimuoverlo. 

Dalla **Casella degli strumenti** (l'area a destra) immettere `text` nel campo di ricerca e trascinare un widget **Testo (grande)** nell'area di progettazione (l'area al centro):

[![Aggiungere widget di testo di grandi dimensioni](hello-android-quickstart-images/xs/06-large-text-sml.png)](hello-android-quickstart-images/xs/06-large-text.png#lightbox)

Con il widget **Testo (grande)** selezionato nell'area di progettazione, è possibile usare il riquadro **Proprietà** per modificare la proprietà `Text` del widget **Testo (grande)** in `Enter a Phoneword:` come illustrato di seguito:

[![Impostare le proprietà del widget di testo di grandi dimensioni](hello-android-quickstart-images/xs/07-enter-a-phoneword-sml.png)](hello-android-quickstart-images/xs/07-enter-a-phoneword.png#lightbox)

Trascinare un widget **Testo normale** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Testo (grande)**. Si noti che è possibile usare il campo di ricerca per individuare i widget per nome:

[![Aggiungere un widget Testo normale](hello-android-quickstart-images/xs/08-plain-text-sml.png)](hello-android-quickstart-images/xs/08-plain-text.png#lightbox)

Con il widget **Testo normale** selezionato nell'area di progettazione, è possibile usare il riquadro **Proprietà** per modificare la proprietà `Id` del widget **Testo normale** in `@+id/PhoneNumberText` e modificare la proprietà `Text` in `1-855-XAMARIN`:

[![Impostare le proprietà del widget Testo normale](hello-android-quickstart-images/xs/09-add-properties-sml.png)](hello-android-quickstart-images/xs/09-add-properties.png#lightbox)

Trascinare un **Pulsante** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Testo normale**:

[![Aggiungere un pulsante](hello-android-quickstart-images/xs/10-drag-button-sml.png)](hello-android-quickstart-images/xs/10-drag-button.png#lightbox)

Con l'opzione **Pulsante** selezionata nell'area di progettazione, è possibile usare il riquadro **Proprietà** per modificare la proprietà `Id` del **Pulsante** in `@+id/TranslateButton` e modificare la proprietà `Text` in `Translate`:

[![Configurare come pulsante di traduzione](hello-android-quickstart-images/xs/11-translate-button-sml.png)](hello-android-quickstart-images/xs/11-translate-button.png#lightbox)

Trascinare un oggetto **TextView** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Pulsante**. Con l'opzione **TextView** selezionata, impostare la proprietà `id` di **TextView** su `@+id/TranslatedPhoneWord` e modificare `text` in una stringa vuota:

[![Impostare le proprietà nella visualizzazione di testo.](hello-android-quickstart-images/xs/12-textview-properties-sml.png)](hello-android-quickstart-images/xs/12-textview-properties.png#lightbox)    

Salvare premendo **&#8984;+S**.

### <a name="writing-translation-code"></a>Scrittura del codice di conversione

Aggiungere ora codice per convertire i numeri di telefono da alfanumerici a numerici. Aggiungere un nuovo file al progetto facendo clic sull'icona di ingranaggio accanto al progetto **Phoneword** nel riquadro della **soluzione** e scegliendo **Aggiungi > Nuovo file...** :

[![Aggiungere un nuovo file al progetto](hello-android-quickstart-images/xs/14-add-new-file-sml.png)](hello-android-quickstart-images/xs/14-add-new-file.png#lightbox)

Nella finestra di dialogo **Nuovo file** selezionare **Generale > Classe vuota**, assegnare al nuovo file il nome **PhoneTranslator** e fare clic su **Nuovo**. Viene creata una nuova classe C# vuota.

Rimuovere tutto il codice del modello nella nuova classe e sostituirlo con il codice seguente:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
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
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Salvare le modifiche al file **PhoneTranslator.cs** scegliendo **File > Salva** (o premendo **&#8984;+S**), quindi chiudere il file. Verificare che non siano presenti errori di compilazione eseguendo la ricompilazione della soluzione.

### <a name="wiring-up-the-interface"></a>Associazione dell'interfaccia

Il passaggio successivo consiste nell'aggiungere codice per collegare l'interfaccia utente tramite l'aggiunta di codice sottostante nella classe `MainActivity`.
Fare doppio clic su **MainActivity.cs** nel **riquadro della soluzione** per aprirlo.

Iniziare aggiungendo un gestore eventi al pulsante **Traduci**. Nella classe `MainActivity` cercare il metodo `OnCreate`. Aggiungere il codice del pulsante all'interno di `OnCreate`, sotto le chiamate `base.OnCreate(bundle)` e `SetContentView (Resource.Layout.Main)`. Rimuovere il codice di gestione del pulsante del modello in modo che il metodo `OnCreate` sia simile al seguente:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Our code will go here
        }
    }
}
```

È necessario quindi un riferimento ai controlli che sono stati creati nel file di layout con Android Designer. Aggiungere il codice seguente all'interno del metodo `OnCreate` dopo la chiamata a `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Aggiungere il codice che risponde alla scelta del pulsante **Traduci** da parte dell'utente aggiungendo il codice seguente al metodo `OnCreate` (dopo le righe aggiunte nell'ultimo passaggio):

```csharp
// Add code to translate number
string translatedNumber = string.Empty;

translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Salvare e compilare l'applicazione selezionando **Compila > Compila tutto** o premendo **&#8984;+B**. Se la compilazione dell'applicazione viene eseguita, viene visualizzato un messaggio di operazione completata nella parte superiore di Visual Studio per Mac:

Se sono presenti errori, rivedere i passaggi precedenti e correggere eventuali errori fino a quando l'applicazione non viene compilata correttamente. Se si verifica un errore di compilazione, ad esempio _La risorsa non esiste nel contesto corrente_, verificare che il nome dello spazio dei nomi in **MainActivity.cs** corrisponda al nome del progetto (`Phoneword`) e quindi ricompilare completamente la soluzione. Se si verificano ancora errori di compilazione, verificare di aver installato gli ultimi aggiornamenti di Xamarin.Android e Visual Studio per Mac.

### <a name="setting-the-label-and-app-icon"></a>Impostazione dell'etichetta e dell'icona dell'app

L'applicazione è ora funzionante ed è possibile finalizzarla. Iniziare modificando `Label` per `MainActivity`.
Android visualizza `Label` nella parte superiore della schermata per indicare agli utenti la loro posizione all'interno dell'applicazione. Nella parte superiore della classe `MainActivity` modificare `Label` in `Phone Word` come illustrato di seguito:

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

A questo punto è possibile impostare l'icona dell'applicazione. Per impostazione predefinita, Visual Studio per Mac offre un'icona predefinita per il progetto. Eliminare questi file dall'applicazione e sostituirli con un'icona diversa. Espandere la cartella **Risorse** nel **riquadro della soluzione**. Si noti che sono presenti cinque cartelle che iniziano con **mipmap -** e che ogni cartella contiene un singolo file **Icon.png**:

[![Cartelle mipmap- e file Icon.png](hello-android-quickstart-images/xs/23-mipmap-folders-sml.png)](hello-android-quickstart-images/xs/23-mipmap-folders.png#lightbox)

È necessario eliminare ognuno di questi file di icona dal progetto. Fare clic con il pulsante destro su ogni file **Icon.png** e selezionare **Rimuovi** dal menu di scelta rapida:

[![Eliminare il file Icon.png predefinito](hello-android-quickstart-images/xs/23-delete-icon-sml.png)](hello-android-quickstart-images/xs/23-delete-icon.png#lightbox)

Fare clic sul pulsante **Elimina** nella finestra di dialogo.

Quindi, scaricare e decomprimere il [set di icone di app Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Il file zip contiene le icone per l'applicazione. Ogni icona è visivamente identica ma a risoluzioni diverse ne viene eseguito il rendering correttamente in dispositivi diversi con densità dello schermo diverse.  Il set di file deve essere copiato nel progetto Xamarin.Android. In Visual Studio per Mac nel **riquadro della soluzione** fare clic con il pulsante destro del mouse sulla cartella **mipmap-hdpi** e selezionare **Aggiungi > Aggiungi file**:

[![Aggiungere file](hello-android-quickstart-images/xs/24-add-files-sml.png)](hello-android-quickstart-images/xs/24-add-files.png#lightbox)

Dalla finestra di dialogo di selezione passare alla directory delle icone delle app Xamarin decompressa e aprire la cartella **mipmap-hdpi**. Selezionare **Icon.png** e fare clic su **Apri**.

Nella finestra di dialogo **Aggiungi file a cartella** selezionare **Copia file nella directory** e fare clic su **OK**:

[![Finestra di dialogo Copia file nella directory](hello-android-quickstart-images/xs/26-copy-to-directory-sml.png)](hello-android-quickstart-images/xs/26-copy-to-directory.png#lightbox)

Ripetere questi passaggi per ogni cartella **mipmap-** fino a quando le cartelle delle icone delle app Xamarin **mipmap-** non vengono copiate nelle cartelle **mipmap-** corrispondenti nel progetto **Phoneword**.

Dopo aver copiato tutte le icone nel progetto Xamarin.Android, aprire la finestra di dialogo **Opzioni progetto** facendo clic con il pulsante destro del mouse sul progetto nel **riquadro della soluzione**. Selezionare **Compila > Applicazione Android** e selezionare **@mipmap/icon** dalla casella combinata **Icona applicazione**:

[![Impostazione dell'icona di progetto](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

### <a name="running-the-app"></a>Esecuzione dell'app

Infine, testare l'applicazione eseguendola su un dispositivo Android o un emulatore e convertendo Phoneword:

[![Screenshot dell'app completata](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

-----

L'applicazione Xamarin.Android è stata completata.
È ora possibile analizzare nel dettaglio gli strumenti e le funzionalità descritte. Passare a [Hello, Android Deep Dive](~/android/get-started/hello-android/hello-android-deepdive.md) (Hello, Android: approfondimenti).


## <a name="related-links"></a>Collegamenti correlati

- [Icone delle app Android Xamarin (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (sample)](https://developer.xamarin.com/samples/monodroid/Phoneword) (Phoneword - Esempio)
