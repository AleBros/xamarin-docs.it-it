---
title: Concetti avanzati e interni iOS
description: Questo argomento illustra la progettazione dell'API MonoTouch, gli assembly e le classi della libreria di classi base .NET e il modo in cui Visual Studio per Mac si integra con la Interface Builder di Xcode e la libreria di base di Apple.
ms.prod: xamarin
ms.assetid: 951713CD-D6AD-981C-A09E-4F2C98588D8B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 4157e73379be2adc7c92b8cdbc05c4cc4489daec
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022330"
---
# <a name="ios-advanced-concepts-and-internals"></a>Concetti avanzati e interni iOS

_Questo argomento illustra la progettazione dell'API MonoTouch, gli assembly e le classi della libreria di classi base .NET e il modo in cui Visual Studio per Mac si integra con la Interface Builder di Xcode e la libreria di base di Apple._

## <a name="api-designiosinternalsapi-designindexmd"></a>[Progettazione dell'API](~/ios/internals/api-design/index.md)

Vengono illustrati i principi di progettazione alla base dell'associazione API.

## <a name="available-assembliescross-platforminternalsavailable-assembliesmd"></a>[Assembly disponibili](~/cross-platform/internals/available-assemblies.md)

Elenca gli assembly e le classi disponibili dalla libreria di classi base .NET (BCL).

## <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[Generazione di codice XIB](~/ios/internals/xib-code-generation.md)

Viene inoltre illustrato il modo in cui Visual Studio per Mac e Xcode Interface Builder consentono di utilizzare Interface Builder per progettare l'interfaccia utente.

> [!IMPORTANT]
> In questo documento viene illustrata l'integrazione di Visual Studio per Mac solo con Interface Builder di Xcode. Per altre informazioni su iOS designer, vedere il documento di [iOS designer](~/ios/user-interface/designer/index.md) .

## <a name="ios-architectureiosinternalsarchitecturemd"></a>[Architettura iOS](~/ios/internals/architecture.md)

Le applicazioni Xamarin.iOS vengono eseguite nell'ambiente di esecuzione mono e usano la compilazione AOT (Full ahead of Time) C# per compilare il codice nel linguaggio assembly ARM. Questa guida esamina Xamarin.iOS a un livello basso

## <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Selettori Objective-C](~/ios/internals/objective-c-selectors.md)

Note e utilizzo per chiamare direttamente i selettori Objective-C (metodi).

## <a name="limitationslimitationsmd"></a>[Limitazioni](limitations.md)

Insidie e limitazioni da tenere presenti con Xamarin.iOS.
