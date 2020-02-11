---
title: Riferimenti nativi progetti iOS, Mac e bindings
description: I riferimenti nativi offrono la possibilità di incorporare un Framework nativo in un progetto Xamarin.iOS, Xamarin.Mac o di binding.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: b3adfac067964e0a0f169b5d8f8860f34deffe62
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015610"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Riferimenti nativi nei progetti iOS, Mac e bindings

_I riferimenti nativi offrono la possibilità di incorporare un Framework nativo in un progetto Xamarin.iOS o Xamarin.Mac o in un progetto di associazione._

Poiché iOS 8,0 è stato possibile creare un Framework incorporato per condividere il codice tra le estensioni dell'app e l'app principale in Xcode. Utilizzando la funzionalità di riferimento nativo, sarà possibile utilizzare questi Framework incorporati (creati con Xcode) in Xamarin.iOS.

> [!IMPORTANT]
> Non sarà possibile creare Framework incorporati da qualsiasi tipo di progetto Xamarin.iOS o Xamarin.Mac, i riferimenti nativi consentono solo il consumo di framework nativi (Objective-C) esistenti.

<a name="Terminology" />

## <a name="terminology"></a>Terminologia

In iOS 8 (e versioni successive) i **Framework incorporati** possono essere incorporati in modo statico e collegato in modo dinamico. Per distribuirle correttamente, è necessario inserirle in Framework "Fat" che includono tutte le _sezioni_ per ogni architettura del dispositivo che si vuole supportare con l'app.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Framework statici e dinamici

I **Framework statici** sono collegati in fase di compilazione, in cui i **Framework dinamici** sono collegati in fase di esecuzione e possono essere modificati senza ricollegare. Se è stato usato un Framework di terze parti prima di iOS 8, si stava usando un **Framework statico** compilato nell'app. Per altri dettagli, vedere la documentazione relativa alla [programmazione della libreria dinamica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) di Apple.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Framework incorporati rispetto a Framework di sistema

I **Framework incorporati** sono inclusi nel bundle di app e sono accessibili solo all'app specifica tramite la relativa sandbox. I **Framework di sistema** vengono archiviati a livello di sistema operativo e sono disponibili per tutte le app nel dispositivo. Attualmente, solo Apple è in grado di creare Framework a livello di sistema operativo.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Framework Thin rispetto a FAT

I **Framework sottili** contengono solo il codice compilato per un'architettura di sistema specifica in cui i **Framework Fat** contengono codice per più architetture. Ogni codebase specifica dell'architettura compilata in un Framework viene definita _sezione_. Se, ad esempio, avessimo un Framework compilato per le due architetture del simulatore iOS (i386 e x86_64), conterrebbe due sezioni.

Se si è provato a distribuire questo framework di esempio con l'app, l'esecuzione viene eseguita correttamente nel simulatore, ma si verifica un errore nel dispositivo perché il Framework non contiene sezioni specifiche del codice per un dispositivo iOS. Per assicurarsi che un Framework funzioni in tutte le istanze, è necessario includere anche sezioni specifiche del dispositivo, ad esempio ARM64, ARMv7 e armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Utilizzo di Framework incorporati

È necessario completare due passaggi per lavorare con i Framework incorporati in un'app Xamarin.iOS o Xamarin.Mac: creazione di un Framework FAT e incorporamento del Framework.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Creazione di un Framework Fat

Come indicato in precedenza, per poter usare un Framework incorporato nell'app, deve essere un Framework FAT che include tutte le sezioni delle architetture di sistema per i dispositivi in cui verrà eseguita l'app.

Quando il Framework e l'app consumer si trovano nello stesso progetto Xcode, questo non è un problema perché Xcode creerà sia il Framework che l'app usando le stesse impostazioni di compilazione. Poiché le app Xamarin non sono in grado di creare Framework incorporati, non è possibile usare questa tecnica.

Per risolvere questo problema, è possibile usare lo strumento da riga di comando `lipo` per unire due o più Framework in un unico framework Fat contenente tutte le sezioni necessarie. Per ulteriori informazioni sull'utilizzo del comando `lipo`, consultare la documentazione relativa al [collegamento di librerie native](~/ios/platform/native-interop.md) .

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Incorporamento di un Framework

Il passaggio seguente è necessario per incorporare un Framework in un progetto Xamarin.iOS o Xamarin.Mac usando i riferimenti nativi:

1. Creare una nuova o aprire un progetto Xamarin.iOS, Xamarin.Mac o di binding esistente.
2. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **Aggiungi riferimento nativo**: 

    [![](native-references-images/ref01.png "In the Solution Explorer, right-click on the project name and select Add Native Reference")](native-references-images/ref01.png#lightbox)
3. Nella finestra di dialogo **Apri** selezionare il nome del Framework nativo che si vuole incorporare e fare clic sul pulsante **Apri** : 

    [![](native-references-images/ref02.png "Select the name of the Native Framework to embed and click the Open button")](native-references-images/ref02.png#lightbox)
4. Il Framework verrà aggiunto all'albero del progetto: 

    [![](native-references-images/ref03.png "The framework will be added to the projects tree")](native-references-images/ref03.png#lightbox)

Quando il progetto viene compilato, il Framework nativo verrà incorporato nel bundle dell'app.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Estensioni di app e Framework incorporati

Xamarin.iOS internamente può sfruttare questa funzionalità per eseguire il collegamento al runtime di mono come Framework (quando la destinazione di distribuzione è > = iOS 8,0), riducendo in modo significativo le dimensioni dell'app per le app con estensioni (poiché il runtime di mono verrà incluso una sola volta per intero bundle dell'app, anziché una volta per l'app contenitore e una volta per ogni estensione.

Le estensioni si collegano al runtime di mono come Framework, perché tutte le estensioni richiedono iOS 8,0.

App che non dispongono di estensioni e app destinate a iOS 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in modo dettagliato l'incorporamento di un Framework nativo in un'applicazione Xamarin.iOS o Xamarin.Mac.
