---
title: 'Procedura dettagliata: Uso di Apple Instruments'
description: Questo articolo descrive come usare lo strumento Instruments di Apple per diagnosticare problemi di memoria in un'applicazione iOS compilata con Xamarin. Illustra come avviare Instruments, creare snapshot dell'heap, analizzare la crescita della memoria e molto altro.
ms.prod: xamarin
ms.assetid: 8f21db1d-7107-4158-8058-d47e417689a0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 41254fb6aac176cd796fba851478b31f774553d2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023452"
---
# <a name="walkthrough---using-apples-instruments-tool"></a>Procedura dettagliata: Uso di Apple Instruments

_Questo articolo illustra in dettaglio l'uso di Apple Instruments per diagnosticare problemi di memoria in un'applicazione iOS compilata con Xamarin. Illustra come avviare Instruments, come creare snapshot dell'heap e come analizzare la crescita della memoria. Descrive anche come usare Instruments per visualizzare ed evidenziare le righe di codice specifiche che causano il problema di memoria._

Questo articolo spiega come usare lo strumento **Xcode Instruments** per diagnosticare un problema di memoria in un'applicazione iOS.
Per iniziare, scaricare l'[esempio MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo) e aprire la soluzione **before** in Visual Studio per Mac.

## <a name="diagnosing-the-memory-issues"></a>Diagnostica dei problemi di memoria

1. In Visual Studio per Mac avviare **Instruments** scegliendo **Strumenti > Avvia Instruments**.
2. Caricare l'applicazione nel dispositivo scegliendo **Esegui > Carica nel dispositivo**.
3. Scegliere il modello **Allocations** (Allocazioni) (icona arancione con una scatola bianca).

    ![](walkthrough-apples-instrument-images/00-allocations-tempate.png "Choose the Allocations template")

4. Selezionare l'applicazione **Memory Demo** (Demo memoria) nell'elenco **Choose a profiling template for** (Scegliere un modello di profilatura per) nella parte superiore della finestra. Fare clic sul dispositivo iOS per espandere il menu che mostra le applicazioni installate.

    ![](walkthrough-apples-instrument-images/01-mem-demo.png "Select the Memory Demo application")

5. Scegliere il pulsante **Choose** (Scegli) nell'angolo inferiore destro della finestra per avviare **Instruments**. Questo modello mostra due elementi nel riquadro superiore: Allocations (Allocazioni) e VM Tracker (Registro VM).

6. Scegliere il pulsante **Record** (Registra) (il cerchio rosso in alto a sinistra) in Instruments per avviare l'applicazione.

7. Selezionare la riga **VM Tracker** (Registro VM) nel riquadro superiore. Ora che l'app è in esecuzione, conterrà due sezioni: Dirty (Modificato) e Resident Size (Dimensioni residenti). Nel riquadro **Inspector** (Finestra di ispezione) selezionare l'opzione **Show Display Settings** (Mostra impostazioni di visualizzazione) (l'icona a forma di ingranaggi), quindi selezionare la casella di controllo **Automatic Snapshotting** (Creazione automatica snapshot), visualizzata nell'angolo inferiore destro della schermata seguente:

    ![](walkthrough-apples-instrument-images/02-auto-snapshot.png "Choose the Show Display Settings option the gear icon then tick the Automatic Snapshotting checkbox")

8. Selezionare la riga **Allocations** (Allocazioni) nel riquadro superiore (ora che l'app è in esecuzione, riporterà il testo *All Heap and Anonymous VM* [Tutti gli heap e le VM anonime]).
9. Nel riquadro **Inspector** (Finestra di ispezione) selezionare l'opzione **Show Display Settings** (Mostra impostazioni di visualizzazione) (l'icona a forma di ingranaggi), quindi fare clic sul pulsante **Mark Generation** (Contrassegna generazione) per stabilire una baseline. Nella sequenza temporale nella parte superiore della finestra verrà visualizzato un piccolo contrassegno rosso.
10. Scorrere l'applicazione, quindi selezionare di nuovo **Mark Generation** (Contrassegna generazione) (ripetere alcune volte).
11. Fare clic sul pulsante **Stop** (Arresta).
12. Espandere il nodo **Generation** (Generazione) con il valore più alto di **Growth** (Crescita) e ordinare per **Growth** (ordine decrescente).
13. Impostare il riquadro **Inspector** (Finestra di ispezione) su **Show Extended Detail** (Mostra dettagli estesi) (la "E"), che mostra l'analisi dello stack (**Stack Trace**).

14. Come si può notare, il nodo **&lt;non-object>** mostra una crescita eccessiva della memoria. Fare clic sulla freccia accanto a questo nodo per visualizzare maggiori dettagli. Fare clic con il pulsante destro del mouse sull'analisi dello stack per aggiungere **Source Location** (Posizione di origine) al riquadro:

    ![](walkthrough-apples-instrument-images/03-mem-growth.png "Add Source Location to the pane")

15. Ordinare per **Size** (Dimensioni) e passare alla visualizzazione **Extended Detail** (Dettagli estesi):

    ![](walkthrough-apples-instrument-images/04-extended-detail.png "Sort by Size and display the  Extended Detail view")

16. Fare clic sulla voce desiderata nello stack di chiamate per visualizzare il codice correlato:

    ![](walkthrough-apples-instrument-images/05-related-code.png "Viewing the related code")

In questo caso, una nuova immagine viene creata e archiviata in una raccolta per ogni cella e le celle della visualizzazione della raccolta esistente non vengono riutilizzate.

## <a name="resolving-the-memory-issues"></a>Risoluzione dei problemi di memoria

È possibile risolvere questi problemi ed eseguire di nuovo l'applicazione tramite Instruments.

Se si dichiara una singola istanza a livello di classe, sarà possibile riutilizzare l'immagine e recuperare l'oggetto cella da un pool esistente, anziché doverli creare ogni volta, come mostrato di seguito:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Dequeue a cell from the reuse pool
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);

    // Reuse the image declared at the class level
    imageCell.ImageView.Image = image;

    return imageCell;
}
```

Ora, quando l'applicazione viene eseguita, l'uso della memoria risulta significativamente ridotto. La crescita (**Growth**) tra una generazione e l'altra ora viene misurata in Kib (kilobyte) anziché in MiB (megabyte) come accadeva prima di correggere il codice:

![](walkthrough-apples-instrument-images/06-reduced-memory.png "Showing the app memory usage")

Il codice migliorato è disponibile nell'[esempio MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo) nella soluzione **after** in Visual Studio per Mac.

Questo blog di community su [Garbage Collection di Xamarin.iOS](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/) è un riferimento utile per la gestione dei problemi di memoria di Xamarin.iOS.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come usare Instruments per diagnosticare i problemi di memoria.
È stato descritto come avviare Instruments da Visual Studio per Mac, caricare il modello di allocazione della memoria e usare gli snapshot per individuare i problemi di memoria.
Infine, l'applicazione è stata riesaminata per verificare che il problema sia stato corretto.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)
- [Xamarin.iOS Garbage Collection (blog post)](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/) (Garbage Collection di Xamarin.iOS - post di blog)
