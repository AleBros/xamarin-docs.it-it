---
title: Compilazione per dispositivi diversi in Xamarin.iOS
description: Questo documento descrive le varie opzioni di configurazione della build che possono essere usate per personalizzare una compilazione di Xamarin.iOS per dispositivi diversi.
ms.prod: xamarin
ms.assetid: 3B259248-887E-3E4F-E09C-7AD28C2A8CEE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 1b3bc6874a8584b60b15d1d373ad97fde565dfc4
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527124"
---
# <a name="compiling-for-different-devices-in-xamarinios"></a>Compilazione per dispositivi diversi in Xamarin.iOS

Le proprietà di compilazione del file eseguibile possono essere configurate dalla pagina delle proprietà **Compilazione iOS** del progetto, a cui si accede facendo clic con il pulsante destro del mouse sul nome del progetto e scegliendo **Opzioni > Compilazione iOS** in Visual Studio per Mac e **Proprietà** in Visual Studio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)


[![](compiling-for-different-devices-images/image1.png "Pagina delle proprietà Compilazione iOS del progetto")](compiling-for-different-devices-images/image1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](compiling-for-different-devices-images/image1a.png "Pagina delle proprietà Compilazione iOS del progetto")](compiling-for-different-devices-images/image1a.png#lightbox)

-----

Oltre alle opzioni di configurazione disponibili nell'interfaccia utente, è anche possibile passare il proprio set di opzioni della riga di comando allo [strumento di compilazione Xamarin.iOS (mtouch)](~/ios/deploy-test/mtouch.md).

[http://iossupportmatrix.com/](http://iossupportmatrix.com/) è una risorsa utile per verificare di includere tutti i dispositivi, le architetture e le versioni di iOS necessarie.

 <a name="SDK_Options" />


## <a name="sdk-options"></a>Opzioni SDK

Visual Studio per Mac consente di configurare due importanti proprietà relative all'SDK: la versione dell'SDK di iOS usata per compilare il software e la destinazione della distribuzione (o la versione minima richiesta di iOS).

L'opzione **Versione SDK** di iOS consente di usare versioni diverse di un SDK pubblicato da Apple, per indirizzare Xamarin.iOS ai compilatori, ai linker e alle librerie a cui deve fare riferimento durante la compilazione. 

L'impostazione **Destinazione distribuzione** viene usata per selezionare la versione minima richiesta del sistema operativo in cui verrà eseguita l'applicazione, che viene impostata nel file Info.plist del progetto. È consigliabile selezionare la versione minima con tutte le API necessarie per eseguire l'applicazione.

In generale, l'API Xamarin.iOS espone tutti i metodi disponibili nella versione più recente dell'SDK e, se necessario, vengono fornite pratiche proprietà che consentono di rilevare se la funzionalità è disponibile in fase di esecuzione. `UIDevice.UserInterfaceIdiom` e `UIDevice.IsMultitaskingSupported`, ad esempio, funzionano sempre in Xamarin.iOS e tutte le operazioni vengono eseguite in background.

 <a name="Linking" />


## <a name="linking"></a>Collegamento

Per altre informazioni su come il [linker](~/ios/deploy-test/linker.md) consente di ridurre le dimensioni dei file eseguibili e su come usarlo in modo efficiente, vedere la pagina dedicata.

 <a name="Code_Generation_Engine" />


## <a name="code-generation-engine"></a>Motore di generazione del codice

A partire da Xamarin.iOS 4.0, sono disponibili due back-end di generazione del codice per Xamarin.iOS. Il normale motore di generazione del codice Mono e uno basato sul compilatore ottimizzatore LLVM. Ogni motore presenta vantaggi e svantaggi.

Durante il processo di sviluppo, si userà in genere il motore di generazione del codice Mono perché consente di eseguire l'iterazione rapidamente. Per le build di rilascio e la distribuzione nell'App Store, si passerà al motore di generazione del codice LLVM.

Il motore back-end di ottimizzazione LLVM genera codice più veloce e sintetico del motore Mono, ma implica tempi di compilazione lunghi.

È possibile abilitarli dalle opzioni di Compilazione iOS in Visual Studio per Mac o Visual Studio.

[![](compiling-for-different-devices-images/image2.png "Abilitazione di LLVM")](compiling-for-different-devices-images/image2.png#lightbox)

[![](compiling-for-different-devices-images/image2a.png "Abilitazione di LLVM")](compiling-for-different-devices-images/image2a.png#lightbox)

 <a name="ARMV7_and_ARMV7s_support" />


## <a name="architecture-support"></a>Supporto delle architetture

<a name="armv6-discontinued" />

### <a name="armv6-xamarinios-discontinued-support-for-armv6-with-v810"></a>ARMv6 (Xamarin.iOS non supporta più ARMv6 con la versione 8.10)

- iPhone (originale), 3G
- iPod 1a, 2a generazione

### <a name="armv7"></a>ARMv7

- iPhone 3GS, 4, 4S
- iPad 1, 2, 3, Mini
- iPod 3a, 4a, 5a generazione

### <a name="armv7s"></a>ARMv7s

- iPhone 5
- iPhone 5c
- iPad 4

Se si specifica come destinazione solo il processore ARMv7s, il codice generato sarà leggermente più veloce, ma non verrà più eseguito in sistemi ARMv7 o ARMv6 a meno che non si compili un file binario FAT contenente più file eseguibili nel pacchetto.

### <a name="arm64-xamarinios-started-supporting-arm64-in-v86"></a>ARM64 (Xamarin.iOS ha iniziato a supportare ARM64 nella versione 8.6)

- iPhone 5s
- iPhone SE
- iPhone 6, 6 Plus
- iPhone 6s, 6s Plus
- iPhone 7, 7 Plus
- iPhone 8, 8 Plus
- iPhone X
- iPad Air
- iPad Air 2
- iPad Mini 2, 3, 4
- iPad Pro (tutti)

Si noti che tutte le compilazioni inviate all'App Store devono contenere il supporto a 64 bit. Si tratta di un requisito impostato da [Apple](https://developer.apple.com/news/?id=12172014b). Inoltre, iOS 11 supporta solo le applicazioni a 64 bit.

 <a name="ARM_Thumb_Support" />


### <a name="arm-thumb-2-support"></a>Supporto ARM per Thumb-2

Thumb è un set di istruzioni più compatto usato dai processori ARM. Abilitando il supporto di Thumb, è possibile ridurre le dimensioni del file eseguibile, rallentando tuttavia i tempi di esecuzione. Thumb è supportato in ARMv7 e ARMv7s.

 <a name="Conditional_framwork_useage" />


## <a name="conditional-framework-usage"></a>Utilizzo di framework condizionale

Se si vuole che il progetto sfrutti determinate funzionalità delle versioni più recenti di iOS, potrebbe essere necessario usare in modo condizionale alcuni nuovi framework. Un tipico esempio è il caso in cui si vuole usare iAd quando si esegue iOS 4.0 o versione successiva, ma si supportano ancora i dispositivi 3.x. A questo scopo, è necessario indicare a Xamarin.iOS che ci si vuole collegare al framework iAd "in modo debole". I binding deboli assicurano che il framework venga caricato solo on demand la prima volta che è necessaria una classe del framework.

A tale scopo, seguire questa procedura:

- Aprire **Opzioni progetto** e passare al riquadro **Compilazione iOS**.
- Aggiungere `'-gcc_flags "-weak_framework iAd"'` a **Opzioni aggiuntive** per ogni configurazione con cui si vuole stabilire un collegamento debole:


[![](compiling-for-different-devices-images/image3.png "Opzioni aggiuntive")](compiling-for-different-devices-images/image3.png#lightbox)


Sarà anche necessario evitare l'esecuzione dell'utilizzo dei tipi in versioni meno recenti di iOS in cui potrebbero non esistere. Esistono diversi metodi per farlo, uno dei quali è l'analisi di `UIDevice.CurrentDevice.SystemVersion`.



## <a name="related-links"></a>Collegamenti correlati

- [Linker](~/ios/deploy-test/linker.md)
- [External - iOS Support Matrix](http://iossupportmatrix.com/) (Esterno - Matrice di supporto iOS)
