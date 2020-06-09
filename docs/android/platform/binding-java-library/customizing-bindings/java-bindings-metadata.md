---
title: Metadati per le associazioni Java
description: Il codice C# in Novell. Android chiama le librerie Java tramite binding, un meccanismo che astrae i dettagli di basso livello specificati in Java Native Interface (JNI). Novell. Android offre uno strumento che genera queste associazioni. Questo strumento consente allo sviluppatore di controllare il modo in cui viene creata un'associazione usando i metadati, che consente procedure come la modifica degli spazi dei nomi e la ridenominazione dei membri. In questo documento viene illustrato il funzionamento dei metadati, vengono riepilogati gli attributi supportati dai metadati e viene illustrato come risolvere i problemi di associazione modificando questi metadati.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 2a88888b2306589930ad6386fb69bbd3b48924b7
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571376"
---
# <a name="java-bindings-metadata"></a>Metadati per le associazioni Java

_Il codice C# in Novell. Android chiama le librerie Java tramite binding, un meccanismo che astrae i dettagli di basso livello specificati in Java Native Interface (JNI). Novell. Android offre uno strumento che genera queste associazioni. Questo strumento consente allo sviluppatore di controllare il modo in cui viene creata un'associazione usando i metadati, che consente procedure come la modifica degli spazi dei nomi e la ridenominazione dei membri. In questo documento viene illustrato il funzionamento dei metadati, vengono riepilogati gli attributi supportati dai metadati e viene illustrato come risolvere i problemi di associazione modificando questi metadati._

## <a name="overview"></a>Panoramica

Una **libreria di binding Java** Novell. Android tenta di automatizzare gran parte del lavoro necessario per l'associazione di una libreria Android esistente con l'ausilio di uno strumento noto a volte come _Generatore di binding_. Quando si associa una libreria Java, Novell. Android esamina le classi Java e genera un elenco di tutti i pacchetti, i tipi e i membri che devono essere associati. Questo elenco di API viene archiviato in un file XML che si trova nella ** \{ directory del progetto} \obj\Release\api.XML** per una build di **rilascio** e nella ** \{ directory del progetto} \obj\Debug\api.XML** per una compilazione di **debug** .

![Percorso del file API. XML nella cartella obj/debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

Il generatore di binding utilizzerà il file **API. XML** come linee guida per la generazione delle classi wrapper C# necessarie. Il contenuto di questo file XML è una variante del formato di _progetto open source Android_ di Google.
Il frammento di codice seguente è un esempio del contenuto di **API. XML**:

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

In questo esempio **API. XML** dichiara una classe nel `android` pacchetto denominato `Manifest` che estende `java.lang.Object` .

In molti casi, l'assistenza umana è necessaria per rendere l'API Java più ".NET like" o per correggere i problemi che impediscono la compilazione dell'assembly di associazione. Ad esempio, potrebbe essere necessario modificare i nomi dei pacchetti Java in spazi dei nomi .NET, rinominare una classe o modificare il tipo restituito di un metodo.

Queste modifiche non vengono realizzate modificando direttamente il **file API. XML** .
Al contrario, le modifiche vengono registrate in file XML speciali forniti dal modello libreria di associazione Java. Quando si compila l'assembly di binding Novell. Android, il generatore di binding sarà influenzato da questi file di mapping quando si crea l'assembly di associazione

Questi file di mapping XML possono essere trovati nella cartella **transforms** del progetto:

- **Metadata. XML** &ndash; consente di apportare modifiche all'API finale, ad esempio la modifica dello spazio dei nomi dell'associazione generata. 

- **EnumFields. XML** &ndash; contiene il mapping tra le `int` costanti Java e C# `enums` . 

- **EnumMethods. XML** &ndash; consente di modificare i parametri del metodo e i tipi restituiti da `int` costanti Java a C# `enums` . 

Il file **Metadata. XML** è la maggior parte dell'importazione di questi file poiché consente di apportare modifiche generali all'associazione, ad esempio:

- Rinominare gli spazi dei nomi, le classi, i metodi o i campi in modo che seguano le convenzioni .NET. 

- Rimuovere gli spazi dei nomi, le classi, i metodi o i campi che non sono necessari. 

- Trasferimento di classi in spazi dei nomi diversi. 

- L'aggiunta di altre classi di supporto per la progettazione dell'associazione segue i criteri di .NET Framework. 

Consente di passare al documento **Metadata. XML** in modo più dettagliato.

## <a name="metadataxml-transform-file"></a>File di trasformazione Metadata. XML

Come abbiamo già imparato, il file **Metadata. XML** viene usato dal generatore di binding per influenzare la creazione dell'assembly di associazione.
Il formato dei metadati usa la sintassi [XPath](https://www.w3.org/TR/xpath/) ed è quasi identico ai *metadati GAPI* descritti nella Guida ai [metadati di GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) . Questa implementazione è quasi un'implementazione completa di XPath 1,0 e pertanto supporta gli elementi dello standard 1,0. Questo file è un potente meccanismo basato su XPath per modificare, aggiungere, nascondere o spostare qualsiasi elemento o attributo nel file dell'API. Tutti gli elementi Rule nella specifica dei metadati includono un attributo path per identificare il nodo a cui deve essere applicata la regola. Le regole vengono applicate nell'ordine seguente:

- **Aggiungi nodo** &ndash; Accoda un nodo figlio al nodo specificato dall'attributo path.
- **attr** &ndash; Imposta il valore di un attributo dell'elemento specificato dall'attributo path.
- **Remove-node** &ndash; Rimuove i nodi corrispondenti a un XPath specificato.

Di seguito è riportato un esempio di file **Metadata. XML** :

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

Di seguito sono elencati alcuni degli elementi XPath usati più di frequente per le API Java:

- `interface`&ndash;Usato per individuare un'interfaccia java. ad esempio `/interface[@name='AuthListener']`.

- `class`&ndash;Usato per individuare una classe. ad esempio `/class[@name='MapView']`.

- `method`&ndash;Usato per individuare un metodo su una classe o un'interfaccia java. ad esempio `/class[@name='MapView']/method[@name='setTitleSource']`.

- `parameter`&ndash;Identificare un parametro per un metodo. Ad esempio: `/parameter[@name='p0']`

### <a name="adding-types"></a>Aggiunta di tipi

L' `add-node` elemento indica al progetto di binding Novell. Android di aggiungere una nuova classe wrapper a **API. XML**. Il frammento di codice seguente, ad esempio, consente di indirizzare il generatore di associazione per creare una classe con un costruttore e un singolo campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Rimozione di tipi

È possibile indicare al generatore di binding Novell. Android di ignorare un tipo Java e non associarlo. Questa operazione viene eseguita aggiungendo un `remove-node` elemento XML al file **Metadata. XML** :

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Ridenominazione di membri

La ridenominazione dei membri non può essere eseguita modificando direttamente il file **API. XML** perché Novell. Android richiede i nomi originali JNI (Java Native Interface). Pertanto, l' `//class/@name` attributo non può essere modificato; in caso contrario, l'associazione non funzionerà.

Si consideri il caso in cui si desidera rinominare un tipo, `android.Manifest` .
A tale scopo, è possibile provare a modificare direttamente il **file API. XML** e rinominare la classe come segue:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Il generatore di associazioni creerà quindi il codice C# seguente per la classe wrapper:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Si noti che la classe wrapper è stata rinominata `NewName` , mentre il tipo Java originale è ancora `Manifest` . Per la classe di binding Novell. Android non è più possibile accedere ai metodi in. `android.Manifest` la classe wrapper è associata a un tipo Java non esistente.

Per modificare correttamente il nome gestito di un tipo o di un metodo di cui è stato eseguito il Wrapped, è necessario impostare l' `managedName` attributo come illustrato nell'esempio seguente:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes"></a>

#### <a name="renaming-eventarg-wrapper-classes"></a>Ridenominazione di `EventArg` classi wrapper

Quando il generatore di binding Novell. Android identifica un `onXXX` Metodo Setter per un _tipo di listener_, verranno generati un evento e una sottoclasse C# per `EventArgs` supportare un'API con aroma .NET per il modello di listener basato su Java. Si consideri, ad esempio, la classe e il metodo Java seguenti:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Novell. Android eliminerà il prefisso `on` dal metodo setter e userà invece `2DSignNextManuever` come base per il nome della `EventArgs` sottoclasse. Alla sottoclasse verrà assegnato un nome simile al seguente:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Non si tratta di un nome di classe C# valido. Per risolvere il problema, l'autore del binding deve usare l' `argsType` attributo e fornire un nome C# valido per la `EventArgs` sottoclasse:

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Attributi supportati

Le sezioni seguenti descrivono alcuni degli attributi per la trasformazione delle API Java.

### <a name="argstype"></a>argsType

Questo attributo viene inserito nei metodi setter per assegnare un nome alla `EventArg` sottoclasse che verrà generata per supportare i listener Java. Questa procedura viene descritta più dettagliatamente nella sezione [ridenominazione delle classi wrapper di EventArg](#Renaming_EventArg_Wrapper_Classes) più avanti in questa guida.

### <a name="eventname"></a>eventName

Specifica un nome per un evento. Se vuota, impedisce la generazione di eventi.
Questa operazione viene descritta più dettagliatamente nella sezione titolo [rinominare le classi wrapper EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>gestito

Viene utilizzato per modificare il nome di un pacchetto, una classe, un metodo o un parametro. Ad esempio, per modificare il nome della classe Java `MyClass` in `NewClassName` :

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

Nell'esempio seguente viene illustrata un'espressione XPath per rinominare il metodo `java.lang.object.toString` in `Java.Lang.Object.NewManagedName` :

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType`viene usato per modificare il tipo restituito di un metodo. In alcune situazioni il generatore di associazioni dedurrà erroneamente il tipo restituito di un metodo Java, operazione che comporterà un errore in fase di compilazione. Una possibile soluzione in questa situazione è modificare il tipo restituito del metodo.

Il generatore di binding, ad esempio, ritiene che il metodo Java `de.neom.neoreadersdk.resolution.compareTo()` debba restituire un oggetto `int` e accetta `Object` come parametri, generando l'errore del messaggio di errore **CS0535:' de. Neom. Neoreadersdk. Resolution ' non implementa il membro di interfaccia ' Java. lang. IComparable. CompareTo (Java. lang. Object)'**. Il frammento di codice seguente illustra come modificare il tipo del primo parametro del metodo C# generato da un oggetto `DE.Neom.Neoreadersdk.Resolution` a un oggetto `Java.Lang.Object` : 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Modifica il tipo restituito di un metodo. Questa operazione non modifica l'attributo return (poiché le modifiche apportate agli attributi restituiti possono causare modifiche incompatibili alla firma JNI). Nell'esempio seguente, il tipo restituito del `append` metodo viene modificato da `SpannableStringBuilder` a `IAppendable` (ricordare che C# non supporta i tipi restituiti covariante):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>offuscato

Gli strumenti per offuscare le librerie Java potrebbero interferire con il generatore di binding Novell. Android e la relativa capacità di generare classi wrapper C#. Le caratteristiche delle classi offuscate includono: 

- Il nome della classe include **$** , ad esempio, **una classe $.**
- Il nome della classe è interamente compromesso da caratteri minuscoli, ad esempio **una classe.**

Questo frammento di codice è un esempio di come generare un tipo C# "non offuscato":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Questo attributo può essere utilizzato per modificare il nome di una proprietà gestita.

Un caso specializzato di utilizzo di `propertyName` implica la situazione in cui una classe Java dispone solo di un metodo di richiamo per un campo. In questa situazione il generatore di associazioni vuole creare una proprietà di sola scrittura, un elemento sconsigliato in .NET. Nel frammento di codice seguente viene illustrato come rimuovere le proprietà .NET impostando `propertyName` su una stringa vuota:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Si noti che i metodi setter e getter verranno comunque creati dal generatore di associazioni.

### <a name="sender"></a>mittente

Specifica quale parametro di un metodo deve essere il `sender` parametro quando viene eseguito il mapping del metodo a un evento. Il valore può essere `true` o `false` . Ad esempio:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibility

Questo attributo viene usato per modificare la visibilità di una classe, di un metodo o di una proprietà. Ad esempio, potrebbe essere necessario alzare di livello un `protected` metodo Java in modo che sia il wrapper C# corrispondente `public` :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields. XML e EnumMethods. XML

Esistono casi in cui le librerie Android usano costanti integer per rappresentare gli stati passati alle proprietà o ai metodi delle librerie. In molti casi, è utile associare queste costanti Integer alle enumerazioni in C#. Per semplificare questo mapping, usare i file **EnumFields. XML** e **EnumMethods. XML** nel progetto di binding. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definizione di un'enumerazione mediante EnumFields. XML

Il file **EnumFields. XML** contiene il mapping tra le `int` costanti Java e C# `enums` . Di seguito viene riportato l'esempio di un'enum C# creato per un set di `int` costanti: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Qui è stata adottata la classe Java e è stata `SKRealReachSettings` definita un'enumerazione C# denominata `SKMeasurementUnit` nello spazio dei nomi `Skobbler.Ngx.Map.RealReach` . Le `field` voci definiscono il nome della costante Java (ad esempio `UNIT_SECOND` ), il nome della voce enum (esempio `Second` ) e il valore Integer rappresentato da entrambe le entità (ad esempio `0` ). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definizione di metodi Get/Setter mediante EnumMethods. XML

Il file **EnumMethods. XML** consente la modifica dei parametri del metodo e dei tipi restituiti dalle `int` costanti Java a C# `enums` . In altre parole, esegue il mapping della lettura e della scrittura delle enumerazioni C# (definite nel file **EnumFields. XML** ) ai `int` `get` metodi e costanti Java `set` .

Data l' `SKRealReachSettings` enumerazione definita in precedenza, il file **EnumMethods. XML** seguente definisce il getter/setter per l'enumerazione:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La prima `method` riga esegue il mapping del valore restituito del `getMeasurementUnit` metodo Java all' `SKMeasurementUnit` enum. La seconda `method` riga esegue il mapping del primo parametro dell'oggetto `setMeasurementUnit` alla stessa enumerazione.

Con tutte queste modifiche, è possibile usare il codice seguente in Novell. Android per impostare `MeasurementUnit` : 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Summary

Questo articolo ha illustrato come Novell. Android usa i metadati per trasformare una definizione dell'API dal formato *Google* *AOSP*. Una volta incluse le modifiche possibili utilizzando *Metadata. XML*, sono state esaminate le limitazioni rilevate durante la ridenominazione dei membri e viene visualizzato l'elenco degli attributi XML supportati, che descrive quando ogni attributo deve essere utilizzato.

## <a name="related-links"></a>Collegamenti correlati

- [Uso di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [Metadati GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
