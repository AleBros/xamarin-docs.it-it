---
title: Associazione di un file AAR
description: In questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di associazioni Java Xamarin.Android da un dispositivo Android. file AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 103720c8cb47b1ac4cfe5cfadeb6b18828318ad3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73618540"
---
# <a name="binding-an-aar"></a>Associazione di un file AAR

_In questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di associazioni Java Xamarin.Android da un dispositivo Android. file AAR._

## <a name="overview"></a>Panoramica

*L'archivio Android (. AAR)* è il formato di file per le librerie Android.
Un. Il file AAR è un file . archivio zip che contiene quanto segue:

- Codice Java compilato
- ID risorsa
- Risorse
- Metadati (ad esempio, dichiarazioni di attività, autorizzazioni)Meta-data (for example, Activity declarations, permissions)

In questa guida verranno fornite le nozioni di base per la creazione di una libreria di associazioni per un singolo oggetto . file AAR. Per una panoramica dell'associazione di librerie Java in generale (con un esempio di codice di base), consultate [Associazione](~/android/platform/binding-java-library/index.md)di una libreria Java .

> [!IMPORTANT]
> Un progetto di associazione può includere solo un file . file AAR. Se il file . AAR dipende da altri . AAR, quindi tali dipendenze devono essere contenute nel proprio progetto di associazione e quindi fare riferimento. Vedere [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).

## <a name="walkthrough"></a>Procedura dettagliata

Creeremo una libreria di associazioni per un file di archivio Android di esempio creato in Android Studio, [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Questo. AAR contiene `TextCounter` una classe con metodi statici che contano il numero di vocali e consonanti in una stringa. Inoltre, **textanalyzer.aar** contiene una risorsa immagine per visualizzare i risultati del conteggio.

Si userà la procedura seguente per creare una libreria di associazioni dal . File AAR:

1. Creare un nuovo progetto Libreria di associazioni Java.Create a new Java Bindings Library project.

2. Aggiungere un singolo file . AAR al progetto. Un progetto di associazione può contenere un solo oggetto . Aar.

3. Impostare l'azione di compilazione appropriata per il file . file AAR.

4. Scegliere un framework di destinazione che il file . Supporti AAR.

5. Compilare la libreria di associazioni.

Una volta creata la libreria di associazioni, svilupperemo una piccola app Android che richiede all'utente una stringa di testo, chiama . Metodi AAR per analizzare il testo, recupera l'immagine dal file . AAR e visualizza i risultati insieme all'immagine.

L'app di `TextCounter` esempio accederà alla classe di **textanalyzer.aar**:

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

Inoltre, questa app di esempio recupererà e visualizzerà una risorsa immagine inclusa nel pacchetto **textanalyzer.aar**:

[![Immagine scimmia Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Questa risorsa immagine si trova in **res/drawable/monkey.png** in **textanalyzer.aar**.

### <a name="creating-the-bindings-library"></a>Creazione della libreria di associazioniCreating the Bindings Library

Prima di iniziare con la procedura seguente, scaricare il file di archivio Android [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) di esempio:

1. Creare un nuovo progetto libreria di associazioni a partire dal modello di libreria di associazioni Android.Create a new Bindings Library project starting with the Android Bindings Library template. È possibile usare Visual Studio per Mac o Visual Studio (le schermate seguenti mostrano Visual Studio, ma Visual Studio per Mac è molto simile). Denominare la soluzione **AarBinding**:

    [![Creare un progetto AarBindingsCreate AarBindings project](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. Il modello include una cartella **Jars** in cui si aggiunge il file . AAR(s) per il progetto libreria di associazioni. Fare clic con il pulsante destro del mouse sulla cartella **Jars** e selezionare **Aggiungi > elemento esistente:**

    [![Aggiungi elemento esistente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. Passare al file **textanalyzer.aar** scaricato in precedenza, selezionarlo e fare clic su **Aggiungi:**

    [![Aggiungere textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. Verificare che il file **textanalyzer.aar** sia stato aggiunto correttamente al progetto:

    [![Il file textanalyzer.aar è stato aggiunto](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. Impostare l'azione di compilazione `LibraryProjectZip`per **textanalyzer.aar** su . In Visual Studio per Mac fare clic con il pulsante destro del mouse su **textanalyzer.aar** per impostare l'azione di compilazione. In Visual Studio, l'azione di compilazione può essere impostata nel riquadro **Proprietà):In** Visual Studio, the Build Action can be set in the Properties pane):

    [![Impostazione dell'azione di compilazione textanalyzer.aar su LibraryProject](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. Aprire le proprietà del progetto per configurare il Framework di *destinazione*. Se il file . AAR utilizza le API Android, impostare il Framework di destinazione per il livello di API che il . AAR si aspetta. Per altre informazioni sull'impostazione Framework di destinazione e sui livelli API Android in generale, vedere [Informazioni sui livelli API Android.](~/android/app-fundamentals/android-api-levels.md)

    Impostare il livello di API di destinazione per la libreria di associazioni. In questo esempio, siamo liberi di usare il livello API della piattaforma più recente (livello API 23) perché il nostro textanalyzer non ha una dipendenza dalle API Android:In this example, we are free to use the latest platform API level (API level 23) because our **textanalyzer** does not have a dependency on Android APIs:

    [![Impostazione del livello di destinazione sull'API 23Setting the target level to API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. Compilare la libreria di associazioni. Il progetto Libreria di associazioni deve essere compilato correttamente e produrre un output . DLL nel seguente percorso: **AarBinding/bin/Debug/AarBinding.dll**

### <a name="using-the-bindings-library"></a>Utilizzo della libreria di associazioniUsing the Bindings Library

Per consumare questo file . DLL nell'app Xamarin.Android, è innanzitutto necessario aggiungere un riferimento alla libreria di associazioni. Eseguire la procedura descritta di seguito:

1. Stiamo creando questa app nella stessa soluzione della libreria di associazioni per semplificare questa procedura dettagliata. L'app che utilizza la libreria di associazioni potrebbe anche risiedere in una soluzione diversa. Creare una nuova app Xamarin.Android: fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi nuovo progetto**. Denominare il nuovo progetto **BindingTest**:

    [![Creare un nuovo progetto BindingTestCreate new BindingTest project](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. Fare clic con il pulsante destro del mouse sul nodo **Riferimenti** del progetto **BindingTest** e selezionare **Aggiungi riferimento...**:

    [![Fare clic su Aggiungi riferimento](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. Selezionare il progetto **AarBinding** creato in precedenza e fare clic su **OK**:

    [![Controllare il progetto di associazione AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. Aprire il nodo **Riferimenti** del progetto **BindingTest** per verificare che il riferimento **AarBinding** sia presente:

    [![AarBinding è elencato in Riferimenti](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

Se si desidera visualizzare il contenuto del progetto Libreria di associazioni, è possibile fare doppio clic sul riferimento per aprirlo nel **Visualizzatore oggetti**. È possibile visualizzare il contenuto `Com.Xamarin.Textcounter` mappato dello spazio `com.xamarin.textanalyzezr` dei nomi (mappato dal `TextCounter` pacchetto Java) ed è possibile visualizzare i membri della classe:

[![Visualizzazione del Visualizzatore oggetti](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

La schermata precedente `TextAnalyzer` evidenzia i due metodi `NumConsonants` che verranno chiami dall'app di esempio: (che esegue il wrapping del metodo Java `numConsonants` sottostante) e `NumVowels` (che esegue il wrapping del metodo Java `numVowels` sottostante).

### <a name="accessing-aar-types"></a>Accedere. Tipi AAR

Dopo aver aggiunto un riferimento all'app che punta alla libreria di associazione, è possibile accedere ai tipi Java in . AAR come si accederebbe ai tipi C , grazie ai wrapper di C. Il codice dell'app c'è in grado di chiamare i metodi come illustrato nell'esempio seguente:C'è app code can call `TextAnalyzer` methods as illustrated in this example:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

Nell'esempio precedente, stiamo chiamando metodi `TextCounter` statici nella classe. Tuttavia, è anche possibile creare un'istanza di classi e chiamare metodi di istanza. Ad esempio, se il file . AAR esegue il `Employee` wrapping di una `buildFullName`classe denominata con il metodo di istanza , è possibile crearne un'istanza `MyClass` e utilizzarla come illustrato di seguito:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

I passaggi seguenti aggiungono codice all'app in modo che `TextCounter` richieda all'utente il testo, usi per analizzare il testo e quindi visualizzino i risultati.

Sostituire il layout **BindingTest** (**Main.axml**) con il codice XML seguente. Questo layout `EditText` ha un per l'input di testo e due pulsanti per l'inserimento di vocali e conteggi di consonanti:

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

Sostituire il contenuto di **MainActivity.cs** con il codice seguente. Come illustrato in questo esempio, i `TextCounter` gestori eventi del pulsante chiamano i metodi di cui è stato eseguito il wrapping che risiedono nell'oggetto . AAR e utilizzare avvisi popup per visualizzare i risultati. Si `using` noti l'istruzione per lo spazio `Com.Xamarin.Textcounter`dei nomi della libreria associata (in questo caso, ):

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

Compilare ed eseguire il progetto **BindingTest.Compile** and run the BindingTest project. L'app si avvierà e presenterà `EditText` lo screenshot a sinistra (l'è inizializzato con del testo, ma è possibile toccarlo per modificarlo). Quando tocchi **COUNT VOWELS**, un avviso popup visualizza il numero di vocali come mostrato a destra:

[![Screenshot dell'esecuzione di BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Provare a toccare il pulsante **CONSONANTS CONTA.** Inoltre, è possibile modificare la riga di testo e toccare di nuovo questi pulsanti per verificare i diversi conteggi vocali e consonanti.

### <a name="accessing-aar-resources"></a>Accedere. Risorse AAR

Gli strumenti Xamarin uniscono i dati **R** dal file . AAR nella classe **Resource** dell'app. Di conseguenza, è possibile accedere a . Risorse AAR dal layout (e dal code-behind) nello stesso modo in cui si accede alle risorse che si trovano nel percorso **delle risorse** del progetto.

Per accedere a una risorsa immagine, utilizzare il nome **Resource.Drawable** per l'immagine compressa all'interno del file . Aar. Ad esempio, potete fare riferimento a **image.png** nel file . AAR utilizzando `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

È inoltre possibile accedere ai layout delle risorse che risiedono nel file . Aar. A tale scopo, utilizzare il nome **Resource.Layout** per il layout incluso nell'oggetto . Aar. Ad esempio:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

L'esempio **textanalyzer.aar** contiene un file di immagine che si trova in **res/drawable/monkey.png**. Accedere a questa risorsa immagine e usarla nell'app di esempio:Let's access this image resource and use it in our example app:

Modificare il layout **BindingTest** (**Main.axml**) e aggiungere an `ImageView` alla fine del `LinearLayout` contenitore. Questo `ImageView` mostra l'immagine trovata a ** \@drawable / scimmia**; questa immagine verrà caricata dalla sezione delle risorse di **textanalyzer.aar**:

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

Compilare ed eseguire il progetto **BindingTest.Compile** and run the BindingTest project. L'applicazione si avvierà e &ndash; presenterà lo screenshot a sinistra quando si tocca **COUNT CONSONANTS**, i risultati vengono visualizzati come mostrato a destra:

[![BindingTest che visualizza il conteggio delle consonanti](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

Congratulazioni! Una libreria Java è stata associata correttamente. Aar!

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata creata una libreria di associazioni per un oggetto . AAR, aggiunto la libreria di associazioni a un'app di test minima ed eseguito l'applicazione per verificare che il nostro codice c'è possibile chiamare il codice Java che risieda nel . file AAR.
Inoltre, abbiamo esteso l'app per accedere e visualizzare una risorsa immagine che risiede nel file . file AAR.

## <a name="related-links"></a>Collegamenti correlati

- [Compilazione di una libreria di associazioni Java (video)Building a Java Bindings Library (video)](https://university.xamarin.com/classes#10090)
- [Associazione di un file JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (esempio)AarBinding (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573 - Un progetto non può associare più file con estensione aar](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
