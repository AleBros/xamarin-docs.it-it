---
title: Associazione di una libreria di Java
description: "La Comunità Android ha molte librerie Java che vuoi usare nell'app; Questa guida viene illustrato come incorporare librerie Java nell'applicazione xamarin mediante la creazione di una raccolta di associazioni."
ms.topic: article
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: f336767cb6aea8bd8c7ce44f6479850a63d473a6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="binding-a-java-library"></a>Associazione di una libreria di Java

_La Comunità Android ha molte librerie Java che vuoi usare nell'app; Questa guida viene illustrato come incorporare librerie Java nell'applicazione xamarin mediante la creazione di una raccolta di associazioni._

## <a name="overview"></a>Panoramica

L'ecosistema di libreria di terze parti per Android è notevole. Per questo motivo, è spesso opportuno utilizzare una libreria Android esistente di to crearne uno nuovo. Xamarin offre due modalità di utilizzo di queste librerie:

-   Creare un *libreria associazioni* la libreria con wrapper c# che include automaticamente in modo è possibile richiamare il codice Java tramite c# chiama.

-   Utilizzare il *Java Native Interface* (*JNI*) per richiamare direttamente le chiamate nel codice della libreria di Java. JNI è un framework di programmazione che consente al codice Java chiamare ed essere chiamato da applicazioni native o raccolte.

Questa guida viene illustrata la prima opzione: come creare un *libreria associazioni* che esegue il wrapping di uno o più librerie Java esistenti in un assembly che è possibile collegare a nell'applicazione. Per ulteriori informazioni sull'utilizzo JNI, vedere [utilizzo JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin implementa associazioni tramite *Callable Wrapper gestiti* (*MCW*). MCW è bridge JNI che viene utilizzato quando è necessario richiamare il codice Java codice gestito. Callable wrapper gestito fornisce anche il supporto per la creazione di sottoclassi tipi Java e per eseguire l'override di metodi virtuali su tipi Java. Allo stesso modo, ogni volta che si desidera richiamare il codice gestito codice runtime Android (ART), non tramite un altro bridge JNI noto come Android Callable Wrapper (ACW). Questo [architettura](~/android/internals/architecture.md) è illustrato nel diagramma seguente:

[![Architettura di bridge Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Una raccolta di associazioni è un assembly contenente Callable Wrapper gestiti per i tipi Java. Ad esempio, di seguito è un tipo Java, `MyClass`, che si desidera eseguire il wrapping in una raccolta di associazioni:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Dopo che si genera una raccolta di associazioni per il **JAR** contenente `MyClass`, è possibile crearne un'istanza e chiamare i metodi da c#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Per creare questa raccolta di associazioni, utilizzare il xamarin *Java associazioni libreria* modello. Il progetto di associazione risultante viene creato un assembly .NET con le classi MCW **JAR** file e risorse per i progetti di libreria Android incorporati. È inoltre possibile creare raccolte di associazioni per Android di archivio (. I file AAR) e i progetti di libreria Android Eclipse. Facendo riferimento all'assembly associazioni libreria DLL risultante, è possibile riutilizzare una libreria di Java esistente nel progetto xamarin.

Quando si fa riferimento a tipi nella libreria di associazione, è necessario utilizzare lo spazio dei nomi della libreria di associazione. In genere, si aggiungono un `using` direttiva all'inizio dell'origine c# di file che rappresenta la versione dello spazio dei nomi .NET del nome del pacchetto Java. Ad esempio, se il pacchetto Java nome per l'associazione **JAR** è il seguente:

```csharp
com.company.package
```

Quindi inserire il seguente `using` istruzione all'inizio del file di origine c# per accedere a tipi di associazione **JAR** file:

```csharp
using Com.Company.Package;
```


Quando si associa una libreria Android esistente, è necessario tenere presente quanto segue:

* **Esistono dipendenze esterne per la libreria?** &ndash; Eventuali dipendenze Java richieste dalla libreria Android devono essere incluso nel progetto di xamarin come un **ReferenceJar** o come un **EmbeddedReferenceJar**. Qualsiasi assembly nativo deve essere aggiunto al progetto associazione come un **EmbeddedNativeLibrary**.  

* **La versione dell'API di Android è la destinazione della libreria Android?** &ndash; Non è possibile effettuare il "downgrade" livello di API Android. Assicurarsi che xamarin associazione destinato il progetto è la stessa API livello (o versione successiva) della libreria Android.

* **La versione di JDK è stata utilizzata per compilare la libreria?** &ndash; Errori di associazione possono verificarsi se la libreria Android xamarin è stata compilata con una versione diversa di JDK più in uso. Se possibile, ricompilare la libreria Android utilizzando la stessa versione di JDK che viene utilizzato per l'installazione di xamarin.


## <a name="build-actions"></a>Azioni di compilazione

Quando si crea una raccolta di associazioni, impostare *le operazioni di compilazione* sul **JAR** o. I file AAR che viene incorporato nel progetto di libreria associazioni &ndash; ogni azione di compilazione determina il modo in **JAR** o. I file AAR verrà incorporato in (o a cui fa riferimento) la raccolta di associazioni. Nell'elenco seguente sono riepilogate queste azioni di compilazione:

* `EmbeddedJar` &ndash; Incorpora il **JAR** nella DLL risultante associazioni libreria come risorsa incorporata. Questo è il più semplice e la maggior parte delle azione di compilazione di uso comune. Utilizzare questa opzione quando si desidera che il **JAR** automaticamente compilato in codice byte e inserite in una raccolta di associazioni.

* `InputJar` &ndash; Non consente di incorporare il **JAR** nella raccolta di associazioni risultante. DLL. Raccolta di associazioni. DLL avrà una dipendenza su questo **JAR** in fase di esecuzione. Utilizzare questa opzione quando non si desidera includere il **JAR** nella raccolta di associazioni (ad esempio, per motivi di licenza). Se si utilizza questa opzione, è necessario assicurarsi che l'input **JAR** è disponibile nel dispositivo che esegue l'app.

* `LibraryProjectZip` &ndash; Incorpora un. File AAR nella raccolta di associazioni risultante. DLL. È simile a EmbeddedJar, ad eccezione del fatto che è possibile accedere alle risorse (così come codice) nel limite. File AAR. Utilizzare questa opzione se si desidera incorporare un. AAR nella libreria del binding.

* `ReferenceJar` &ndash; Specifica un riferimento **JAR**: un riferimento **JAR** è un **JAR** che uno dei associati **JAR** o. Dipende dal file AAR. Questo riferimento **JAR** viene utilizzato solo per soddisfare le dipendenze in fase di compilazione. Quando si usa questa azione di compilazione, le associazioni di c# non vengono create per il riferimento **JAR** e non è incorporato nella raccolta di associazioni risultante. DLL. Utilizzare questa opzione quando si effettuano una raccolta di associazioni per il riferimento **JAR** ma non è fatto ancora. Questa azione di compilazione è utile per creare il pacchetto più **JAR**s (e/o. AARs) in più raccolte di associazioni interdipendenti.

* `EmbeddedReferenceJar` &ndash; Incorpora un riferimento **JAR** nella raccolta di associazioni risultante. DLL. Utilizzare questa azione di compilazione quando si desidera creare associazioni c# per l'input sia **JAR** (o. AAR) e tutti i relativi riferimenti **JAR**(s) nella raccolta di associazioni.

* `EmbeddedNativeLibrary` &ndash; Incorpora nativo **. so** nell'associazione. Questa azione di compilazione viene utilizzata per **. so** i file necessari per il **JAR** file da associare. Potrebbe essere necessario caricare manualmente il **. so** libreria prima di eseguire codice dalla libreria Java. Come descritto di seguito.

Queste azioni sono illustrate in dettaglio nelle guide seguenti di compilazione.

Inoltre, le seguenti azioni di compilazione vengono utilizzate per consentire l'importazione di documentazione dell'API Java e convertirli in documentazione XML in c#:

* `JavaDocJar` viene usato per puntare all'archivio Javadoc Jar per una libreria di linguaggio conforme a uno stile di Maven pacchetto (in genere `FOOBAR-javadoc**.jar**`).
* `JavaDocIndex` viene usato per puntare al `index.html` file all'interno della documentazione di riferimento API HTML.
* `JavaSourceJar` viene utilizzato per integrare `JavaDocJar`, per generare innanzitutto JavaDoc da origini e quindi considerare i risultati come `JavaDocIndex`, per una libreria di linguaggio conforme a un Maven lo stile del pacchetto (in genere `FOOBAR-sources**.jar**`).

La documentazione dell'API deve essere il valore predefinito doclet da Java8, Java7 o Java6 SDK (si tratta di un formato diverso), o lo stile DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Tra cui una libreria nativa in un'associazione

Potrebbe essere necessario includere un **. so** libreria in un progetto di associazione di xamarin come parte dell'associazione di una libreria di Java. Quando viene eseguito il wrapping codice Java, xamarin avrà esito negativo rendere la chiamata JNI e il messaggio di errore _java.lang.UnsatisfiedLinkError: metodo nativo non trovato:_ verranno visualizzati nella finestra di logcat out per l'applicazione.

La correzione per questo consiste nel caricare manualmente il **. so** libreria con una chiamata a `Java.Lang.JavaSystem.LoadLibrary`. Ad esempio presupponendo che lo stato di un progetto xamarin è libreria condivisa **libpocketsphinx_jni.so** incluso nel progetto di associazione con un'azione di compilazione di **EmbeddedNativeLibrary**, le operazioni seguenti frammento di codice (eseguito prima di utilizzare la libreria condivisa) caricherà il **. so** libreria:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adattamento API Java in C&eparsl;

Il generatore di xamarin Binding verrà modificato alcune idiomi Java e i pattern da corrispondono ai modelli di .NET. L'elenco seguente descrive come viene eseguito il mapping Java a c# / .NET:

-   _Metodi set/get_ in Java vengono _proprietà_ in .NET.

-   _Campi_ in Java vengono _proprietà_ in .NET.

-   _Interfacce del listener/Listener_ in Java vengono _eventi_ in .NET. I parametri dei metodi delle interfacce di callback saranno rappresentati da un `EventArgs` sottoclasse.

-   Oggetto _classe nidificata statico_ in Java è un _le classi annidate_ in .NET.

-   Un _classe interna_ in Java è un _le classi annidate_ con un costruttore di istanza in c#.



## <a name="binding-scenarios"></a>Scenari di associazione

Le guide di uno scenario di associazione seguenti consentono di associare un linguaggio (o più librerie) per l'incorporazione nell'app:

-   [Associazione di una. File JAR](~/android/platform/binding-java-library/binding-a-jar.md) è una procedura dettagliata per la creazione di raccolte di associazioni per **JAR** file.

-   [Associazione di un. AAR](~/android/platform/binding-java-library/binding-an-aar.md) è una procedura dettagliata per la creazione di raccolte di associazioni per. File AAR. Leggere questa procedura dettagliata per informazioni su come associare le raccolte di Android Studio.

-   [Associazione di un progetto libreria di Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) è una procedura dettagliata per la creazione di librerie di associazione da progetti di libreria Android. Leggere questa procedura dettagliata per informazioni su come associare i progetti di libreria Android Eclipse.

-   [Personalizzazione delle associazioni](~/android/platform/binding-java-library/customizing-bindings/index.md) viene illustrato come rendere le modifiche manuali all'associazione per risolvere gli errori di compilazione e definire l'API risulta in modo che risulti più "c#-ad esempio".

-   [Risoluzione dei problemi di associazioni](~/android/platform/binding-java-library/troubleshooting-bindings.md) elenchi comuni scenari di errore di associazione, vengono illustrate le possibili cause e offre suggerimenti per la risoluzione di questi errori.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadati GAPI](http://www.mono-project.com/GAPI#Metadata)
- [Uso di librerie native](~/android/platform/native-libraries.md)
