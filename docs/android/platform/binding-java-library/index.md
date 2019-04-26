---
title: Associazione di una libreria Java
description: La community Android ha molte librerie Java che vuoi usare nell'app; Questa guida illustra come incorporare librerie Java nell'applicazione xamarin. Android tramite la creazione di una libreria di binding.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 0f4ec3cfd7c154e43db9f8e123259317c0d17e21
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957439"
---
# <a name="binding-a-java-library"></a>Associazione di una libreria Java

_La community Android ha molte librerie Java che vuoi usare nell'app; Questa guida illustra come incorporare librerie Java nell'applicazione xamarin. Android tramite la creazione di una libreria di binding._

## <a name="overview"></a>Panoramica

L'ecosistema delle librerie di terze parti per Android è enorme. Per questo motivo, è spesso opportuno usare una libreria Android esistente rispetto a to crearne uno nuovo. Xamarin. Android offre due modi per usare queste librerie:

-   Creare un *libreria di binding* che esegue automaticamente il wrapping con la libreria C# del codice wrapper in modo che è possibile richiamare Java tramite C# le chiamate.

-   Usare la *Java Native Interface* (*JNI*) per richiamare le chiamate nel codice della libreria Java direttamente. JNI è un framework di programmazione che consente il codice Java e chiamate da app native o librerie.

Questa guida illustra la prima opzione: come creare un *libreria di binding* che esegue il wrapping di uno o più librerie Java esistenti in un assembly a cui è possibile collegare a nell'applicazione. Per altre informazioni sull'uso di JNI, vedere [utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin. Android implementa le associazioni utilizzando *Callable Wrapper gestiti* (*MCW*). MCW è un bridge JNI che viene usato quando il codice gestito deve richiamare il codice Java. Wrapper richiamabili gestiti garantiscono inoltre supporto per la creazione di una sottoclasse tipi Java e per eseguire l'override di metodi virtuali su tipi Java. Analogamente, ogni volta che il codice di runtime Android (ART) intende richiamare il codice gestito, viene eseguita tramite un altro bridge JNI noto come Android Callable Wrapper (ACW). Ciò [architettura](~/android/internals/architecture.md) è illustrata nel diagramma seguente:

[![Architettura di bridge Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Una libreria di binding è un assembly contenente Callable Wrapper gestiti per i tipi Java. Ad esempio, ecco un tipo di Java, `MyClass`, che si vuole eseguire il wrapping in una libreria di binding:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Dopo che si genera una libreria di binding per il **JAR** che contiene `MyClass`, è possibile crearne un'istanza e chiamare metodi su di esso da C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Per creare questa raccolta di associazioni, si utilizza di xamarin. Android *libreria di binding Java* modello. Il progetto di associazione risultante viene creato un assembly .NET con le classi in MCW **JAR** più file e risorse per i progetti di libreria Android incorporati. È anche possibile creare librerie di binding per l'archivio Android (. I file AAR) e i progetti di Eclipse libreria Android. Facendo riferimento all'assembly risultante DLL della libreria di binding, è possibile riutilizzare una libreria Java esistente nel progetto xamarin. Android.

Quando si fa riferimento ai tipi nella libreria di Binding, è necessario usare lo spazio dei nomi della libreria di binding. In genere, si aggiunge un `using` direttiva all'inizio del C# file di origine che rappresenta la versione dello spazio dei nomi .NET del nome del pacchetto Java. Ad esempio, se il pacchetto Java assegnare un nome per l'associazione **JAR** è il seguente:

```csharp
com.company.package
```

Quindi si dovrà inserire quanto segue `using` istruzione all'inizio del C# file di origine in tipi di in verrà restituito il limite di accesso **con estensione jar** file:

```csharp
using Com.Company.Package;
```


Quando si associa una libreria Android esistente, è necessario tenere presente quanto riportato di seguito:

* **Sono presenti le dipendenze esterne per la libreria?** &ndash; Eventuali dipendenze Java richieste dalla libreria di Android devono essere incluso nel progetto xamarin. Android come un **ReferenceJar** o come un **EmbeddedReferenceJar**. Qualsiasi assembly nativi deve essere aggiunto al progetto di associazione come un **EmbeddedNativeLibrary**.  

* **Versione dell'API Android inclusa se la destinazione di libreria Android.** &ndash; Non è possibile effettuare "il downgrade di" livello di API Android. Assicurarsi che il progetto di associazione di xamarin. Android è destinato alla stessa API livello (o versione successiva) come la libreria Android.

* **Quale versione del pacchetto JDK è stato usato per compilare la libreria?** &ndash; Se la libreria Android è stata creata con una versione diversa di JDK più in uso da xamarin. Android, potrebbero verificarsi errori di associazione. Se possibile, ricompilare la libreria Android usando la stessa versione di JDK che viene usato per l'installazione di xamarin. Android.


## <a name="build-actions"></a>Azioni di compilazione

Quando si crea una libreria di binding, si imposta *azioni di compilazione* nel **JAR** o. I file AAR che viene incorporato nel progetto libreria di binding &ndash; ogni azione di compilazione determina come il **JAR** o. File AAR verrà incorporate nel (o fa riferimento) la raccolta di associazioni. Nell'elenco seguente sono riepilogate queste azioni di compilazione:

* `EmbeddedJar` &ndash; Incorpora il **JAR** nella DLL risultante associazioni libreria come risorsa incorporata. Questo è il più semplice e la maggior parte delle azione di compilazione di uso comune. Usare questa opzione quando si desidera che il **JAR** automaticamente compilate nel codice byte e inserite in libreria di binding.

* `InputJar` &ndash; Non consente di incorporare le **JAR** nella libreria di binding risultante. DLL. La libreria di binding. DLL avranno una dipendenza su questo **JAR** in fase di esecuzione. Usare questa opzione quando non si desidera includere il **JAR** nella libreria del binding (ad esempio, per motivi di licenza). Se si usa questa opzione, è necessario assicurarsi che l'input **JAR** è disponibile nel dispositivo che esegue l'app.

* `LibraryProjectZip` &ndash; Incorpora un. File AAR nella libreria di binding risultante. DLL. Ciò è simile a EmbeddedJar, ad eccezione del fatto che sia possibile accedere alle risorse (così come codice) in verrà restituito il limite. File AAR. Usare questa opzione quando si vuole incorporare un. AAR alla libreria di binding.

* `ReferenceJar` &ndash; Specifica un riferimento **JAR**: un riferimento **con estensione jar** è un **JAR** che uno dei associati **JAR** o. I file AAR dipende. Questo riferimento **JAR** viene usato solo per soddisfare le dipendenze in fase di compilazione. Quando si usa questa azione di compilazione, C# per il riferimento non vengono creati i binding **JAR** e non è incorporato nella libreria di binding risultante. DLL. Usare questa opzione quando si apporterà una libreria di binding per il riferimento **JAR** ma non fatto ancora. Questa azione di compilazione è utile per creare il pacchetto multiplo **JAR**s (e/o. AARs) in più raccolte di associazioni interdipendenti.

* `EmbeddedReferenceJar` &ndash; Incorpora un riferimento **JAR** nella libreria di binding risultante. DLL. Usare questa azione di compilazione quando si desidera creare C# binding per l'input sia **JAR** (o. AAR) e tutti i relativi riferimenti **JAR**(s) nella libreria di binding.

* `EmbeddedNativeLibrary` &ndash; Incorpora nativo **SO** nell'associazione. Questa azione di compilazione viene utilizzata per **SO** i file necessari per il **JAR** file a cui è associata. Potrebbe essere necessario caricare manualmente il **SO** libreria prima di eseguire codice dalla libreria Java. Come descritto di seguito.

Queste azioni sono descritti in dettaglio nelle guide seguenti di compilazione.

Inoltre, le azioni di compilazione seguenti vengono usate per consentire l'importazione di documentazione dell'API Java e convertirli in C# documentazione XML:

* `JavaDocJar` viene usato per puntare all'archivio Javadoc Jar per una libreria Java che è conforme a uno stile di pacchetto Maven (in genere `FOOBAR-javadoc**.jar**`).
* `JavaDocIndex` viene usato per puntare a `index.html` file all'interno della documentazione di riferimento API HTML.
* `JavaSourceJar` viene utilizzato come complemento `JavaDocJar`, per generare innanzitutto JavaDoc da origini e quindi considerare i risultati come `JavaDocIndex`, per una libreria Java che è conforme a un Maven lo stile del pacchetto (in genere `FOOBAR-sources**.jar**`).

La documentazione dell'API deve essere il doclet predefinito da Java8, Java7 o SDK Java6 (sono tutte le diverse formato), o lo stile DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Inclusione di una libreria nativa in un'associazione

Potrebbe essere necessario includere un **SO** libreria in un progetto di associazione di xamarin. Android come parte dell'associazione di una libreria Java. Quando viene eseguito il codice Java sottoposto a wrapping, xamarin. Android riuscirà a effettuare la chiamata JNI e il messaggio di errore _java.lang.UnsatisfiedLinkError: Metodo nativo non trovato:_ verranno visualizzati nella finestra di logcat out per l'applicazione.

La soluzione per questo problema consiste nel caricare manualmente il **SO** libreria con una chiamata a `Java.Lang.JavaSystem.LoadLibrary`. Ad esempio presupponendo che un progetto xamarin. Android ha condiviso library **libpocketsphinx_jni.so** incluso nel progetto di associazione con un'azione di compilazione **EmbeddedNativeLibrary**, il seguente frammento di codice (eseguito prima di usare la libreria condivisa) verranno caricati i **SO** libreria:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adattamento delle API Java per C&eparsl;

Il generatore di Binding xamarin. Android cambierà alcuni idiomi di Java e i modelli in modo che corrispondano ai modelli di .NET. L'elenco seguente descrive come Java è mappata a C#/.NET:

-   _I metodi Getter/Setter_ in Java vengono _proprietà_ in .NET.

-   _I campi_ in Java vengono _proprietà_ in .NET.

-   _Interfacce del listener/Listener_ in Java vengono _eventi_ in .NET. I parametri dei metodi nelle interfacce di callback saranno rappresentati da un `EventArgs` sottoclasse.

-   Oggetto _classi annidate statiche_ in Java è un _classi annidate_ in .NET.

-   Un' _classe interna_ in Java è un _classi annidate_ con un costruttore di istanza in C#.



## <a name="binding-scenarios"></a>Scenari di associazione

Guide relative agli scenari di associazione seguenti consentono di associare una libreria Java (o le librerie) per l'incorporazione nell'app:

-   [Associazione di una. JAR](~/android/platform/binding-java-library/binding-a-jar.md) è una procedura dettagliata per la creazione di librerie di associazioni per **JAR** file.

-   [Associazione di una. AAR](~/android/platform/binding-java-library/binding-an-aar.md) è una procedura dettagliata per la creazione di librerie di associazioni per. File AAR. Leggere questa procedura dettagliata per apprendere come associare le raccolte di Android Studio.

-   [Associazione di un progetto di libreria Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) è una procedura dettagliata per la creazione di librerie di binding da progetti di libreria Android. Leggere questa procedura dettagliata per apprendere come associare i progetti di libreria Android Eclipse.

-   [Personalizzazione delle associazioni](~/android/platform/binding-java-library/customizing-bindings/index.md) spiega come rendere le modifiche manuali all'associazione per risolvere gli errori di compilazione e la forma dell'API risulta in modo che risulti più "C#-ad esempio".

-   [Risoluzione dei problemi di associazioni](~/android/platform/binding-java-library/troubleshooting-bindings.md) vengono elencati scenari comuni errori di associazione, vengono illustrate le possibili cause e offre suggerimenti per la risoluzione di questi errori.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadati GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Uso di librerie native](~/android/platform/native-libraries.md)
