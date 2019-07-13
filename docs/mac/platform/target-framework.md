---
title: Framework di destinazione per xamarin. Mac
description: Questo articolo illustra il framework di destinazione (librerie di classi di Base) disponibile per xamarin. Mac e le implicazioni di utilizzarle nel progetto xamarin. Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e9e20b4966e9e6cb8a4ce3ad6724cf0ba2565c33
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865870"
---
# <a name="target-framework-for-xamarinmac"></a>Framework di destinazione per xamarin. Mac

_Questo articolo illustra il framework di destinazione (librerie di classi di Base) disponibile per xamarin. Mac e le implicazioni di utilizzarle nel progetto xamarin. Mac._

![Opzioni di framework per xamarin. Mac di destinazione](target-framework-images/select-target.png "opzioni framework per xamarin. Mac di destinazione")

## <a name="background"></a>Sfondo

Ogni programma .NET o una raccolta dipende dalle funzionalità fornite dalla libreria di classi Base (BCL). Questa libreria di classi base sono inclusi assembly, ad esempio mscorlib, System, System.NET. http e System. XML che forniscono le funzionalità comuni incorporata in tutti i linguaggi .NET.

Nel corso degli anni hanno sviluppato più versioni diverse di questa libreria di classi base, ottimizzati per diversi casi d'uso. Libreria di classi base "desktop" include un set più completo di librerie che potrebbe essere troppo ad densità per altri casi, per dispositivi mobili è incentrato su come verificare che le API sono sicure per il collegamento, che rimuove il codice inutilizzato per ridurre il footprint dell'applicazione.

Uno delle ripercussioni di questi framework di destinazione diverso, più importante è che tutti gli assembly in un determinato programma *necessario* assembly BCL compatibili di destinazione. Se questo non è il caso, è possibile che due assembly collegato le diverse versioni del **System. dll** disagreeing sulla firma di un determinato tipo. Una libreria condivisa è una destinazione [.NET Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), che è il subset più comune del Framework di destinazione o un framework di destinazione specifico.

Sono disponibili tre opzioni Framework di destinazione per xamarin. Mac, ognuna con diversi vantaggi e compromessi:

- **Moderna** (nella documentazione precedente la chiamata per dispositivi mobili), un subset molto simile alle competenze di xamarin. IOS, altamente ottimizzati per le prestazioni e dimensioni. Questo Framework di destinazione è linker-safe, pertanto questi progetti possono avere loro finale footprint ridotto drasticamente rimuovendo il codice inutilizzato.

- **Completa** (denominato XM 4.5 nella documentazione precedente): un subset molto simile alla libreria di classi base "desktop", con pochi rimozioni di piccole dimensioni. Poiché il Framework di destinazione è quasi identico a TargetFramework="net45 (e versioni successive), può facilmente utilizzare molti pacchetti NuGet che non forniscono entrambi netstandard2 o compilazioni specifiche xamarin. Mac. Tuttavia, a causa dell'utilizzo di System. Configuration non è compatibile con il collegamento.

- **Non supportato** (sistema nella documentazione precedente) – invece chiamato un collegamento a una libreria di classi base fornito da xamarin. Mac, l'utilizzo di mono di sistema installato corrente. In questo modo il set più completo di assembly, inclusi alcuni notoriamente problematici (System. Drawing, ad esempio). Questa opzione è disponibile solo con "strettamente" ed è fortemente consigliato ad per esaurire le altre opzioni prima di poterla usare. Come suggerisce il nome, utilizzo non è supportato dai canali di supporto ufficiale.

## <a name="setting-the-target-framework"></a>Impostazione del framework di destinazione

Per modificare il tipo di Framework di destinazione per un progetto xamarin. Mac, eseguire le operazioni seguenti:

1. Aprire il progetto Xamarin.Mac in Visual Studio per Mac.
2. In **Esplora soluzioni** fare doppio clic sul file di progetto per aprire la finestra di dialogo **Opzioni progetto**.
3. Dal **generali** scheda, selezionare il tipo di **Framework di destinazione** che si adatta alle esigenze dell'applicazione:

    [![Usa finestra di dialogo Opzioni progetto per scegliere un framework di destinazione](target-framework-images/select-target-full.png "Usa finestra di dialogo Opzioni progetto per scegliere un framework di destinazione")](target-framework-images/select-target-full-large.png#lightbox)

4. Fare clic sul pulsante **OK** per salvare le modifiche.

È consigliabile **Pulisci** e quindi **ricompilare** il progetto xamarin. Mac dopo aver cambiato il tipo di Framework di destinazione.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato brevemente i diversi tipi di Framework di destinazione (librerie di classi di Base) disponibile per un'applicazione xamarin. Mac e quando usare ogni tipo di framework.


## <a name="related-links"></a>Collegamenti correlati

- [iOS e Mac condivisione del codice](~/cross-platform/macios/index.md)
- [API unificata](~/cross-platform/macios/unified/index.md)
- [Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)
- [Assembly](~/cross-platform/internals/available-assemblies.md)
- [L'aggiornamento di App Mac esistenti](~/cross-platform/macios/unified/updating-mac-apps.md)
