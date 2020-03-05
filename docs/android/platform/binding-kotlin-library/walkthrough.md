---
title: 'Procedura dettagliata: associare una libreria Android Kotlin'
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'associazione Novell. Android per una libreria Kotlin esistente, BubblePicker. Vengono trattati argomenti come la compilazione di una libreria Kotlin, l'associazione e l'uso dell'associazione in un'applicazione Novell. Android.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "78291592"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Procedura dettagliata: associare una libreria Android Kotlin

Novell consente agli sviluppatori di dispositivi mobili di creare app per dispositivi mobili native multipiattaforma C#usando Visual Studio e. È possibile usare i componenti di Android Platform SDK predefiniti, ma in molti casi si vogliono usare anche gli SDK di terze parti scritti per tale piattaforma e Novell consente di eseguire questa operazione tramite binding. Per incorporare un Framework Android di terze parti nell'applicazione Novell. Android, è necessario creare un'associazione Novell. Android per poterla usare nelle applicazioni.

La piattaforma Android, insieme ai linguaggi e agli strumenti nativi, è in continua evoluzione, inclusa la recente introduzione del linguaggio Kotlin, che è stato impostato per sostituire Java. Sono disponibili diversi SDK di entità 3D, che sono già stati migrati da Java a Kotlin e presentano nuove problemi. Anche se il processo di associazione Kotlin è simile a Java, richiede passaggi aggiuntivi e impostazioni di configurazione per compilare ed eseguire correttamente come parte di un'applicazione Novell. Android.  

Lo scopo di questo documento è quello di definire un approccio di alto livello per risolvere questo scenario e fornire una guida dettagliata con un semplice esempio.

## <a name="background"></a>Background

Kotlin è stata rilasciata nel febbraio 2016 ed è stata posizionata come alternativa al compilatore Java standard in Android Studio da 2017. Più avanti nel 2019, Google ha annunciato che il linguaggio di programmazione Kotlin sarebbe diventato il linguaggio preferito per gli sviluppatori di app Android. L'approccio di associazione generale è simile al [processo di associazione delle librerie Java normali](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) con alcuni importanti passaggi specifici per Kotlin.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo scenario, saranno necessari gli elementi seguenti:

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Decompilatore Java](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Creare una libreria nativa

Il primo passaggio consiste nel compilare una libreria Kotlin nativa usando Android Studio. La libreria viene in genere fornita da uno sviluppatore di terze parti o disponibile nel [repository maven di Google](https://maven.google.com/web/index.html) e in altri repository remoti. Ad esempio, in questa esercitazione viene creata un'associazione per la libreria Kotlin di selezione Bubble:

![Demo su GitHub BubblePicker](walkthrough-images/github-bubblepicker-demo.png)

1. Scaricare [il codice sorgente](https://github.com/igalata/Bubble-Picker/archive/develop.zip) da GitHub per la libreria e decomprimerlo in una cartella locale **selezione a bolle**.

1. Avviare il Android Studio e scegliere **Apri un'opzione di menu progetto Android Studio esistente** scegliendo la cartella locale di selezione bolle:

    ![Apri progetto Android Studio](walkthrough-images/android-studio-open-project.png)

1. Verificare che il Android Studio sia aggiornato, incluso Gradle. Il codice sorgente può essere compilato correttamente in Android Studio v 3.5.3, Gradle v 5.4.1. Le istruzioni su come aggiornare Gradle alla versione più recente di Gradle [sono disponibili qui](https://gradle.org/install/).

1. Verificare che sia installato il Android SDK obbligatorio. Il codice sorgente richiede Android SDK V25. Aprire **strumenti >** opzione di menu di SDK Manager per installare i componenti SDK.

1. Aggiornare e sincronizzare il file di configurazione principale **Build. Gradle** che si trova nella directory radice della cartella del progetto:

    - Impostare la versione di Kotlin su 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Registrare il repository maven di Google predefinito in modo che la dipendenza della libreria di supporto possa essere risolta:

        ```gradle
        allprojects {
            repositories {
                jcenter()
                maven {
                    url "https://maven.google.com"
                }
            }
        }
        ```

    - Una volta aggiornato, il file di configurazione non è sincronizzato e Gradle Mostra il pulsante **Sincronizza ora** , lo preme e attende il completamento del processo di sincronizzazione:

        ![Android Studio sincronizzazione Gradle ora](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > La cache delle dipendenze di Gradle potrebbe essere danneggiata. questa situazione si verifica talvolta dopo un timeout della connessione di rete. Riscaricare le dipendenze e il progetto di sincronizzazione (richiede la rete).

        > [!TIP]
        > Lo stato di un processo di compilazione Gradle (daemon) potrebbe essere danneggiato. L'arresto di tutti i daemon Gradle può risolvere questo problema. Arrestare i processi di compilazione Gradle (richiede il riavvio). Nel caso di processi Gradle danneggiati, è anche possibile provare a chiudere l'IDE e quindi a terminare tutti i processi Java.

        > [!TIP]
        > Il progetto potrebbe usare un plug-in di terze parti, che non è compatibile con gli altri plug-in del progetto o la versione di Gradle richiesta dal progetto.

1. Aprire il menu Gradle a destra, passare al menu **attività di bubblepicker >** , eseguire l'attività di **compilazione** facendo doppio tocco su di esso e attendere il completamento del processo di compilazione:

    ![Android Studio attività di esecuzione Gradle](walkthrough-images/android-studio-gradle-execute-task.png)

1. Aprire il browser file cartella radice e passare alla cartella di compilazione: **Bubble-Picker-> bubblepicker-> Build-> Outputs-> AAR**, salvare il file **bubblepicker-release. AAR** come **bubblepicker-v 1.0. AAR**, questo file verrà usato in un secondo momento nel processo di associazione:

    ![Output di Android Studio AAR](walkthrough-images/android-studio-aar-output.png)

Il file AAR è un archivio Android, che contiene il codice sorgente e gli asset Kotlin compilati richiesti da Android per eseguire un'applicazione con questo SDK.  

## <a name="prepare-metadata"></a>Preparare i metadati

Il secondo passaggio consiste nel preparare il file di trasformazione dei metadati, che viene usato da Novell. Android per C# generare le rispettive classi. Un progetto di binding Novell. Android individua tutte le classi native e i membri di un determinato archivio Android generando successivamente un file XML con i metadati appropriati. Il file di trasformazione dei metadati creato manualmente viene quindi applicato alla baseline generata in precedenza per creare il file di definizione XML finale usato C# per generare il codice.

I metadati utilizzano la sintassi [XPath](https://www.w3.org/TR/xpath/) e vengono utilizzati dal generatore di associazioni per influenzare la creazione dell'assembly di associazione. L'articolo [metadati di binding Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata) fornisce ulteriori informazioni sulle trasformazioni, che possono essere applicate:

1. Creare un file **Metadata. XML** vuoto:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Definire le trasformazioni XML:

- La libreria Kotlin nativa presenta due dipendenze, che non si desidera esporre a C# tutto il mondo, definire due trasformazioni per ignorarle completamente. Importante dire che i membri nativi non verranno rimossi dal file binario risultante, ma solo C# le classi non verranno generate. Il [decompilatore Java](http://java-decompiler.github.io/) può essere usato per identificare le dipendenze. Eseguire lo strumento e aprire il file AAR creato in precedenza, di conseguenza verrà visualizzata la struttura dell'archivio Android, che riflette tutte le dipendenze, i valori, le risorse, il manifesto e le classi:  

    ![Dipendenze Java Decompiler](walkthrough-images/java-decompiler-dependencies.png)

    Le trasformazioni per ignorare l'elaborazione di questi pacchetti vengono definite utilizzando le istruzioni XPath:

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- La classe `BubblePicker` nativa dispone di due metodi `getBackgroundColor` e `setBackgroundColor` e la trasformazione seguente lo modificherà in C# una proprietà `BackgroundColor`:

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- I tipi senza segno `UInt, UShort, ULong, UByte` richiedono una gestione speciale. Per questi tipi Kotlin modifica automaticamente i nomi dei metodi e i tipi di parametri, che vengono riflessi nel codice generato:

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    Questo codice viene compilato nel codice byte Java seguente:

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    Inoltre, i tipi correlati, ad esempio `UIntArray, UShortArray, ULongArray, UByteArray`, sono interessati anche da Kotlin. Il nome del metodo viene modificato in modo da includere un suffisso aggiuntivo e i parametri vengono modificati in una matrice di elementi di versioni firmate degli stessi tipi. Nell'esempio seguente un parametro di tipo `UIntArray` viene convertito automaticamente in `int[]` e il nome del metodo viene modificato da `fooUIntArrayMethod` a `fooUIntArrayMethod--ajY-9A`. Quest'ultimo viene individuato dagli strumenti Novell. Android e generato come nome di metodo valido:

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    Questo codice viene compilato nel codice byte Java seguente:

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    Per assegnargli un nome significativo, è possibile aggiungere i metadati seguenti al file **Metadata. XML**, che aggiornerà il nome a definito originariamente nel codice Kotlin:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    Nell'esempio BubblePicker non sono presenti membri che usano tipi senza segno, quindi non sono necessarie altre modifiche.

- Per impostazione predefinita, i membri Kotlin con parametri generici sono stati trasformati in parametri di Java.`Lang.Object` tipo. Ad esempio, un metodo Kotlin ha un parametro generico \<T >:

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Una volta generata un'associazione Novell. Android, il metodo viene esposto a C# come indicato di seguito:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    I generics Java e Kotlin non sono supportati dalle associazioni Novell. Android, pertanto viene creato un C# metodo generalizzato per accedere all'API generica. Per risolvere il proprio lavoro, è possibile creare una libreria wrapper Kotlin ed esporre le API richieste in modo fortemente tipizzato senza generics. In alternativa, è possibile creare Helper sul C# lato per risolvere il problema nello stesso modo tramite API fortemente tipizzate.

    > [!TIP]
    > Trasformando i metadati, è possibile che tutte le modifiche vengano applicate all'associazione generata. Nell'articolo relativo all' [associazione della libreria Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) viene illustrato in dettaglio il modo in cui vengono generati ed elaborati i metadati.

## <a name="build-a-binding-library"></a>Compilazione di una libreria di associazione

Il passaggio successivo consiste nel creare un progetto di binding Novell. Android usando il modello di associazione di Visual Studio, aggiungere i metadati necessari, i riferimenti nativi e quindi compilare il progetto per produrre una libreria utilizzabile:

1. Aprire Visual Studio per Mac e creare un nuovo progetto libreria di binding Novell. Android, assegnargli un nome, in questo caso **testBubblePicker. Binding** e completare la procedura guidata. Il modello di binding Novell. Android si trova nel percorso seguente: libreria **Android > library > binding**:

    ![Associazione di creazione di Visual Studio](walkthrough-images/visual-studio-create-binding.png)

    Nella cartella Transformations sono disponibili tre file di trasformazione principali:

    - **Metadata. XML** : consente di apportare modifiche all'API finale, ad esempio la modifica dello spazio dei nomi dell'associazione generata.
    - **EnumFields. XML** : contiene il mapping tra le costanti e le enumerazioni di C# Java int.
    - **EnumMethods. XML** : consente la modifica dei parametri del metodo e dei tipi restituiti dalle costanti C# Java int alle enumerazioni.

    Lasciare vuoti i file **EnumFields. XML** e **EnumMethods. XML** e aggiornare **Metadata. XML** per definire le trasformazioni.

1. Sostituire il file **Transformations/Metadata. XML** esistente con il file **Metadata. XML** creato nel passaggio precedente. Nella finestra Proprietà verificare che l' **azione di compilazione** file sia impostata su **TransformationFile**:

    ![Metadati di Visual Studio](walkthrough-images/visual-studio-metadata.png)

1. Aggiungere il file **bubblepicker-v 1.0. AAR** creato nel passaggio 1 al progetto di associazione come riferimento nativo. Per aggiungere riferimenti alla libreria nativa, aprire Finder e passare alla cartella con l'archivio Android. Trascinare e rilasciare l'archivio nella cartella jar in Esplora soluzioni. In alternativa, è possibile usare l'opzione **Aggiungi** menu di scelta rapida nella cartella jar e scegliere **file esistenti...** . Scegliere di copiare il file nella directory ai fini di questa procedura dettagliata. Assicurarsi di verificare che l' **azione di compilazione** sia impostata su **LibraryProjectZip**:

    ![Riferimenti nativi di Visual Studio](walkthrough-images/visual-studio-native-reference.png)

1. Aggiungere un riferimento al pacchetto [NuGet Novell. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) . Questo pacchetto è un'associazione per la libreria standard Kotlin. Senza questo pacchetto, l'associazione funzionerà solo se la libreria Kotlin non usa tipi specifici di Kotlin, in caso contrario tutti questi membri non verranno esposti C# a e qualsiasi app che tenti di utilizzare l'associazione si arresterà in modo anomalo in fase di esecuzione.

    > [!TIP]
    > A causa di una limitazione di Novell. Android, gli strumenti di associazione possono essere aggiunti solo a un singolo archivio Android (AAR) per ogni progetto di binding. Se è necessario includere più file AAR, sono necessari più progetti Novell. Android, uno per ogni AAR. Se questo è il caso di questa procedura dettagliata, le quattro azioni precedenti di questo passaggio dovrebbero essere ripetute per ogni archivio. Come opzione alternativa, è possibile unire manualmente più archivi Android come singolo archivio e, di conseguenza, è possibile usare un singolo progetto di binding Novell. Android.

1. L'azione finale consiste nel compilare la libreria e non si verificano errori di compilazione. In caso di errori di compilazione, è possibile risolverli e gestirli utilizzando il file Metadata. XML creato in precedenza aggiungendo i metadati della trasformazione XML, per aggiungere, rimuovere o rinominare i membri della libreria.

## <a name="consume-the-binding-library"></a>Utilizzare la libreria di associazione

Il passaggio finale consiste nell'utilizzare la libreria di binding Novell. Android in un'applicazione Novell. Android. Creare un nuovo progetto Novell. Android, aggiungere un riferimento alla libreria di binding e all'interfaccia utente di selettore Bubble di rendering:

1. Creare il progetto Novell. Android. Usare l' **app android > > app Android** come punto di partenza e selezionare l'opzione più **recente e più grande** per le piattaforme di destinazione per evitare problemi di compatibilità. Tutti i passaggi seguenti sono destinati a questo progetto:

    ![Creazione dell'app in Visual Studio](walkthrough-images/visual-studio-create-app.png)

1. Aggiungere un riferimento al progetto di associazione o aggiungere un riferimento alla DLL creata in precedenza:

    ![Visual Studio aggiungere binding Reference. png](walkthrough-images/visual-studio-add-binding-reference.png)

1. Aggiungere un riferimento al pacchetto [NuGet Novell. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) , aggiunto in precedenza al progetto di associazione Novell. Android. Aggiunge il supporto per tutti i tipi specifici di Kotlin che devono essere gestiti in fase di esecuzione.  Senza questo pacchetto l'app può essere compilata ma si arresterà in modo anomalo in fase di esecuzione

    ![Visual Studio aggiungere StdLib NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Aggiungere il controllo `BubblePicker` al layout Android per `MainActivity`. Aprire il file **testBubblePicker/Resources/layout/content_main. XML** e aggiungere il nodo di controllo BubblePicker come ultimo elemento del controllo sul relativelayout radice:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout …>
        …
        <com.igalata.bubblepicker.rendering.BubblePicker
            android:id="@+id/picker"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:backgroundColor="@android:color/white" />
    </RelativeLayout>
    ```

1. Aggiornare il codice sorgente dell'app e aggiungere la logica di inizializzazione alla `MainActivity`, che attiva l'SDK di selezione Bubble:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState)
    {
        ...
        var picker = FindViewById<BubblePicker>(Resource.Id.picker);
        picker.BubbleSize = 20;
        picker.Adapter = new BubblePickerAdapter();
        picker.Listener = new BubblePickerListener(picker);
        ...
    }
    ```

    `BubblePickerAdapter` e `BubblePickerListener` sono due classi da creare da zero, che gestiscono l'interazione tra i dati e il controllo delle bolle:

    ```csharp
    public class BubblePickerAdapter : Java.Lang.Object, IBubblePickerAdapter
    {
        private List<string> _bubbles = new List<string>();
        public int TotalCount => _bubbles.Count;
        public BubblePickerAdapter()
        {
            for (int i = 0; i < 10; i++)
            {
                _bubbles.Add($"Item {i}");
            }
        }

        public PickerItem GetItem(int itemIndex)
        {
            if (itemIndex < 0 || itemIndex >= _bubbles.Count)
                return null;

            var result = _bubbles[itemIndex];
            var item = new PickerItem(result);
            return item;
        }
    }

    public class BubblePickerListener : Java.Lang.Object, IBubblePickerListener
    {
        public View Picker { get; }
        public BubblePickerListener(View picker)
        {
            Picker = picker;
        }

        public void OnBubbleDeselected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Deselected: {item.Title}", Snackbar.LengthLong)
                .SetAction("Action", (Android.Views.View.IOnClickListener)null)
                .Show();
        }

        public void OnBubbleSelected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Selected: {item.Title}", Snackbar.LengthLong)
            .SetAction("Action", (Android.Views.View.IOnClickListener)null)
            .Show();
        }
    }
    ```

1. Eseguire l'app, che dovrebbe eseguire il rendering dell'interfaccia utente di selezione Bubble:

    ![Demo di BubblePicker](walkthrough-images/bubble-picker-demo.png)

    L'esempio richiede codice aggiuntivo per eseguire il rendering dello stile degli elementi e gestire le interazioni, ma il controllo `BubblePicker` è stato creato e attivato correttamente.

Congratulazioni! È stata creata un'app Novell. Android e una libreria di binding, che usa una libreria Kotlin.  

A questo punto dovrebbe essere presente un'applicazione Novell. Android di base che usa una libreria Kotlin nativa tramite una libreria di binding Novell. Android. Questa procedura dettagliata usa intenzionalmente un esempio di base per evidenziare meglio i concetti chiave introdotti. Negli scenari reali, probabilmente sarà necessario esporre un numero maggiore di API e applicare le trasformazioni dei metadati.

## <a name="related-links"></a>Collegamenti correlati

- [Android Studio](https://developer.android.com/studio)
- [Installazione di Gradle](https://gradle.org/install/)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Decompilatore Java](http://java-decompiler.github.io/)
- [Libreria Kotlin BubblePicker](https://github.com/igalata/Bubble-Picker)
- [Associazione libreria Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadati di binding Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Novell. Kotlin. StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repository del progetto di esempio](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
