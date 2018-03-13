---
title: Associazione di una. FILE JAR
description: Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una raccolta di associazioni xamarin Java da un Android. File JAR.
ms.topic: article
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: bbbf3fb09edb802f1315977fb14ecfe154b2572f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="binding-a-jar"></a>Associazione di una. FILE JAR

_Questa procedura dettagliata vengono fornite istruzioni dettagliate per la creazione di una raccolta di associazioni xamarin Java da un Android. File JAR._

## <a name="overview"></a>Panoramica

Community di Android sono disponibili molte librerie Java che vuoi usare nell'app. Queste librerie Java vengono spesso inseriti nel pacchetto. Formato di file JAR (Java Archive), ma è possibile creare un pacchetto una. File JAR in un *Java associazioni libreria* in modo che la relativa funzionalità è disponibile per le app xamarin. Lo scopo della libreria di associazioni di Java è affinché le API di. File JAR disponibile per il codice c# tramite wrapper del codice generato automaticamente.

Gli strumenti di Xamarin è possono generare una raccolta di associazioni da uno o più input. File JAR. La raccolta di associazioni (. Assembly DLL) contiene quanto segue: 

-   Il contenuto dell'originale. File JAR.

-   Gestito Callable Wrapper (MCW), che in c# sono tipi che eseguono il wrapping per i tipi Java corrispondente all'interno di. File JAR.

Il codice generato MCW Usa JNI (Java Native Interface) per inoltrare le chiamate API al sottostante. File JAR. È possibile creare raccolte di associazioni per qualsiasi. File JAR destinazione originale per l'utilizzo con Android (si noti che gli strumenti di Xamarin attualmente non supporta l'associazione delle librerie non Android Java). È anche possibile scegliere di compilare la libreria di associazioni senza includere il contenuto del. File JAR, in modo che la DLL presenta una dipendenza di. File JAR in fase di esecuzione.

In questa Guida, verrà scorrere le nozioni di base della creazione di una raccolta di associazioni per un singolo. File JAR. Per illustrare un esempio in cui tutti gli elementi vengono immessi direttamente &ndash; in alcuna personalizzazione o il debug delle associazioni è obbligatorio. 
[Creazione di metadati utilizzando associazioni](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) offre un esempio di uno scenario più avanzato in cui il processo di associazione non è completamente automatico e alcune entità dell'intervento manuale è obbligatorio. Per una panoramica del linguaggio libreria binding in generale (con un esempio di codice di base), vedere [associazione di una libreria di Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Procedura dettagliata

Nella seguente procedura dettagliata verrà creata una raccolta di associazioni per [Picasso](http://square.github.io/picasso/), un Android più diffusi. File JAR che fornisce l'immagine durante il caricamento e la funzionalità di cache. Si utilizzerà la procedura seguente per associare **picasso 2.x.x.jar** per creare un nuovo assembly .NET che è possibile utilizzare in un progetto xamarin: 

1. Creare un nuovo progetto libreria di binding di linguaggio.

2. Aggiungere il. File JAR nel progetto.

3. Impostare l'azione di compilazione appropriati per il. File JAR.

4. Scegliere un framework di destinazione che il. File JAR supporta.

5. Compilare la libreria di associazioni.

Dopo averla creata la raccolta di associazioni, si viene sviluppata un'app Android di piccole dimensioni che illustra la possibilità di chiamare le API nella raccolta di associazioni. In questo esempio si desidera accedere ai metodi di **picasso 2.x.x.jar**:

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

```csharp
After we generate a Bindings Library for **picasso-2.x.x.jar**,
we can call these methods from C#. For example:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Creare la raccolta di associazioni

Prima di iniziare con la procedura seguente, scaricare [picasso 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Innanzitutto, creare un nuovo progetto libreria di associazioni. In Visual Studio per Mac o Visual Studio, creare una nuova soluzione e selezionare il *libreria associazioni Android* modello. (Le schermate in questa procedura dettagliata utilizzano Visual Studio, ma Visual Studio per Mac è molto simile). Denominare la soluzione **JarBinding**: 

[![Creare il progetto di libreria JarBinding](binding-a-jar-images/01-new-bindings-library-sml.png)](binding-a-jar-images/01-new-bindings-library.png#lightbox)

Il modello include un **file JAR** cartella in cui si aggiunge il. JAR(s) al progetto libreria di associazioni. Fare doppio clic su di **file JAR** cartella e selezionare **Aggiungi > elemento esistente**: 

[![Aggiungi elemento esistente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Passare il **picasso 2.x.x.jar** file scaricato in precedenza, selezionarla e fare clic su **Aggiungi**: 

[![Selezionare il file jar e fare clic su Aggiungi](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verificare che il **picasso 2.x.x.jar** file sia stato aggiunto correttamente al progetto: 

[![File JAR aggiunto al progetto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Quando si crea un progetto libreria di binding di linguaggio, è necessario specificare se il. File JAR è incorporato nella raccolta di associazioni o disponibile come pacchetto separato. A tale scopo, specificare uno dei seguenti *le operazioni di compilazione*: 

-   **EmbeddedJar** &ndash; il. File JAR verrà incorporato nella raccolta di associazioni.

-   **InputJar** &ndash; il. File JAR verrà mantenuto separato dalla raccolta di associazioni.

In genere, si utilizza il **EmbeddedJar** azione di compilazione in modo che il. JAR viene automaticamente creato un pacchetto nella raccolta di associazioni. Si tratta dell'opzione più semplice &ndash; bytecode Java nel. File JAR viene convertito in bytecode Dex e incorporati (insieme gestito Callable Wrapper) nei file APK. Se si desidera mantenere il. File JAR separato dalla raccolta di associazioni, è possibile utilizzare il **InputJar** option; tuttavia, è necessario assicurarsi che il. File JAR è disponibile nel dispositivo che esegue l'app. 

Impostare l'azione di compilazione su **EmbeddedJar**: 

[![Selezionare l'azione di compilazione EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Successivamente, aprire il progetto le proprietà per configurare il *Framework di destinazione*. Se il. File JAR utilizza le API di Android, impostare il Framework di destinazione a livello dell'API di. File JAR prevista. In genere, lo sviluppatore del. File JAR indicherà quale livello di API (o livelli) che il. È compatibile con i file JAR. (Per ulteriori informazioni di impostazione del Framework di destinazione e i livelli API Android in generale, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).)

Impostare livello API di destinazione per la raccolta di associazioni (in questo esempio viene usato il livello API 19): 

[![Livello di API di destinazione impostato su API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Infine, compilare la libreria di associazioni. Anche se potrebbero essere visualizzati alcuni messaggi di avviso, il progetto libreria di binding deve compilare correttamente e produrre un output. DLL nel percorso seguente: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>Utilizzo della libreria di associazioni

Per utilizzare questo. DLL in app xamarin, eseguire le operazioni seguenti:

1.  Aggiungere un riferimento alla raccolta di associazioni.

2.  Effettuare chiamate nel. File JAR tramite Callable Wrapper gestiti. 

Nei passaggi seguenti, si creerà un'applicazione minimo che utilizza la raccolta di associazioni per scaricare e visualizzare un'immagine in un `ImageView`; il "pesante" viene eseguito dal codice che si trova nel. File JAR. 

Innanzitutto, creare una nuova app xamarin che utilizza la raccolta di associazioni. La soluzione e scegliere **Aggiungi nuovo progetto**; denominare il nuovo progetto **BindingTest**. Si sta creando questa applicazione nella stessa soluzione come la raccolta di associazioni per semplificare questa procedura dettagliata; Tuttavia l'app che utilizza la raccolta di associazioni invece potrebbe trovarsi in una soluzione diversa: 

[![Aggiungi nuovo progetto BindingTest](binding-a-jar-images/07-add-new-project-sml.png)](binding-a-jar-images/07-add-new-project.png#lightbox)

Fare doppio clic su di **riferimenti** nodo del **BindingTest** del progetto e selezionare **Aggiungi riferimento...** :

[![Diritto di aggiungere riferimenti](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Controllare il **JarBinding** progetto creato in precedenza e fare clic su **OK**:

[![Selezionare il progetto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Aprire il **riferimenti** nodo del **BindingTest** del progetto e verificare che il **JarBinding** riferimento è presente: 

[![JarBinding viene visualizzata sotto i riferimenti](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modificare il **BindingTest** layout (**axml**) in modo che abbia un unico `ImageView`:

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

Aggiungere il seguente `using` istruzione **Mainactivity** &ndash; in questo modo è possibile accedere facilmente ai metodi di Java basata sul `Picasso` classe che si trova nella raccolta di associazioni:

```csharp
using Com.Squareup.Picasso;
```

Modificare il `OnCreate` metodo in modo che utilizzi il `Picasso` classe per caricare un'immagine da un URL e visualizzarli nel `ImageView`: 

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

Compilare ed eseguire il **BindingTest** progetto. L'app verrà avviato e, dopo un breve ritardo (in base alle condizioni di rete), è necessario scaricare e visualizzare un'immagine simile alla schermata riportata di seguito:

[![Schermata di BindingTest esecuzione](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

La procedura è stata completata. È stato associato correttamente una libreria di Java. File JAR e utilizzata nell'app xamarin.
 
 
## <a name="summary"></a>Riepilogo

In questa procedura dettagliata, è creata una raccolta di associazioni per terze parti. File JAR aggiunta la raccolta di associazioni per un'app di test minimo, e poi si esegue l'app per verificare che il codice c# è possibile chiamare codice Java che si trovano nel. File JAR. 



## <a name="related-links"></a>Collegamenti correlati

- [La creazione di una raccolta di associazioni di Java (video)](https://university.xamarin.com/classes#10090)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
