---
title: Riferimenti nativi
description: I riferimenti nativi offre la possibilità di incorporare un Framework nativo in un progetto xamarin. IOS o Xamarin.Mac o un progetto di associazione.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: c56e392420debb21998363cfffa288aec51691ea
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="native-references"></a>Riferimenti nativi

_I riferimenti nativi offre la possibilità di incorporare un Framework nativo in un progetto xamarin. IOS o Xamarin.Mac o un progetto di associazione._


Da iOS 8.0 è stato possibile creare un framework incorporato per condividere il codice tra le estensioni app e l'app principale in Xcode. Utilizza la funzionalità nativa riferimento sarà possibile utilizzare questi Framework incorporati (creati con Xcode) in xamarin. IOS.
 
> [!IMPORTANT]
> I riferimenti nativi non sarà possibile creare un framework incorporato da qualsiasi tipo di xamarin. IOS o Xamarin.Mac progetti, consentire solo per l'utilizzo del Framework (Objective-C) native esistenti.




<a name="Terminology" />

## <a name="terminology"></a>Terminologia

In iOS 8 (e versioni successive) **Framework incorporato** può essere incorporata Framework collegato staticamente e collegato in modo dinamico. Per distribuirle in modo corretto, è necessario effettuare tali in Framework "fat" inclusi tutti i relativi _sezioni_ per ogni architettura di dispositivo che si desidera supportare con l'app.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Porte statiche e. Framework Dynamic

**Framework statico** collegati in fase di compilazione in cui **Framework dinamica** sono collegati in fase di esecuzione e, pertanto, può essere modificato senza collegamento nuovamente. Se è stato usato un Framework di terze parti 3rd prima iOS 8, si utilizzava un **Framework statico** che è stato compilato nell'app. Vedere Apple [programmazione libreria dinamica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) documentazione per maggiori dettagli.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Visual Studio incorporato. Framework di sistema

**Framework incorporato** sono inclusi nel bundle dell'App e accessibili solo all'App specifiche tramite la sandbox. **Framework di sistema** archiviati a livello di sistema operativo e sono disponibili per tutte le app nel dispositivo. Attualmente, solo Apple ha la possibilità di creare il framework di livello del sistema operativo.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Visual Studio thin. Framework FAT

**Thin Framework** contengono solo il codice compilato per un'architettura di sistema specifico in cui **Framework Fat** contengono codice per diverse architetture. Ogni codebase specifici per l'architettura compilato in un Framework viene definito un _sezione_. In tal caso, ad esempio, se si è verificato un Framework che è stato compilato per due iOS architetture simulatore (i386 e X86_64), sarebbe contiene due sezioni.

Se si tenta di distribuire in questo esempio Framework con l'app, verrebbe eseguito correttamente, nel simulatore, ma non venga completato sul dispositivo perché il Framework non contiene le sezioni di codice specifico per un dispositivo iOS. Per garantire che un Framework funzionerà in tutte le istanze, inoltre necessario includere sezioni specifiche del dispositivo, ad esempio arm64, armv7 e armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Utilizzo di un framework incorporato

Sono disponibili due passaggi che devono essere completati per lavorare con un framework incorporato in un'app xamarin o Xamarin.Mac: creazione di un Framework Fat e il Framework di incorporamento.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Creazione di un Framework Fat

Come descritto in precedenza, per essere in grado di utilizzare un Framework incorporato nell'app, è necessario un Framework Fat che include tutte le architetture di sistema sezioni per i dispositivi in cui verrà eseguita l'app.

Quando il Framework e l'applicazione consumer sono nello stesso progetto in Xcode, non è un problema come Xcode compilerà il Framework sia l'App usando le stesse impostazioni di compilazione. Poiché App Xamarin non è possibile creare un framework incorporato, questa tecnica non può essere utilizzata.

Per risolvere questo problema, il `lipo` strumento della riga di comando può essere utilizzato per unire due o più Framework in un Framework Fat contenente tutte le sezioni necessarie. Per ulteriori informazioni sull'utilizzo di `lipo` , vedere la [collegamento librerie Native](~/ios/platform/native-interop.md) documentazione.

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Incorporamento di un Framework

Eseguire i passaggi sono necessari per incorporare un framework in un progetto xamarin. IOS o Xamarin.Mac utilizzando i riferimenti nativi:

1. Creare un nuovo o aprire un progetto xamarin. IOS, Xamarin.Mac o associazione esistente.
2. Nel **Esplora**del mouse sul nome del progetto e scegliere **Aggiungi** > **Aggiungi riferimento nativo**: 

    [![](native-references-images/ref01.png "In Esplora soluzioni, fare clic sul nome del progetto e selezionare Aggiungi riferimento nativo")](native-references-images/ref01.png#lightbox)
3. Dal **aprire** finestra di dialogo, selezionare il nome di Framework nativo che si desidera incorporare e fare clic su di **aprire** pulsante: 

    [![](native-references-images/ref02.png "Selezionare il nome del Framework nativo per incorporare e fare clic sul pulsante aprirlo")](native-references-images/ref02.png#lightbox)
4. Il framework verrà aggiunte alla struttura ad albero del progetto: 

    [![](native-references-images/ref03.png "Il framework verrà aggiunto alla struttura di progetti")](native-references-images/ref03.png#lightbox)

Quando viene compilato il progetto, il Framework nativo verrà incorporato nel pacchetto dell'App.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Le estensioni e Framework incorporato

Internamente xamarin possono avvalersi di questa funzionalità per il collegamento con il runtime come un framework Mono (quando la destinazione di distribuzione è > = iOS 8.0), riducendo le dimensioni di app in modo significativo per le app con le estensioni (dal momento che il runtime Mono verrà incluso una sola volta per l'intero bundle dell'app, anziché una volta per l'applicazione contenitore e una volta per ogni estensione).

Estensioni verranno collegato con il runtime Mono come un framework, poiché tutte le estensioni richiedono iOS 8.0.

Applicazioni che non hanno estensioni e le app iOS di destinazione 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'incorporamento di un Framework nativo in un'applicazione di xamarin. IOS o Xamarin.Mac un'analisi approfondita.

