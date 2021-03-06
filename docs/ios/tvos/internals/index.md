---
title: tvOS in Xamarin-elementi interni
description: Documenti che descrivono il funzionamento interno di tvOS in Xamarin, basato su Xamarin.iOS. Il contenuto del collegamento illustra gli assembly, i Framework di destinazione e i concetti relativi a iOS correlati.
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 4712b7b75e735da047d7f44f7c6c47f42b9ad7a8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030652"
---
# <a name="tvos-in-xamarin-internals"></a>tvOS in Xamarin-elementi interni 

## <a name="assembliesiostvosinternalsassembliesmd"></a>[Assembly](~/ios/tvos/internals/assemblies.md)

Elenco degli assembly supportati da Xamarin per le applicazioni Xamarin.tvOS.

## <a name="target-frameworksiostvosinternalsframeworksmd"></a>[Framework di destinazione](~/ios/tvos/internals/frameworks.md)

Questo articolo illustra i tipi di Framework di destinazione (librerie di classi base) disponibili in Xamarin.tvOS e le implicazioni della selezione di una destinazione specifica per l'applicazione Xamarin.tvOS.

## <a name="related-ios-articles"></a>Articoli iOS correlati

Gli articoli seguenti sono specifici di iOS ma rilevanti per tvOS (poiché tvOS 9 è un subset di iOS 9).

### <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[API unificata](~/cross-platform/macios/unified/index.md)

Introduce le nuove API unificate che consentono la condivisione di codice più semplice tra le codebase di Apple TV e iOS, nonché l'introduzione del supporto per le API a 64 bit e la compilazione a 64 bit.  

### <a name="api-designiosinternalsapi-designindexmd"></a>[Progettazione dell'API](~/ios/internals/api-design/index.md)

Vengono illustrati i principi di progettazione alla base dell'associazione API.

### <a name="limitationsiosinternalslimitationsmd"></a>[Limitazioni](~/ios/internals/limitations.md)

In questa sezione vengono illustrati i problemi e le limitazioni da tenere presenti per quanto riguarda Xamarin.iOS, molti dei quali sono applicabili a Xamarin.tvOS.

### <a name="linkeriosdeploy-testlinkermd"></a>[Linker](~/ios/deploy-test/linker.md)

Viene illustrato il funzionamento del linker per assicurare il pacchetto di applicazioni più piccolo possibile, nonché come modificare le impostazioni e l'utilizzo.

### <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[Localizzazione e internazionalizzazione](~/ios/app-fundamentals/localization/index.md)

Questa guida illustra l'aggiunta di codifiche a un'applicazione Xamarin.iOS per supportare l'internazionalizzazione.

### <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

Note e informazioni su mtouch.exe, lo strumento da riga di comando che compila il progetto in un'applicazione utilizzabile con iOS.

### <a name="linking-native-librariesiosplatformnative-interopmd"></a>[Collegamento di librerie native](~/ios/platform/native-interop.md)

Xamarin.iOS supporta il collegamento con le librerie C native e le librerie Objective-C. Questo documento illustra come collegare le librerie C native al progetto Xamarin.iOS. Per informazioni su come eseguire la stessa operazione per le librerie Objective-C, vedere l'&nbsp; [associazione di tipi Objective-c](~/ios/platform/binding-objective-c/index.md)&nbsp;documento.

## <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Selettori Objective-C](~/ios/internals/objective-c-selectors.md)

Note e utilizzo per chiamare direttamente i selettori Objective-C (metodi).

### <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

Informazioni e istruzioni sull'uso di System. Data per accedere al sistema di database SQLite incorporato.

### <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Note sull'uso del threading nelle applicazioni Xamarin.iOS.

### <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[Generazione di codice XIB](~/ios/internals/xib-code-generation.md)

In che modo Visual Studio per Mac si integra con Interface Builder di Xcode per consentire l'uso di Interface Builder per progettare l'interfaccia utente.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
