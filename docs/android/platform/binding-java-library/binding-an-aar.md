---
title: Associazione di un file AAR
description: Questa procedura dettagliata include istruzioni dettagliate per la creazione di una libreria di binding Java Novell. Android da un dispositivo Android. File AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: ee29f54ac68f370cb9499d315116a030247f6044
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225724"
---
# <a name="binding-an-aar"></a>Associazione di un file AAR

_Questa procedura dettagliata include istruzioni dettagliate per la creazione di una libreria di binding Java Novell. Android da un dispositivo Android. File AAR._


## <a name="overview"></a>Panoramica

*Archivio Android (. (AAR)* file è il formato di file per le librerie Android.
Un. Il file AAR è un. Archivio ZIP che contiene gli elementi seguenti:

- Codice Java compilato
- ID risorsa
- Risorse
- Metadati (ad esempio, dichiarazioni di attività, autorizzazioni)

In questa guida verranno illustrate le nozioni di base per la creazione di una libreria di binding per un singolo. File AAR. Per una panoramica dell'associazione di librerie Java in generale (con un esempio di codice di base), vedere [binding di una libreria Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Un progetto di associazione può includerne solo uno. File AAR. Se il. Dipendenze AAR su altro. AAR, le dipendenze devono essere contenute nel proprio progetto di binding e quindi farvi riferimento. Vedere il [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Procedura dettagliata

Verrà creata una libreria di binding per un esempio di file di archivio Android creato in Android Studio, [textanalyzer. AAR](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Questo. AAR contiene una `TextCounter` classe con metodi statici che contano il numero di vocali e consonanti in una stringa. Inoltre, **textanalyzer. AAR** contiene una risorsa immagine che consente di visualizzare i risultati del conteggio.

Verranno usati i passaggi seguenti per creare una libreria di binding da. File AAR:

1. Creare un nuovo progetto di libreria di binding Java.

2. Aggiungere un singolo. File AAR nel progetto. Un progetto di associazione può contenere solo un singolo. Aar.

3. Impostare l'azione di compilazione appropriata per l'oggetto. File AAR.

4. Scegliere un Framework di destinazione per il. AAR supporta.

5. Compilare la libreria dei binding.

Una volta creata la libreria bindings, verrà sviluppata una piccola app per Android che richiede all'utente una stringa di testo, chiama. Metodi AAR per analizzare il testo, recupera l'immagine da. AAR e Visualizza i risultati insieme all'immagine.

L'app di esempio accederà `TextCounter` alla classe di **textanalyzer. AAR**:

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

Questa app di esempio, inoltre, recupererà e visualizzerà una risorsa immagine composta in un pacchetto in **textanalyzer. AAR**:

[![Immagine di Novell Monkey](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Questa risorsa immagine si trova in **res/disegnatore/Monkey. png** in **textanalyzer. AAR**.



### <a name="creating-the-bindings-library"></a>Creazione della libreria bindings

Prima di iniziare con la procedura seguente, scaricare il file di archivio Android [textanalyzer. AAR](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) di esempio:

1. Creare un nuovo progetto di libreria di binding a partire dal modello di libreria di associazioni Android. È possibile usare Visual Studio per Mac o Visual Studio (gli screenshot seguenti mostrano Visual Studio, ma Visual Studio per Mac è molto simile). Assegnare alla soluzione il nome **AarBinding**:

    [![Crea progetto AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. Il modello include una cartella **jar** in cui si aggiunge il. AAR (s) per il progetto della libreria di binding. Fare clic con il pulsante destro del mouse sulla cartella **jar** e scegliere **Aggiungi > elemento esistente**:

    [![Aggiungi elemento esistente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3. Passare al file **textanalyzer. AAR** scaricato in precedenza, selezionarlo e fare clic su **Aggiungi**:

    [![Aggiungere textanalayzer. AAR](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4. Verificare che il file **textanalyzer. AAR** sia stato aggiunto correttamente al progetto:

    [![Il file textanalyzer. Aar è stato aggiunto](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. Impostare l'azione di compilazione per **textanalyzer. AAR** su `LibraryProjectZip`. In Visual Studio per Mac fare clic con il pulsante destro del mouse su **textanalyzer. AAR** per impostare l'azione di compilazione. In Visual Studio è possibile impostare l'azione di compilazione nel riquadro **Proprietà** :

    [![Impostazione dell'azione di compilazione textanalyzer. AAR su LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. Aprire le proprietà del progetto per configurare il *Framework di destinazione*. Se il. AAR usa qualsiasi API Android, impostare il Framework di destinazione sul livello API di. AAR prevede. Per ulteriori informazioni sull'impostazione del Framework di destinazione e sui livelli dell'API Android in generale, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

    Impostare il livello API di destinazione per la libreria dei binding. In questo esempio, è possibile usare il livello API della piattaforma più recente (livello API 23) perché la **textanalyzer** non ha una dipendenza dalle API Android:

    [![Impostazione del livello di destinazione su API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. Compilare la libreria dei binding. Il progetto di libreria Bindings deve essere compilato correttamente e produrre un output. DLL nel percorso seguente: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>Uso della libreria bindings

Per utilizzare questo. DLL nell'app Novell. Android, è necessario innanzitutto aggiungere un riferimento alla libreria Bindings. Seguire questa procedura:

1. Questa app viene creata nella stessa soluzione della libreria Bindings per semplificare questa procedura dettagliata. L'app che utilizza la libreria dei binding potrebbe trovarsi anche in una soluzione diversa. Creare una nuova app Novell. Android: fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi nuovo progetto**. Assegnare al nuovo progetto il nome **BindingTest**:

    [![Crea nuovo progetto BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. Fare clic con il pulsante destro del mouse sul nodo **riferimenti** del progetto **BindingTest** e scegliere **Aggiungi riferimento...** :

    [![Fare clic su Aggiungi riferimento](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. Selezionare il progetto **AarBinding** creato in precedenza e fare clic su **OK**:

    [![Controllare il progetto di associazione AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. Aprire il nodo **riferimenti** del progetto **BindingTest** per verificare che sia presente il riferimento **AarBinding** :

    [![AarBinding è elencato sotto References](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Se si desidera visualizzare il contenuto del progetto libreria di binding, è possibile fare doppio clic sul riferimento per aprirlo nella **Visualizzatore oggetti**. È possibile visualizzare il contenuto mappato dello `Com.Xamarin.Textcounter` spazio dei nomi (mappato `com.xamarin.textanalyzezr` dal pacchetto Java) ed è possibile `TextCounter` visualizzare i membri della classe:

[![Visualizzazione della Visualizzatore oggetti](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

Lo screenshot precedente evidenzia i due `TextAnalyzer` metodi che l'app di esempio chiamerà: `NumConsonants` (che esegue il wrapping del metodo `numConsonants` Java sottostante) e `NumVowels` (che esegue il wrapping del metodo `numVowels` Java sottostante).



### <a name="accessing-aar-types"></a>Accesso. Tipi AAR

Dopo aver aggiunto un riferimento all'app che punta alla libreria di associazione, è possibile accedere ai tipi Java in. AAR come si accede C# ai tipi (grazie ai C# Wrapper). C#il codice dell'app `TextAnalyzer` può chiamare i metodi, come illustrato in questo esempio:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

Nell'esempio precedente vengono chiamati metodi statici nella `TextCounter` classe. Tuttavia, è anche possibile creare istanze delle classi e chiamare metodi di istanza. Ad esempio, se il. AAR esegue il wrapping di una `Employee` classe denominata con il metodo `buildFullName`di istanza `MyClass` , è possibile crearne un'istanza e usarla come illustrato di seguito:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

I passaggi seguenti aggiungono codice all'app in modo da richiedere all'utente il testo, USA `TextCounter` per analizzare il testo e quindi Visualizza i risultati.

Sostituire il layout **BindingTest** (**Main. aXML**) con il codice XML seguente. Questo layout include un `EditText` oggetto per l'input di testo e due pulsanti per l'avvio dei conteggi di vocali e consonanti:

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

Sostituire il contenuto di **MainActivity.cs** con il codice seguente. Come illustrato in questo esempio, i gestori eventi dei pulsanti chiamano i `TextCounter` metodi di cui è stato eseguito il wrapper che si trovano in. AAR e usare i toast per visualizzare i risultati. Si noti `using` l'istruzione per lo spazio dei nomi della libreria associata (in questo `Com.Xamarin.Textcounter`caso,):

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

Compilare ed eseguire il progetto **BindingTest** . L'app verrà avviata e presenterà la schermata a sinistra ( `EditText` il viene inizializzato con un testo, ma è possibile toccarlo per modificarlo). Quando si tocca **conteggio vocali**, un avviso popup Visualizza il numero di vocali, come illustrato a destra:

[![Screenshot dell'esecuzione di BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Provare a toccare il pulsante per il **conteggio** delle consonanti. Inoltre, è possibile modificare la riga di testo e toccare di nuovo questi pulsanti per verificare la presenza di diversi conteggi vocali e consonanti.



### <a name="accessing-aar-resources"></a>Accesso. Risorse AAR

Gli strumenti Novell uniscono i dati **R** da. AAR nella classe di **risorse** dell'app. Di conseguenza, è possibile accedere a. Le risorse AAR dal layout (e dal code-behind) nello stesso modo in cui si accede alle risorse presenti nel percorso delle **risorse** del progetto.

Per accedere a una risorsa immagine, è possibile usare il nome **Resource. disegnatore** per l'immagine compressa all'interno di. Aar. Ad esempio, è possibile fare riferimento a **Image. png** in. File AAR usando `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

È anche possibile accedere ai layout delle risorse che si trovano in. Aar. A tale scopo, usare il nome **Resource. layout** per il layout incluso nel. Aar. Ad esempio:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

L'esempio **textanalyzer. AAR** contiene un file di immagine che risiede in **res/disegnatore/Monkey. png**. Per accedere a questa risorsa immagine e usarla nell'app di esempio:

Modificare il layout di **BindingTest** (**Main. aXML**) e aggiungere `ImageView` un alla fine del `LinearLayout` contenitore. Viene visualizzata l'immagine trovata in **@drawable/monkey** . questa immagine verrà caricata dalla sezione delle risorse di **textanalyzer. AAR:** `ImageView`

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

Compilare ed eseguire il progetto **BindingTest** . L'app verrà avviata e presenterà la schermata a &ndash; sinistra quando si tocca il **conteggio**delle consonanti, i risultati vengono visualizzati come mostrato a destra:

[![BindingTest che Visualizza il conteggio delle consonanti](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


La procedura è stata completata. La libreria Java è stata associata correttamente. Aar!



## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata creata una libreria Bindings per un. Il file AAR ha aggiunto la libreria bindings a un'app di test minima ed è stata eseguita l'app per C# verificare che il codice possa chiamare il codice Java che risiede in. File AAR.
Inoltre, l'app è stata estesa per accedere e visualizzare una risorsa immagine che risiede in. File AAR.


## <a name="related-links"></a>Collegamenti correlati

- [Compilazione di una libreria di binding Java (video)](https://university.xamarin.com/classes#10090)
- [Associazione di un file JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573-un progetto non può associare più file. AAR](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
