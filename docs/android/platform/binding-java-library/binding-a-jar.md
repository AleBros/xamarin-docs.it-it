---
title: Associazione di un file JAR
description: Questa procedura dettagliata include istruzioni dettagliate per la creazione di una libreria di binding Java Xamarin.Android da un dispositivo Android. File JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 59969abae739db1d9035ec31738c39a3912f47ae
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027767"
---
# <a name="binding-a-jar"></a>Associazione di un file JAR

_Questa procedura dettagliata include istruzioni dettagliate per la creazione di una libreria di binding Java Xamarin.Android da un dispositivo Android. File JAR._

## <a name="overview"></a>Panoramica

La community Android offre molte librerie Java che è possibile usare nell'app. Queste librerie Java sono spesso impacchettate in. Formato JAR (Java Archive), ma è possibile creare il pacchetto a. JAR it in una *libreria di binding Java* in modo che la relativa funzionalità sia disponibile per le app Xamarin.Android. Lo scopo della libreria dei binding Java è quello di rendere le API in. File JAR disponibile per C# il codice tramite wrapper di codice generati automaticamente.

Gli strumenti Xamarin possono generare una libreria di binding da uno o più input. File JAR. Libreria di binding (. Assembly DLL) contiene gli elementi seguenti: 

- Contenuto dell'oggetto originale. File JAR.

- Gestiti Callable Wrapper (MCW), che sono C# tipi che eseguono il wrapping dei tipi Java corrispondenti all'interno di. File JAR.

Il codice MCW generato USA JNI (Java Native Interface) per inviare le chiamate API all'oggetto sottostante. File JAR. È possibile creare librerie di binding per qualsiasi. File JAR originariamente destinato a essere usato con Android (si noti che gli strumenti Xamarin attualmente non supportano l'associazione di librerie Java non Android). È inoltre possibile scegliere di compilare la libreria dei binding senza includere il contenuto di. File JAR in modo che la DLL abbia una dipendenza da. JAR in fase di esecuzione.

In questa guida verranno illustrate le nozioni di base per la creazione di una libreria di binding per un singolo. File JAR. Verrà illustrato un esempio in cui tutto va bene &ndash; ovvero, in cui non è richiesta alcuna personalizzazione o debug delle associazioni. 
La [creazione di associazioni usando i metadati](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) offre un esempio di scenario più avanzato in cui il processo di associazione non è completamente automatico e è necessaria una certa quantità di intervento manuale. Per una panoramica dell'associazione di librerie Java in generale (con un esempio di codice di base), vedere [binding di una libreria Java](~/android/platform/binding-java-library/index.md). 

## <a name="walkthrough"></a>Procedura dettagliata

Nella procedura dettagliata seguente verrà creata una libreria di binding per [Picasso](https://square.github.io/picasso/), un dispositivo Android molto diffuso. JAR che fornisce funzionalità di caricamento di immagini e memorizzazione nella cache. Usare i passaggi seguenti per associare **Picasso-2. x.x. x. jar** per creare un nuovo assembly .NET che è possibile usare in un progetto Xamarin.Android: 

1. Creare un nuovo progetto di libreria di binding Java.

2. Aggiungere il. File JAR nel progetto.

3. Impostare l'azione di compilazione appropriata per l'oggetto. File JAR.

4. Scegliere un Framework di destinazione per il. JAR supporta.

5. Compilare la libreria dei binding.

Una volta creata la libreria bindings, verrà sviluppata una piccola app per Android che dimostra la possibilità di chiamare le API nella libreria Bindings. In questo esempio si vuole accedere ai metodi di **Picasso-2. x.x. x. jar**:

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

Dopo aver generato una libreria di binding per **Picasso-2. x.x. x. jar**, è possibile chiamare questi metodi da C#. Esempio:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>Creazione della libreria bindings

Prima di iniziare con la procedura seguente, scaricare [Picasso-2. x.x. x. jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Per prima cosa, creare un nuovo progetto di libreria di binding. In Visual Studio per Mac o Visual Studio creare una nuova soluzione e selezionare il modello *libreria bindings Android* . Gli screenshot di questa procedura dettagliata usano Visual Studio, ma Visual Studio per Mac è molto simile. Assegnare alla soluzione il nome **JarBinding**: 

[![creare un progetto di libreria JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

Il modello include una cartella **jar** in cui si aggiunge il. JAR (s) per il progetto di libreria dei binding. Fare clic con il pulsante destro del mouse sulla cartella **jar** e scegliere **Aggiungi > elemento esistente**: 

[![Aggiungi elemento esistente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Passare al file **Picasso-2. x. x. jar** scaricato in precedenza, selezionarlo e fare clic su **Aggiungi**: 

[![selezionare il file jar e fare clic su Aggiungi](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verificare che il file **Picasso-2. x.x. x. jar** sia stato aggiunto correttamente al progetto: 

[Jar![aggiunto al progetto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Quando si crea un progetto di libreria di binding Java, è necessario specificare se. JAR deve essere incorporato nella libreria bindings o essere incluso in un pacchetto separatamente. A tale scopo, è necessario specificare una delle *azioni di compilazione*seguenti: 

- **EmbeddedJar** &ndash;. JAR sarà incorporato nella libreria Bindings.

- **InputJar** &ndash;. Il file JAR verrà mantenuto separato dalla libreria dei binding.

In genere, si usa l'azione di compilazione **EmbeddedJar** in modo che. JAR viene automaticamente incluso nella libreria Bindings. Questa è l'opzione più semplice &ndash; bytecode Java in. JAR viene convertito in bytecode DEX ed è incorporato (insieme ai wrapper richiamabili gestiti) nell'APK. Se si vuole usare. JAR separato dalla libreria Bindings. è possibile usare l'opzione **InputJar** . Tuttavia, è necessario assicurarsi che. Il file JAR è disponibile nel dispositivo che esegue l'app. 

Impostare l'azione di compilazione su **EmbeddedJar**: 

[![selezionare l'azione di compilazione EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Successivamente, aprire le proprietà del progetto per configurare il *Framework di destinazione*. Se il. JAR usa qualsiasi API Android, impostare il Framework di destinazione sul livello API. JAR prevede. In genere, lo sviluppatore del. Il file JAR indicherà il livello o i livelli API di. JAR è compatibile con. Per ulteriori informazioni sull'impostazione del Framework di destinazione e sui livelli dell'API Android in generale, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

Impostare il livello API di destinazione per la libreria dei binding (in questo esempio si usa il livello API 19): 

[![livello API di destinazione impostato su API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

Infine, compilare la libreria Bindings. Sebbene sia possibile visualizzare alcuni messaggi di avviso, il progetto di libreria di associazioni dovrebbe compilarsi correttamente e produrre un output. DLL nel percorso seguente: **JarBinding/bin/debug/JarBinding. dll**

### <a name="using-the-bindings-library"></a>Uso della libreria bindings

Per utilizzare questo. DLL nell'app Xamarin.Android, seguire questa procedura:

1. Aggiungere un riferimento alla libreria dei binding.

2. Effettuare chiamate a. JAR tramite i wrapper richiamabili gestiti. 

Nei passaggi seguenti verrà creata un'app minima che usa la libreria Bindings per scaricare e visualizzare un'immagine in un `ImageView`; il "sollevamento intensivo" viene eseguito dal codice che risiede in. File JAR. 

Per prima cosa, creare una nuova app Xamarin.Android che utilizza la libreria Bindings. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi nuovo progetto**. assegnare al nuovo progetto il nome **BindingTest**. Questa app viene creata nella stessa soluzione della libreria Bindings per semplificare questa procedura dettagliata. Tuttavia, l'app che utilizza la libreria dei binding può invece risiedere in una soluzione diversa: 

[![aggiungere un nuovo progetto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Fare clic con il pulsante destro del mouse sul nodo **riferimenti** del progetto **BindingTest** e scegliere **Aggiungi riferimento...** :

[![Aggiungi riferimento a destra](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Controllare il progetto **JarBinding** creato in precedenza e fare clic su **OK**:

[![selezionare il progetto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Aprire il nodo **riferimenti** del progetto **BindingTest** e verificare che sia presente il riferimento **JarBinding** : 

[![JarBinding viene visualizzato sotto riferimenti](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modificare il layout di **BindingTest** (**Main. aXML**) in modo che disponga di un solo `ImageView`:

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

Aggiungere la seguente istruzione `using` a **MainActivity.cs** &ndash; in questo modo è possibile accedere facilmente ai metodi della classe `Picasso` basata su Java che risiede nella libreria bindings:

```csharp
using Com.Squareup.Picasso;
```

Modificare il metodo `OnCreate` in modo che usi la classe `Picasso` per caricare un'immagine da un URL e visualizzarla nel `ImageView`: 

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

Compilare ed eseguire il progetto **BindingTest** . L'app verrà avviata e dopo un breve ritardo (a seconda delle condizioni della rete) dovrebbe scaricare e visualizzare un'immagine simile alla schermata seguente:

[![screenshot di BindingTest in esecuzione](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

La procedura è stata completata. La libreria Java è stata associata correttamente. JAR e usarlo nell'app Xamarin.Android.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata creata una libreria Bindings per un di terze parti. File JAR, è stata aggiunta la libreria bindings a un'app di test minima e quindi l'app è stata eseguita C# per verificare che il codice sia in grado di chiamare il codice Java che risiede in. File JAR. 

## <a name="related-links"></a>Collegamenti correlati

- [Compilazione di una libreria di binding Java (video)](https://university.xamarin.com/classes#10090)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
