---
title: Associazione di librerie iOS
description: Questo documento descrive come creare binding C# per il codice Objective-C, rendendo possibile l'utilizzo di librerie native e CocoaPods in un'applicazione Novell. iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1e0342a41587b7479381ad763790227aba2ef414
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853083"
---
# <a name="binding-ios-libraries"></a>Associazione di librerie iOS

> [!IMPORTANT]
> Stiamo attualmente analizzando l'utilizzo dell'associazione personalizzata nella piattaforma Novell. Segui [**questo sondaggio**](https://www.surveymonkey.com/r/KKBHNLT) per informare le attività di sviluppo future.

Seguire questi collegamenti per informazioni sull'associazione di librerie Objective-C e CocoaPods per Novell. iOS e Novell. Mac:

- [**Panoramica**](~/cross-platform/macios/binding/overview.md) -
   di Viene descritto il funzionamento dell'associazione.
- [**Binding di librerie**](~/cross-platform/macios/binding/objective-c-libraries.md) -
   Objective-C Istruzioni su come associare le librerie Objective-C per l'uso nei progetti Novell.
- Guida di riferimento per la [**definizione del tipo**](~/cross-platform/macios/binding/binding-types-reference.md) -
   Descrive tutti gli attributi disponibili per associare gli autori al processo di generazione del binding.

## <a name="objective-sharpie"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objective Sharpie è uno strumento da riga di comando che consente di avviare il primo passaggio di un'associazione.
Funziona analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica nella [definizione di binding](~/cross-platform/macios/binding/objective-c-libraries.md) (un processo che altrimenti viene eseguito manualmente). L'obiettivo Sharpie non crea un'associazione autonomamente, ma può essere utile per iniziare.

Objective Sharpie 3,0 ha introdotto la possibilità di associare direttamente Cocoapods.

## <a name="walkthrough---binding-an-ios-objective-c-library"></a>[Procedura dettagliata: associazione di una libreria Objective-C di iOS](walkthrough.md)

Questa pagina fornisce una procedura dettagliata per la creazione di un progetto di associazione iOS usando il progetto open source [**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker) Objective-C come esempio. La libreria **InfColorPicker** offre un controller di visualizzazione riutilizzabile che consente all'utente di selezionare un colore in base alla rappresentazione HSB, rendendo la selezione di colore più intuitiva.
L'obiettivo Sharpie verrà usato per semplificare il processo di associazione.

## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**Associazioni iOS nel video C/C++**

## <a name="related-links"></a>Collegamenti correlati

- [Binding di Objective-C](~/cross-platform/macios/binding/index.md)
- [Binding Mac](~/mac/platform/binding.md)
