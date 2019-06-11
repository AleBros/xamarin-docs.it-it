---
title: Visualizzazione di immagini con xamarin. IOS
description: Questo documento viene descritto come visualizzare le immagini in xamarin. IOS. Viene descritto come aggiungere immagini a un'app a livello di codice o tramite la finestra di progettazione di iOS.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: 25c5595fac2ea2aa7a87d6640cc6b5c399ab5e5e
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827812"
---
# <a name="displaying-images-with-xamarinios"></a>Visualizzazione di immagini con xamarin. IOS

Aggiunta di immagini per l'app richiede due passaggi: in primo luogo, aggiungere le immagini al progetto. quindi, aggiungere i controlli e codice per visualizzarli in una schermata. Vedere la [utilizzo di immagini](~/ios/app-fundamentals/images-icons/index.md) articolo per informazioni dettagliate di code coverage dell'immagine gestisce in xamarin. IOS.

## <a name="adding-images-to-your-app"></a>Aggiunta di immagini all'App

In qualsiasi cartella in Visual Studio per la soluzione Mac, è possibile aggiungere immagini e, se il **Build Action** è impostata su **contenuto** quindi il file verrà incluso con l'app e possono essere visualizzato.

Visual Studio per Mac supporta anche una directory speciale chiamata **risorse** che possono anche contenere file di immagine. I file nella cartella delle risorse devono avere la **Build Action** impostata su **BundleResource**.

In questo screenshot appare il **azione di compilazione** si fa clic opzioni visualizzate quando un file:

 [![](image-images/image30a.png "Menu azione di compilazione")](image-images/image30a.png#lightbox)

Visual Studio per Mac sceglierà in genere i valori corretti **azione di compilazione** automaticamente, ma è necessario essere consapevoli di queste impostazioni, in particolare se si spostano i file all'interno del progetto.

### <a name="adding-an-image-file"></a>Aggiunta di un file di immagine

Per aggiungere un file di immagine al progetto, prima di tutto fare clic sul progetto e scegliere **Aggiungi file...**

 [![](image-images/image31a.png "Aggiungere i file... dal menu")](image-images/image31a.png#lightbox)

Selezionare l'immagine (o le immagini) che si desidera includere nella finestra di dialogo file standard. Azione di compilazione predefinito per le immagini saranno **BundleResource** : non eseguire l'override di questo valore, a meno che non esista un motivo specifico.

 [![](image-images/image32a.png "Aggiungi finestra di dialogo file")](image-images/image32a.png#lightbox)

L'immagine verrà aggiunto al progetto e disponibile per essere caricati e visualizzati nel codice. Questo screenshot Mostra un'immagine aggiunta a un progetto di applicazione iOS:

 [![](image-images/image33a.png "Immagine nel progetto")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>Che cos'è la directory delle risorse?

File inseriti nella **le risorse** directory vengono trattati in modo diverso dai file normali: il contenuto del **risorse** cartella vengono copiati nella radice dell'applicazione e possono essere referenziate da qui in il codice. Ciò può risultare utile per diversi motivi:

-  Archiviare le immagini configurate nelle proprietà dell'applicazione, ad esempio le immagini di avvio predefinite e le icone dell'applicazione.
-  Archiviare altre immagini, file separatamente dal codice, pertanto risultano più semplici da gestire (le sottodirectory vengono mantenute quando viene copiato il contenuto della directory delle risorse).


Il **risorse** directory è particolarmente utile in un progetto di libreria, poiché il codice può presupporre che verranno copiate nella radice dell'applicazione consumer, rendendo più semplice le librerie di codice condiviso di scrittura che richiedono tali immagini immagine, audio, video XML o altri file.

Il **le risorse** directory deve essere così denominata, e tutti i file devono avere l'azione di compilazione impostata su **BundleResource**.

## <a name="displaying-the-image"></a>La visualizzazione dell'immagine

In iOS Designer, usare un **visualizzazione immagine** per visualizzare un'immagine animata serie di immagini. Il **visualizzazione immagine** icona dalla casella degli strumenti è illustrato di seguito:

 [![](image-images/image35a.png "ImageView nella casella degli strumenti")](image-images/image35.png#lightbox)

Trascinare il **Visualizza immagine** dalle **della casella degli strumenti** al controller di visualizzazione. Quindi, sotto **visualizzazione immagine > immagine** l'elenco a discesa verrà visualizzato un elenco di tutti i file di immagine disponibile nel progetto. Selezionare una delle seguenti per aggiungerlo alla visualizzazione delle immagini.

 [![](image-images/image36a.png "ImageView nella casella degli strumenti")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>La visualizzazione dell'immagine a livello di codice

Poiché **SF Monkey.jpg** si trova nella radice del **risorse** directory sarà disponibile in fase di esecuzione nella radice dell'aggregazione dell'applicazione. Per visualizzare questa immagine in un controllo di visualizzazione immagine, usare il codice seguente:

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Se avessimo è stato inserito un immagine nel **Monkey.jpg Pics/risorse/SF**, quindi il codice includerà il **Pics** cartella nel percorso:

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

File di risorse fa riferimento a mai necessario includere il **risorse** cartella.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/monotouch/Controls/)
