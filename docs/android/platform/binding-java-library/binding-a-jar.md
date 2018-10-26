---
title: Associazione di una. FILE JAR
description: Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di binding di xamarin. Android Java da Android. File JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 3c84b29807fd4a181ed867095645005bf9ba4df0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120008"
---
# <a name="binding-a-jar"></a>Associazione di una. FILE JAR

_Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una libreria di binding di xamarin. Android Java da Android. File JAR._

## <a name="overview"></a>Panoramica

La community di Android sono disponibili molte librerie Java che vuoi usare nell'app. Queste librerie Java sono spesso inseriti in pacchetti. Formato di file JAR (Java Archive), ma è possibile creare un pacchetto una. File JAR in una *libreria di binding Java* in modo che la relativa funzionalità è disponibile per le app xamarin. Android. Lo scopo della libreria di binding di linguaggio consiste nel rendere le API nel. File con estensione JAR disponibili per C# codice tramite wrapper del codice generato automaticamente.

Gli strumenti di Xamarin è possono generare una libreria di binding da uno o più input. File con estensione JAR. La libreria di binding (. Assembly DLL) contiene quanto segue: 

-   Il contenuto dell'originale. File con estensione JAR.

-   Gestito Callable Wrapper (MCW), che sono C# i tipi che eseguono il wrapping per i tipi Java corrispondente all'interno di. File con estensione JAR.

Il codice generato in MCW Usa JNI (Java Native Interface) per inoltrare le chiamate all'API sottostante. File JAR. È possibile creare librerie di associazioni per qualsiasi. File JAR destinazione originale per l'uso con Android (si noti che gli strumenti di Xamarin non supporta attualmente l'associazione delle librerie non Android Java). È anche possibile scegliere di compilare la libreria di binding senza includere il contenuto della. File JAR in modo che la DLL presenta una dipendenza di. File JAR in fase di esecuzione.

In questa Guida, si sarà scorrere le nozioni di base della creazione di una libreria di binding per una singola. File JAR. Verrà illustrata con un esempio in cui tutto va a destra &ndash; in cui nessuna personalizzazione o il debug delle associazioni è obbligatorio. 
[Creazione di metadati utilizzando associazioni](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) offre un esempio di uno scenario più avanzato in cui il processo di associazione non è completamente automatico e alcune entità dell'intervento manuale è obbligatorio. Per una panoramica di Java della libreria di associazione in generale (con un esempio di codice di base), vedere [associazione di una libreria Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Procedura dettagliata

Nella seguente procedura dettagliata, si creerà una libreria di binding per [Picasso](http://square.github.io/picasso/), un Android più diffusi. File JAR che fornisce il caricamento e la funzionalità di memorizzazione nella cache di immagini. Si userà la procedura seguente per associare **picasso 2.x.x.jar** per creare un nuovo assembly .NET che è possibile usare in un progetto xamarin. Android: 

1. Creare un nuovo progetto di libreria di binding Java.

2. Aggiungere il. File JAR al progetto.

3. Impostare l'azione di compilazione appropriata per il. File JAR.

4. Scegliere un framework di destinazione che il. Supporta file JAR.

5. Compilare la libreria di binding.

Dopo aver creato la libreria di binding, sviluppiamo sarà una piccola app di Android che illustra la possibilità di chiamare le API nella libreria di binding. In questo esempio si vuole accedere ai metodi di **picasso 2.x.x.jar**:

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

Dopo che si genera una libreria di binding per **picasso 2.x.x.jar**, è possibile chiamare questi metodi da C#. Ad esempio:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Creare la libreria di binding

Prima di iniziare con la procedura seguente, scaricare [picasso 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

In primo luogo, creare un nuovo progetto di libreria di binding. In Visual Studio per Mac o Visual Studio, creare una nuova soluzione e selezionare il *libreria di binding Android* modello. (Le schermate contenute in questa procedura dettagliata usano Visual Studio, ma Visual Studio per Mac è molto simile). Denominare la soluzione **JarBinding**: 

[![Creare il progetto di libreria JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

Il modello include un **Jars** cartella in cui si aggiungono i. JAR(s) al progetto libreria di binding. Fare doppio clic il **Jars** cartella e selezionare **Aggiungi > elemento esistente**: 

[![Aggiungi elemento esistente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Passare il **picasso 2.x.x.jar** file scaricato in precedenza, selezionarla e fare clic su **Add**: 

[![Selezionare il file con estensione jar e fare clic su Aggiungi](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verificare che il **picasso 2.x.x.jar** file è stato aggiunto al progetto: 

[![Con estensione jar aggiunti al progetto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Quando si crea un progetto di libreria di binding Java, è necessario specificare se il. File JAR si trova nella raccolta di associazioni incorporati o disponibile come pacchetto separato. A tale scopo, specificare uno dei seguenti *azioni di compilazione*: 

-   **EmbeddedJar** &ndash; il. File JAR verrà incorporato nella raccolta di associazioni.

-   **InputJar** &ndash; il. File JAR verrà mantenuto separato dalla libreria di binding.

In genere, si usa la **EmbeddedJar** azione di compilazione in modo che il. Viene automaticamente incluso nel pacchetto JAR nella libreria di binding. Si tratta dell'opzione più semplice &ndash; bytecode Java nel. File JAR viene convertito in Dex bytecode e viene incorporata (insieme ad gestiti Callable Wrapper) nel pacchetto APK. Se si desidera mantenere il. JAR separato dalla libreria di binding, è possibile usare la **InputJar** option; tuttavia, è necessario assicurarsi che il. File con estensione JAR è disponibili sul dispositivo che esegue l'app. 

Impostare l'azione di compilazione **EmbeddedJar**: 

[![Selezionare l'azione di compilazione EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Successivamente, aprire il progetto di proprietà per configurare il *Framework di destinazione*. Se il. File JAR Usa qualsiasi API Android, impostare il Framework di destinazione per il livello API di. File JAR si aspetta. In genere, lo sviluppatore della. File con estensione JAR indicherà quale livello API (o livelli) che il. È compatibile con i file JAR. (Per altre informazioni sui Framework di destinazione e i livelli API Android in generale, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).)

Impostazione livello API di destinazione per la libreria di binding (in questo esempio, utilizziamo API livello 19): 

[![Livello API di destinazione impostato su API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Infine, compilare la libreria di binding. Anche se potrebbero essere visualizzati alcuni messaggi di avviso, il progetto libreria di binding deve compilare correttamente e produrre un output. DLL nel percorso seguente: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>Uso della libreria di binding

Per utilizzare questa soluzione. DLL nell'app xamarin. Android, eseguire le operazioni seguenti:

1.  Aggiungere un riferimento alla libreria di binding.

2.  Effettuare chiamate nel. JAR tramite Callable Wrapper gestiti. 

In questa procedura, si creerà un'app minima che usa la libreria di binding per scaricare e visualizzare un'immagine in un `ImageView`; il "pesante" viene eseguito dal codice che si trova nel. File JAR. 

Innanzitutto, creare una nuova app xamarin. Android che usa la libreria di binding. La soluzione e scegliere **Aggiungi nuovo progetto**; denominare il nuovo progetto **BindingTest**. Stiamo creando l'app nella stessa soluzione come libreria di binding per semplificare questa procedura dettagliata; Tuttavia, le app che usa la libreria di binding possibile, invece, si trovano in una soluzione diversa: 

[![Aggiungi nuovo progetto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Fare doppio clic sui **riferimenti** nodo del **BindingTest** del progetto e selezionare **Aggiungi riferimento...** :

[![A destra aggiungere riferimento](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Verificare i **JarBinding** progetto creato in precedenza e fare clic su **OK**:

[![Selezionare il progetto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Aprire il **riferimenti** nodo delle **BindingTest** del progetto e verificare che il **JarBinding** riferimento è presente: 

[![JarBinding viene visualizzato in riferimenti](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modificare il **BindingTest** layout (**Main. axml**) in modo che includa un unico `ImageView`:

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

Aggiungere il codice seguente `using` dell'istruzione **MainActivity.cs** &ndash; in questo modo è possibile accedere facilmente ai metodi del basata su Java `Picasso` classe che si trova nella libreria di binding:

```csharp
using Com.Squareup.Picasso;
```

Modificare il `OnCreate` metodo in modo che utilizzi il `Picasso` classe per caricare un'immagine da un URL e lo visualizza nel `ImageView`: 

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

Compilare ed eseguire la **BindingTest** progetto. L'app verrà avviato e, dopo un breve ritardo (in base alle condizioni di rete), è necessario scaricare e visualizzare un'immagine simile allo screenshot seguente:

[![Schermata di BindingTest esecuzione](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

La procedura è stata completata. È stata associata correttamente una libreria Java. File JAR e quindi averla usata nell'app xamarin. Android.
 
 
## <a name="summary"></a>Riepilogo

In questa procedura dettagliata, viene creata una libreria di binding per una terza parte. JAR file, aggiungere la libreria di binding a un'app di test minima e poi si esegue l'app e verificare che il C# codice può chiamare codice Java che si trovano nel. File JAR. 



## <a name="related-links"></a>Collegamenti correlati

- [Creazione di una libreria di binding Java (video)](https://university.xamarin.com/classes#10090)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
