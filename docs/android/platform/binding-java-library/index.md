---
title: Associazione di una libreria Java
description: La community di Android ha molte librerie Java che potresti voler usare nella tua app; questa guida viene illustrato come incorporare librerie Java nell'applicazione Xamarin.Android creando una libreria di associazioni.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027610"
---
# <a name="binding-a-java-library"></a>Associazione di una libreria Java

_La community di Android ha molte librerie Java che potresti voler usare nella tua app; questa guida viene illustrato come incorporare librerie Java nell'applicazione Xamarin.Android creando una libreria di associazioni._

## <a name="overview"></a>Panoramica

L'ecosistema della libreria di terze parti per Android è enorme. Per questo motivo, spesso ha senso usare una libreria Android esistente piuttosto che crearne una nuova. Xamarin.Android offre due modi per utilizzare queste librerie:

- Creare una libreria di *associazioni* che esegue automaticamente il wrapping della libreria con i wrapper di C , in modo da poter richiamare il codice Java tramite chiamate in C.

- Utilizzare *Java Native Interface* (*JNI*) per richiamare direttamente le chiamate nel codice della libreria Java. JNI è un framework di programmazione che consente al codice Java di chiamare ed essere chiamato da applicazioni o librerie native.

In questa guida viene illustrata la prima opzione: come creare una libreria di *associazioni* che esegue il wrapping di una o più librerie Java esistenti in un assembly che è possibile collegare nell'applicazione. Per ulteriori informazioni sull'utilizzo di JNI, vedere [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin.Android implementa le associazioni utilizzando *Managed Callable Wrapper* (*MCW*). MCW è un bridge JNI che viene utilizzato quando il codice gestito deve richiamare il codice Java.MCW is a JNI bridge that is used when managed code needs to invoke Java code. I wrapper richiamabili gestiti forniscono inoltre il supporto per la sottoclasse di tipi Java e per l'override di metodi virtuali su tipi Java. Analogamente, ogni volta che il codice di runtime Android (ART) desidera richiamare codice gestito, lo fa tramite un altro bridge JNI noto come Android Callable Wrapper (ACW). Questa architettura è illustrata nel diagramma seguente:This [architecture](~/android/internals/architecture.md) is illustrated in the following diagram:

[![Architettura del bridge JNI Android](images/architecture.png)](images/architecture.png#lightbox)

Una libreria di associazioni è un assembly contenente Managed Callable Wrapper per i tipi Java. Ad esempio, di seguito `MyClass`è riportato un tipo Java, che si desidera eseguire il wrapping in una libreria di associazioni:For example, here is a Java type, , that we want to wrap in a Bindings Library:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Dopo aver generato una libreria di associazioni `MyClass`per il file **JAR** che contiene , è possibile crearne un'istanza e chiamare i metodi su di essa da C :

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Per creare questa libreria di associazioni, si utilizza il modello Xamarin.Android *Java Bindings Library.* Il progetto di associazione risultante crea un assembly .NET con le classi MCW, i file **JAR** e le risorse per i progetti di libreria Android incorporati in esso. È anche possibile creare librerie di associazioni per Android Archive (. AAR) e progetti Eclipse Android Library. Facendo riferimento all'assembly DLL della libreria Bindings risultante, è possibile riutilizzare una libreria Java esistente nel progetto Xamarin.Android.

Quando si fa riferimento ai tipi nella libreria di associazione, è necessario usare lo spazio dei nomi della libreria di associazione. In genere, `using` si aggiunge una direttiva all'inizio dei file di origine di C , che è la versione dello spazio dei nomi .NET del nome del pacchetto Java. Ad esempio, se il nome del pacchetto Java per il file **JAR** associato è il seguente:

```csharp
com.company.package
```

Quindi è necessario `using` inserire la seguente istruzione all'inizio dei file di origine C , per accedere ai tipi nel file **JAR** associato:

```csharp
using Com.Company.Package;
```

Quando si associa una libreria Android esistente, è necessario tenere presenti i seguenti punti:

- **Esistono dipendenze esterne per la libreria?** &ndash;Tutte le dipendenze Java richieste dalla libreria Android devono essere incluse nel progetto Xamarin.Android come **ReferenceJar** o come **EmbeddedReferenceJar**. Tutti gli assembly nativi devono essere aggiunti al progetto di associazione come **EmbeddedNativeLibrary**.  

- **Quale versione dell'API Android ha la destinazione della libreria Android?** &ndash;Non è possibile eseguire il downgrade del livello dell'API Android; assicurarsi che il progetto di binding Xamarin.Android è destinato allo stesso livello DI API (o superiore) come la libreria Android.

- **Quale versione di JDK è stata utilizzata per compilare la libreria?** &ndash;Binding errors may occur if the Android library was built with a different version of JDK than in use by Xamarin.Android. Se possibile, ricompilare la libreria Android utilizzando la stessa versione di JDK utilizzata dall'installazione di Xamarin.Android.

## <a name="build-actions"></a>Azioni di compilazione

Quando si crea una libreria di associazioni, si impostano le azioni di *compilazione* sul **file JAR** o . File AAR incorporati nel progetto &ndash; Libreria di associazioni ogni azione di compilazione determina il modo in cui il **jar** o . Il file AAR verrà incorporato (o a cui fa riferimento) la libreria di associazioni. Nell'elenco seguente sono riepilogate queste azioni di compilazione:The following list summarizes these build actions:

- `EmbeddedJar`&ndash; Incorpora il **file JAR** nella DLL della libreria di associazioni risultante come risorsa incorporata. Questa è l'azione di compilazione più semplice e più comunemente utilizzata. Utilizzare questa opzione quando si desidera che il **file JAR** venga compilato automaticamente in codice byte e incluso nel pacchetto nella libreria di associazioni.

- `InputJar`&ndash; Non incorpora il **file JAR** nella libreria di associazioni risultante. DLL. La libreria di associazioni . DLL avrà una dipendenza da questo **file JAR** in fase di esecuzione. Utilizzare questa opzione quando non si desidera includere il **file JAR** nella libreria di associazioni (ad esempio, per motivi di licenza). Se usi questa opzione, devi assicurarti che l'input **.jar** sia disponibile nel dispositivo che esegue l'app.

- `LibraryProjectZip`&ndash; Incorpora un file . AAR nella libreria di associazioni risultante . DLL. È simile a EmbeddedJar, ad eccezione del fatto che è possibile accedere alle risorse (così come al codice) nell'oggetto associato . file AAR. Utilizzare questa opzione quando si desidera incorporare un file . AAR nella libreria di associazioni.

- `ReferenceJar`&ndash; Specifica un riferimento **.jar**: un riferimento **.jar** è un **.jar** che uno dei tuoi **.jar** associati o . I file AAR dipendono. Questo riferimento **.jar** viene utilizzato solo per soddisfare le dipendenze in fase di compilazione. Quando si utilizza questa azione di compilazione, le associazioni di C, non vengono create per il riferimento **JAR** e non sono incorporate nella libreria di associazioni risultante. DLL. Utilizzare questa opzione quando si crea una libreria di associazioni per il riferimento **JAR** ma non è ancora stato fatto. Questa azione di compilazione è utile per il pacchetto di più **file jar**(e/o . AAR) in più librerie di associazioni interdipendenti.

- `EmbeddedReferenceJar`&ndash; Incorpora un riferimento **.jar** nella libreria di associazioni risultante. DLL. Utilizzare questa azione di compilazione quando si desidera creare associazioni C , sia per l'input **.jar** (o . AAR) e tutti i relativi **.jar**(s) di riferimento nella libreria di associazioni.

- `EmbeddedNativeLibrary`&ndash; Incorpora un **file con estensione so** nativo nell'associazione. Questa azione di compilazione viene utilizzata per i file **.so** richiesti dal file **JAR** da associare. Potrebbe essere necessario caricare manualmente la libreria **.so** prima di eseguire il codice dalla libreria Java. Questo è descritto di seguito.

Queste azioni di compilazione vengono illustrate in modo più dettagliato nelle guide seguenti.

Inoltre, le seguenti azioni di compilazione vengono utilizzate per facilitare l'importazione della documentazione dell'API Java e convertirle nella documentazione XML di C:

- `JavaDocJar`viene utilizzato per puntare all'archivio Javadoc Jar per una libreria `FOOBAR-javadoc**.jar**`Java conforme a uno stile di pacchetto Maven (in genere ).
- `JavaDocIndex`viene utilizzato per `index.html` puntare al file all'interno della documentazione di riferimento API HTML.
- `JavaSourceJar`viene utilizzato `JavaDocJar`per integrare , per generare prima `JavaDocIndex`JavaDoc da origini e quindi trattare i `FOOBAR-sources**.jar**`risultati come , per una libreria Java conforme a uno stile di pacchetto Maven (in genere ).

La documentazione dell'API deve essere il doclet predefinito di Java8, Java7 o Java6 SDK (sono tutti di formato diverso) o lo stile DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Inclusione di una libreria nativa in un'associazioneIncluding a Native Library in a Binding

Potrebbe essere necessario includere una libreria .così in un progetto di associazione Xamarin.Android come parte dell'associazione di una libreria Java.It may be necessary to include a **.so** library in a Xamarin.Android binding project as part of binding a Java library. Quando viene eseguito il codice Java di cui è stato eseguito il wrapping, Xamarin.Android non riuscirà a effettuare la chiamata JNI e il messaggio di errore _java.lang.UnsatisfiedLinkError: metodo nativo non trovato:_ apparirà nel logcat out per l'applicazione.

La correzione per questo consiste nel caricare manualmente `Java.Lang.JavaSystem.LoadLibrary`la libreria **.così** con una chiamata a . Ad esempio, supponendo che un progetto Xamarin.Android abbia una libreria condivisa **libpocketsphinx_jni.così** inclusa nel progetto di associazione con un'azione di compilazione **Di EmbeddedNativeLibrary**, il frammento seguente (eseguito prima di utilizzare la libreria condivisa) caricherà la libreria **.so:**

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adattamento delle API Java a C&eparsl;

Il generatore di associazioni Xamarin.Android modificherà alcuni idiomi java e modelli in modo che corrispondano ai modelli .NET. Nell'elenco seguente viene descritto il modo in cui Java viene mappato a C .NET:

- _I metodi Setter/Getter_ in Java sono _proprietà_ in .NET.

- _I campi_ in Java sono _proprietà_ in .NET.

- _Le interfacce listener/listener_ in Java sono _eventi_ in .NET. I parametri dei metodi nelle interfacce di `EventArgs` callback saranno rappresentati da una sottoclasse.

- Una _classe annidata Static_ in Java è una classe _Nested_ in .NET.

- Una _classe Inner_ in Java è una classe _Nested_ con un costruttore di istanza in C.

## <a name="binding-scenarios"></a>Scenari di bindingBinding Scenarios

Le guide agli scenari di binding seguenti consentono di associare una libreria Java (o librerie) per l'inserimento nell'app:The following binding scenario guides can help you bind a Java library (or libraries) for incorporation into your app:

- [Associazione di un oggetto . JAR](~/android/platform/binding-java-library/binding-a-jar.md) è una procedura dettagliata per la creazione di librerie di associazioni per i file **JAR.**

- [Associazione di un file . AAR](~/android/platform/binding-java-library/binding-an-aar.md) è una procedura dettagliata per la creazione di librerie di associazioni per . file AAR. Leggere questa procedura dettagliata per informazioni su come associare le librerie di Android Studio.Read this walkthrough to learn how to bind Android Studio libraries.

- [Associazione di un progetto di libreria Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) è una procedura dettagliata per la creazione di librerie di associazione da progetti di libreria Android.Binding an Eclipse Library Project is a walkthrough for creating binding libraries from Android Library Projects. Leggere questa procedura dettagliata per informazioni su come associare i progetti di libreria Android eclipse.Read this walkthrough to learn how to bind Eclipse Android Library Projects.

- [La personalizzazione delle associazioni](~/android/platform/binding-java-library/customizing-bindings/index.md) viene illustrato come apportare modifiche manuali all'associazione per risolvere gli errori di compilazione e modellare l'API risultante in modo che sia più "c'è-like".

- [Risoluzione dei problemi relativi alle associazioni](~/android/platform/binding-java-library/troubleshooting-bindings.md) sono elencati scenari di errore di associazione comuni, vengono illustrate le possibili cause e vengono forniti suggerimenti per la risoluzione di questi errori.

## <a name="related-links"></a>Collegamenti correlati

- [Lavorare con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadati GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Uso di librerie native](~/android/platform/native-libraries.md)
