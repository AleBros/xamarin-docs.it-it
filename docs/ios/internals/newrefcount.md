---
title: Nuovo sistema di conteggio dei riferimenti in Xamarin.iOS
description: Questo documento descrive il sistema di conteggio dei riferimenti migliorato di Xamarin.abilitato per impostazione predefinita in tutte le applicazioni Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: 8d8ad5b5f79b90fc415c9e3cdf6809a4e196056f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022295"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Nuovo sistema di conteggio dei riferimenti in Xamarin.iOS

Per impostazione predefinita, Xamarin.iOS 9.2.1 ha introdotto il sistema di conteggio dei riferimenti migliorato per tutte le applicazioni. Può essere usato per eliminare molti problemi di memoria difficili da rilevare e correggere nelle versioni precedenti di Xamarin.iOS.

## <a name="enabling-the-new-reference-counting-system"></a>Abilitazione del nuovo sistema di conteggio dei riferimenti

A partire da Xamarin 9.2.1, per impostazione predefinita, il nuovo sistema di conteggio dei riferimenti è abilitato per **tutte** le applicazioni.

Se si sta sviluppando un'applicazione esistente, è possibile controllare il file con estensione csproj per assicurarsi che tutte le occorrenze di `MtouchUseRefCounting` siano impostate su `true`, come indicato di seguito:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Se è impostato su `false` l'applicazione non utilizzerà i nuovi strumenti.

### <a name="using-older-versions-of-xamarin"></a>Uso di versioni precedenti di Novell

Xamarin.iOS 7.2.1 e versioni successive dispongono di un'anteprima migliorata del nuovo sistema di conteggio dei riferimenti.

**API classica:**

Per abilitare questo nuovo sistema di conteggio dei riferimenti, selezionare la casella di controllo **Usa l'estensione** per il conteggio dei riferimenti disponibile nella scheda **Avanzate** delle **Opzioni di compilazione iOS**del progetto, come illustrato di seguito: 

[![](newrefcount-images/image1.png "Enable the new Reference Counting System")](newrefcount-images/image1.png#lightbox)

Si noti che queste opzioni sono state rimosse nelle versioni più recenti di Visual Studio per Mac.

 **[API unificata:](~/cross-platform/macios/unified/index.md)**

 La nuova estensione per il conteggio dei riferimenti è obbligatoria per l'API unificata e deve essere abilitata per impostazione predefinita. Le versioni precedenti dell'IDE potrebbero non avere questo valore selezionato automaticamente e potrebbe essere necessario effettuare un controllo manualmente.

> [!IMPORTANT]
> Una versione precedente di questa funzionalità è stata aggirata rispetto a MonoTouch 5,2 ma era disponibile solo per **SGen** come anteprima sperimentale. Questa nuova versione migliorata è ora disponibile anche per la Garbage Collector di **Boehm** .

Storicamente sono presenti due tipi di oggetti gestiti da Xamarin.iOS: quelli che sono semplicemente un wrapper per un oggetto nativo (oggetti peer) e quelli che hanno esteso o incorporato nuove funzionalità (oggetti derivati), in genere mantenendo lo stato in memoria aggiuntivo. In precedenza era possibile che venisse migliorato un oggetto peer con lo stato (ad esempio aggiungendo un C# gestore eventi), ma che si lascia che l'oggetto venga senza riferimenti e quindi raccolto. Questo potrebbe causare un arresto anomalo in un secondo momento, ad esempio se il runtime di Objective-C ha richiamato l'oggetto gestito.

Il nuovo sistema aggiorna automaticamente gli oggetti peer in oggetti gestiti dal runtime quando archivia informazioni aggiuntive.

In questo modo vengono risolti i diversi arresti anomali che si sono verificati in situazioni come questa:

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

Senza l'estensione per il conteggio dei riferimenti, questo codice si arresterà in modo anomalo perché `cell` diventa ritirabile, quindi il delegato `TouchDown`, che verrà convertito in un puntatore a penzoloni.

L'estensione per il conteggio dei riferimenti garantisce che l'oggetto gestito rimanga attivo e ne impedisca la raccolta, purché l'oggetto nativo venga mantenuto dal codice nativo.

Il nuovo sistema elimina inoltre la necessità della *maggior parte dei* campi di backup privati utilizzati nelle associazioni, ovvero l'approccio predefinito per la conservazione dell'istanza. Il linker gestito è sufficientemente intelligente da rimuovere tutti i campi non *necessari* dalle applicazioni usando la nuova estensione per il conteggio dei riferimenti.

Ciò significa che ogni istanza di oggetti gestiti utilizza meno memoria rispetto a prima. Consente inoltre di risolvere un problema correlato in cui alcuni campi di supporto contengono riferimenti che non sono più necessari per il runtime di Objective-C, rendendo difficile la richiesta di memoria.
