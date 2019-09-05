---
title: Associazione di librerie iOS
description: In questo documento viene descritto come C# creare binding a codice Objective-C, rendendo possibile l'utilizzo di librerie native e CocoaPods in un'applicazione Novell. iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 24203d8a3a4356fb4de08d132c164d9f2d19a0c9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291798"
---
# <a name="binding-ios-libraries"></a>Associazione di librerie iOS

Seguire questi collegamenti per informazioni sull'associazione di librerie Objective-C e CocoaPods per Novell. iOS e Novell. Mac:

- [**Panoramica**](~/cross-platform/macios/binding/overview.md) di -
  Viene descritto il funzionamento dell'associazione.
- [**Binding di librerie Objective-C**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  Istruzioni su come associare le librerie Objective-C per l'uso nei progetti Novell.
- [**Guida di riferimento**](~/cross-platform/macios/binding/binding-types-reference.md) per la definizione del tipo -
  Descrive tutti gli attributi disponibili per associare gli autori al processo di generazione del binding.

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
