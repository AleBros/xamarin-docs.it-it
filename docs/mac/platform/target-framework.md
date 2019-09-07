---
title: Framework di destinazione per Novell. Mac
description: Questo articolo illustra i Framework di destinazione (librerie di classi base) disponibili per Novell. Mac e le implicazioni dell'uso nel progetto Novell. Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: a612c2c23ceff13ea1d602465573514547628e55
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769798"
---
# <a name="target-framework-for-xamarinmac"></a>Framework di destinazione per Novell. Mac

_Questo articolo illustra i Framework di destinazione (librerie di classi base) disponibili per Novell. Mac e le implicazioni dell'uso nel progetto Novell. Mac._

![Opzioni del Framework di destinazione per Novell. Mac](target-framework-images/select-target.png "Opzioni del Framework di destinazione per Novell. Mac")

## <a name="background"></a>Sfondo

Ogni programma o libreria .NET dipende dalla funzionalità fornita dalla libreria di classi base (BCL). Questa BCL include assembly quali mscorlib, System, System .NET. http e System. XML che forniscono la funzionalità comune incorporata in tutti i linguaggi .NET.

Nel corso degli anni sono state sviluppate più versioni diverse di questa BCL, ottimizzate per diversi casi d'uso. La BCL "desktop" include un set più completo di librerie che potrebbero essere troppo pesanti per altri casi d'uso, mentre per i dispositivi mobili è necessario garantire la sicurezza delle API per il collegamento, che rimuove il codice inutilizzato per ridurre il footprint dell'applicazione.

Una delle ripercussioni più importanti di questi diversi framework di destinazione è che tutti gli assembly in un determinato programma *devono* avere come destinazione gli assembly BCL compatibili. In caso contrario, è possibile che siano presenti due assembly collegati a versioni diverse di **System. dll** che non accettano la firma di un tipo specificato. Una libreria condivisa può essere destinata a [.NET standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), ovvero al subset comune dei framework di destinazione o a un Framework di destinazione specifico.

Per Novell. Mac sono disponibili tre opzioni del Framework di destinazione, ognuna con vantaggi e compromessi diversi:

- **Moderna** (chiamato mobile nella documentazione precedente): un subset molto simile a quello di Powers Novell. iOS, altamente ottimizzato per le prestazioni e le dimensioni. Questo framework di destinazione è sicuro dal linker, quindi questi progetti possono avere un footprint finale drasticamente ridotto rimuovendo il codice inutilizzato.

- **Completo** (chiamato XM 4,5 nella documentazione precedente): un subset molto simile alla BCL "desktop", con alcune piccole rimozioni. Poiché il Framework di destinazione è quasi identico a Net45 (e versioni successive), può utilizzare facilmente molti NuGet che non forniscono netstandard2 o compilazioni Novell. Mac specifiche. Tuttavia, a causa dell'utilizzo di System. Configuration, non è compatibile con il collegamento.

- Non **supportato** (chiamato sistema nella documentazione precedente): invece di collegarsi a una BCL fornita da Novell. Mac, usare il sistema corrente installato mono. Questo fornisce il set completo di assembly, inclusi alcuni noti come problematici (System. Drawing, ad esempio). Questa opzione esiste solo con una "ultima risorsa" ed è consigliabile esaurire altre opzioni prima di usarla. Come suggerisce il nome, l'utilizzo non è supportato dai canali di supporto ufficiali.

## <a name="setting-the-target-framework"></a>Impostazione del Framework di destinazione

Per passare al tipo di Framework di destinazione per un progetto Novell. Mac, procedere come segue:

1. Aprire il progetto Xamarin.Mac in Visual Studio per Mac.
2. In **Esplora soluzioni** fare doppio clic sul file di progetto per aprire la finestra di dialogo **Opzioni progetto**.
3. Dalla scheda **generale** selezionare il tipo di Framework di **destinazione** che soddisfa le esigenze dell'applicazione:

    [![Uso della finestra delle opzioni del progetto per scegliere un Framework di destinazione](target-framework-images/select-target-full.png "Uso della finestra delle opzioni del progetto per scegliere un Framework di destinazione")](target-framework-images/select-target-full-large.png#lightbox)

4. Fare clic sul pulsante **OK** per salvare le modifiche.

È necessario **pulire** e quindi **ricompilare** il progetto Novell. Mac dopo aver cambiato il tipo di Framework di destinazione.

## <a name="summary"></a>Riepilogo

Questo articolo ha brevemente trattato i diversi tipi di Framework di destinazione (librerie di classi base) disponibili per un'applicazione Novell. Mac e quando deve essere usato ogni tipo di Framework.

## <a name="related-links"></a>Collegamenti correlati

- [condivisione di codice iOS e Mac](~/cross-platform/macios/index.md)
- [API unificata](~/cross-platform/macios/unified/index.md)
- [Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)
- [Assembly](~/cross-platform/internals/available-assemblies.md)
- [Aggiornamento delle app Mac esistenti](~/cross-platform/macios/unified/updating-mac-apps.md)
