---
title: 'Procedura dettagliata: associare una libreria Android KotlinWalkthrough: Bind an Android Kotlin library'
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'associazione Xamarin.Android per una libreria Kotlin esistente, BubblePicker.This article provides a hands-on walkthrough of creating a Xamarin.Android binding for an existing Kotlin library, BubblePicker. Vengono trattati argomenti quali la compilazione di una libreria Kotlin, il binding e l'utilizzo dell'associazione in un'applicazione Xamarin.Android.It covers topics such as compiling a Kotlin library, binding it, and using the binding in a Xamarin.Android application.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291592"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Procedura dettagliata: associare una libreria Android KotlinWalkthrough: Bind an Android Kotlin library

Xamarin consente agli sviluppatori di dispositivi mobili di creare app per dispositivi mobili native multipiattaforma utilizzando Visual Studio e C. È possibile utilizzare i componenti SDK della piattaforma Android out-of-the-box, ma in molti casi si desidera anche utilizzare SDK di terze parti scritti per quella piattaforma e Xamarin consente di farlo tramite associazioni. Per incorporare un framework Android di terze parti nell'applicazione Xamarin.Android, è necessario creare un binding Xamarin.Android prima di poterlo utilizzare nelle applicazioni.

La piattaforma Android, insieme alle sue lingue native e agli strumenti, sono in continua evoluzione, compresa la recente introduzione del linguaggio Kotlin, che alla fine è impostato per sostituire Java. Ci sono un certo numero di SDK di terze parti, che sono già stati migrati da Java a Kotlin e ci presenta nuove sfide. Anche se il processo di associazione Kotlin è simile a Java, richiede passaggi aggiuntivi e impostazioni di configurazione per compilare ed eseguire correttamente come parte di un'applicazione Xamarin.Android.  

L'obiettivo di questo documento è delineare un approccio di alto livello per affrontare questo scenario e fornire una guida dettagliata con un semplice esempio.

## <a name="background"></a>Background

Kotlin è stato rilasciato nel febbraio 2016 ed è stato posizionato come alternativa al compilatore Java standard in Android Studio entro il 2017. Più tardi nel 2019, Google ha annunciato che il linguaggio di programmazione Kotlin sarebbe diventato il suo linguaggio preferito per gli sviluppatori di app Android. L'approccio di associazione di alto livello è simile [al processo di associazione delle librerie Java regolari](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) con alcuni passaggi specifici di Kotlin importanti.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo scenario, saranno necessari gli elementi seguenti:

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Java Decompiler](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Creare una libreria nativaBuild a native library

Il primo passaggio consiste nel creare una libreria Kotlin nativa usando Android Studio.The first step is to build a native Kotlin library using Android Studio. La libreria è di solito fornita da uno sviluppatore di terze parti o disponibile presso [il repository Maven di Google](https://maven.google.com/web/index.html) e altri repository remoti. Ad esempio, in questa esercitazione viene creata un'associazione per la libreria Kotlin di Bubble Picker:As an example, in this tutorial a binding for the Bubble Picker Kotlin Library is created:

![Demo di GitHub BubblePicker](walkthrough-images/github-bubblepicker-demo.png)

1. Scaricare [il codice sorgente](https://github.com/igalata/Bubble-Picker/archive/develop.zip) da GitHub per la libreria e decomprimerlo in una cartella locale **Bubble-Picker**.

1. Avviare Android Studio e selezionare l'opzione di menu **Apri un progetto Android Studio esistente** scegliendo la cartella locale Bubble-Picker:

    ![Progetto aperto di Android Studio](walkthrough-images/android-studio-open-project.png)

1. Verifica che Android Studio sia aggiornato, incluso Gradle. Il codice sorgente può essere compilato con successo su Android Studio v3.5.3, Gradle v5.4.1. Le istruzioni su come aggiornare Gradle all'ultima versione di Gradle [potrebbero essere trovate qui](https://gradle.org/install/).

1. Verificare che Android SDK richiesto sia installato. Il codice sorgente richiede Android SDK v25.The source code requires Android SDK v25. Aprire l'opzione di menu **Strumenti > SDK Manager** per installare i componenti SDK.

1. Aggiornare e sincronizzare il file di configurazione **main build.gradle** che si trova nella radice della cartella del progetto:

    - Impostare la versione di Kotlin su 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Registrare il repository Maven predefinito di Google in modo che la dipendenza della libreria di supporto possa essere risolta:

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

    - Una volta aggiornato, il file di configurazione non è sincronizzato e Gradle mostra il pulsante **Sincronizza ora,** premerlo e attendere il completamento del processo di sincronizzazione:

        ![Android Studio Gradle Sync Ora](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > La cache delle dipendenze di Gradle può essere danneggiata, ciò si verifica talvolta dopo un timeout della connessione di rete. Scaricare nuovamente le dipendenze e sincronizzare il progetto (richiede la rete).

        > [!TIP]
        > Lo stato di un processo di compilazione Gradle (daemon) può essere danneggiato. Fermare tutti i daemonGrad Gradle può risolvere questo problema. Arrestare i processi di compilazione Gradle (richiede il riavvio). Nel caso di processi Gradle corrotti, è anche possibile provare a chiudere l'IDE e quindi uccidere tutti i processi Java.

        > [!TIP]
        > Il progetto potrebbe utilizzare un plugin di terze parti, che non è compatibile con gli altri plugin del progetto o con la versione di Gradle richiesta dal progetto.

1. Aprire il menu Gradle a destra, passare al menu **bubblepicker > Attività,** eseguire l'attività di **compilazione** toccando due volte su di essa e attendere il completamento del processo di compilazione:

    ![Attività di esecuzione di Android Studio Gradle](walkthrough-images/android-studio-gradle-execute-task.png)

1. Aprire il browser dei file della cartella radice e passare alla cartella di compilazione: **Bubble-Picker -> bubblepicker -> build -> outputs -> aar**, salvare il file **bubblepicker-release.aar** come **bubblepicker-v1.0.aar**, questo file verrà utilizzato più avanti nel processo di associazione:

    ![Android Studio AAR Output](walkthrough-images/android-studio-aar-output.png)

Il file AAR è un archivio Android, che contiene il codice sorgente Kotlin compilato e le risorse, richiesto da Android per eseguire un'applicazione utilizzando questo SDK.  

## <a name="prepare-metadata"></a>Preparare i metadati

Il secondo passaggio consiste nel preparare il file di trasformazione dei metadati, che viene usato da Xamarin.Android per generare le rispettive classi C. Un progetto di associazione Xamarin.Android scoprirà tutte le classi native e i membri da un determinato archivio Android che successivamente genera un file XML con i metadati appropriati. Il file di trasformazione dei metadati creato manualmente viene quindi applicato alla linea di base generata in precedenza per creare il file di definizione XML finale utilizzato per generare il codice C.

I metadati utilizzano la sintassi [XPath](https://www.w3.org/TR/xpath/) e vengono utilizzati dal generatore di associazioni per influenzare la creazione dell'assembly di associazione. L'articolo [Metadati di associazione Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata) fornisce ulteriori informazioni sulle trasformazioni, che possono essere applicate:The Java Binding Metadata article provides more information on transformations, which could be applied:

1. Creare un file **Metadata.xml** vuoto:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Definire le trasformazioni xml:Define xml transformations:

- La libreria Kotlin nativa ha due dipendenze, che non si desidera esporre al mondo c'è, definire due trasformazioni per ignorarle completamente. Importante dire, i membri nativi non verranno rimossi dal file binario risultante, solo le classi di C . [Il decompiler Java](http://java-decompiler.github.io/) può essere utilizzato per identificare le dipendenze. Eseguire lo strumento e aprire il file AAR creato in precedenza, di conseguenza verrà visualizzata la struttura dell'archivio Android, che riflette tutte le dipendenze, i valori, le risorse, il manifesto e le classi:  

    ![Dipendenze del decompiler Java](walkthrough-images/java-decompiler-dependencies.png)

    Le trasformazioni per ignorare l'elaborazione di questi pacchetti vengono definite usando le istruzioni XPath:The transformations to skip processing these packages are defined using XPath instructions:

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- La `BubblePicker` classe nativa `getBackgroundColor` dispone `setBackgroundColor` di due metodi e e `BackgroundColor` la trasformazione seguente la trasformerà in una proprietà di C:

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- I tipi `UInt, UShort, ULong, UByte` non firmati richiedono una gestione speciale. Per questi tipi Kotlin modifica automaticamente i nomi dei metodi e i tipi di parametri, che si riflette nel codice generato:For these types Kotlin changes method names and parameters types automatically, which is reflected in the generated code:

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    Questo codice viene compilato nel seguente codice byte Java:

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    Inoltre, tipi correlati `UIntArray, UShortArray, ULongArray, UByteArray` come sono anche colpiti da Kotlin. Il nome del metodo viene modificato per includere un suffisso aggiuntivo e i parametri vengono modificati in una matrice di elementi di versioni firmate degli stessi tipi. Nell'esempio riportato di `UIntArray` seguito un `int[]` parametro di tipo `fooUIntArrayMethod` `fooUIntArrayMethod--ajY-9A`viene convertito automaticamente e il nome del metodo viene modificato da in . Quest'ultimo viene scoperto dagli strumenti Xamarin.Android e generato come nome di metodo valido:

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    Questo codice viene compilato nel seguente codice byte Java:

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    Per assegnargli un nome significativo, è possibile aggiungere i seguenti metadati al file **Metadata.xml**, che aggiornerà il nome in modo originariamente definito nel codice Kotlin:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    Nell'esempio BubblePicker non sono presenti membri che utilizzano tipi senza segno, pertanto non sono necessarie ulteriori modifiche.

- Per impostazione predefinita, i membri Kotlin con parametri generici vengono trasformati in parametri di Java.`Lang.Object` digitare. Ad esempio, un metodo Kotlin \<ha un parametro generico T>:For example, a Kotlin method has a generic parameter T>:

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Una volta generata un'associazione Xamarin.Android, il metodo viene esposto a C , come indicato di seguito:Once a Xamarin.Android binding is generated, the method is exposed to C 's, as below:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    I generics Java e Kotlin non sono supportati dalle associazioni Xamarin.Android, pertanto viene creato un metodo generale di C, per accedere all'API generica. Come una velocità di connessione è possibile creare un wrapper libreria Kotlin ed esporre le API necessarie in modo fortemente tipizzato senza generics. In alternativa, è possibile creare helper sul lato C , per risolvere il problema nello stesso modo tramite API con tipizzato forte.

    > [!TIP]
    > Trasformando i metadati, tutte le modifiche possono essere applicate all'associazione generata. L'articolo [Associazione della libreria Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) illustra in dettaglio come vengono generati ed elaborati i metadati.

## <a name="build-a-binding-library"></a>Compilare una libreria di associazioniBuild a binding library

Il passaggio successivo consiste nel creare un progetto di associazione Xamarin.Android usando il modello di associazione di Visual Studio, aggiungere i metadati necessari, i riferimenti nativi e quindi compilare il progetto per produrre una libreria consumabile:The next step is to create a Xamarin.Android binding project using the Visual Studio binding template, add required metadata, native references and then build the project to produce a consumable library:

1. Aprire Visual Studio per Mac e creare un nuovo progetto Xamarin.Android Binding Library, assegnargli un nome, in questo caso **testBubblePicker.Binding** e completare la procedura guidata. Il modello di binding Xamarin.Android si trova nel percorso seguente: **Libreria > Android > Libreria di associazione:**

    ![Visual Studio Create Binding](walkthrough-images/visual-studio-create-binding.png)

    Nella cartella Trasformazioni sono presenti tre file di trasformazione principali:

    - **Metadata.xml:** consente di apportare modifiche all'API finale, ad esempio la modifica dello spazio dei nomi dell'associazione generata.
    - **EnumFields.xml** : Contiene il mapping tra le costanti Java int e le enumerazioni di C.
    - **EnumMethods.xml** – Consente di modificare i parametri del metodo e i tipi restituiti dalle costanti Java int alle enumerazioni C.

    Mantenere vuoti i file **EnumFields.xml** ed **EnumMethods.xml** e aggiornare il file **Metadata.xml** per definire le trasformazioni.

1. Sostituire il file **Transformations/Metadata.xml** esistente con il file **Metadata.xml** creato nel passaggio precedente. Nella finestra delle proprietà verificare che l'azione di **compilazione** del file sia impostata su **TransformationFile**:

    ![Visual Studio Metadata](walkthrough-images/visual-studio-metadata.png)

1. Aggiungere il file **bubblepicker-v1.0.aar** compilato nel passaggio 1 al progetto di associazione come riferimento nativo. Per aggiungere riferimenti a librerie native, aprire Finder e passare alla cartella con l'archivio Android. Trascinare l'archivio nella cartella Jars in Esplora soluzioni. In alternativa, è possibile utilizzare l'opzione del menu **Discelta** nella cartella Jars e scegliere **File esistenti...**. Scegliere di copiare il file nella directory ai fini di questa procedura dettagliata. Assicurarsi di verificare che l'azione di **compilazione** sia impostata su **LibraryProject**:

    ![Visual Studio Native Reference](walkthrough-images/visual-studio-native-reference.png)

1. Aggiungere un riferimento al pacchetto [Xamarin.Kotlin.StdLib NuGet.](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) Questo pacchetto è un binding per la libreria standard di Kotlin.This package is a binding for Kotlin Standard Library. Senza questo pacchetto, l'associazione funzionerà solo se la libreria Kotlin non usa tipi specifici di Kotlin, altrimenti tutti questi membri non verranno esposti a C ' e qualsiasi app che tenta di utilizzare l'associazione si arresterà in fase di esecuzione.

    > [!TIP]
    > A causa di una limitazione del Xamarin.Android, gli strumenti di associazione solo un singolo archivio Android (AAR) può essere aggiunto per ogni progetto di associazione. Se è necessario includere più file AAR, sono necessari più progetti Xamarin.Android, uno per ogni AAR. Se questo fosse il caso per questa procedura dettagliata, le quattro azioni precedenti di questo passaggio dovrebbero essere ripetute per ogni archivio. Come opzione alternativa, è possibile unire manualmente più archivi Android come un unico archivio e di conseguenza è possibile utilizzare un singolo progetto di binding Xamarin.Android.

1. L'azione finale consiste nel compilare la libreria e fare in modo che non presenti errori di compilazione. In caso di errori di compilazione, possono essere risolti e gestiti utilizzando il file Metadata.xml, creato in precedenza aggiungendo metadati di trasformazione xml, che aggiungeranno, rimuoveranno o rinomineranno i membri della libreria.

## <a name="consume-the-binding-library"></a>Usare la libreria di associazioneConsume the binding library

Il passaggio finale consiste nell'utilizzare la libreria di binding Xamarin.Android in un'applicazione Xamarin.Android.The final step is to consume the Xamarin.Android binding library in a Xamarin.Android application. Creare un nuovo progetto Xamarin.Android, aggiungere un riferimento alla libreria di associazione ed eseguire il rendering Bubble Picker UI:Create a new Xamarin.Android project, add reference to the binding library and render Bubble Picker UI:

1. Creare un progetto Xamarin.Android.Create Xamarin.Android project. Usa **l'app > Android >'app Android** come punto di partenza e seleziona **l'opzione Più recente e più grande** come piattaforme di destinazione per evitare problemi di compatibilità. Tutti i passaggi seguenti sono destinati a questo progetto:All the following steps target this project:

    ![Creazione di un'app di Visual Studio](walkthrough-images/visual-studio-create-app.png)

1. Aggiungere un riferimento al progetto di associazione o aggiungere un riferimento creato dalla DLL creata in precedenza:Add a project reference to the binding project or add a reference the DLL created previously:

    ![Visual Studio Add Binding Reference.png](walkthrough-images/visual-studio-add-binding-reference.png)

1. Aggiungere un riferimento al pacchetto [Xamarin.Kotlin.StdLib NuGet,](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) aggiunto in precedenza al progetto di associazione Xamarin.Android. Aggiunge supporto a qualsiasi tipo specifico di Kotlin che devono essere consegnati in fase di esecuzione.  Senza questo pacchetto l'app può essere compilata ma si arresterà in fase di esecuzione:Without this package the app can be compiled but will crash at runtime:

    ![Visual Studio Aggiungere StdLib NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Aggiungere `BubblePicker` il controllo al `MainActivity`layout Android per . Aprire il file **testBubblePicker/Resources/layout/content_main.xml** e aggiungere il nodo del controllo BubblePicker come ultimo elemento del controllo RelativeLayout radice:

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

1. Aggiornare il codice sorgente dell'app e `MainActivity`aggiungere la logica di inizializzazione a , che attiva Bubble Picker SDK:

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

    `BubblePickerAdapter`e `BubblePickerListener` sono due classi da creare da zero, che gestiscono i dati delle bolle e l'interazione di controllo:

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

1. Eseguire l'app, che deve eseguire il rendering dell'interfaccia utente di Selezione bolle:Run the app, which should render the Bubble Picker UI:

    ![Demo di BubblePicker](walkthrough-images/bubble-picker-demo.png)

    L'esempio richiede codice aggiuntivo per eseguire il `BubblePicker` rendering dello stile degli elementi e gestire le interazioni, ma il controllo è stato creato e attivato correttamente.

Congratulazioni! Hai creato correttamente un'app Xamarin.Android e una libreria di associazione, che utilizza una libreria Kotlin.  

Ora dovresti avere un'applicazione Xamarin.Android di base che usa una libreria Kotlin nativa tramite una libreria di binding Xamarin.Android. In questa procedura dettagliata viene utilizzato intenzionalmente un esempio di base per enfatizzare meglio i concetti chiave introdotti. Negli scenari reali, sarà probabilmente necessario esporre un numero maggiore di API e applicarvi trasformazioni di metadati.

## <a name="related-links"></a>Collegamenti correlati

- [Android Studio](https://developer.android.com/studio)
- [Installazione Gradle](https://gradle.org/install/)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Java Decompiler](http://java-decompiler.github.io/)
- [Libreria BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Libreria Java di associazioneBinding Java Library](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadati di associazione JavaJava Binding Metadata](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repository di progetto di esempio](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
