---
title: Xamarin.FormsShell
description: In questa guida viene illustrato come utilizzare la Xamarin.Forms Shell, che riduce la complessità delle Xamarin.Forms applicazioni fornendo le funzionalità di base richieste dalla maggior parte delle applicazioni.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0acf00d85c2bfb823ec1cfba099179bb6743117c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138749"
---
# <a name="xamarinforms-shell"></a>Xamarin.FormsShell

## <a name="introduction"></a>[Introduzione](introduction.md)

Xamarin.FormsShell riduce la complessità dello sviluppo di applicazioni per dispositivi mobili fornendo le funzionalità fondamentali necessarie per la maggior parte delle applicazioni per dispositivi mobili. Queste funzionalità includono un'esperienza utente di navigazione comune, uno schema di navigazione basato su URI e un gestore di ricerca integrato.

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[Creare un' Xamarin.Forms applicazione shell](create.md)

Il processo per la creazione di un' Xamarin.Forms applicazione shell consiste nel creare un file XAML che sottoclassa la `Shell` classe, impostare la `MainPage` proprietà della classe dell'applicazione sull' `App` oggetto sottoclassato `Shell` e quindi descrivere la gerarchia visiva dell'applicazione nella classe sottoclassata `Shell` .

## <a name="flyout"></a>[Flyout](flyout.md)

Il riquadro a comparsa è il menu radice per un'applicazione shell ed è accessibile attraverso un'icona o tramite scorrimento rapido dal lato dello schermo. Il riquadro a comparsa è costituito da un'intestazione facoltativa, elementi del riquadro a comparsa e voci di menu facoltative.

## <a name="tabs"></a>[Schede](tabs.md)

Dopo il riquadro a comparsa, il livello di navigazione successivo in un'applicazione shell è la barra delle schede inferiore. In alternativa, il modello di spostamento per un'applicazione può iniziare con le schede nella parte inferiore e non usare un riquadro a comparsa. In entrambi i casi, quando una scheda inferiore contiene più di una pagina, è possibile spostarsi tra le pagine tramite le schede superiori.

## <a name="page-configuration"></a>[Configurazione della pagina](configuration.md)

La `Shell` classe definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle Xamarin.Forms applicazioni della shell. Ciò include l'impostazione dei colori delle pagine, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle viste nella barra di spostamento.

## <a name="navigation"></a>[Navigazione](navigation.md)

Le applicazioni shell possono utilizzare uno schema di navigazione basato su URI che usa le route per il passaggio a qualsiasi pagina nell'applicazione, senza dover seguire una gerarchia di navigazione specifica.

## <a name="search"></a>[Ricerca](search.md)

Le applicazioni shell possono usare la funzionalità di ricerca integrata fornita da una casella di ricerca che è possibile aggiungere nella parte superiore di ogni pagina.

## <a name="lifecycle"></a>[Ciclo di vita](lifecycle.md)

Le applicazioni shell rispettano il ciclo di vita Xamarin.Forms e un `Appearing` evento viene generato quando una pagina sta per essere visualizzata sullo schermo e `Disappearing` viene generato un evento quando una pagina sta per scomparire dalla schermata.

## <a name="custom-renderers"></a>[Renderer personalizzati](customrenderers.md)

Le applicazioni shell sono estremamente personalizzabili tramite le proprietà e i metodi esposti dalle diverse classi della shell. Tuttavia, è anche possibile creare un renderer personalizzato della shell quando sono necessarie personalizzazioni specifiche della piattaforma più sofisticate.
