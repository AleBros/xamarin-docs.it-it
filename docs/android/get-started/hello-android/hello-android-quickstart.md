---
title: 'Hello, Android: Guida rapida'
description: In questa guida divisa in due parti è possibile creare un'applicazione Xamarin.Android per la prima volta usando Visual Studio o Visual Studio per Mac e comprendere le nozioni di base dello sviluppo di applicazioni Android con Xamarin. Verranno descritti gli strumenti, i concetti e i passaggi necessari per creare e distribuire un'applicazione Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: 378861dfecb2109269677fe13ed4df629a09da67
ms.sourcegitcommit: 6d41b5d48fd626d3f649809ed5480e5356755f14
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55986263"
---
# <a name="hello-android-quickstart"></a>Hello, Android: Guida rapida

_In questa guida divisa in due parti è possibile creare per la prima volta un'applicazione Xamarin.Android usando Visual Studio e comprendere le nozioni di base dello sviluppo di applicazioni Android con Xamarin._

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/monodroid/Phoneword/)

Verrà creata un'applicazione che converte un numero di telefono alfanumerico immesso dall'utente in un numero di telefono numerico e visualizza il numero di telefono numerico all'utente. L'applicazione finale ha l'aspetto seguente:

[![Screenshot dell'app completata](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

::: zone pivot="windows"

## <a name="windows-requirements"></a>Requisiti Windows

Per seguire questa procedura dettagliata è necessario quanto segue:

- Windows 10.

- Visual Studio 2017, edizione Community, Professional o Enterprise (15.8 o versione successiva).

::: zone-end
::: zone pivot="macos"

## <a name="macos-requirements"></a>Requisiti macOS

Per seguire questa procedura dettagliata è necessario quanto segue:

- Ultima versione di Visual Studio per Mac.

- Mac che esegue macOS High Sierra (10.13) o versione successiva.

::: zone-end

In questa procedura si presuppone che l'ultima versione di Xamarin.Android sia installata e in esecuzione sulla piattaforma desiderata. Per istruzioni sull'istallazione di Xamarin.Android, vedere le guide relative all'[installazione di Xamarin.Android](~/android/get-started/installation/index.md).

## <a name="configuring-emulators"></a>Configurazione degli emulatori

Se si usa l'emulatore di Android, è consigliabile configurare l'emulatore per l'uso dell'accelerazione hardware. Le istruzioni per configurare l'accelerazione hardware sono disponibili in [Accelerazione hardware per le prestazioni dell'emulatore](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

## <a name="create-the-project"></a>Creare il progetto

::: zone pivot="windows"

Avviare Visual Studio. Fare clic su **File > Nuovo > Progetto** per creare un nuovo progetto.

Nella finestra di dialogo **Nuovo progetto** fare clic sul modello **App Android**.
Assegnare un nome al nuovo progetto `Phoneword` e fare clic su **OK**:

[![Nuovo progetto Phoneword](hello-android-quickstart-images/vs/01-new-project-name-w158-sml.png)](hello-android-quickstart-images/vs/01-new-project-name-w158.png#lightbox)

Nella finestra di dialogo **Nuova app Android** fare clic su **App vuota** e fare clic su **OK** per creare il nuovo progetto:

[![Selezionare il modello App vuota](hello-android-quickstart-images/vs/02-blank-app-w158-sml.png)](hello-android-quickstart-images/vs/02-blank-app-w158.png#lightbox)

## <a name="create-a-layout"></a>Creare un layout

Dopo aver creato il nuovo progetto, espandere la cartella **Risorse** e quindi la cartella di **layout** in **Esplora soluzioni**.
Fare doppio clic su **activity_main.axml** per aprirlo in Android Designer. Questo è il file di layout per la schermata dell'app:

[![Aprire il file AXML dell'attività](hello-android-quickstart-images/vs/03-open-layout-w158-sml.png)](hello-android-quickstart-images/vs/03-open-layout-w158.png#lightbox)

> [!TIP]
> Le versioni più recenti di Visual Studio contengono un modello di app leggermente diverso.
>
> 1. Il layout non è in **activity_main.axml**, ma in **content_main.axml**.
> 2. Il layout predefinito è un `RelativeLayout`. Per il funzionamento corretto della procedura di questa pagina è necessario cambiare il tag `<RelativeLayout>` in `<LinearLayout>` e aggiungere un altro attributo `android:orientation="vertical"` al tag di apertura `LinearLayout`.

Dalla **Casella degli strumenti** (l'area a sinistra) immettere `text` nel campo di ricerca e trascinare un widget **Testo (grande)** nell'area di progettazione (l'area al centro):

[![Aggiungere widget di testo di grandi dimensioni](hello-android-quickstart-images/vs/04-large-text-w158-sml.png)](hello-android-quickstart-images/vs/04-large-text-w158.png#lightbox)

Con l'opzione **Testo (grande)** selezionata nell'area di progettazione, usare il riquadro **Proprietà** per modificare la proprietà `Text` del widget **Testo (grande)** in `Enter a Phoneword:`:

[![Impostare le proprietà di un testo di grandi dimensioni](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158.png#lightbox)

Trascinare un widget **Testo normale** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Testo (grande)**. Il posizionamento del widget non avverrà finché il puntatore del mouse non verrà spostato in un punto del layout che può accettare il widget. Nelle schermate seguenti, il widget non può essere posizionato (come illustrato a sinistra) finché il puntatore del mouse non viene spostato appena sotto il precedente `TextView` (come illustrato a destra):

[![Il mouse indica il punto in cui il widget può essere posizionato](hello-android-quickstart-images/vs/06a-cant-drop-w158-sml.png)](hello-android-quickstart-images/vs/06a-cant-drop-w158.png#lightbox)

Quando il **Testo normale** (un widget `EditText`) è inserito correttamente, verrà visualizzato come illustrato nello screenshot seguente:

[![Aggiungere un widget Testo normale](hello-android-quickstart-images/vs/06b-plain-text-w158-sml.png)](hello-android-quickstart-images/vs/06b-plain-text-w158.png#lightbox)

Con il widget **Testo normale** selezionato nell'area di progettazione, usare il riquadro **Proprietà** per modificare la proprietà `Id` del widget **Testo normale** in `@+id/PhoneNumberText` e modificare la proprietà `Text` in `1-855-XAMARIN`:

[![Impostare le proprietà del testo normale](hello-android-quickstart-images/vs/07-add-properties-w158-sml.png)](hello-android-quickstart-images/vs/07-add-properties-w158.png#lightbox)

Trascinare un **Pulsante** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Testo normale**:

[![Trascinare il pulsante di traduzione nell'area di progettazione](hello-android-quickstart-images/vs/08-drag-button-w158-sml.png)](hello-android-quickstart-images/vs/08-drag-button-w158.png#lightbox)

Con il **Pulsante** selezionato nell'area di progettazione, usare il riquadro **Proprietà** per modificarne la proprietà `Text` in `Translate` e la proprietà `Id` in `@+id/TranslateButton`:

[![Impostare le proprietà del pulsante di traduzione](hello-android-quickstart-images/vs/09-translate-button-w158-sml.png)](hello-android-quickstart-images/vs/09-translate-button-w158.png#lightbox)

Trascinare un oggetto **TextView** dalla **Casella degli strumenti** all'area di progettazione e inserirlo sotto il widget **Pulsante**. Modificare la proprietà `Text` dell'oggetto **TextView** su una stringa vuota e impostare la relativa proprietà `Id` in `@+id/TranslatedPhoneword`:

[![Impostare le proprietà nella visualizzazione di testo.](hello-android-quickstart-images/vs/10-textview-properties-w158-sml.png)](hello-android-quickstart-images/vs/10-textview-properties-w158.png#lightbox)

Salvare premendo **CTRL+S**.

## <a name="write-some-code"></a>Scrivere codice

Il passaggio successivo consiste nell'aggiungere codice per convertire i numeri di telefono da alfanumerici a numerici. Aggiungere un nuovo file al progetto facendo clic con il pulsante destro del mouse sul progetto **Phoneword** nel riquadro **Esplora soluzioni** e scegliendo **Aggiungi > Nuovo elemento...**  come illustrato di seguito:

[![Aggiungere un nuovo elemento](hello-android-quickstart-images/vs/12-add-new-item-w158-sml.png)](hello-android-quickstart-images/vs/12-add-new-item-w158.png#lightbox)

Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Visual C# > Codice > File di codice** e assegnare al nuovo file di codice il nome **PhoneTranslator.cs**:

[![Aggiungere PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-w158-sml.png)](hello-android-quickstart-images/vs/14-add-class-w158.png#lightbox)

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

## <a name="wire-up-the-user-interface"></a>Collegare l'interfaccia utente

Il passaggio successivo consiste nell'aggiungere codice per collegare l'interfaccia utente tramite l'inserimento di codice sottostante nella classe `MainActivity`. Iniziare collegando il pulsante **Traduci**. Nella classe `MainActivity` cercare il metodo `OnCreate`. Il passaggio successivo consiste nell'aggiungere il codice del pulsante all'interno di `OnCreate`, sotto le chiamate `base.OnCreate(savedInstanceState)` e `SetContentView(Resource.Layout.activity_main)`. In primo luogo, modificare il codice del modello in modo che il metodo `OnCreate` sia simile al seguente:

```csharp
using Android.App;
using Android.OS;
using Android.Support.V7.App;
using Android.Runtime;
using Android.Widget;

namespace Phoneword
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);

            // New code will go here
        }
    }
}
```

Ottenere un riferimento ai controlli che sono stati creati nel file di layout con Android Designer. Aggiungere il codice seguente all'interno del metodo `OnCreate` dopo la chiamata a `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneword);
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

Se sono presenti errori, rivedere i passaggi precedenti e correggere eventuali errori fino a quando l'applicazione non viene compilata correttamente. Se si verifica un errore di compilazione, ad esempio _La risorsa non esiste nel contesto corrente_, verificare che il nome dello spazio dei nomi in **MainActivity.cs** corrisponda al nome del progetto (`Phoneword`) e quindi ricompilare completamente la soluzione. Se si verificano ancora errori di compilazione, verificare di aver installato gli ultimi aggiornamenti di Visual Studio.

## <a name="set-the-app-name"></a>Impostare il nome dell'app

L'applicazione &ndash; è ora funzionante ed occorre quindi impostare il nome dell'app. Espandere la cartella dei **valori** (all'interno della cartella **Risorse**) e aprire il file **strings.xml**. Modificare la stringa del nome dell'app su `Phone Word` come illustrato di seguito:

```xml
<resources>
    <string name="app_name">Phone Word</string>
    <string name="action_settings">Settings</string>
</resources>
```

## <a name="run-the-app"></a>Eseguire l'app

Testare l'applicazione eseguendola su un dispositivo Android o un emulatore.
Toccare il pulsante **TRANSLATE** (CONVERTI) per convertire **1-855-XAMARIN** in un numero di telefono:

[![Screenshot dell'app in esecuzione](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

Per eseguire l'app in un dispositivo Android, vedere come [configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end
::: zone pivot="macos"

Avviare Visual Studio per Mac dalla cartella **Applicazioni** o da **Spotlight**.

Fare clic su **Nuovo progetto...** per creare un nuovo progetto.

Nela finestra di dialogo **Scegli un modello per il nuovo progetto** fare clic su **Android > App** e selezionare il modello **App Android**. Scegliere **Avanti**.

[![Scegliere il modello App Android](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

Nella finestra di dialogo **Configura l'app Android** assegnare alla nuova app il nome `Phoneword` e fare clic su **Avanti**.

[![Configurare l'app Android](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

Nella finestra di dialogo **Configura nuova app Android** lasciare i nomi della soluzione e del progetto impostati su `Phoneword` e fare clic su **Crea** per creare il progetto.

## <a name="create-a-layout"></a>Creare un layout

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

## <a name="write-some-code"></a>Scrivere codice

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

## <a name="wire-up-the-user-interface"></a>Collegare l'interfaccia utente

Il passaggio successivo consiste nell'aggiungere codice per collegare l'interfaccia utente tramite l'aggiunta di codice sottostante nella classe `MainActivity`.
Fare doppio clic su **MainActivity.cs** nel **riquadro della soluzione** per aprirlo.

Iniziare aggiungendo un gestore eventi al pulsante **Traduci**. Nella classe `MainActivity` cercare il metodo `OnCreate`. Aggiungere il codice del pulsante all'interno di `OnCreate`, sotto le chiamate `base.OnCreate(bundle)` e `SetContentView (Resource.Layout.Main)`. Rimuovere qualsiasi codice di gestione del pulsante esistente (ovvero, codice che fa riferimento a `Resource.Id.myButton` e crea un gestore dell'evento clic per il pulsante) in modo che il metodo `OnCreate` sia simile al seguente:

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

## <a name="set-the-label-and-app-icon"></a>Impostare l'etichetta e l'icona dell'app

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

Dopo aver copiato tutte le icone nel progetto Xamarin.Android, aprire la finestra di dialogo **Opzioni progetto** facendo clic con il pulsante destro del mouse sul progetto nel **riquadro della soluzione**. Selezionare **Compila > Applicazione Android** e selezionare `@mipmap/icon` dalla casella combinata **Icona applicazione**:

[![Impostazione dell'icona di progetto](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

## <a name="run-the-app"></a>Eseguire l'app

Infine, testare l'applicazione eseguendola su un dispositivo Android o un emulatore e convertendo Phoneword:

[![Screenshot dell'app completata](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

Per eseguire l'app in un dispositivo Android, vedere come [configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end

L'applicazione Xamarin.Android è stata completata.
È ora possibile analizzare nel dettaglio gli strumenti e le funzionalità descritte. Passare a [Hello, Android Deep Dive](~/android/get-started/hello-android/hello-android-deepdive.md) (Hello, Android: approfondimenti).

## <a name="related-links"></a>Collegamenti correlati

- [Icone delle app Android Xamarin (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (sample)](https://developer.xamarin.com/samples/monodroid/Phoneword) (Phoneword - Esempio)
