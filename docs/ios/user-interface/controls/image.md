---
title: Visualizzazione di immagini con Novell. iOS
description: In questo documento viene descritto come visualizzare le immagini in Novell. iOS. Viene illustrata l'aggiunta di immagini a un'app a livello di codice o tramite iOS designer.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: fb51d0d04c86274d43e23e4912ed16765b7cdc8b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657525"
---
# <a name="displaying-images-with-xamarinios"></a>Visualizzazione di immagini con Novell. iOS

Per aggiungere immagini all'app sono necessari due passaggi: prima di tutto, aggiungere le immagini al progetto; quindi, aggiungere i controlli e il codice per visualizzarli in una schermata. Per informazioni più dettagliate sulla gestione delle immagini in Novell. iOS, vedere l'articolo [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) .

## <a name="adding-images-to-your-app"></a>Aggiunta di immagini all'app

Le immagini possono essere aggiunte a qualsiasi cartella della soluzione Visual Studio per Mac e, se l' **azione di compilazione** è impostata su **contenuto** , il file verrà incluso nell'app e potrà essere visualizzato.

Visual Studio per Mac supporta anche una directory speciale denominata **risorse** che possono contenere anche file di immagine. I file nella cartella Resources devono avere l' **azione di compilazione** impostata su **BundleResource**.

Questa schermata mostra le opzioni di **azione di compilazione** visualizzate quando si fa clic con il pulsante destro del mouse su un file:

 [![](image-images/image30a.png "Menu azione di compilazione")](image-images/image30a.png#lightbox)

Visual Studio per Mac in genere scelgono automaticamente l' **azione di compilazione** corretta, ma è necessario tenere presente queste impostazioni, soprattutto se si spostano i file nel progetto.

### <a name="adding-an-image-file"></a>Aggiunta di un file di immagine

Per aggiungere un file di immagine al progetto, fare prima clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi file.**

 [![](image-images/image31a.png "Aggiungi file... menu")](image-images/image31a.png#lightbox)

Selezionare l'immagine o le immagini che si desidera includere nella finestra di dialogo file standard. L'azione di compilazione predefinita per le immagini sarà **BundleResource** : non eseguire l'override di questo valore a meno che non esista un motivo specifico.

 [![](image-images/image32a.png "Finestra di dialogo Aggiungi file")](image-images/image32a.png#lightbox)

L'immagine verrà aggiunta al progetto e sarà disponibile per essere caricata e visualizzata nel codice. Questa schermata mostra un'immagine aggiunta a un progetto di applicazione iOS:

 [![](image-images/image33a.png "Immagine nel progetto")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>Che cos'è la directory delle risorse?

I file inseriti nella directory **delle risorse** vengono trattati in modo diverso rispetto ai file normali. il contenuto della cartella delle **risorse** viene copiato nella radice dell'applicazione ed è possibile farvi riferimento nel codice. Questa operazione può essere utile per diversi motivi:

-  Archiviazione delle immagini configurate nelle proprietà dell'applicazione, ad esempio le immagini di avvio predefinite e le icone dell'applicazione.
-  L'archiviazione di altre immagini e file separatamente dal codice, in modo che siano più facili da gestire (le sottodirectory vengono mantenute quando viene copiato il contenuto della directory delle risorse).


La directory Resources è particolarmente utile in un progetto di libreria, poiché il codice può presumere che tali immagini verranno copiate nella radice dell'applicazione consumer, semplificando la scrittura di librerie di codice condiviso che richiedono immagine, audio, video, XML o altri file.

La directory resources deve essere denominata e tutti i file devono avere l'azione di compilazione impostata su **BundleResource**.

## <a name="displaying-the-image"></a>Visualizzazione dell'immagine

In iOS designer usare una **visualizzazione immagine** per visualizzare un'immagine o una serie animata di immagini. L'icona **visualizzazione immagine** dalla casella degli strumenti è illustrata di seguito:

 [![](image-images/image35a.png "ImageView nella casella degli strumenti")](image-images/image35.png#lightbox)

Trascinare la **visualizzazione immagine** dalla **casella degli strumenti** sul controller di visualizzazione. Quindi, in **visualizzazione immagine > immagine** l'elenco a discesa fornirà un elenco di tutti i file di immagine disponibili nel progetto. Selezionare uno di questi per aggiungerlo alla visualizzazione immagine.

 [![](image-images/image36a.png "ImageView nella casella degli strumenti")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Visualizzazione dell'immagine a livello di codice

Poiché **SF Monkey. jpg** si trova nella directory principale della directory Resources, sarà disponibile in fase di esecuzione nella radice del bundle dell'applicazione. Per visualizzare questa immagine in un controllo di visualizzazione immagine, usare il codice seguente:

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Se l'immagine è stata inserita in **/Resources/pics/SF Monkey. jpg**, il codice includerà la cartella **pics** nel percorso:

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

I riferimenti ai file di risorse non devono mai includere la cartella Resources.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
