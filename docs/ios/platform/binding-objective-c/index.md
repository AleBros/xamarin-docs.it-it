---
title: Associazione di librerie iOS
description: In questo documento viene descritto come C# creare binding a codice Objective-C, rendendo possibile l'utilizzo di librerie native e CocoaPods in un'applicazione Xamarin.iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 61d1adfc997b34302f1f89f56653af906ca90135
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022216"
---
# <a name="binding-ios-libraries"></a>Associazione di librerie iOS

Seguire questi collegamenti per informazioni sull'associazione di librerie Objective-C e CocoaPods per Xamarin.iOS e Xamarin.Mac:

- [**Panoramica**](~/cross-platform/macios/binding/overview.md) -
  descrive il funzionamento dell'associazione.
- [**Binding di librerie Objective-c**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  istruzioni su come associare le librerie Objective-c per l'uso nei progetti Xamarin.
- [**Guida di riferimento**](~/cross-platform/macios/binding/binding-types-reference.md) per la definizione dei tipi -
  descrive tutti gli attributi disponibili per l'associazione tra autori e il processo di generazione del binding.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objective Sharpie è uno strumento da riga di comando che consente di avviare il primo passaggio di un'associazione.
Funziona analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica nella [definizione di binding](~/cross-platform/macios/binding/objective-c-libraries.md) (un processo che altrimenti viene eseguito manualmente). L'obiettivo Sharpie non crea un'associazione autonomamente, ma può essere utile per iniziare.

Objective Sharpie 3,0 ha introdotto la possibilità di associare direttamente Cocoapods.

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Procedura dettagliata: associazione di una libreria Objective-C di iOS](walkthrough.md)

Questa pagina fornisce una procedura dettagliata per la creazione di un progetto di associazione iOS usando il progetto open source [**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker) Objective-C come esempio. La libreria **InfColorPicker** offre un controller di visualizzazione riutilizzabile che consente all'utente di selezionare un colore in base alla rappresentazione HSB, rendendo la selezione di colore più intuitiva.
L'obiettivo Sharpie verrà usato per semplificare il processo di associazione.

## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**Associazioni iOS in C/C++ video**

## <a name="related-links"></a>Collegamenti correlati

- [Binding di Objective-C](~/cross-platform/macios/binding/index.md)
- [Binding Mac](~/mac/platform/binding.md)
