---
title: Associazione iOS librerie
description: Come creare librerie native di iOS (e CocoaPods) accessibili in App Xamarin.
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3afe1a03299e600502d49b1db039af4c6642e131
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="binding-ios-libraries"></a>Associazione iOS librerie

_Come creare librerie native di iOS (e CocoaPods) accessibili in App Xamarin._

Vedere i collegamenti seguenti per informazioni sull'associazione di raccolte Objective-C e CocoaPods per xamarin. IOS e Xamarin.Mac:

- [**Panoramica** ](~/cross-platform/macios/binding/overview.md) -
  viene descritto il funzionamento dell'associazione.
- [**Associazione di raccolte Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  istruzioni su come associare le raccolte di Objective-C per l'utilizzo nei progetti di Xamarin.
- [**Guida di riferimento di definizione di tipo** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  descrive tutti gli attributi disponibili per gli autori delle associazioni per gestire il processo di generazione di associazione.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Obiettivo Sharpie è uno strumento da riga di comando per avviare il primo passaggio di un'associazione.
Analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica in cui funziona la [definizione di associazioni](~/cross-platform/macios/binding/objective-c-libraries.md) (un processo in caso contrario viene eseguito manualmente). Obiettivo Sharpie non crea un'associazione da sola, ma è possibile iniziare.

Obiettivo Sharpie 3.0 introdotta la possibilità di associare direttamente Cocoapods!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Procedura dettagliata: associazione di una libreria Objective-C per iOS](walkthrough.md)

Questa pagina fornisce una procedura dettagliata Creazione di un progetto di associazione iOS usando open source [ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) progetto Objective-C come esempio. Il **InfColorPicker** libreria fornisce un controller di visualizzazione riutilizzabili che consentono all'utente di selezionare un colore di base nella relativa rappresentazione HSB, rendendo più semplice la selezione dei colori.
Obiettivo Sharpie verrà utilizzato per facilitare il processo di associazione.



## <a name="related-links"></a>Collegamenti correlati

- [Binding di Objective-C](~/cross-platform/macios/binding/index.md)
- [Associazione Mac](~/mac/platform/binding.md)
