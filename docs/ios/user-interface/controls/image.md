---
title: Visualizzazione di immagini
ms.topic: article
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 716189fbf1518e9100a78cc5ae64e9e63a24c949
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="displaying-images"></a>Visualizzazione di immagini

Aggiunta di immagini per l'app richiede due passaggi: innanzitutto, aggiungere le immagini al progetto. quindi, aggiungere controlli e codice per visualizzarli in una schermata. Consultare la [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) articolo per informazioni dettagliate copertura dell'immagine di gestione in xamarin. IOS.

## <a name="adding-images-to-your-app"></a>Aggiunta di immagini per l'App

È possibile aggiungere immagini a qualsiasi cartella di Visual Studio per la soluzione Mac e se il **azione di compilazione** è impostata su **contenuto** quindi il file verrà incluso con l'app e possono essere visualizzato.

Visual Studio per Mac supporta anche una directory speciale denominata risorse che possono contenere anche i file di immagine. I file nella cartella risorse devono avere la **azione di compilazione** impostato su **BundleResource**.

Questa schermata è riportato il **azione di compilazione** opzioni visualizzate quando un file è di tipo pulsante destro del mouse:

 [![](image-images/image30a.png "Dal menu azione di compilazione")](image-images/image30a.png#lightbox)

Visual Studio per Mac in genere sceglierà corrette **azione di compilazione** automaticamente, ma è necessario essere consapevoli di queste impostazioni, soprattutto se si spostare i file nel progetto.

### <a name="adding-an-image-file"></a>Aggiunta di un File di immagine

Per aggiungere un file di immagine al progetto, innanzitutto fare clic sul progetto e scegliere **Aggiungi file...**

 [![](image-images/image31a.png "Aggiungi file … menu")](image-images/image31a.png#lightbox)

Selezionare l'immagine (o immagini) che si desidera includere nella finestra di dialogo file standard. Azione di compilazione il valore predefinito per le immagini verranno **BundleResource** : non eseguire l'override di questo valore, a meno che non si dispone di un motivo specifico.

 [![](image-images/image32a.png "Aggiungere una finestra di dialogo file")](image-images/image32a.png#lightbox)

L'immagine verrà aggiunta al progetto e disponibile per essere caricato e visualizzato nel codice. Questa schermata mostra un'immagine aggiunta a un progetto di applicazione iOS:

 [![](image-images/image33a.png "Immagine di progetto")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>Che cos'è la Directory delle risorse?

I file inseriti nella directory delle risorse sono trattati in modo diverso dai file normale: il contenuto della cartella di risorse venga copiato nella directory radice dell'applicazione e a cui fa riferimento da tale posizione nel codice. Ciò può risultare utile per diversi motivi:

-  Archiviare le immagini configurate nelle proprietà dell'applicazione, ad esempio le icone di applicazione e di immagini di avvio predefinite.
-  L'archiviazione di altri file separatamente dal codice e le immagini, pertanto si più semplici da gestire (sottodirectory vengono mantenute quando viene copiato il contenuto della directory risorse).


Directory delle risorse è particolarmente utile in un progetto di libreria, poiché il codice può presupporre che le immagini verranno copiate nella directory principale dell'applicazione consumer, rendendo più semplice la scrittura di librerie di codice condiviso che richiedono l'immagine, audio, video, XML o altri file.



Directory delle risorse deve essere denominata così e tutti i file devono avere l'azione di compilazione impostata su **BundleResource**

## <a name="displaying-the-image"></a>La visualizzazione dell'immagine

Per visualizzare un'immagine utilizzando la finestra di progettazione, visualizzazione di un'immagine deve essere utilizzata come un contenitore e consente di visualizzare una singola immagine o un'animazione delle immagini. Il **visualizzazione immagine** dalla casella degli strumenti viene visualizzata di seguito:

 [![](image-images/image35a.png "ImageView nella casella degli strumenti")](image-images/image35.png#lightbox)

Trascinare il **immagine vista** dal **Toobox** nel controller di visualizzazione. Quindi in * * visualizzazione immagine > Immagine * * elenco a discesa verrà visualizzato un elenco di tutti i file di immagine disponibile nel progetto. Selezionare una delle seguenti per aggiungerlo alla visualizzazione dell'immagine.

 [![](image-images/image36a.png "ImageView nella casella degli strumenti")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>La visualizzazione dell'immagine a livello di codice

Poiché blocks.jpg si trova nella radice della directory delle risorse sarà disponibile in fase di esecuzione nella radice del pacchetto di applicazione. Per visualizzare l'immagine in un ImageView controllo il codice seguente:

```csharp
imageview1.Image = UIImage.FromBundle ("SF Monkey.png");
```

Se si fosse stata inserita l'immagine in `/Resources/Pics/blocks.jpg` quindi il codice include la cartella Pics nel percorso:

```csharp
imageview1.Image = UIImage.FromBundle ("Pics/SF Monkey.png");
```

File di risorse fa riferimento a mai necessario includere il `Resources` cartella.


## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
