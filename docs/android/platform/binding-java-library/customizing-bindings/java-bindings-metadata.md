---
title: Metadati di binding di linguaggio
description: Codice c# di xamarin chiama librerie Java le associazioni, che sono un meccanismo che estrae i dettagli di basso livello che sono specificati in JNI Java Native Interface (). Xamarin offre uno strumento che genera le associazioni. Questo strumento consente il controllo dello sviluppatore come viene creata un'associazione usando i metadati, che consente alle procedure, ad esempio spazi dei nomi di modifica e ridenominazione di membri. Questo documento illustra il funzionamento di metadati, riepiloga gli attributi dei metadati supporta e viene spiegato come risolvere i problemi di associazione modificando questi metadati.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 6dea13fcda43cad22b8bea9838bbcb23b97820c7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="java-bindings-metadata"></a>Metadati di binding di linguaggio

_Codice c# di xamarin chiama librerie Java le associazioni, che sono un meccanismo che estrae i dettagli di basso livello che sono specificati in JNI Java Native Interface (). Xamarin offre uno strumento che genera le associazioni. Questo strumento consente il controllo dello sviluppatore come viene creata un'associazione usando i metadati, che consente alle procedure, ad esempio spazi dei nomi di modifica e ridenominazione di membri. Questo documento illustra il funzionamento di metadati, riepiloga gli attributi dei metadati supporta e viene spiegato come risolvere i problemi di associazione modificando questi metadati._


## <a name="overview"></a>Panoramica

Un xamarin **Java associazione libreria** tenta di automatizzare gran parte delle operazioni necessarie per l'associazione di una libreria Android esistente con l'aiuto di uno strumento talvolta noto anche come il _generatore di associazioni_. Quando si associa una raccolta di Java, xamarin verrà controllare le classi di Java e generare un elenco di tutti i pacchetti, i tipi e membri che deve essere associata. Questo elenco di API viene archiviato in un file XML che è reperibile in  **\{progetto directory}\obj\Release\api.xml** per un **versione** compilare e  **\{progetto Directory}\obj\Debug\api.XML** per un **DEBUG** di compilazione.

![Percorso del file nella cartella obj/Debug api.xml](java-bindings-metadata-images/java-bindings-metadata-01.png)

Verrà utilizzato il generatore di associazioni di **api.xml** file come linee guida per la generazione di classi wrapper c# necessarie. Il contenuto del file XML è una variante di Google _Android Apri progetto di origine_ formato.
Nel frammento seguente è riportato un esempio del contenuto di **api.xml**:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

In questo esempio, **api.xml** dichiara una classe di `android` pacchetto denominato `Manifest` che estende il `java.lang.Object`.

In molti casi, è necessario per rendere l'API di linguaggio è più ".NET come" o di risolvere i problemi che impediscono l'assembly di associazione dalla compilazione assistenza risorse umane. Ad esempio, potrebbe essere necessario modificare i nomi dei pacchetti Java agli spazi dei nomi .NET, ridenominazione di una classe o modificare il tipo restituito di un metodo.

Queste modifiche non è possibile modificare **api.xml** direttamente.
Al contrario, le modifiche vengono registrate nel file XML speciali che vengono forniti dal modello di libreria di Binding di linguaggio. Quando si compila l'assembly di associazione di xamarin, il generatore di associazioni influirà questi file di mapping durante la creazione di associazione assembly

Questi file di mapping XML, vedere il **trasforma** nella cartella del progetto:

-   **MetaData.xml** &ndash; consente modifiche da apportare all'API finale, ad esempio per cambiare lo spazio dei nomi dell'associazione generata. 

-   **EnumFields.xml** &ndash; contiene il mapping tra Java `int` costanti e c# `enums` . 

-   **EnumMethods.xml** &ndash; consente di modificare i parametri dei metodi e tipi restituiti da Java `int` costanti in c# `enums` . 

Il **MetaData.xml** file è più l'importazione di questi file, come le modifiche di uso generale per l'associazione, ad esempio:

-   Ridenominazione degli spazi dei nomi, classi, metodi o campi in modo seguono le convenzioni di .NET. 

-   Rimozione di spazi dei nomi, classi, metodi o campi che non sono necessari. 

-   Spostamento di classi in spazi dei nomi diversi. 

-   Aggiunta di classi di supporto aggiuntivo per rendere la progettazione dell'associazione seguono i modelli di .NET framework. 

Consente di spostarsi discutere **Metadata.xml** in modo più dettagliato.


## <a name="metadataxml-transform-file"></a>Metadata.xml Transform File

Come è stato già spiegato, il file **Metadata.xml** viene utilizzato dal generatore di associazioni per influenzare la creazione dell'assembly dell'associazione.
Usa il formato dei metadati [XPath](https://www.w3.org/TR/xpath/) sintassi ed è quasi identica a quella di *metadati GAPI* descritto in [GAPI metadati](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) Guida. Questa implementazione è quasi un'implementazione completa di XPath 1.0 e pertanto supporta gli elementi in 1.0 standard. Questo file è un XPath basato su un meccanismo avanzato per modificare, aggiungere, nascondere o spostare qualsiasi attributo o elemento nel file di API. Tutti gli elementi della regola nella specifica i metadati includono un attributo path per identificare il nodo a cui si desidera applicare la regola. Le regole vengono applicate nell'ordine seguente:

* **aggiunta di nodi** &ndash; aggiunge un nodo figlio per il nodo specificato dall'attributo del percorso.
* **Attr** &ndash; imposta il valore di un attributo dell'elemento specificato dall'attributo del percorso.
* **Rimuovi nodo** &ndash; rimuove i nodi che corrispondono a un'espressione XPath specificato.

Di seguito è riportato un esempio di un **Metadata.xml** file:

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

Di seguito sono elencati alcuni degli elementi di XPath usati più comunemente per l'API di Java:

-   `interface` &ndash; Utilizzato per individuare un'interfaccia Java. ad esempio `/interface[@name='AuthListener']`.

-   `class` &ndash; Utilizzato per individuare una classe. ad esempio `/class[@name='MapView']`.

-   `method` &ndash; Utilizzato per individuare un metodo su un'interfaccia o classe Java. ad esempio `/class[@name='MapView']/method[@name='setTitleSource']`.

-   `parameter` &ndash; Identificare un parametro per un metodo. Ad esempio `/parameter[@name='p0']`



### <a name="adding-types"></a>Aggiunta di tipi

Il `add-node` elemento indicherà il progetto di associazione di xamarin per aggiungere una nuova classe wrapper per **api.xml**. Ad esempio, il seguente frammento indirizzerà il generatore di associazione per creare una classe con un costruttore e un singolo campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Rimozione dei tipi

È possibile indicare il generatore di associazioni di xamarin per ignorare un tipo di linguaggio e non associato. Questa operazione viene eseguita aggiungendo un `remove-node` elemento XML da cui il **metadata.xml** file:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Ridenominazione di membri

Ridenominazione di membri non può essere eseguita modificando direttamente il **api.xml** file perché xamarin richiede i nomi originali di JNI Java Native Interface (). Pertanto, il `//class/@name` attributo non può essere modificato; in caso, l'associazione non funzionerà.

Si consideri il caso in cui si desidera rinominare un tipo, `android.Manifest`.
A tale scopo, è possibile provare a modificare direttamente **api.xml** e rinominare la classe come illustrato di seguito:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Ciò comporterà la creazione di codice c# seguente per la classe wrapper il generatore di associazioni:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Si noti che la classe wrapper è stata rinominata in `NewName`, mentre il tipo Java originale è ancora `Manifest`. Non è più possibile per la classe binding xamarin ad accedere automaticamente a tutti i metodi `android.Manifest`; la classe wrapper è associata a un tipo Java inesistente.

Per modificare correttamente il nome gestito di un tipo sottoposto a wrapping (o metodo), è necessario impostare il `managedName` attributo come illustrato in questo esempio:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Ridenominazione `EventArg` classi Wrapper

Quando il generatore di associazione xamarin identifica un `onXXX` metodo di impostazione di un _tipo di listener_, un evento in c# e `EventArgs` sottoclasse verrà generata per supportare .NET al API per il listener basato su Java modello. Ad esempio, si consideri la seguente classe Java e il metodo:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin eliminerà il prefisso `on` dal metodo setter e utilizzare invece `2DSignNextManuever` come base per il nome del `EventArgs` sottoclasse. La sottoclasse verrà denominata qualcosa di simile a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Questo non è un nome di classe c# valido. Per risolvere il problema, è necessario usare l'autore di associazione di `argsType` attributo e fornire un nome c# valido per il `EventArgs` sottoclasse:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Attributi supportati

Nelle sezioni seguenti vengono descritti alcuni degli attributi per la trasformazione API Java.

### <a name="argstype"></a>argsType

Questo attributo viene inserito nei metodi setter per assegnare un nome di `EventArg` sottoclasse che verrà generata per supportare i listener di Java. Questo aspetto viene descritto in modo dettagliato più avanti nella sezione [ridenominazione classi Wrapper EventArg](#Renaming_EventArg_Wrapper_Classes) più avanti in questa Guida.

### <a name="eventname"></a>eventName

Specifica un nome di un evento. Se vuoto, impedisce la generazione di eventi.
Questo aspetto viene descritto più dettagliatamente nel titolo della sezione [ridenominazione classi Wrapper EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Viene utilizzato per modificare il nome di un pacchetto, una classe, un metodo o parametro. Ad esempio per modificare il nome della classe Java `MyClass` a `NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

Nell'esempio seguente viene illustrata un'espressione XPath per rinominare il metodo `java.lang.object.toString` a `Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` viene utilizzato per modificare il tipo restituito di un metodo. In alcune situazioni il generatore di associazioni in modo non corretto dedurrà il tipo restituito di un metodo Java, che produrrà un errore in fase di compilazione. In questo caso una possibile soluzione consiste nel modificare il tipo restituito del metodo.

Ad esempio, il generatore di associazioni ritiene che il metodo Java `de.neom.neoreadersdk.resolution.compareTo()` deve restituire un `int`, che comporta il messaggio di errore **errore CS0535: ' Germania. Neom.Neoreadersdk.Resolution' non implementa il membro di interfaccia 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**. Il frammento di codice seguente viene illustrato come modificare il tipo restituito del metodo c# generato da un `int` per un `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Modifica il tipo restituito di un metodo. Ciò non modifica l'attributo restituito (come le modifiche per restituire gli attributi possono generare modifiche incompatibile con la firma JNI). Nell'esempio seguente, il tipo restituito del `append` metodo viene modificato da `SpannableStringBuilder` a `IAppendable` (tenere presente che c# non supporta tipi restituiti covarianti):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>offuscato

Gli strumenti che coprono le librerie di Java possono interferire con il generatore di associazione di xamarin e la possibilità di generare classi wrapper c#. Caratteristiche delle classi offuscate includono: * include il nome della classe un **$**, vale a dire **una class $** * il nome della classe è interamente compromesso di lettere minuscole, ad esempio  **a.Class**

Questo frammento è riportato un esempio di come generare un tipo c# "non offuscato":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>
          propertyName

Questo attributo può essere utilizzato per modificare il nome di una proprietà gestita.

Un caso di utilizzo specializzato `propertyName` implica la situazione in cui una classe Java ha solo un metodo di richiamo di un campo. In questo caso il generatore di associazione desidera creare una proprietà di sola scrittura, un elemento che è sconsigliato in .NET. Il frammento di codice seguente viene illustrato come "Rimuovi" le proprietà .NET impostando il `propertyName` su una stringa vuota:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Si noti che i metodi di impostazione e il richiamo verranno comunque creati dal generatore di associazioni.

### <a name="sender"></a>sender

Specifica il parametro di un metodo deve essere il `sender` parametro quando il metodo viene eseguito il mapping a un evento. Il valore può essere `true` o `false`. Ad esempio:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilità

Questo attributo viene utilizzato per modificare la visibilità di una classe, un metodo o proprietà. Ad esempio, potrebbe essere necessario alzare di livello un `protected` metodo Java, in modo che è corrispondente c# wrapper è `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml ed EnumMethods.xml

Vi sono casi in cui le librerie Android utilizzano costanti integer per rappresentare gli Stati che vengono passati a proprietà o metodi delle librerie. In molti casi, è utile associare queste costanti integer per le enumerazioni in c#. Per semplificare questo mapping, utilizzare il **EnumFields.xml** e **EnumMethods.xml** file nel progetto di associazione. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definizione di un'enumerazione tramite EnumFields.xml

Il **EnumFields.xml** file contiene il mapping tra Java `int` costanti e c# `enums`. Prendiamo nel seguente esempio di un'enumerazione c# viene creato per un set di `int` costanti: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Qui abbiamo adottato la classe Java `SKRealReachSettings` e definita un'enumerazione c# denominata `SKMeasurementUnit` nello spazio dei nomi `Skobbler.Ngx.Map.RealReach`. Il `field` voci definisce il nome della costante Java (esempio `UNIT_SECOND`), il nome della voce enum (esempio `Second`) e il valore intero rappresentato da entrambe le entità (esempio `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definire i metodi di Getter/Setter usando EnumMethods.xml

Il **EnumMethods.xml** file consente di modificare i parametri dei metodi e tipi restituiti da Java `int` costanti in c# `enums`. In altre parole, viene eseguito il mapping in lettura e scrittura di enumerazioni C# (definito nel **EnumFields.xml** file) per Java `int` costante `get` e `set` metodi.

Dato il `SKRealReachSettings` enum definiti in precedenza, le operazioni seguenti **EnumMethods.xml** file definirebbe getter/setter per questa enumerazione:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

Il primo `method` riga esegue il mapping il valore restituito di Java `getMeasurementUnit` metodo il `SKMeasurementUnit` enum. Il secondo `method` viene eseguito il mapping il primo parametro della riga di `setMeasurementUnit` all'enumerazione stessa.

Con tutte le modifiche sul posto, è possibile utilizzare il codice seguente in xamarin per impostare il `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Riepilogo

In questo articolo viene descritto come xamarin utilizza i metadati per trasformare una definizione dell'API dal *Google* *formato AOSP*. Dopo che coprono le modifiche che sono possibili utilizzando *Metadata.xml*, esaminata le limitazioni durante la ridenominazione di membri e presentato l'elenco degli attributi XML supportati, che descrive quando ogni attributo deve essere utilizzato.



## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [Metadati GAPI](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
