---
title: Nuovo sistema di conteggio dei riferimenti
ms.topic: article
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: dd4aff683b0cfb797147d32f282be1aab4c30667
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="new-reference-counting-system"></a>Nuovo sistema di conteggio dei riferimenti

Xamarin. IOS 9.2.1 è stato introdotto il riferimento avanzato sistema a tutte le applicazioni di conteggio per impostazione predefinita. E può essere utilizzato per eliminare molti problemi di memoria difficili da rilevare e correggere nelle versioni precedenti di xamarin. IOS.

## <a name="enabling-the-new-reference-counting-system"></a>Abilitare il nuovo sistema di conteggio di riferimento

A partire da Xamarin 9.2.1 il riferimento di nuovo il conteggio di sistema è abilitato per **tutti** applicazioni per impostazione predefinita.

Se si sviluppa un'applicazione esistente, è possibile verificare il file con estensione csproj per assicurarsi che tutte le occorrenze di `MtouchUseRefCounting` sono impostate su `true`, come di seguito:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Se è impostato su `false` l'applicazione non verrà utilizzato lo strumento di nuovo.

### <a name="using-older-versions-of-xamarin"></a>Utilizzo delle versioni precedenti di Xamarin

Xamarin. IOS 7.2.1 e sopra le caratteristiche avanzata anteprima il nuovo conteggio dei riferimenti del sistema.

**API classica:**

Per abilitare questo nuovo sistema di conteggio dei riferimenti, controllare il **utilizzare l'estensione di conteggio dei riferimenti** casella di controllo, vedere il **avanzate** scheda della finestra del progetto **opzioni di compilazione iOS** , come illustrato di seguito: 

[ ![](newrefcount-images/image1.png "Abilitare il nuovo sistema di conteggio dei riferimenti")](newrefcount-images/image1.png)

Si noti che queste opzioni sono state rimosse nelle versioni più recenti di Visual Studio per Mac.

 **[API unificata:](~/cross-platform/macios/unified/index.md)**

 Il nuovo riferimento conteggio estensione è necessario per l'API unificata e dovrebbe essere abilitato per impostazione predefinita. Le versioni precedenti dell'IDE non dispone di questo valore estratto e potrebbe essere necessario inserire un segno di spunta da essa manualmente.

    
> [!IMPORTANT]
> **Nota:** una versione precedente di questa funzionalità è stata poiché 5.2 MonoTouch ma è disponibile solo per **sgen** come un'anteprima sperimentale. Questa nuova versione avanzata è anche disponibile per il **Boehm** garbage collector.


In passato sono state apportate due tipi di oggetti gestiti da xamarin: quelli che erano in genere semplicemente un wrapper per un oggetto nativo (oggetti peer) e quelli che esteso o incorporate nuove funzionalità (oggetti derivati) - mantenendo stato extra in memoria. In precedenza era possibile che è possibile incrementare un oggetto peer con stato (ad esempio aggiungendo un gestore eventi in c#) ma che è che l'oggetto privo di riferimento e quindi raccolti. Ciò potrebbe causare un arresto anomalo in un secondo momento (ad esempio, se richiamato l'oggetto gestito Objective-C runtime).

Il nuovo sistema aggiorna automaticamente gli oggetti peer in oggetti che vengono gestiti dal runtime quando archiviano le eventuali informazioni aggiuntive.

Consente di risolvere vari arresti anomali che si sono verificati in situazioni come questa:

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

Senza l'estensione del numero di riferimento questo codice potrebbe arrestarsi in modo anomalo perché `cell` diventa ritirabile e pertanto il relativo `TouchDown` delegato, che verrà convertito in un puntatore tralasciato.

L'estensione di conteggio di riferimento garantisce l'oggetto gestito rimarrà attivo e impedisce il suo insieme, purché l'oggetto nativo viene mantenuto da codice nativo.

Il nuovo sistema rimuove inoltre la necessità di *la maggior parte delle* private i campi utilizzati nelle associazioni - è l'approccio predefinito per mantenere l'istanza attiva di backup. Il linker gestito è abbastanza per rimuovere tutti quelli *non necessari* estensione conteggio dei campi delle applicazioni che utilizzano il nuovo riferimento.

Ciò significa che le istanze di ogni oggetto gestito utilizzano meno memoria rispetto a prima. Inoltre, risolve un problema correlato in cui alcuni campi sottostanti conterrebbe i riferimenti che non sono più necessari dal runtime di Objective-C, rendendo difficile recuperare memoria.
