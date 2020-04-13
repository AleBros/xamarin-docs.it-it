---
title: Metadati per le associazioni Java
description: Il codice di C, in Xamarin.Android chiama le librerie Java tramite associazioni, che sono un meccanismo che astrae i dettagli di basso livello specificati in Java Native Interface (JNI). Xamarin.Android fornisce uno strumento che genera queste associazioni. Questo strumento consente allo sviluppatore di controllare come viene creata un'associazione utilizzando i metadati, che consente procedure quali la modifica degli spazi dei nomi e la ridenominazione dei membri. In questo documento viene illustrato il funzionamento dei metadati, vengono riepilogati gli attributi supportati dai metadati e viene illustrato come risolvere i problemi di associazione modificando questi metadati.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 25a5d79084f7caa78eec4011c047bd19a63ef748
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487789"
---
# <a name="java-bindings-metadata"></a>Metadati per le associazioni Java

_Il codice di C, in Xamarin.Android chiama le librerie Java tramite associazioni, che sono un meccanismo che astrae i dettagli di basso livello specificati in Java Native Interface (JNI). Xamarin.Android fornisce uno strumento che genera queste associazioni. Questo strumento consente allo sviluppatore di controllare come viene creata un'associazione utilizzando i metadati, che consente procedure quali la modifica degli spazi dei nomi e la ridenominazione dei membri. In questo documento viene illustrato il funzionamento dei metadati, vengono riepilogati gli attributi supportati dai metadati e viene illustrato come risolvere i problemi di associazione modificando questi metadati._

## <a name="overview"></a>Panoramica

Una libreria di **associazione Java** Xamarin.Android tenta di automatizzare gran parte del lavoro necessario per associare una libreria Android esistente con l'aiuto di uno strumento talvolta noto come il generatore di _associazioni_. Quando si associa una libreria Java, Xamarin.Android controllerà le classi Java e genererà un elenco di tutti i pacchetti, i tipi e i membri da associare. L'elenco di API è memorizzato in un file XML che è **RELEASE** reperibile nella **DEBUG** ** \{directory del progetto, ad esempio obj, Release, Release.api.xml** per una compilazione di RELEASE e nella directory del ** \{progetto, obj, Debug, api.xml** per una compilazione DEBUG.

![Percorso del file api.xml nella cartella obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

Il generatore di associazioni utilizzerà il file **api.xml** come linea guida per la generazione delle classi wrapper necessarie di C. Il contenuto di questo file XML è una variazione del formato _Android Open Source Project_ di Google.
Il frammento di codice seguente è un esempio del contenuto di **api.xml:**

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

In questo esempio **api.xml** dichiara una `android` classe `Manifest` nel `java.lang.Object`pacchetto denominato che estende l'oggetto .

In molti casi, l'assistenza umana è necessaria per rendere l'API Java più ".NET like" o per correggere i problemi che impediscono la compilazione dell'assembly di associazione. Ad esempio, potrebbe essere necessario modificare i nomi dei pacchetti Java in spazi dei nomi .NET, rinominare una classe o modificare il tipo restituito di un metodo.

Queste modifiche non vengono ottenute modificando direttamente **api.xml.**
Al contrario, le modifiche vengono registrate in file XML speciali forniti dal modello libreria di associazione Java. Durante la compilazione dell'assembly di associazione Xamarin.Android, il generatore di associazioni sarà influenzato da questi file di mapping durante la creazione dell'assembly di associazione

Questi file di mapping XML sono disponibili nella cartella **Trasformazioni** del progetto:

- **MetaData.xml** &ndash; Consente di apportare modifiche all'API finale, ad esempio la modifica dello spazio dei nomi dell'associazione generata. 

- **EnumFields.xml** &ndash; Contiene il `int` mapping tra le `enums` costanti Java e il linguaggio C. 

- **EnumMethods.xml** &ndash; Consente di modificare i `int` parametri del metodo `enums` e i tipi restituiti dalle costanti Java a c'è . 

Il file **MetaData.xml** è la maggior parte dell'importazione di questi file in quanto consente modifiche generiche all'associazione, ad esempio:

- Ridenominazione di spazi dei nomi, classi, metodi o campi in modo che seguano le convenzioni .NET. 

- Rimozione di spazi dei nomi, classi, metodi o campi non necessari. 

- Spostamento di classi in spazi dei nomi diversi. 

- L'aggiunta di classi di supporto aggiuntive per fare in modo che la progettazione dell'associazione segua i modelli del framework .NET. 

Passiamo a discutere **il file Metadata.xml** in modo più dettagliato.

## <a name="metadataxml-transform-file"></a>File di trasformazione Metadata.xml

Come abbiamo già appreso, il file **Metadata.xml** viene utilizzato dal generatore di associazioni per influenzare la creazione dell'assembly di associazione.
Il formato dei metadati utilizza la sintassi [XPath](https://www.w3.org/TR/xpath/) ed è quasi identico ai *metadati GAPI* descritti nella guida [ai metadati GAPI.](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) Questa implementazione è quasi un'implementazione completa di XPath 1.0 e supporta pertanto gli elementi nello standard 1.0.This implementation is almost a complete implementation of XPath 1.0 and thus supports items in the 1.0 standard. Questo file è un potente meccanismo basato su XPath per modificare, aggiungere, nascondere o spostare qualsiasi elemento o attributo nel file API. Tutti gli elementi della regola nella specifica dei metadati includono un attributo path per identificare il nodo a cui deve essere applicata la regola. Le regole vengono applicate nel seguente ordine:

- **add-node** &ndash; Aggiunge un nodo figlio al nodo specificato dall'attributo path.
- **attr** &ndash; Imposta il valore di un attributo dell'elemento specificato dall'attributo path.
- **remove-node** &ndash; Rimuove i nodi corrispondenti a un XPath specificato.

Di seguito è riportato un esempio di file **Metadata.xml:**

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

Di seguito sono elencati alcuni degli elementi XPath più comunemente utilizzati per le API Java:

- `interface`&ndash; Utilizzato per individuare un'interfaccia Java. ad esempio `/interface[@name='AuthListener']`.

- `class`&ndash; Utilizzato per individuare una classe . ad esempio `/class[@name='MapView']`.

- `method`&ndash; Utilizzato per individuare un metodo in una classe o interfaccia Java. ad esempio `/class[@name='MapView']/method[@name='setTitleSource']`.

- `parameter`&ndash; Identificare un parametro per un metodo. Ad esempio: `/parameter[@name='p0']`

### <a name="adding-types"></a>Aggiunta di tipi

L'elemento `add-node` indicherà al progetto di binding Xamarin.Android di aggiungere una nuova classe wrapper a **api.xml**. Ad esempio, il frammento di codice seguente indirizzerà il generatore di associazione per creare una classe con un costruttore e un singolo campo:For example, the following snippet will direct the Binding Generator to create a class with a constructor and a single field:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Rimozione di tipiRemoving Types

È possibile indicare al generatore di associazioni Xamarin.Android di ignorare un tipo Java e non associarlo. Questa operazione viene `remove-node` eseguita aggiungendo un elemento XML al file **metadata.xml:This** is done by adding a XML element to the metadata.xml file:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Ridenominazione dei membri

La ridenominazione dei membri non può essere eseguita modificando direttamente il file **api.xml** perché Xamarin.Android richiede i nomi JNI (Java Native Interface) originali. Pertanto, `//class/@name` l'attributo non può essere modificato; in caso affermativo, l'associazione non funzionerà.

Si consideri il caso in `android.Manifest`cui si desidera rinominare un tipo, .
A tale scopo, è possibile provare a modificare direttamente api.xml e rinominare la classe in questo modo:To accomplish this, we might try to directly edit **api.xml** and rename the class like so:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

In questo modo il generatore di associazioni la creazione del seguente codice c'è per la classe wrapper:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Si noti che la classe `NewName`wrapper è stata rinominata in , mentre il tipo Java originale è ancora `Manifest`. Non è più possibile per la classe di associazione Xamarin.Android accedere a qualsiasi metodo su `android.Manifest`; la classe wrapper è associata a un tipo Java inesistente.

Per modificare correttamente il nome gestito di un tipo o `managedName` metodo di cui è stato eseguito il wrapping, è necessario impostare l'attributo come illustrato nell'esempio seguente:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Ridenominazione delle `EventArg` classi wrapper

Quando il generatore di associazione Xamarin.Android identifica un `onXXX` metodo setter per un tipo di _listener_, verranno generati un evento e `EventArgs` una sottoclasse di C, per supportare un'API insaporita di .NET per il modello di listener basato su Java. Si consideri ad esempio la classe e il metodo Java seguenti:As an example, consider the following Java class and method:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android eslascerà `on` il prefisso dal `2DSignNextManuever` metodo setter e invece userà come base per il nome della `EventArgs` sottoclasse. La sottoclasse avrà un nome simile al:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Non si tratta di un nome di classe valido di C. Per risolvere questo problema, l'autore dell'associazione deve utilizzare l'attributo `argsType` e fornire un nome valido di C ': `EventArgs`

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Attributi supportati

Nelle sezioni seguenti vengono descritti alcuni attributi per la trasformazione delle API Java.

### <a name="argstype"></a>tipo di args

Questo attributo viene inserito nei `EventArg` metodi setter per denominare la sottoclasse che verrà generata per supportare i listener Java. Ciò è descritto in modo più dettagliato di seguito nella sezione [Ridenominazione](#Renaming_EventArg_Wrapper_Classes) delle classi wrapper EventArg più avanti in questa guida.

### <a name="eventname"></a>eventName

Specifica un nome per un evento. Se vuoto, inibisce la generazione di eventi.
Ciò è descritto in modo più dettagliato nella sezione intitolata [Ridenominazione](#Renaming_EventArg_Wrapper_Classes)delle classi wrapper EventArg .

### <a name="managedname"></a>managedName

Viene utilizzato per modificare il nome di un pacchetto, una classe, un metodo o un parametro. Ad esempio, per modificare il `MyClass` `NewClassName`nome della classe Java in:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

Nell'esempio seguente viene illustrata un'espressione `java.lang.object.toString` `Java.Lang.Object.NewManagedName`XPath per la ridenominazione del metodo in :

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType (tipo gestito)

`managedType`viene utilizzato per modificare il tipo restituito di un metodo. In alcune situazioni il generatore di associazioni dedurrà in modo non corretto il tipo restituito di un metodo Java, che genererà un errore in fase di compilazione. Una possibile soluzione in questa situazione consiste nel modificare il tipo restituito del metodo.

Ad esempio, il generatore di associazioni `de.neom.neoreadersdk.resolution.compareTo()` ritiene `int` che `Object` il metodo Java deve restituire un e prendere come parametri, che genera il messaggio di errore **Errore CS0535: 'DE. Neom.Neoreadersdk.Resolution' non implementa il membro di interfaccia 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'**. Nel frammento di codice riportato di seguito viene illustrato come `DE.Neom.Neoreadersdk.Resolution` modificare `Java.Lang.Object`il tipo del primo parametro del metodo generato di C, da a a : 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn (Ritorno gestito)

Modifica il tipo restituito di un metodo. Ciò non modifica l'attributo return (poiché le modifiche agli attributi restituite possono causare modifiche incompatibili alla firma JNI). Nell'esempio seguente, il tipo `append` restituito del `SpannableStringBuilder` `IAppendable` metodo viene modificato da a (richiamare che il linguaggio C' non supporta i tipi restituiti covarianti):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>Offuscato

Gli strumenti che offuscano le librerie Java possono interferire con il generatore di associazioni Xamarin.Android e la sua capacità di generare classi wrapper C. Le caratteristiche delle classi offuscate includono: 

- Il nome della **$** classe include un, ad esempio **a.class**
- Il nome della classe è interamente compromesso da caratteri minuscoli, ad esempio **a.class**

Questo frammento di codice è un esempio di come generare un tipo di C ,senza offuscamento:This snippet is an example of how to generate an "un-obfuscated" C' type:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Questo attributo può essere utilizzato per modificare il nome di una proprietà gestita.

Un caso specializzato di utilizzo `propertyName` comporta la situazione in cui una classe Java ha solo un metodo getter per un campo. In questo caso il generatore di associazione desidera creare una proprietà di sola scrittura, operazione sconsigliata in .NET. Nel frammento di codice seguente viene illustrato come `propertyName` "rimuovere" le proprietà .NET impostando il su una stringa vuota:

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

Specifica quale parametro di un `sender` metodo deve essere il parametro quando il metodo viene mappato a un evento. Il valore `true` può `false`essere o . Ad esempio:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibility

Questo attributo viene utilizzato per modificare la visibilità di una classe, metodo o proprietà. Ad esempio, potrebbe essere necessario `protected` alzare di livello un metodo Java `public`in modo che il wrapper corrispondente di C è:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml ed EnumMethods.xml

Esistono casi in cui le librerie Android usano costanti integer per rappresentare gli stati che vengono passati alle proprietà o ai metodi delle librerie. In molti casi, è utile associare queste costanti integer alle enumerazioni in C. Per facilitare questo mapping, utilizzare i file **EnumFields.xml** e **EnumMethods.xml** nel progetto di associazione. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definizione di un'enumerazione tramite EnumFields.xmlDefining an Enum using EnumFields.xml

Il file **EnumFields.xml** contiene `int` il mapping tra `enums`le costanti Java e il file di codice . Prendiamo l'esempio seguente di un'enumerazione C , `int` creata per un set di costanti:Let's take the following example of a C'è enum being created for a set of constants: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

In questo caso è `SKRealReachSettings` stata acquisita la `SKMeasurementUnit` classe Java `Skobbler.Ngx.Map.RealReach`e definito un'enumerazione di C, denominata nello spazio dei nomi . Le `field` voci definiscono il nome della `UNIT_SECOND`costante Java (esempio ), il `Second`nome della voce enum (esempio `0`) e il valore intero rappresentato da entrambe le entità (esempio ). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definizione dei metodi Getter/Setter mediante EnumMethods.xmlDefining Getter/Setter Methods using EnumMethods.xml

Il file **EnumMethods.xml** consente di modificare `int` i parametri del `enums`metodo e i tipi restituiti dalle costanti Java a c'è . In altre parole, esegue il mapping della lettura e della scrittura delle enumerazioni di `int` `get` C, definite nel file **EnumFields.xml,** su costanti e `set` metodi Java.

Dato `SKRealReachSettings` l'enumerazione definita in precedenza, il seguente file **EnumMethods.xml** definirà il getter/setter per questa enumerazione:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La `method` prima riga esegue il `getMeasurementUnit` mapping del `SKMeasurementUnit` valore restituito del metodo Java all'enumerazione. La `method` seconda riga esegue il `setMeasurementUnit` mapping del primo parametro di all'enumerazione stessa.

Con tutte queste modifiche sul posto, è possibile utilizzare il codice seguente `MeasurementUnit`in Xamarin.Android per impostare il : 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come Xamarin.Android utilizza i metadati per trasformare una definizione di API dal formato *Google* *AOSP*. Dopo aver trattato le modifiche possibili utilizzando *Metadata.xml,* ha esaminato le limitazioni incontrate durante la ridenominazione dei membri e ha presentato l'elenco degli attributi XML supportati, descrivendo quando deve essere utilizzato ogni attributo.

## <a name="related-links"></a>Collegamenti correlati

- [Lavorare con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [Metadati GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
