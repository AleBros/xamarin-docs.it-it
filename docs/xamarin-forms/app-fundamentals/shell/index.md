---
title: Xamarin.Forms Shell
description: Questa guida illustra come usare la shell Xamarin.Forms, che riduce la complessità delle applicazioni Xamarin.Forms offrendo le principali funzionalità richieste dalla maggior parte delle applicazioni.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.openlocfilehash: 20ac6ad748e7056f7f8037a73a95de66b9eae3b6
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888911"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

La shell Xamarin.Forms riduce la complessità dello sviluppo di applicazioni per dispositivi mobili offrendo le principali funzionalità richieste dalla maggior parte delle applicazioni per dispositivi mobili. Queste funzionalità includono un'esperienza utente di navigazione comune, uno schema di navigazione basato su URI e un gestore di ricerca integrato.

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[Creare un'applicazione shell Xamarin.Forms](create.md)

Il processo per la creazione di un'applicazione shell Xamarin.Forms prevede la creazione di un file XAML che definisce una sottoclasse della classe `Shell`, l'impostazione della proprietà `MainPage` della classe `App` dell'applicazione sull'oggetto `Shell` sottoclassato e quindi la descrizione della gerarchia visiva dell'applicazione nella classe `Shell` sottoclassata.

## <a name="flyoutflyoutmd"></a>[Riquadro a comparsa](flyout.md)

Il riquadro a comparsa è il menu radice per un'applicazione shell ed è accessibile attraverso un'icona o tramite scorrimento rapido dal lato dello schermo. Il riquadro a comparsa è costituito da un'intestazione facoltativa, elementi del riquadro a comparsa e voci di menu facoltative.

## <a name="tabstabsmd"></a>[Schede](tabs.md)

Dopo il riquadro a comparsa, il livello di navigazione successivo in un'applicazione shell è la barra delle schede inferiore. In alternativa, il modello di spostamento per un'applicazione può iniziare con le schede nella parte inferiore e non usare un riquadro a comparsa. In entrambi i casi, quando una scheda inferiore contiene più di una pagina, è possibile spostarsi tra le pagine tramite le schede superiori.

## <a name="page-configurationconfigurationmd"></a>[Configurazione della pagina](configuration.md)

La classe `Shell` definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle applicazioni shell Xamarin.Forms. Ciò include l'impostazione dei colori delle pagine, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle viste nella barra di spostamento.

## <a name="navigationnavigationmd"></a>[Navigazione](navigation.md)

Le applicazioni shell possono utilizzare uno schema di navigazione basato su URI che usa le route per il passaggio a qualsiasi pagina nell'applicazione, senza dover seguire una gerarchia di navigazione specifica.

## <a name="searchsearchmd"></a>[Ricerca](search.md)

Le applicazioni shell possono usare la funzionalità di ricerca integrata fornita da una casella di ricerca che è possibile aggiungere nella parte superiore di ogni pagina.

## <a name="lifecyclelifecyclemd"></a>[Ciclo di vita](lifecycle.md)

Le applicazioni shell rispettano il ciclo di vita di Xamarin.Forms. Quando una pagina sta per essere visualizzata sullo schermo, viene generato un evento `Appearing`. Quando invece una pagina sta per scomparire dallo schermo, viene generato un evento `Disappearing`.

## <a name="custom-rendererscustomrenderersmd"></a>[Renderer personalizzati](customrenderers.md)

Le applicazioni shell sono estremamente personalizzabili tramite le proprietà e i metodi esposti dalle diverse classi della shell. Tuttavia, è anche possibile creare un renderer personalizzato della shell quando sono necessarie personalizzazioni specifiche della piattaforma più sofisticate.
