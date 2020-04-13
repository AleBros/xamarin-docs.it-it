---
title: Associazione di un file JAR
description: In questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di associazioni Java Xamarin.Android da un dispositivo Android. File JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 59969abae739db1d9035ec31738c39a3912f47ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027767"
---
# <a name="binding-a-jar"></a>Associazione di un file JAR

_In questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di associazioni Java Xamarin.Android da un dispositivo Android. File JAR._

## <a name="overview"></a>Panoramica

La community di Android offre molte librerie Java che potresti voler usare nella tua app. Queste librerie Java sono spesso confezionate in . JAR (Java Archive), ma è possibile creare un pacchetto . JAR in una *libreria di associazioni Java* in modo che la sua funzionalità è disponibile per le applicazioni Xamarin.Android. Lo scopo della libreria di associazioni Java è rendere le API nel file . File JAR disponibile per il codice In c'è tramite wrapper di codice generati automaticamente.

Gli strumenti Xamarin possono generare una libreria di associazioni da uno o più input. JAR. La libreria di associazioni (. DLL) contiene quanto segue: 

- Il contenuto dell'originale . JAR.i.

- Managed Callable Wrapper (MCW), che sono i tipi di C, che eseguono il wrapping dei tipi Java corrispondenti all'interno di . JAR.i.

Il codice MCW generato utilizza JNI (Java Native Interface) per inoltrare le chiamate API all'oggetto sottostante. File JAR. È possibile creare librerie di associazioni per qualsiasi file . Jar file che è stato originariamente mirato per essere utilizzato con Android (si noti che gli strumenti Xamarin attualmente non supporta l'associazione di librerie Java non Android). È inoltre possibile scegliere di compilare la libreria di associazioni senza includere il contenuto del file . JAR in modo che la DLL abbia una dipendenza dal file . JAR in fase di esecuzione.

In questa guida verranno fornite le nozioni di base per la creazione di una libreria di associazioni per un singolo oggetto . File JAR. Illustreremo con un esempio &ndash; in cui tutto va bene che è, dove non è necessaria alcuna personalizzazione o debug delle associazioni. 
[La creazione di associazioni tramite metadati](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) offre un esempio di uno scenario più avanzato in cui il processo di associazione non è completamente automatico ed è necessaria una certa quantità di intervento manuale. Per una panoramica dell'associazione di librerie Java in generale (con un esempio di codice di base), consultate [Associazione](~/android/platform/binding-java-library/index.md)di una libreria Java . 

## <a name="walkthrough"></a>Procedura dettagliata

Nella procedura dettagliata seguente verrà creata una libreria di associazioni per [Picasso](https://square.github.io/picasso/), un dispositivo Android popolare. JAR che fornisce funzionalità di caricamento e memorizzazione nella cache delle immagini. Utilizzeremo i seguenti passaggi per legare **picasso-2.x.x.jar** per creare un nuovo assembly .NET che possiamo usare in un progetto Xamarin.Android: 

1. Creare un nuovo progetto Libreria di associazioni Java.Create a new Java Bindings Library project.

2. Aggiungere il file . JAR al progetto.

3. Impostare l'azione di compilazione appropriata per il file . File JAR.

4. Scegliere un framework di destinazione che il file . JAR supporta.

5. Compilare la libreria di associazioni.

Dopo aver creato la libreria di associazioni, svilupperemo una piccola app Android che dimostra la nostra capacità di chiamare le API nella libreria di associazioni. In questo esempio, vogliamo accedere ai metodi di **picasso-2.x.x.jar**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}
```

Dopo aver generato una libreria di associazioni per **picasso-2.x.x.jar**, è possibile chiamare questi metodi da C . Ad esempio:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>Creazione della libreria di associazioniCreating the Bindings Library

Prima di iniziare con i passaggi seguenti, scaricare [picasso-2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Creare innanzitutto un nuovo progetto Libreria di associazioni. In Visual Studio per Mac o Visual Studio creare una nuova soluzione e selezionare il modello Libreria di associazioni Android.In Visual Studio for Mac or Visual Studio, create a new Solution and select the *Android Bindings Library* template. (Le schermate in questa procedura dettagliata usano Visual Studio, ma Visual Studio per Mac è molto simile.) Denominare la soluzione **JarBinding**: 

[![Creare un progetto di libreria JarBindingCreate JarBinding library project](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

Il modello include una cartella **Jars** in cui si aggiunge il file . JAR(s) al progetto Libreria di associazioni. Fare clic con il pulsante destro del mouse sulla cartella **Jars** e selezionare **Aggiungi > elemento esistente:** 

[![Aggiungi elemento esistente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Passare al file **picasso-2.x.x.jar** scaricato in precedenza, selezionarlo e fare clic su **Aggiungi**: 

[![Selezionare il file jar e fare clic su Aggiungi](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verificare che il file **picasso-2.x.x.jar** sia stato aggiunto correttamente al progetto: 

[![Jar aggiunto al progetto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Quando si crea un progetto di libreria di associazioni Java, è necessario specificare se il file . JAR deve essere incorporato nella libreria di associazioni o incluso nel pacchetto separatamente. A tale scopo, specificare una delle seguenti azioni di *compilazione:* 

- **EmbeddedJar** &ndash; il file . JAR verrà incorporato nella libreria di associazioni.

- **InputJar** &ndash; il file . JAR verrà mantenuto separato dalla libreria di associazioni.

In genere, si utilizza l'azione di compilazione **EmbeddedJar** in modo che il file . JAR viene automaticamente incluso nella libreria di associazioni. Questa è l'opzione &ndash; più semplice Bytecode Java nel file . JAR viene convertito in bytecode Dex ed è incorporato (insieme ai Managed Callable Wrapper) nell'APK. Se si desidera mantenere il file . JAR separato dalla libreria di associazioni, è possibile utilizzare l'opzione **InputJar;** tuttavia, è necessario assicurarsi che il file . Il file JAR è disponibile nel dispositivo che esegue l'app. 

Impostare l'azione di compilazione **su EmbeddedJar**: 

[![Selezionare l'azione di compilazione EmbeddedJarSelect EmbeddedJar build action](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Aprire quindi le proprietà del progetto per configurare il Framework di *destinazione*. Se il file . JAR utilizza tutte le API Android, impostare il Framework di destinazione per il livello di API che il file . JAR si aspetta. In genere, lo sviluppatore del file . JAR indicherà quale livello API (o livelli) JAR è compatibile con. Per altre informazioni sull'impostazione Framework di destinazione e sui livelli API Android in generale, vedere [Informazioni sui livelli API Android.](~/android/app-fundamentals/android-api-levels.md)

Impostare il livello di API di destinazione per la libreria di associazioni (in questo esempio, si usa il livello API 19):Set the target API level for your Bindings Library (in this example, we are using API level 19): 

[![Livello API di destinazione impostato su API 19Target API level set to API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

Infine, compilare la libreria di associazioni. Anche se alcuni messaggi di avviso possono essere visualizzati, il progetto Libreria di associazioni deve essere compilato correttamente e produrre un output . DLL nel seguente percorso: **JarBinding/bin/Debug/JarBinding.dll**

### <a name="using-the-bindings-library"></a>Utilizzo della libreria di associazioniUsing the Bindings Library

Per consumare questo file . DLL nell'app Xamarin.Android, procedere come segue:

1. Aggiungere un riferimento alla libreria di associazioni.

2. Effettuare chiamate nel file . JAR tramite i wrapper chiamabili gestiti. 

Nei passaggi seguenti verrà creata un'app minima che usa la libreria di `ImageView`associazioni per scaricare e visualizzare un'immagine in un'istruzione ; il "lifting pesante" è fatto dal codice che risiede nel . File JAR. 

Innanzitutto, creare una nuova app Xamarin.Android che utilizza la libreria di associazioni. Fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Aggiungi nuovo progetto**; assegnare al nuovo progetto il nome **BindingTest**. Stiamo creando questa app nella stessa soluzione della libreria di associazioni per semplificare questa procedura dettagliata; Tuttavia, l'app che usa la libreria di associazioni potrebbe, invece, risiedere in una soluzione diversa:However, the app that consumes the Bindings Library could, instead, reside in a different Solution: 

[![Aggiungere un nuovo progetto BindingTestAdd new BindingTest project](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Fare clic con il pulsante destro del mouse sul nodo **Riferimenti** del progetto **BindingTest** e selezionare **Aggiungi riferimento...**:

[![Aggiungi a destra Riferimento](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Controllare il progetto **JarBinding** creato in precedenza e fare clic su **OK**:

[![Selezionare il progetto JarBindingSelect JarBinding project](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Aprire il nodo **Riferimenti** del progetto **BindingTest** e verificare che il riferimento **JarBinding** sia presente: 

[![JarBinding viene visualizzato in Riferimenti](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modificare il layout **BindingTest** (**Main.axml**) `ImageView`in modo che abbia un solo :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

Aggiungere `using` l'istruzione seguente a **MainActivity.cs** &ndash; in questo modo è `Picasso` possibile accedere facilmente ai metodi della classe basata su Java che risiede nella libreria di associazioni:

```csharp
using Com.Squareup.Picasso;
```

Modificare `OnCreate` il metodo in `Picasso` modo che utilizzi la classe per caricare `ImageView`un'immagine da un URL e visualizzarla nel : 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

Compilare ed eseguire il progetto **BindingTest.Compile** and run the BindingTest project. L'applicazione verrà avviato, e dopo un breve ritardo (a seconda delle condizioni di rete), dovrebbe scaricare e visualizzare un'immagine simile alla seguente schermata:

[![Schermata dell'esecuzione di BindingTest](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Congratulazioni! Una libreria Java è stata associata correttamente. JAR e usato nella vostra applicazione Xamarin.Android.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata creata una libreria di associazioni per una terza parte. JAR, aggiunto la libreria di associazioni a un'app di test minima e quindi ha eseguito l'app per verificare che il codice in linguaggio C' possa chiamare il codice Java che risieda nel file . File JAR. 

## <a name="related-links"></a>Collegamenti correlati

- [Compilazione di una libreria di associazioni Java (video)Building a Java Bindings Library (video)](https://university.xamarin.com/classes#10090)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
