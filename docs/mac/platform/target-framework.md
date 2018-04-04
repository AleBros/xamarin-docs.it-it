---
title: Framework di destinazione
description: In questo articolo vengono illustrati il framework di destinazione (librerie di classi di Base) disponibile per Xamarin.Mac e le implicazioni del loro uso nel progetto Xamarin.Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 053cdd2dbfc7741257e6630e5b11b77b1055428e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="target-framework"></a>Framework di destinazione

_In questo articolo vengono illustrati il framework di destinazione (librerie di classi di Base) disponibile per Xamarin.Mac e le implicazioni del loro uso nel progetto Xamarin.Mac._

![Opzioni per Xamarin.Mac di framework di destinazione](target-framework-images/select-target.png "per Xamarin.Mac opzioni di framework di destinazione")

## <a name="background"></a>Sfondo

Ogni programma .NET o una raccolta dipende da funzionalità fornite dalla libreria di classi Base (BCL). Questa libreria di classi di base include gli assembly, ad esempio mscorlib, System, System.NET. http e System. XML che forniscono le funzionalità comuni incorporate in tutti i linguaggi .NET.

Nel corso degli anni, hanno sviluppato più versioni diverse di questa libreria di classi base, ottimizzato per diversi casi d'uso. La libreria di classi base "desktop" include un set più completo delle librerie che potrebbe essere troppo ad densità per altri casi, mentre i dispositivi mobili è incentrata su come verificare che le API sono sicure per il collegamento, che consente di rimuovere il codice non utilizzato per ridurre il footprint dell'applicazione.

Uno dei più importanti ripercussioni di questi diversi framework di destinazione, è che tutti gli assembly in un determinato programma *deve* compatibile assembly libreria di classi base di destinazione. Se questo non è il caso, è possibile che due assembly collegato le diverse versioni del **System.dll** disagreeing sulla firma di un determinato tipo. Una libreria condivisa possibile destinazione [.NET Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), che è il subset più comune di, il framework di destinazione o un framework di destinazione specifico.

Sono disponibili tre opzioni di .NET Framework di destinazione per Xamarin.Mac, ognuna con compromessi e diversi vantaggi:

- **Moderna** (denominati Mobile nella documentazione precedente), un subset molto simile a quali potenze di xamarin. IOS, ottimizzate per le dimensioni e prestazioni. Il Framework di destinazione è linker-safe, pertanto tali progetti possono avere rispettivi finale footprint ridotto drasticamente rimuovendo codice non usato.

- **Completa** (denominati 4.5 XM nella documentazione precedente), un subset molto simile per la BCL "desktop", con pochi rimozioni di piccole dimensioni. Il Framework di destinazione è quasi identico a net45 (e versioni successive), può facilmente utilizzare molti nugets che non forniscono entrambi netstandard2 o compilazioni Xamarin.Mac specifico. Tuttavia, a causa dell'utilizzo di System. Configuration non è compatibile con il collegamento.

- **Non supportato** (denominati sistema nella documentazione precedente), invece di un collegamento a una libreria di classi base fornito da Xamarin.Mac, utilizzare il mono di sistema corrente. In questo modo il set più completo di assembly, inclusi alcuni noto problematiche (System. Drawing, ad esempio). Questa opzione è disponibile solo è "strettamente" e si consiglia di allocare altre opzioni prima di utilizzarlo. Come suggerisce il nome, utilizzo non è supportato dai canali di supporto ufficiale.

## <a name="setting-the-target-framework"></a>Impostazione del framework di destinazione

Per modificare il tipo di Framework di destinazione per un progetto Xamarin.Mac, eseguire le operazioni seguenti:

1. Aprire il progetto Xamarin.Mac in Visual Studio per Mac.
2. In **Esplora soluzioni** fare doppio clic sul file di progetto per aprire la finestra di dialogo **Opzioni progetto**.
3. Dal **generale** scheda, selezionare il tipo di **Framework di destinazione** che si adatta alle esigenze dell'applicazione:

  [![Utilizzare la finestra Opzioni di progetto scegliere un framework di destinazione](target-framework-images/select-target-full.png "utilizzando la finestra Opzioni progetto scegliere un framework di destinazione")](target-framework-images/select-target-full-large.png#lightbox)

4. Fare clic sul pulsante **OK** per salvare le modifiche.

È necessario **Pulisci** e quindi **ricompilare** progetto Xamarin.Mac dopo il passaggio il tipo di Framework di destinazione.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati brevemente i diversi tipi di Framework di destinazione (librerie di classi di Base) disponibile per un'applicazione Xamarin.Mac e quando utilizzare ogni tipo di framework.


## <a name="related-links"></a>Collegamenti correlati

- [iOS e Mac condivisione del codice](~/cross-platform/macios/index.md)
- [API unificata](~/cross-platform/macios/unified/index.md)
- [Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)
- [Assembly](~/cross-platform/internals/available-assemblies.md)
- [L'aggiornamento di App Mac esistente](~/cross-platform/macios/unified/updating-mac-apps.md)
