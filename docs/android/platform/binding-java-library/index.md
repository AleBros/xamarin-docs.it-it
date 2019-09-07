---
title: Associazione di una libreria Java
description: La community Android include molte librerie Java che è possibile usare nell'app; Questa guida illustra come incorporare le librerie Java nell'applicazione Novell. Android creando una libreria di binding.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: e829c953278d8edeb697d27da8e3707ee1c91784
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757591"
---
# <a name="binding-a-java-library"></a>Associazione di una libreria Java

_La community Android include molte librerie Java che è possibile usare nell'app; Questa guida illustra come incorporare le librerie Java nell'applicazione Novell. Android creando una libreria di binding._

## <a name="overview"></a>Panoramica

L'ecosistema di librerie di terze parti per Android è molto grande. Per questo motivo, è spesso consigliabile usare una libreria Android esistente anziché crearne una nuova. Novell. Android offre due modi per usare queste librerie:

- Creare una *libreria di binding* che esegue automaticamente il wrapping della libreria C# con wrapper per poter richiamare il codice Java tramite C# chiamate.

- Usare *Java Native Interface* (*JNI*) per richiamare direttamente le chiamate nel codice della libreria Java. JNI è un Framework di programmazione che consente al codice Java di chiamare ed essere chiamato da applicazioni o librerie native.

Questa guida illustra la prima opzione: come creare una *libreria di binding* che esegue il wrapping di una o più librerie Java esistenti in un assembly a cui è possibile collegarsi nell'applicazione. Per altre informazioni sull'uso di JNI, vedere [uso di JNI](~/android/platform/java-integration/working-with-jni.md).

Novell. Android implementa associazioni usando *Managed wrappers* (*MCW*). MCW è un Bridge JNI usato quando il codice gestito deve richiamare codice Java. I wrapper richiamabili gestiti forniscono anche il supporto per la sottoclasse di tipi Java e per l'override di metodi virtuali su tipi Java. Analogamente, ogni volta che il codice di Android Runtime (ART) desidera richiamare il codice gestito, lo esegue tramite un altro Bridge JNI noto come Android Callable Wrapper (ACW). Questa [architettura](~/android/internals/architecture.md) è illustrata nel diagramma seguente:

[![Architettura del Bridge Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Una libreria di binding è un assembly che contiene wrapper richiamabili gestiti per i tipi Java. Ad esempio, di seguito è riportato un tipo `MyClass`Java,, di cui si vuole eseguire il wrapping in una libreria di binding:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Dopo aver generato una libreria di binding per il file con **estensione jar** che contiene `MyClass`, è possibile crearne un'istanza e chiamare i C#metodi da:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Per creare questa libreria di binding, usare il modello Novell. Android *Java bindings Library* . Il progetto di associazione risultante crea un assembly .NET con le classi MCW, i file con **estensione jar** e le risorse per i progetti di libreria Android incorporati. È anche possibile creare librerie di binding per l'archivio Android (. AAR) e i progetti della libreria Android di Eclipse. Facendo riferimento all'assembly DLL della libreria di binding risultante, è possibile riutilizzare una libreria Java esistente nel progetto Novell. Android.

Quando si fa riferimento ai tipi nella libreria di associazione, è necessario utilizzare lo spazio dei nomi della libreria di associazione. In genere, è possibile `using` aggiungere una direttiva all'inizio dei C# file di origine che corrisponde alla versione dello spazio dei nomi .NET del nome del pacchetto Java. Ad esempio, se il nome del pacchetto Java per il file con estensione **jar** associato è il seguente:

```csharp
com.company.package
```

Quindi, inserire la seguente `using` istruzione all'inizio dei file di C# origine per accedere ai tipi nel file con **estensione jar** associato:

```csharp
using Com.Company.Package;
```

Quando si associa una libreria Android esistente, è necessario tenere presente quanto segue:

- **Sono presenti dipendenze esterne per la libreria?** &ndash;Tutte le dipendenze Java richieste dalla libreria Android devono essere incluse nel progetto Novell. Android come **ReferenceJar** o come **EmbeddedReferenceJar**. Tutti gli assembly nativi devono essere aggiunti al progetto di associazione come **EmbeddedNativeLibrary**.  

- **Quale versione dell'API Android è la destinazione della libreria Android?** &ndash;Non è possibile effettuare il "downgrade" del livello API Android; Verificare che il progetto di binding Novell. Android abbia come destinazione lo stesso livello API (o versione successiva) della libreria Android.

- **Quale versione del JDK è stata usata per compilare la libreria?** &ndash;È possibile che si verifichino errori di binding se la libreria Android è stata compilata con una versione di JDK diversa da quella usata da Novell. Android. Se possibile, ricompilare la libreria Android usando la stessa versione del JDK usata dall'installazione di Novell. Android.

## <a name="build-actions"></a>Azioni di compilazione

Quando si crea una libreria di binding, si impostano le *azioni di compilazione* nel file con **estensione jar** o. File AAR incorporati nel progetto &ndash; di libreria dei binding ogni azione di compilazione determina il modo in cui il file con **estensione jar** o. Il file AAR verrà incorporato nella libreria dei binding (o a cui fa riferimento). Nell'elenco seguente sono riepilogate le azioni di compilazione seguenti:

- `EmbeddedJar`Incorpora il file con **estensione jar** nella dll della libreria di associazioni risultante come risorsa incorporata. &ndash; Si tratta dell'azione di compilazione più semplice e usata più di frequente. Usare questa opzione quando si vuole che il file con **estensione jar** venga compilato automaticamente nel codice byte e incluso in un pacchetto nella libreria dei binding.

- `InputJar`Non incorpora il file con **estensione jar** nella libreria dei binding risultante. &ndash; DLL. Libreria di binding. La DLL avrà una dipendenza da this **. jar** in fase di esecuzione. Utilizzare questa opzione quando non si desidera includere il file con **estensione jar** nella libreria dei binding (ad esempio, per motivi di licenza). Se si usa questa opzione, è necessario assicurarsi che il file input **. jar** sia disponibile nel dispositivo che esegue l'app.

- `LibraryProjectZip`&ndash; Incorpora un oggetto. File AAR nella libreria dei binding risultante. DLL. Questa operazione è simile a EmbeddedJar, ad eccezione del fatto che è possibile accedere alle risorse (oltre al codice) nell'associazione. File AAR. Utilizzare questa opzione se si desidera incorporare un. AAR nella libreria dei binding.

- `ReferenceJar` Specifica un file di riferimento. jar: un file di riferimento. jar è un file con estensione jar. &ndash; I file AAR dipendono da. Questo riferimento **. jar** viene usato solo per soddisfare le dipendenze in fase di compilazione. Quando si usa questa azione di compilazione C# , le associazioni non vengono create per il file Reference **. jar** e non sono incorporate nella libreria dei binding risultante. DLL. Usare questa opzione quando si renderà una libreria di binding per il file di riferimento **. jar** ma non ancora eseguita. Questa azione di compilazione è utile per la creazione di pacchetti di più file con **estensione jar**(e/o. AARs) in più librerie di binding interdipendenti.

- `EmbeddedReferenceJar`Incorpora un file Reference **. jar** nella libreria dei binding risultante. &ndash; DLL. Utilizzare questa azione di compilazione quando si desidera creare C# binding per il file di input **. jar** (o. AAR) e tutti i relativi file di riferimento **. jar**nella libreria dei binding.

- `EmbeddedNativeLibrary`Incorpora un oggetto nativo **. quindi** nell'associazione. &ndash; Questa azione di compilazione viene utilizzata per i file **. so** necessari per il file con estensione **jar** associato. Potrebbe essere necessario caricare manualmente la libreria **. so** prima di eseguire il codice dalla libreria Java. Questa procedura è descritta di seguito.

Queste azioni di compilazione sono descritte più dettagliatamente nelle guide seguenti.

Inoltre, le azioni di compilazione seguenti consentono di importare la documentazione dell'API Java e di convertirle nella C# documentazione XML:

- `JavaDocJar`viene usato per puntare al file jar di archivio Javadoc per una libreria Java conforme a uno stile del pacchetto Maven ( `FOOBAR-javadoc**.jar**`in genere).
- `JavaDocIndex`viene usato per puntare al `index.html` file all'interno della documentazione di riferimento dell'API HTML.
- `JavaSourceJar`viene usato per completare `JavaDocJar`, prima di generare Javadoc dalle origini e quindi trattare i risultati come `JavaDocIndex`, per una libreria Java conforme a uno stile del pacchetto Maven (in genere `FOOBAR-sources**.jar**`).

La documentazione dell'API deve essere il valore predefinito di doclet da Java8, Java7 o java6 SDK (sono tutti formati diversi) o lo stile DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Inclusione di una libreria nativa in un'associazione

Potrebbe essere necessario includere una libreria **. so** in un progetto di binding Novell. Android come parte dell'associazione di una libreria Java. Quando viene eseguito il codice Java di cui è stato eseguito il Wrapped, Novell. Android non riuscirà a eseguire _la chiamata JNI e il messaggio di errore Java. lang. UnsatisfiedLinkError: Metodo nativo non trovato:_ verrà visualizzato in logcat out per l'applicazione.

La correzione per questa operazione consiste nel caricare manualmente la libreria **. so** con una chiamata `Java.Lang.JavaSystem.LoadLibrary`a. Si supponga, ad esempio, che un progetto Novell. Android disponga di una libreria condivisa **libpocketsphinx_jni. pertanto** , incluso nel progetto di binding con un'azione di compilazione **EmbeddedNativeLibrary**, il frammento di codice seguente (eseguito prima di utilizzare la libreria condivisa) caricherà la libreria **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adattamento delle API Java a C&eparsl;

Il generatore di binding Novell. Android cambierà alcuni idiomi e modelli Java in modo che corrispondano ai modelli .NET. L'elenco seguente descrive come viene eseguito il mapping C#di Java a/.NET:

- I _metodi setter/Getter_ in Java sono _Proprietà_ in .NET.

- I _campi_ in Java sono _Proprietà_ in .NET.

- Le _interfacce listener/listener_ in Java sono _eventi_ in .NET. I parametri dei metodi nelle interfacce di callback verranno rappresentati da una `EventArgs` sottoclasse.

- Una _classe annidata statica_ in Java è una _classe annidata_ in .NET.

- Una _classe interna_ in Java è una _classe annidata_ con un costruttore di C#istanza in.

## <a name="binding-scenarios"></a>Scenari di associazione

Le guide dello scenario di associazione seguenti consentono di associare una libreria o librerie Java per l'integrazione nell'app:

- [Associazione di un oggetto. JAR](~/android/platform/binding-java-library/binding-a-jar.md) è una procedura dettagliata per la creazione di librerie di binding per i file con **estensione jar** .

- [Associazione di un oggetto. AAR](~/android/platform/binding-java-library/binding-an-aar.md) è una procedura dettagliata per la creazione di librerie di binding per. File AAR. Leggere questa procedura dettagliata per informazioni su come associare Android Studio librerie.

- Il [binding di un progetto di libreria Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) è una procedura dettagliata per la creazione di librerie di binding da progetti di libreria Android. Leggere questa procedura dettagliata per informazioni su come associare progetti di libreria Android per Eclipse.

- La [personalizzazione delle associazioni](~/android/platform/binding-java-library/customizing-bindings/index.md) spiega come apportare modifiche manuali al binding per risolvere gli errori di compilazione e definire l'API risultante in modo che sia più "C#simile".

- Le associazioni per la [risoluzione dei problemi](~/android/platform/binding-java-library/troubleshooting-bindings.md) elencano gli scenari di errore di associazione comuni, spiega le possibili cause e offre suggerimenti per la risoluzione di questi errori.

## <a name="related-links"></a>Collegamenti correlati

- [Uso di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadati GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Uso di librerie native](~/android/platform/native-libraries.md)
