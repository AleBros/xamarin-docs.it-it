---
title: Associazione di una libreria Java
description: La community Android include molte librerie Java che è possibile usare nell'app; Questa guida illustra come incorporare le librerie Java nell'applicazione Xamarin.Android creando una libreria di binding.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027610"
---
# <a name="binding-a-java-library"></a>Associazione di una libreria Java

_La community Android include molte librerie Java che è possibile usare nell'app; Questa guida illustra come incorporare le librerie Java nell'applicazione Xamarin.Android creando una libreria di binding._

## <a name="overview"></a>Panoramica

L'ecosistema di librerie di terze parti per Android è molto grande. Per questo motivo, è spesso consigliabile usare una libreria Android esistente anziché crearne una nuova. Xamarin.Android offre due modi per usare queste librerie:

- Creare una *libreria di binding* che esegue automaticamente il wrapping della libreria C# con wrapper per poter richiamare il codice Java tramite C# chiamate.

- Usare *Java Native Interface* (*JNI*) per richiamare direttamente le chiamate nel codice della libreria Java. JNI è un Framework di programmazione che consente al codice Java di chiamare ed essere chiamato da applicazioni o librerie native.

Questa guida illustra la prima opzione: come creare una *libreria di binding* che esegue il wrapping di una o più librerie Java esistenti in un assembly a cui è possibile collegarsi nell'applicazione. Per altre informazioni sull'uso di JNI, vedere [uso di JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin.Android implementa associazioni usando *Managed wrappers* (*MCW*). MCW è un Bridge JNI usato quando il codice gestito deve richiamare codice Java. I wrapper richiamabili gestiti forniscono anche il supporto per la sottoclasse di tipi Java e per l'override di metodi virtuali su tipi Java. Analogamente, ogni volta che il codice di Android Runtime (ART) desidera richiamare il codice gestito, lo esegue tramite un altro Bridge JNI noto come Android Callable Wrapper (ACW). Questa [architettura](~/android/internals/architecture.md) è illustrata nel diagramma seguente:

[![architettura Bridge Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Una libreria di binding è un assembly che contiene wrapper richiamabili gestiti per i tipi Java. Ad esempio, di seguito è riportato un tipo Java, `MyClass`, di cui si vuole eseguire il wrapping in una libreria di binding:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Dopo aver generato una libreria di binding per il file con **estensione jar** contenente `MyClass`, è possibile crearne un'istanza e chiamarvi i C#metodi da:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Per creare questa libreria di binding, usare il modello Xamarin.Android *Java bindings Library* . Il progetto di associazione risultante crea un assembly .NET con le classi MCW, i file con **estensione jar** e le risorse per i progetti di libreria Android incorporati. È anche possibile creare librerie di binding per l'archivio Android (. AAR) e i progetti della libreria Android di Eclipse. Facendo riferimento all'assembly DLL della libreria di binding risultante, è possibile riutilizzare una libreria Java esistente nel progetto Xamarin.Android.

Quando si fa riferimento ai tipi nella libreria di associazione, è necessario utilizzare lo spazio dei nomi della libreria di associazione. In genere, si aggiunge una direttiva `using` all'inizio dei file C# di origine che corrisponde alla versione dello spazio dei nomi .NET del nome del pacchetto Java. Ad esempio, se il nome del pacchetto Java per il file con estensione **jar** associato è il seguente:

```csharp
com.company.package
```

Quindi, inserire l'istruzione `using` seguente all'inizio dei file di C# origine per accedere ai tipi nel file con **estensione jar** associato:

```csharp
using Com.Company.Package;
```

Quando si associa una libreria Android esistente, è necessario tenere presente quanto segue:

- **Sono presenti dipendenze esterne per la libreria?** &ndash; le dipendenze Java richieste dalla libreria Android devono essere incluse nel progetto Xamarin.Android come **ReferenceJar** o come **EmbeddedReferenceJar**. Tutti gli assembly nativi devono essere aggiunti al progetto di associazione come **EmbeddedNativeLibrary**.  

- **Quale versione dell'API Android è la destinazione della libreria Android?** &ndash; non è possibile effettuare il "downgrade" del livello API Android; Verificare che il progetto di binding Xamarin.Android abbia come destinazione lo stesso livello API (o versione successiva) della libreria Android.

- **Quale versione del JDK è stata usata per compilare la libreria?** è possibile che si verifichino errori di associazione &ndash; se la libreria Android è stata compilata con una versione di JDK diversa da quella usata da Xamarin.Android. Se possibile, ricompilare la libreria Android usando la stessa versione del JDK usata dall'installazione di Xamarin.Android.

## <a name="build-actions"></a>Azioni di compilazione

Quando si crea una libreria di binding, si impostano le *azioni di compilazione* nel file con **estensione jar** o. I file AAR incorporati nel progetto di libreria di binding &ndash; ogni azione di compilazione determina il modo in cui il file con **estensione jar** o. Il file AAR verrà incorporato nella libreria dei binding (o a cui fa riferimento). Nell'elenco seguente sono riepilogate le azioni di compilazione seguenti:

- `EmbeddedJar` &ndash; incorpora il file con **estensione jar** nella dll della libreria di associazioni risultante come risorsa incorporata. Si tratta dell'azione di compilazione più semplice e usata più di frequente. Usare questa opzione quando si vuole che il file con **estensione jar** venga compilato automaticamente nel codice byte e incluso in un pacchetto nella libreria dei binding.

- `InputJar` &ndash; non incorpora il file con **estensione jar** nella libreria dei binding risultante. DLL. Libreria di binding. La DLL avrà una dipendenza da this **. jar** in fase di esecuzione. Utilizzare questa opzione quando non si desidera includere il file con **estensione jar** nella libreria dei binding (ad esempio, per motivi di licenza). Se si usa questa opzione, è necessario assicurarsi che il file input **. jar** sia disponibile nel dispositivo che esegue l'app.

- `LibraryProjectZip` &ndash; incorpora un. File AAR nella libreria dei binding risultante. DLL. Questa operazione è simile a EmbeddedJar, ad eccezione del fatto che è possibile accedere alle risorse (oltre al codice) nell'associazione. File AAR. Utilizzare questa opzione se si desidera incorporare un. AAR nella libreria dei binding.

- `ReferenceJar` &ndash; specifica un file di **riferimento. jar: un**file di **riferimento. jar è un** file con **estensione** jar **. jar o** . I file AAR dipendono da. Questo riferimento **. jar** viene usato solo per soddisfare le dipendenze in fase di compilazione. Quando si usa questa azione di compilazione C# , le associazioni non vengono create per il file Reference **. jar** e non sono incorporate nella libreria dei binding risultante. DLL. Usare questa opzione quando si renderà una libreria di binding per il file di riferimento **. jar** ma non ancora eseguita. Questa azione di compilazione è utile per la creazione di pacchetti di più file con **estensione jar**(e/o. AARs) in più librerie di binding interdipendenti.

- `EmbeddedReferenceJar` &ndash; incorpora un file Reference **. jar** nella libreria dei binding risultante. DLL. Utilizzare questa azione di compilazione quando si desidera creare C# binding per il file di input **. jar** (o. AAR) e tutti i relativi file di riferimento **. jar**nella libreria dei binding.

- `EmbeddedNativeLibrary` &ndash; incorpora un oggetto nativo **. quindi** nell'associazione. Questa azione di compilazione viene utilizzata per i file **. so** necessari per il file con estensione **jar** associato. Potrebbe essere necessario caricare manualmente la libreria **. so** prima di eseguire il codice dalla libreria Java. Questa procedura è descritta di seguito.

Queste azioni di compilazione sono descritte più dettagliatamente nelle guide seguenti.

Inoltre, le azioni di compilazione seguenti consentono di importare la documentazione dell'API Java e di convertirle nella C# documentazione XML:

- `JavaDocJar` viene usato per puntare a Javadoc Archive jar per una libreria Java conforme a uno stile di pacchetto Maven (in genere `FOOBAR-javadoc**.jar**`).
- `JavaDocIndex` viene usato per puntare al file di `index.html` all'interno della documentazione di riferimento dell'API HTML.
- `JavaSourceJar` viene usato per completare `JavaDocJar`, per prima cosa generare JavaDoc dalle origini e quindi trattare i risultati come `JavaDocIndex`, per una libreria Java conforme a uno stile del pacchetto Maven (in genere `FOOBAR-sources**.jar**`).

La documentazione dell'API deve essere il valore predefinito di doclet da Java8, Java7 o java6 SDK (sono tutti formati diversi) o lo stile DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Inclusione di una libreria nativa in un'associazione

Potrebbe essere necessario includere una libreria **. so** in un progetto di binding Xamarin.Android come parte dell'associazione di una libreria Java. Quando viene eseguito il codice Java di cui è stato eseguito il Wrapped, Xamarin.Android non riuscirà a eseguire la chiamata JNI e il messaggio di errore _java. lang. UnsatisfiedLinkError: il metodo nativo non è stato trovato:_ verrà visualizzato in logcat out per l'applicazione.

La correzione per questa operazione consiste nel caricare manualmente la libreria **. so** con una chiamata a `Java.Lang.JavaSystem.LoadLibrary`. Si supponga, ad esempio, che un progetto Xamarin.Android disponga di una libreria condivisa **libpocketsphinx_jni. pertanto** , incluso nel progetto di binding con un'azione di compilazione **EmbeddedNativeLibrary**, il frammento di codice seguente (eseguito prima di utilizzare la libreria condivisa) caricherà la libreria **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adattamento delle API Java a C&eparsl;

Il generatore di binding Xamarin.Android cambierà alcuni idiomi e modelli Java in modo che corrispondano ai modelli .NET. L'elenco seguente descrive come viene eseguito il mapping C#di Java a/.NET:

- I _metodi setter/Getter_ in Java sono _Proprietà_ in .NET.

- I _campi_ in Java sono _Proprietà_ in .NET.

- Le _interfacce listener/listener_ in Java sono _eventi_ in .NET. I parametri dei metodi nelle interfacce di callback verranno rappresentati da una sottoclasse `EventArgs`.

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
