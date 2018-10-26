---
title: Associazione di una. AAR
description: Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di binding di xamarin. Android Java da Android. File AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 7f71ccf4ff61c176e73be6d3855136697a5c2130
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103998"
---
# <a name="binding-an-aar"></a>Associazione di una. AAR

_Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di binding di xamarin. Android Java da Android. File AAR._


## <a name="overview"></a>Panoramica

Il *archivio Android (. AAR)* file è il formato di file per le librerie di Android.
Un oggetto. File AAR è una. Archivio ZIP che contiene gli elementi seguenti:

-   Codice compilato di Java
-   ID di risorsa
-   Risorse
-   Meta-data (ad esempio, le dichiarazioni di attività, le autorizzazioni)

In questa Guida, si sarà scorrere le nozioni di base della creazione di una libreria di binding per una singola. File AAR. Per una panoramica di Java della libreria di associazione in generale (con un esempio di codice di base), vedere [associazione di una libreria Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Un progetto di associazione può includere solo uno. File AAR. Se il. Dipendenze AAR di altro. AAR, quindi tali dipendenze devono essere contenute nel proprio progetto di associazione e fare riferimento a. Visualizzare [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Procedura dettagliata

Si creerà una libreria di binding per un esempio di file di archivio Android è stato creato in Android Studio [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). In questo. AAR contiene un `TextCounter` classe con metodi statici che contino il numero di consonanti in una stringa e vocali. È inoltre **textanalyzer.aar** contiene una risorsa immagine per consentire di visualizzare i risultati di conteggio.

Si userà la procedura seguente per creare una libreria di binding da di. File AAR:

1. Creare un nuovo progetto di libreria di binding Java.

2. Aggiungere un singolo oggetto. File AAR nel progetto. Un progetto di associazione può contenere solo un singolo. AAR.

3. Impostare l'azione di compilazione appropriata per il. File AAR.

4. Scegliere un framework di destinazione che il. Supporta AAR.

5. Compilare la libreria di binding.

Dopo aver creato la libreria di binding, sviluppiamo sarà una piccola app di Android che richiede l'immissione di una stringa di testo, le chiamate. I metodi AAR per analizzare il testo, recupera l'immagine da di. AAR e visualizza i risultati insieme all'immagine.

L'app di esempio accederà il `TextCounter` della classe **textanalyzer.aar**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

Inoltre, questa app di esempio e visualizzerà una risorsa immagine in un pacchetto nel **textanalyzer.aar**:

[![Immagine di monkey Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Questa risorsa immagine si trova nella **res/drawable/monkey.png** nelle **textanalyzer.aar**.



### <a name="creating-the-bindings-library"></a>Creare la libreria di binding

Prima di iniziare con la procedura seguente, scaricare l'esempio [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) file di archivio Android:

1.  Creare un nuovo progetto di libreria di binding inizia con il modello libreria di binding Android. È possibile usare Visual Studio per Mac o Visual Studio (schermate riportate di seguito mostra Visual Studio, ma Visual Studio per Mac è molto simile). Denominare la soluzione **AarBinding**:

    [![Crea progetto AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w157.png)](binding-an-aar-images/01-new-bindings-library-vs.w157.png#lightbox)

2.  Il modello include un **Jars** cartella in cui si aggiungono i. AAR(s) al progetto libreria di binding. Fare doppio clic il **Jars** cartella e selezionare **Aggiungi > elemento esistente**:

    [![Aggiungi elemento esistente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  Passare il **textanalyzer.aar** file scaricato in precedenza, selezionarla e fare clic su **Add**:

    [![Aggiungere textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  Verificare che il **textanalyzer.aar** file è stato aggiunto al progetto:

    [![È stato aggiunto il file textanalyzer.aar](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  Impostare l'azione di compilazione per **textanalyzer.aar** a `LibraryProjectZip`. In Visual Studio per Mac, fare doppio clic su **textanalyzer.aar** per impostare l'azione di compilazione. In Visual Studio, l'azione di compilazione possono essere impostata nella **proprietà** riquadro):

    [![Impostazione azione di compilazione textanalyzer.aar su LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  Aprire il progetto di proprietà per configurare il *Framework di destinazione*. Se il. AAR Usa qualsiasi API Android, impostare il Framework di destinazione per il livello API di. Si aspetta che AAR. (Per altre informazioni sui Framework di destinazione e i livelli API Android in generale, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).)

    Impostare il livello API di destinazione per la libreria di binding. In questo esempio, si possono utilizzare la piattaforma più recente livello di API (livello API 23) perché nostri **textanalyzer** non ha una dipendenza su API Android:

    [![Impostazione del livello di destinazione su API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  Compilare la libreria di binding. Il progetto libreria di binding deve compilare correttamente e produrre un output. DLL nel percorso seguente: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>Uso della libreria di binding

Per utilizzare questa soluzione. DLL nell'app xamarin. Android, è innanzitutto necessario aggiungere un riferimento alla libreria di binding. Usare la procedura seguente:

1.  Viene creato l'app nella stessa soluzione come libreria di binding per semplificare questa procedura dettagliata. (L'app che usa la libreria di binding può inoltre trovarsi in una soluzione diversa). Creare una nuova app xamarin. Android: la soluzione e scegliere **Aggiungi nuovo progetto**. Denominare il nuovo progetto **BindingTest**:

    [![Crea nuovo progetto BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2.  Fare doppio clic sui **riferimenti** nodo del **BindingTest** del progetto e selezionare **Aggiungi riferimento...** :

    [![Fare clic su Aggiungi riferimento](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  Selezionare il **AarBinding** progetto creato in precedenza e fare clic su **OK**:

    [![Archiviare il progetto di associazione AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  Aprire il **riferimenti** nodo delle **BindingTest** progetto per verificare che il **AarBinding** riferimento è presente:

    [![AarBinding è elencato in riferimenti](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Se si desidera visualizzare il contenuto del progetto libreria di Binding, è possibile fare doppio clic sul riferimento per aprirlo nel **Visualizzatore oggetti**. È possibile visualizzare il contenuto con mapping del `Com.Xamarin.Textcounter` dello spazio dei nomi (mapping eseguito da Java `com.xamarin.textanalyzezr` pacchetto) ed è possibile visualizzare i membri del `TextCounter` classe:

[![Visualizzazione del Visualizzatore oggetti](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

Lo screenshot precedente evidenzia due `TextAnalyzer` metodi che chiamerà l'app di esempio: `NumConsonants` (che esegue il wrapping di Java sottostante `numConsonants` metodo), e `NumVowels` (che esegue il wrapping di Java sottostante `numVowels` (metodo)).



### <a name="accessing-aar-types"></a>L'accesso. Tipi di AAR

Dopo aver aggiunto un riferimento all'App che fa riferimento alla libreria di Binding, è possibile accedere a tipi di Java nel. AAR come si accederà a C# tipi (grazie alla C# wrapper). C#codice dell'App può chiamare `TextAnalyzer` metodi come illustrato in questo esempio:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

Nell'esempio precedente, stiamo chiamando i metodi statici `TextCounter` classe. Tuttavia, è anche possibile creare istanze di classi e chiamare i metodi di istanza. Ad esempio, se il. AAR esegue il wrapping di una classe denominata `Employee` con il metodo di istanza `buildFullName`, è possibile creare un'istanza `MyClass` e usarlo come illustrato di seguito:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

I passaggi seguenti aggiungono codice all'app in modo che venga richiesta l'utente per il testo, Usa `TextCounter` per analizzare il testo e quindi Visualizza i risultati.

Sostituire il **BindingTest** layout (**Main. axml**) con il codice XML seguente. Questo layout è un `EditText` per input di testo e due pulsanti per l'avvio vocale e consonante conteggi:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

Sostituire il contenuto del **MainActivity.cs** con il codice seguente. Come illustrato in questo esempio, la chiamata di gestori di eventi pulsante inclusa `TextCounter` metodi che si trovano nel. Avvisi popup AAR e utilizzare per visualizzare i risultati. Si noti che il `using` istruzione dello spazio dei nomi della libreria con binding (in questo caso, `Com.Xamarin.Textcounter`):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

Compilare ed eseguire la **BindingTest** progetto. L'app viene avviato e presentare la schermata a sinistra (la `EditText` viene inizializzata con un testo, ma è possibile toccare per modificarlo). Quando si tocca **VOCALI conteggio**, un avviso popup Visualizza il numero di vocali, come illustrato a destra:

[![Screenshot in esecuzione BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Provare a toccare le **CONSONANTI conteggio** pulsante. Inoltre, è possibile modificare la riga di testo e toccare questi pulsanti nuovamente da verificare per alfabeto diversi e conta consonante.



### <a name="accessing-aar-resources"></a>L'accesso. Risorse AAR

Il Xamarin tooling unioni il **R** i dati di. Nella tua app AAR **risorsa** classe. Di conseguenza, è possibile accedere. Risorse AAR dal layout del (e dal code-behind) nello stesso modo come è possibile accedere a risorse che si trovano i **risorse** percorso del progetto.

Per accedere a una risorsa immagine, si utilizza il **Resource.Drawable** assegnare un nome per l'immagine compressa all'interno di. AAR. Ad esempio, è possibile fare riferimento a **Image. PNG** nel. File con estensione AAR utilizzando `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

È anche possibile accedere i layout di risorsa che si trovano nel. AAR. A tale scopo, si utilizza il **Resource.Layout** nome per il layout inserito all'interno di. AAR. Ad esempio:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

Il **textanalyzer.aar** esempio contiene un file di immagine che si trova a **res/drawable/monkey.png**. È possibile accedere a questa risorsa immagine e usarla nella nostra app di esempio:

Modificare il **BindingTest** layout (**Main. axml**) e aggiungere un `ImageView` alla fine del `LinearLayout` contenitore. Ciò `ImageView` Visualizza l'immagine, vedere **@drawable/monkey**; questa immagine verrà caricata dalla sezione delle risorse **textanalyzer.aar**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

Compilare ed eseguire la **BindingTest** progetto. L'app viene avviato e presentare la schermata a sinistra &ndash; quando si tocca **conteggio CONSONANTI**, i risultati vengono visualizzati come illustrato a destra:

[![Visualizzazione conteggio consonante BindingTest](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


La procedura è stata completata. È stata associata correttamente una libreria Java. AAR.



## <a name="summary"></a>Riepilogo

In questa procedura dettagliata, è stata creata una libreria di binding per una. File AAR, aggiungere la libreria di binding a un'app di test minima ed è stata eseguita l'app e verificare che il C# codice può chiamare codice Java che si trovano nel. File AAR.
Inoltre, è stato esteso l'app per accedere e visualizzare una risorsa immagine che si trova nel. File AAR.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di una libreria di binding Java (video)](https://university.xamarin.com/classes#10090)
- [Associazione di un file JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (esempio)](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Progetto 44573-One bug non è possibile associare più file con estensione AAR](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
