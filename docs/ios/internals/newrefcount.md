---
title: Nuovo riferimento il conteggio di sistema in xamarin. IOS
description: Questo documento descrive system, abilitato per impostazione predefinita in tutte le applicazioni xamarin. IOS nel conteggio dei riferimenti avanzati di Xamarin.
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 8c7b1a88284156cb5d4261f18d5659ed66dfaf64
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879329"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Nuovo riferimento il conteggio di sistema in xamarin. IOS

Xamarin. IOS 9.2.1 ha introdotto il riferimento avanzato il conteggio di tutte le applicazioni del sistema per impostazione predefinita. Può essere utilizzato per eliminare molti problemi di memoria difficili da rilevare e correggere nelle versioni precedenti di xamarin. IOS.

## <a name="enabling-the-new-reference-counting-system"></a>Abilitazione del nuovo riferimento il conteggio di sistema

A partire da Xamarin 9.2.1 il riferimento di nuovo il conteggio di sistema è abilitato per **tutti** applicazioni per impostazione predefinita.

Se si sta sviluppando un'applicazione esistente, è possibile controllare il file con estensione csproj per assicurarsi che tutte le occorrenze di `MtouchUseRefCounting` sono impostati su `true`, come di seguito:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Se è impostato su `false` l'applicazione non utilizzeranno i nuovi strumenti.

### <a name="using-older-versions-of-xamarin"></a>Con le versioni precedenti di Xamarin

Xamarin. IOS 7.2.1 e sopra un'anteprima avanzata del nostro sistema di conteggio dei riferimenti nuove funzionalità.

**API classica:**

Per abilitare questo nuovo sistema di conteggio dei riferimenti, selezionare la **usare l'estensione conteggio dei riferimenti** casella di controllo disponibili nel **Advanced** scheda della finestra del progetto **opzioni di compilazione iOS** , come illustrato di seguito: 

[![](newrefcount-images/image1.png "Abilitare il nuovo sistema di conteggio dei riferimenti")](newrefcount-images/image1.png#lightbox)

Si noti che queste opzioni sono state rimosse nelle versioni più recenti di Visual Studio per Mac.

 **[API unificata:](~/cross-platform/macios/unified/index.md)**

 La nuova estensione conteggio dei riferimenti è obbligatorio per l'API unificata e deve essere abilitata per impostazione predefinita. Le versioni precedenti dell'IDE dell'utente non possono avere questo valore selezionato automaticamente e potrebbe essere necessario inserire un segno di spunta dalla funzione se stessi.

    
> [!IMPORTANT]
> Poiché 5.2 MonoTouch ma erano disponibili solo per una versione precedente di questa funzionalità dura all'incirca **sgen** come anteprima sperimentale. Questa versione nuovi e migliorata è ora disponibile anche per il **Boehm** garbage collector.


In passato erano disponibili due tipi di oggetti gestiti da xamarin. ios: quelli che sono stati semplicemente un wrapper per un oggetto nativo (oggetti peer) e quelli che esteso o incorporato (oggetti derivati) - le nuove funzionalità in genere, mantenendo lo stato aggiuntivi in memoria. In precedenza era possibile avremmo forniamo un oggetto peer con stato (ad esempio aggiungendo un C# gestore dell'evento), ma che facciamo passare senza riferimenti e quindi raccogliere l'oggetto. Ciò potrebbe causare un arresto anomalo in un secondo momento (ad esempio, se il runtime di Objective-C richiamata nell'oggetto gestito).

Il nuovo sistema di aggiornare automaticamente gli oggetti peer in oggetti gestiti dal runtime quando si archiviano le eventuali informazioni aggiuntive.

Ciò consente di risolvere vari arresti anomali che si sono verificati in casi come questo:

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

Senza l'estensione di conteggio di riferimento questo codice potrebbe arrestarsi in modo anomalo perché `cell` diventa ritirabile e pertanto relativo `TouchDown` delegato, che verrà convertito in un puntatore inesatti.

L'estensione di conteggio dei riferimenti garantisce l'oggetto gestito rimane attivo e impedisce il suo insieme, purché l'oggetto nativo viene mantenuto dal codice nativo.

Il nuovo sistema rimuove anche la necessità di *la maggior parte delle* private utilizzati nelle associazioni - è l'approccio predefinito per keep-alive istanza campi sottostanti. Il linker gestito è abbastanza intelligente da rimuovere tutti quelli *non necessarie* campi dalle applicazioni usando il nuovo riferimento al numero di estensione.

Ciò significa che ciascuna istanza di oggetto gestito utilizzata meno memoria rispetto a prima. Viene risolto un problema correlato in cui alcuni campi sottostanti conserverebbero i riferimenti che non sono necessari più dal runtime di Objective-C, rendendo difficile recuperare parte della memoria.
