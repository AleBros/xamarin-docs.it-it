---
title: IOS, Mac e i progetti di binding riferimenti nativi
description: Riferimenti nativi ti offre la possibilità di incorporare un framework nativo in un progetto di associazione, xamarin. Mac o xamarin. IOS.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3a497d0bb4674014b8063cb1fbc91eec6e7ae5ea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61201543"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Riferimenti nativi in iOS, Mac e i progetti di binding

_Riferimenti nativi ti offre la possibilità di incorporare un framework nativo in un progetto xamarin. IOS o xamarin. Mac o un progetto di associazione._

Poiché iOS 8.0 è stato possibile creare un framework incorporato per condividere codice tra le estensioni app e l'app principale in Xcode. Uso della funzionalità di riferimento nativo sarà possibile utilizzare questi Framework incorporati (creati con Xcode) in xamarin. IOS.
 
> [!IMPORTANT]
> Riferimenti nativi non sarà possibile creare Framework incorporati da qualsiasi tipo di progetti xamarin. IOS o xamarin. Mac, consentire solo per l'utilizzo del Framework (Objective-C) native esistenti.

<a name="Terminology" />

## <a name="terminology"></a>Terminologia

In iOS 8 e versioni successive, **Framework incorporati** possono essere entrambi incorporato Framework collegato in modo statico e dinamico collegato. Per distribuirli correttamente, è necessario renderli nel Framework "fat" che inclusi tutti i loro _sezioni_ per ogni architettura di dispositivo che si desidera supportare con l'app.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Porte statiche e Framework dinamici

**Framework statici** collegate in fase di compilazione in cui **dinamica Framework** sono collegati in fase di esecuzione e, pertanto, può essere modificato senza collegare nuovamente. Se si usa qualsiasi Framework di parti 3rd precedenti a iOS 8, si usava una **statici Framework** che è stato compilato nell'app. Vedere di Apple [programmazione libreria dinamica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) informazioni più dettagliate.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Visual Studio incorporato. Framework di sistema

**Framework incorporati** sono inclusi nel bundle dell'App e accessibili solo all'App specifiche tramite la sandbox. **Framework di sistema** vengono archiviate a livello di sistema operativo e sono disponibili per tutte le app nel dispositivo. Attualmente, solo Apple ha la possibilità di creare Framework a livello di sistema operativo.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Visual Studio thin. Framework FAT

**Thin Frameworks** contengono solo il codice compilato per un'architettura di sistema specifico in cui **Fat Framework** contengono codice per più architetture. Ogni codebase specifici dell'architettura compilato in un Framework viene definito un _Slice_. Quindi, ad esempio, se avessimo un Framework che è stato compilato per due iOS architetture del simulatore (i386 e X86_64), conterrebbe due sezioni.

Se si è provato a distribuire in questo esempio Framework con l'app, verrà eseguito correttamente nel simulatore, ma non venga completato sul dispositivo perché il Framework non contiene altre sezioni di codice specifico per un dispositivo iOS. Per garantire che un Framework funziona in tutte le istanze, sarebbe inoltre necessario includere le sezioni specifiche del dispositivo, ad esempio arm64, armv7 e armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Utilizzo di Framework incorporati

Esistono due passaggi che devono essere completati per lavorare con i framework incorporato in un'app xamarin. IOS o xamarin. Mac: Creazione di un Framework Fat e incorporando il Framework.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Creazione di un Framework Fat

Come indicato in precedenza, per poter utilizzare un Framework incorporato nell'app, deve essere un Framework Fat che include tutte le architetture di sistema sezioni per i dispositivi che verrà eseguita l'app.

Quando il Framework e la dispendiosa in termini di app si trovano nello stesso progetto Xcode, non è un problema perché Xcode creerà il Framework sia l'App usando le stesse impostazioni di compilazione. Poiché le app Xamarin non è possibile creare Framework incorporati, questa tecnica non può essere usata.

Per risolvere questo problema, il `lipo` strumento della riga di comando può essere usato per unire due o più Framework in un unico Framework Fat contenente tutte le sezioni necessarie. Per altre informazioni sull'uso del `lipo` comando, vedere la [collegare librerie Native](~/ios/platform/native-interop.md) documentazione.

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Incorporamento di un Framework

Per incorporare un framework in un progetto xamarin. IOS o xamarin. Mac usando i riferimenti nativi sono necessari il passaggio seguente:

1. Creare un nuovo o aprire un progetto xamarin. IOS, xamarin. Mac o associazione esistente.
2. Nel **Esplora soluzioni**, fare doppio clic sul nome del progetto e selezionare **Add** > **Aggiungi riferimento nativo**: 

    [![](native-references-images/ref01.png "In Esplora soluzioni, fare doppio clic sul nome del progetto e selezionare Aggiungi riferimento nativo")](native-references-images/ref01.png#lightbox)
3. Dal **aperto** finestra di dialogo, selezionare il nome del Framework nativo che si desidera incorporare e fare clic sui **Open** pulsante: 

    [![](native-references-images/ref02.png "Selezionare il nome del Framework nativo per incorporare e fare clic sul pulsante Apri")](native-references-images/ref02.png#lightbox)
4. Il framework verrà aggiunto alla struttura ad albero del progetto: 

    [![](native-references-images/ref03.png "Il framework verrà aggiunto all'albero di progetti")](native-references-images/ref03.png#lightbox)

Quando viene compilato il progetto, il Framework nativo verrà incorporato nel bundle dell'App.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Le estensioni App e i framework incorporati

Internamente xamarin. IOS possono sfruttare i vantaggi di questa funzionalità per il collegamento con il runtime di Mono come framework (quando la destinazione di distribuzione è > = iOS 8.0), riducendo in tal modo le dimensioni di app in modo significativo per le app con le estensioni (dal momento che il runtime di Mono verrà incluso una sola volta per l'intero bundle dell'app, anziché una volta per l'app contenitore e una volta per ogni estensione).

Le estensioni collegato con il runtime di Mono come framework, perché tutte le estensioni richiedono iOS 8.0.

App che non hanno le estensioni e le app iOS di destinazione 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di incorporamento di un Framework nativo in un'applicazione xamarin. IOS o xamarin. Mac.

