---
title: 'Hello, Android Multiscreen: guida introduttiva'
description: Questa guida in due parti consente di ampliare l'applicazione Phoneword per gestire una seconda schermata. Nel corso della trattazione, è stato introdotto il concetto fondamentale dei blocchi predefiniti per le applicazioni Android, con un approfondimento dell'architettura Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: ED99584A-BA3B-429A-AEE5-CF3CB0116762
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 114373b6c4b194fe6e566255eb09eb82a8208312
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020969"
---
# <a name="hello-android-multiscreen-quickstart"></a>Hello, Android Multiscreen: guida introduttiva

_Questa guida in due parti espande l'applicazione Phoneword per gestire una seconda schermata. I blocchi predefiniti di base per le applicazioni Android sono stati introdotti con un approfondimento dell'architettura Android._

Nella parte relativa alla procedura dettagliata di questa guida, verrà aggiunta una seconda schermata all'applicazione [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) per tenere traccia della cronologia dei numeri convertiti tramite l'app. L'[applicazione finale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen) avrà una seconda schermata che visualizzerà i numeri che sono stati "convertiti", come illustrato nello screenshot a destra:

[![Screenshot dell'app di esempio](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

L'[approfondimento](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md) associato consente di rivedere ciò che è stato fatto e illustra l'architettura, lo spostamento e altri concetti nuovi di Android incontrati durante la trattazione.

## <a name="requirements"></a>Requisiti

Questa guida inizia dal punto in cui [Hello, Android](~/android/get-started/hello-android/index.md) termina. È quindi necessario aver completato [Hello, Android: guida introduttiva](~/android/get-started/hello-android/hello-android-quickstart.md).
Se si vuole passare direttamente alla procedura dettagliata che segue, è possibile scaricare la versione completata di [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) (da Hello, Android: guida introduttiva) e usarla per iniziare la procedura dettagliata.

## <a name="walkthrough"></a>Procedura dettagliata

In questa procedura dettagliata si aggiungerà la schermata **Translation History** (Cronologia delle conversioni) per l'applicazione **Phoneword**.

::: zone pivot="windows"

Per iniziare, aprire l'applicazione **Phoneword** in Visual Studio e modificare **Main.axml** da **Esplora soluzioni**.

> [!TIP]
> Le versioni più recenti di Visual Studio supportano l'apertura dei file con estensione xml all'interno di Android Designer.
>
> I file con estensione axml e xml sono entrambi supportati in Android Designer.

### <a name="updating-the-layout"></a>Aggiornamento del layout

Dalla **Casella degli strumenti** trascinare un oggetto **Pulsante** nell'area di progettazione, posizionandolo sotto l'oggetto TextView **TranslatedPhoneWord**. Nel riquadro **Proprietà** modificare la proprietà **Id** del pulsante in `@+id/TranslationHistoryButton`

[![Trascinare un nuovo pulsante](hello-android-multiscreen-quickstart-images/vs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/vs/02-new-button.png#lightbox)

Impostare la proprietà **Testo** del pulsante su `@string/translationHistory`. Android Designer interpreterà questa istruzione in modo letterale, ma con poche modifiche il testo del pulsante verrà visualizzato correttamente:

[![Impostare il testo del pulsante della cronologia delle conversioni](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string-sml.png)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string.png#lightbox)

Espandere il nodo **values** nella cartella **Risorse** in **Esplora soluzioni** e fare doppio clic sul file di risorse stringa, **Strings.xml**:

[![Aprire Strings.xml](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file.png#lightbox)

Aggiungere il nome e il valore della stringa `translationHistory` al file **Strings.xml** e salvarlo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

Il testo del pulsante **Translation History** (Cronologia delle conversioni) dovrebbe aggiornarsi per riflettere il nuovo valore della stringa:

[![Il pulsante riflette il nuovo valore della stringa](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png)](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png#lightbox)

Dopo aver selezionato il pulsante **Translation History** (Cronologia delle conversioni) nell'area di progettazione, individuare l'impostazione `enabled` nel riquadro **Proprietà** e impostarne il valore su `false` per disabilitare il pulsante. Il pulsante diventerà più scuro all'interno dell'area di progettazione:

[![Disabilitare il pulsante della cronologia delle conversioni](hello-android-multiscreen-quickstart-images/vs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Creazione della seconda attività

Creare una seconda attività per consentire il funzionamento della seconda schermata. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Phoneword** e scegliere **Aggiungi > Nuovo elemento...** :

[![Aggiungere un nuovo file](hello-android-multiscreen-quickstart-images/vs/07-add-new-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/07-add-new-file.png#lightbox)

Nella finestra di dialogo **Aggiungi nuovo elemento** scegliere **Visual C# > Attività** e assegnare al file dell'attività il nome **TranslationHistoryActivity.cs**.

Sostituire il codice del modello in **TranslationHistoryActivity.cs** con quanto segue:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

In questa classe si sta creando e popolando un oggetto `ListActivity` a livello di codice. Non è quindi necessario creare un nuovo file di layout per questa attività. Questo aspetto viene trattato più dettagliatamente in [Hello, Android Multiscreen: approfondimenti](~/android/get-started/hello-android/hello-android-deepdive.md).

### <a name="adding-a-list"></a>Aggiunta dell'elenco

Questa app raccoglie i numeri di telefono che l'utente ha convertito nella prima schermata e li passa alla seconda schermata. I numeri di telefono vengono archiviati sotto forma di elenco di stringhe. Per il supporto di elenchi e Intent, usati più avanti, aggiungere le direttive `using` seguenti all'inizio di **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Creare quindi un elenco vuoto che possa essere compilato con numeri di telefono.
La classe `MainActivity` avrà l'aspetto seguente:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

Nella classe `MainActivity` aggiungere il codice seguente per registrare il pulsante **Translation History** (Cronologia delle conversioni). Inserire questa riga dopo la dichiarazione di `translateButton`:

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Aggiungere il codice seguente alla fine del metodo `OnCreate` per associare il pulsante **Translation History** (Cronologia delle conversioni):

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Aggiornare il pulsante **Translate** (Converti) per aggiungere il numero di telefono all'elenco `phoneNumbers`. Il gestore `Click` per `translateButton` deve essere simile al codice seguente:

```csharp
// Add code to translate number
string translatedNumber = string.Empty;
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

Salvare e compilare l'applicazione per verificare che non vi siano errori.

### <a name="running-the-app"></a>Esecuzione dell'app

Distribuire l'applicazione in un emulatore o in un dispositivo. Gli screenshot seguenti illustrano l'applicazione **Phoneword** in esecuzione:

[![Screenshot di esempio](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end
::: zone pivot="macos"

Per iniziare, aprire il progetto **Phoneword** in Visual Studio per Mac e modificare **Main.axml** dal **riquadro della soluzione**.

> [!TIP]
> Le versioni più recenti di Visual Studio supportano l'apertura dei file con estensione xml all'interno di Android Designer.
>
> I file con estensione axml e xml sono entrambi supportati in Android Designer.

### <a name="updating-the-layout"></a>Aggiornamento del layout

Dalla **Casella degli strumenti** trascinare un oggetto **Pulsante** nell'area di progettazione, posizionandolo sotto l'oggetto TextView **TranslatedPhoneWord**. Nel riquadro delle **proprietà** modificare la proprietà **Id** del pulsante in `@+id/TranslationHistoryButton`:

[![Trascinare un nuovo pulsante](hello-android-multiscreen-quickstart-images/xs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/xs/02-new-button.png#lightbox)

Impostare la proprietà **Testo** del pulsante su `@string/translationHistory`. Android Designer interpreterà questa istruzione in modo letterale, ma con poche modifiche il testo del pulsante verrà visualizzato correttamente:

[![Impostare il testo del pulsante della cronologia delle conversioni](hello-android-multiscreen-quickstart-images/xs/03-call-history-string-sml.png)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string.png#lightbox)

Espandere il nodo **values** nella cartella **Risorse** nel **riquadro della soluzione** e fare doppio clic sul file di risorse stringa, **Strings.xml**:

[![Aprire Strings](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file.png#lightbox)

Aggiungere il nome e il valore della stringa `translationHistory` al file **Strings.xml** e salvarlo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

Il testo del pulsante **Translation History** (Cronologia delle conversioni) dovrebbe aggiornarsi per riflettere il nuovo valore della stringa:

[![Il pulsante riflette il nuovo valore della stringa](hello-android-multiscreen-quickstart-images/xs/05-new-string-value-sml.png)](hello-android-multiscreen-quickstart-images/xs/05-new-string-value.png#lightbox)

Dopo aver selezionato il pulsante **Translation History** (Cronologia delle conversioni) nell'area di progettazione, aprire la scheda **Comportamento** nel **riquadro delle proprietà** e fare doppio clic sulla casella di controllo **Abilitato**  per disabilitare il pulsante. Il pulsante diventerà più scuro all'interno dell'area di progettazione:

[![Disabilitare il pulsante della cronologia delle conversioni](hello-android-multiscreen-quickstart-images/xs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Creazione della seconda attività

Creare una seconda attività per consentire il funzionamento della seconda schermata. Nel **riquadro della soluzione** fare clic sull'icona grigia a forma di ingranaggio accanto al progetto **Phoneword** e scegliere **Aggiungi > Nuovo File...** :

Nella finestra di dialogo **Nuovo file** scegliere **Android > Attività**, assegnare all'attività il nome `TranslationHistoryActivity` e quindi fare clic su **Aggiungi**.

Sostituire il codice del modello in `TranslationHistoryActivity` con quanto segue:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

In questa classe si sta creando e popolando un oggetto `ListActivity` a livello di codice. Non è quindi necessario creare un nuovo file di layout per questa attività. Questo aspetto viene spiegato più dettagliatamente in [Hello, Android Multiscreen: approfondimenti](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

### <a name="adding-a-list"></a>Aggiunta dell'elenco

Questa app raccoglie i numeri di telefono che l'utente ha convertito nella prima schermata e li passa alla seconda schermata. I numeri di telefono vengono archiviati sotto forma di elenco di stringhe. Per il supporto di elenchi e Intent, usati più avanti, aggiungere le direttive `using` seguenti all'inizio di **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Creare quindi un elenco vuoto che possa essere compilato con numeri di telefono. La classe `MainActivity` avrà l'aspetto seguente:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

Nella classe `MainActivity` aggiungere il codice seguente per registrare il pulsante **Translation History** (Cronologia delle conversioni). Inserire questa riga dopo la dichiarazione di `TranslationHistoryButton`:

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Aggiungere il codice seguente alla fine del metodo `OnCreate` per associare il pulsante **Translation History** (Cronologia delle conversioni):

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Aggiornare il pulsante **Translate** (Converti) per aggiungere il numero di telefono all'elenco `phoneNumbers`. Il gestore `Click` per `TranslateHistoryButton` deve essere simile al codice seguente:

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

### <a name="running-the-app"></a>Esecuzione dell'app

Distribuire l'applicazione in un emulatore o in un dispositivo. Gli screenshot seguenti illustrano l'applicazione **Phoneword** in esecuzione:

[![Screenshot di esempio](hello-android-multiscreen-quickstart-images/screenshot.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end

L'applicazione Xamarin.Android multischermata è stata completata. È ora possibile analizzare gli strumenti e le competenze appena acquisite. Passare quindi a [Hello, Android Multiscreen: approfondimenti](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

## <a name="related-links"></a>Collegamenti correlati

- [Icone e schermate di avvio delle app Xamarin (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) (Phoneword - Esempio)
- [PhonewordMultiscreen (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen) (PhonewordMultiscreen - Esempio)
