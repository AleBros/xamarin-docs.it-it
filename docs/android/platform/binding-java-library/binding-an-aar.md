---
title: Associazione di un. AAR
description: Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una raccolta di associazioni xamarin Java da un Android. File AAR.
ms.topic: article
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 92cacd7ca5ff52a2bfe9060f47332b57d637609e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="binding-an-aar"></a>Associazione di un. AAR

_Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una raccolta di associazioni xamarin Java da un Android. File AAR._


## <a name="overview"></a>Panoramica

Il *Android archivio (. AAR)* file è il formato di file per le librerie di Android.
Un oggetto. I file AAR una. Archivio ZIP che contiene quanto segue:

-   Codice Java compilato
-   ID di risorsa
-   Risorse
-   I metadati (ad esempio, le dichiarazioni di attività, le autorizzazioni)

In questa Guida, verrà scorrere le nozioni di base della creazione di una raccolta di associazioni per un singolo. File AAR. Per una panoramica del linguaggio libreria binding in generale (con un esempio di codice di base), vedere [associazione di una libreria di Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Un progetto di associazione può includere solo uno. File AAR. Se il. Dipendenze AAR da altri. AAR, quindi tali dipendenze devono essere contenuta nel proprio progetto di associazione e quindi a cui fa riferimento. Vedere [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).

<a name="walkthrough" />

## <a name="walkthrough"></a>Procedura dettagliata

Verrà creata una raccolta di associazioni per un esempio che il file di archivio Android è stato creato in Android Studio [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Questo. AAR contiene un `TextCounter` classe con metodi statici che contare il numero di vocali e consonanti in una stringa. Inoltre, **textanalyzer.aar** contiene una risorsa immagine per visualizzare i risultati di conteggio.

Verrà utilizzata la procedura seguente per creare una raccolta di associazioni del. File AAR:

1. Creare un nuovo progetto libreria di binding di linguaggio.

2. Aggiungere un singolo oggetto. File AAR al progetto. Un progetto di associazione può contenere solo un singolo. AAR.

3. Impostare l'azione di compilazione appropriati per il. File AAR.

4. Scegliere un framework di destinazione che il. Supporta AAR.

5. Compilare la libreria di associazioni.

Dopo averla creata la raccolta di associazioni, si viene sviluppata un'app Android di piccole dimensioni che richiede l'immissione di una stringa di testo, le chiamate. Metodi AAR per analizzare il testo, recupera l'immagine il. AAR e consente di visualizzare i risultati insieme all'immagine.

L'app di esempio accederà il `TextCounter` classe di **textanalyzer.aar**:

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

Inoltre, questa app di esempio e visualizzerà una risorsa immagine che viene compresso in **textanalyzer.aar**:

[ ![Immagine di monkey Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png)

Questa risorsa immagine si trova in **res/drawable/monkey.png** in **textanalyzer.aar**.


<a name="creating" />

### <a name="creating-the-bindings-library"></a>Creare la raccolta di associazioni

Prima di iniziare con la procedura seguente, scaricare l'esempio [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) file di archivio Android:

1.  Creare un nuovo progetto di libreria di associazioni a partire dal modello libreria Android di associazioni. È possibile utilizzare Visual Studio per Mac o Visual Studio (schermate riportate di seguito mostrano Visual Studio, ma Visual Studio per Mac è molto simile). Denominare la soluzione **AarBinding**:

    [ ![Creare il progetto AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.png)](binding-an-aar-images/01-new-bindings-library-vs.png)

2.  Il modello include un **file JAR** cartella in cui si aggiunge il. AAR(s) al progetto libreria di associazioni. Fare doppio clic su di **file JAR** cartella e selezionare **Aggiungi > elemento esistente**:

    [ ![Aggiungi elemento esistente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png)


3.  Passare il **textanalyzer.aar** file scaricato in precedenza, selezionarla e fare clic su **Aggiungi**:

    [ ![Aggiungere textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png)


4.  Verificare che il **textanalyzer.aar** file sia stato aggiunto correttamente al progetto:

    [ ![È stato aggiunto il file textanalyzer.aar](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png)

5.  Impostare l'azione di compilazione per **textanalyzer.aar** a `LibraryProjectZip`. In Visual Studio per Mac, fare doppio clic su **textanalyzer.aar** per impostare l'azione di compilazione. In Visual Studio, è possibile impostare l'azione di compilazione **proprietà** riquadro):

    [ ![Impostare l'azione di compilazione textanalyzer.aar LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png)

6.  Aprire le proprietà per configurare il progetto di *Framework di destinazione*. Se il. AAR Usa le API di Android, impostare il Framework di destinazione a livello dell'API di. È previsto AAR. (Per ulteriori informazioni di impostazione del Framework di destinazione e i livelli API Android in generale, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).)

    Impostare il livello API di destinazione per la raccolta di associazioni. In questo esempio, si possono utilizzare la piattaforma più recente livello di API (livello API 23) perché il nostro **textanalyzer** non ha una dipendenza sulle API Android:

    [ ![Impostazione del livello di destinazione su API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png)

7.  Compilare la libreria di associazioni. Il progetto libreria di binding deve compilare correttamente e produrre un output. DLL nel percorso seguente: **AarBinding/bin/Debug/AarBinding.dll**


<a name="using" />

### <a name="using-the-bindings-library"></a>Utilizzo della libreria di associazioni

Per utilizzare questo. DLL in app xamarin, è necessario prima aggiungere un riferimento alla raccolta di associazioni. Utilizzare la procedura seguente:

1.  Si sta creando questa applicazione nella stessa soluzione come libreria di associazioni per semplificare questa procedura dettagliata. (L'app che utilizza la raccolta di associazioni può inoltre trovarsi in una soluzione diversa). Creare una nuova app xamarin: la soluzione e scegliere **Aggiungi nuovo progetto**. Denominare il nuovo progetto **BindingTest**:

    [ ![Crea nuovo progetto BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.png)](binding-an-aar-images/07-add-new-project-vs.png)

2.  Fare doppio clic su di **riferimenti** nodo del **BindingTest** del progetto e selezionare **Aggiungi riferimento...** :

    [ ![Fare clic su Aggiungi riferimento](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png)

3.  Selezionare il **AarBinding** progetto creato in precedenza e fare clic su **OK**:

    [ ![Archiviare il progetto di associazione AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png)

4.  Aprire il **riferimenti** nodo del **BindingTest** progetto per verificare che il **AarBinding** riferimento è presente:

    [ ![AarBinding è elencato in riferimenti](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png)


Se si desidera visualizzare il contenuto del progetto libreria di associazione, è possibile fare doppio clic sul riferimento per aprirlo nel **Visualizzatore oggetti**. È possibile visualizzare il contenuto di mappato il `Com.Xamarin.Textcounter` dello spazio dei nomi (mappato da di Java `com.xamarin.textanalyzezr` pacchetto) ed è possibile visualizzare i membri del `TextCounter` classe:

[ ![Visualizzazione del Visualizzatore oggetti](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png)

Nella schermata precedente vengono evidenziate due `TextAnalyzer` metodi che esegue la chiamata dell'applicazione di esempio: `NumConsonants` (che esegue il wrapping di Java sottostante `numConsonants` metodo), e `NumVowels` (che esegue il wrapping di Java sottostante `numVowels` (metodo)).


<a name="accessing_types" />

### <a name="accessing-aar-types"></a>L'accesso. Tipi AAR

Dopo aver aggiunto un riferimento all'App che fa riferimento alla libreria di associazione, è possibile accedere a tipi Java di. AAR come accedere a tipi c# (grazie a c# wrapper). Codice app c# è possibile chiamare `TextAnalyzer` metodi, come illustrato in questo esempio:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

Nell'esempio precedente, stiamo chiamando i metodi statici `TextCounter` classe. Tuttavia, è possibile creare istanze di classi e chiamare i metodi di istanza. Ad esempio, se il. AAR esegue il wrapping di una classe denominata `Employee` che include il metodo di istanza `buildFullName`, è possibile creare istanze `MyClass` e usarlo come illustrato di seguito:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

I passaggi seguenti aggiungono codice per l'app in modo che viene richiesto all'utente per il testo utilizza `TextCounter` per analizzare il testo e quindi Visualizza i risultati.

Sostituire il **BindingTest** layout (**axml**) con il seguente codice XML. Il layout presenta un `EditText` per l'input di testo e due pulsanti per l'avvio vocale e consonante conteggi:

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

Sostituire il contenuto di **Mainactivity** con il codice seguente. Come illustrato in questo esempio, la chiamata di gestori di eventi di pulsante inclusa `TextCounter` metodi che si trovano nel. Avvisi popup AAR e utilizzare per visualizzare i risultati. Si noti il `using` istruzione dello spazio dei nomi della libreria di binding (in questo caso, `Com.Xamarin.Textcounter`):

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

Compilare ed eseguire il **BindingTest** progetto. L'app viene avviato e presenti nella schermata di sinistra (il `EditText` viene inizializzata con un testo, ma è possibile toccare per modificarlo). Quando si tocca **conteggio VOCALI**, un avviso popup Visualizza il numero di vocali, come illustrato nella parte destra:

[ ![Schermate dall'esecuzione BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png)

Provare a toccare il **conteggio CONSONANTI** pulsante. Inoltre, è possibile modificare la riga di testo e toccare questi pulsanti per verificare la presenza di diversa vocale e conta consonante.


<a name="accessing_resources" />

### <a name="accessing-aar-resources"></a>L'accesso. Risorse AAR

Il Xamarin tooling unisce il **R** dati da di. AAR in un'applicazione **risorse** classe. Di conseguenza, è possibile accedere. Le risorse AAR il layout (e dal code-behind) nello stesso modo come è possibile accedere a risorse che si trovano i **risorse** percorso del progetto.

Per accedere a una risorsa immagine, utilizzare il **Resource.Drawable** assegnare un nome per l'immagine compressa all'interno di. AAR. Ad esempio, è possibile fare riferimento **image.png** nel. I file AAR utilizzando `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

È inoltre possibile accedere layout di risorsa che si trovano nel. AAR. A tale scopo, utilizzare il **Resource.Layout** nome per il layout del pacchetto all'interno di. AAR. Ad esempio:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

Il **textanalyzer.aar** esempio contiene un file di immagine che si trova in **res/drawable/monkey.png**. Consente di accedere alla risorsa immagine e utilizzarlo nella nostra app di esempio:

Modificare il **BindingTest** layout (**axml**) e aggiungere un `ImageView` alla fine del `LinearLayout` contenitore. Questo `ImageView` Visualizza l'immagine nella  **@drawable/monkey** ; questa immagine verrà caricata da sezione delle risorse di **textanalyzer.aar**:

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

Compilare ed eseguire il **BindingTest** progetto. L'app viene avviato e presenti nella schermata sinistra &ndash; quando si tocca **conteggio CONSONANTI**, i risultati vengono visualizzati come illustrato nella parte destra:

[ ![Visualizzazione conteggio consonante BindingTest](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png)


La procedura è stata completata. È stato associato correttamente una libreria di Java. AAR!


<a name="summary" />

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata creata una raccolta di associazioni per un. File AAR, aggiungere la raccolta di associazioni per un'app di test minima ed è stata eseguita l'app per verificare che il codice c# è possibile chiamare codice Java che si trovano nel. File AAR.
Inoltre, è stato esteso e l'app per accedere e visualizzare una risorsa immagine che si trova nel. File AAR.


## <a name="related-links"></a>Collegamenti correlati

- [La creazione di una raccolta di associazioni di Java (video)](https://university.xamarin.com/classes#10090)
- [Associazione di un file JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (esempio)](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Progetto 44573 un bug non è possibile associare più file AAR](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
