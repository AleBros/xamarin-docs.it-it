---
title: Dimensioni del pacchetto dell'applicazione
description: Questo articolo esamina le parti costituenti di un pacchetto dell'applicazione Xamarin.Android e le strategie associate che possono essere usate per una distribuzione efficiente del pacchetto durante le fasi di debug e rilascio dello sviluppo.
ms.topic: article
ms.prod: xamarin
ms.assetid: 8D70CDDD-3D3C-9949-8045-AB8F93D18E74
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0fff4de7420bceda8c15ae33b03886eb6b332aeb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="application-package-size"></a>Dimensioni del pacchetto dell'applicazione

_Questo articolo esamina le parti costituenti di un pacchetto dell'applicazione Xamarin.Android e le strategie associate che possono essere usate per una distribuzione efficiente del pacchetto durante le fasi di debug e rilascio dello sviluppo._


## <a name="overview"></a>Panoramica

Xamarin.Android usa svariati meccanismi per ridurre al minimo le dimensioni del pacchetto, mantenendo al contempo l'efficienza del processo di distribuzione di debug e rilascio. In questo articolo viene esaminato il flusso di lavoro di distribuzione di rilascio e debug e viene illustrato come la piattaforma Xamarin.Android assicura che vengano compilati e rilasciati pacchetti dell'applicazione di piccole dimensioni.


## <a name="release-packages"></a>Rilasciare i pacchetti

Per fornire un'applicazione completamente indipendente, il pacchetto deve includere l'applicazione, le librerie associate, il contenuto, il runtime Mono e gli assembly di librerie di classi base necessari. Se ad esempio si usa il modello "Hello World" predefinito, il contenuto di una compilazione completa del pacchetto sarà simile al seguente:

[ ![Dimensioni del pacchetto prima del linker](app-package-size-images/hello-world-package-size-before-linker.png)](app-package-size-images/hello-world-package-size-before-linker.png)

15,8 MB è una dimensione troppo grande per il download. Il problema è dato dalle librerie di classi base, perché includono mscorlib, System e Mono.Android, che forniscono molti componenti necessari per eseguire l'applicazione, ma anche funzionalità che forse non vengono usate nell'applicazione, quindi potrebbe essere preferibile escludere questi componenti.

Quando si compila un'applicazione per la distribuzione, viene eseguito un processo, noto come collegamento, che esamina l'applicazione e rimuove il codice non usato direttamente. Questo processo è simile alla funzionalità fornita da [Garbage Collection](~/android/internals/garbage-collection.md) per la memoria con allocazione dell'heap, ma invece di operare sugli oggetti, il collegamento agisce sul codice. In System.dll, ad esempio, è presente un intero spazio dei nomi per inviare e ricevere posta elettronica, ma, se l'applicazione non usa questa funzionalità, tale codice spreca spazio inutilmente. Dopo aver eseguito il linker sull'applicazione Hello World, il pacchetto è ora simile al seguente:

[ ![Dimensioni del pacchetto dopo il linker](app-package-size-images/hello-world-package-size-after-linker.png)](app-package-size-images/hello-world-package-size-after-linker.png)

Come si può vedere, viene rimossa una quantità significativa di librerie di classi base non è in uso. Si noti che le dimensioni finali delle librerie di classi base dipendono da ciò che l'applicazione usa effettivamente. Se ad esempio si esamina un'applicazione di esempio più significativa, denominata ApiDemo, si noterà che le dimensioni del componente della libreria di classi base sono aumentate perché ApiDemo usa la libreria di classi base più di Hello, World:

![Dimensioni del pacchetto ApiDemo dopo il collegamento](app-package-size-images/api-demo-package-size-after-linker.png)

Come illustrato qui, le dimensioni del pacchetto dell'applicazione saranno in genere maggiori di circa 2,9 MB rispetto a quelle dell'applicazione e delle dipendenze.


## <a name="debug-packages"></a>Eseguire il debug dei pacchetti

Le compilazioni di debug vengono gestite in modo leggermente diverso. Quando si esegue più volte la distribuzione in un dispositivo, un'applicazione deve essere il più veloce possibile, quindi nei pacchetti di debug si ottimizza la velocità di distribuzione più che le dimensioni.

In Android la copia e l'installazione di un pacchetto sono relativamente lente, quindi è preferibile che le dimensioni del pacchetto siano più piccole possibile. Come già indicato in precedenza, uno dei modi per ridurre al minimo le dimensioni del pacchetto è usare il linker. Il collegamento è tuttavia lento e in genere è necessario distribuire solo le parti dell'applicazione che sono state modificate dopo l'ultima distribuzione. A tale scopo, l'applicazione viene separata dai componenti principali di Xamarin.Android.

La prima volta che si esegue il debug nel dispositivo, vengono copiati due pacchetti di grandi dimensioni denominati *Runtime condiviso* e *Piattaforma condivisa*. Runtime condiviso contiene il runtime Mono e la libreria di classi base, mentre Piattaforma condivisa contiene gli assembly specifici del livello API di Android:

[ ![Dimensioni del pacchetto Runtime condiviso](app-package-size-images/shared-runtime-package-size.png)](app-package-size-images/shared-runtime-package-size.png)

La copia di questi componenti principali viene eseguita solo una volta perché richiede molto tempo, ma consente alle applicazioni successive in esecuzione in modalità di debug di utilizzarli. Viene infine copiata l'applicazione effettiva, che è piccola e veloce:

![L'applicazione effettiva è di piccole dimensioni](app-package-size-images/hello-world-debug-application-no-link.png)

### <a name="fast-assembly-deployment"></a>Distribuzione rapida di assembly

L'opzione di compilazione *Distribuzione rapida di assembly* può essere usata per ridurre ulteriormente le dimensioni del pacchetto di installazione di debug non includendo gli assembly nel pacchetto dell'applicazione, installando gli assembly direttamente nel dispositivo una sola volta e copiando solo i file che sono stati modificati dopo l'ultima distribuzione.

Per abilitare *Distribuzione rapida di assembly*, seguire questa procedura:

1.  Fare clic con il pulsante destro del mouse sul progetto Android in Esplora soluzioni e scegliere **Opzioni**.

2.  Nella finestra di dialogo Opzioni progetto selezionare **Compilazione Android**:  

    ![Compilazione Android in Opzioni progetto](app-package-size-images/fastdev0.png)

3.  Selezionare le caselle di controllo **Usa runtime di Mono condiviso** e **Distribuzione rapida di assembly**:  

    ![Caselle di controllo selezionate nella scheda Creazione pacchetto](app-package-size-images/fastdev.png)

4.  Fare clic su **OK** per salvare le modifiche e chiudere la finestra di dialogo Opzioni progetto.


La prossima volta che l'applicazione verrà compilata per il debug, gli assembly verranno installati direttamente nel dispositivo (se non lo sono già) e un pacchetto dell'applicazione di dimensioni inferiori (che non include gli assembly) verrà installato nel dispositivo. In questo modo le modifiche apportate all'applicazione saranno operative per il test più velocemente.

Anche se la prima distribuzione del runtime condiviso e della piattaforma condivisa richiede tempo, ogni volta che si apporta una modifica all'applicazione, è possibile distribuire la nuova versione rapidamente e senza problemi e poter quindi avere un ciclo veloce di modifica/distribuzione/esecuzione.


## <a name="summary"></a>Riepilogo

In questo articolo sono stati esaminati i facet della creazione dei pacchetti dei profili di rilascio e debug di Xamarin.Android. Sono anche state illustrate le strategie usate dalla piattaforma Mono for Android per facilitare la distribuzione efficiente del pacchetto durante le fasi di debug e rilascio dello sviluppo.
