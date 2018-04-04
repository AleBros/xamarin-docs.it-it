---
title: Riepilogo del capitolo 9. Chiamate API specifiche della piattaforma
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a988c207185fa2305631be67bdd628de089d247e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Riepilogo del capitolo 9. Chiamate API specifiche della piattaforma

In alcuni casi è necessario eseguire il codice che varia in base alla piattaforma. In questo capitolo illustra le tecniche.

## <a name="preprocessing-in-the-shared-asset-project"></a>Nel progetto Asset condiviso di pre-elaborazione

Un progetto Asset condiviso di xamarin. Forms possono eseguire codice diverso per ogni piattaforma che utilizza le direttive del preprocessore c# `#if`, `#elif`, e `endif`. Come illustrato nel [ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Schermata triplo della variabile formattato paragrafo](images/ch09fg01-small.png "modello di dispositivo e del sistema operativo")](images/ch09fg01-large.png#lightbox "modello di dispositivo e del sistema operativo")

Tuttavia, il codice risulta può essere difficoltosa e difficili da leggere.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classi parallele nel progetto Asset condiviso

Un approccio più strutturato per l'esecuzione di codice specifico della piattaforma in SAP viene illustrato la [ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) esempio. Progetti di tutte le piattaforme contiene una classe con lo stesso nome e i metodi, ma implementato per la piattaforma specifica. SAP quindi semplicemente crea un'istanza della classe e chiama il metodo.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e libreria di classi portabile

Una libreria non è in genere di accedere alle classi nei progetti di applicazione. Questa restrizione sembra per impedire la tecnica illustrata in **PlatInfoSap2** venga utilizzato in una PCL. Tuttavia, xamarin. Forms contiene una classe denominata [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) che utilizza la reflection .NET per accedere a classi pubbliche nel progetto di applicazione da libreria di classi Portabile.

Libreria di classi Portabile deve definire un `interface` con i membri che deve essere utilizzato in ogni piattaforma. Quindi, ognuna delle piattaforme contiene un'implementazione di tale interfaccia. La classe che implementa l'interfaccia deve essere identificata con un [DependencyAttribute](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyAttribute/) a livello di assembly.

Libreria di classi Portabile utilizza quindi l'oggetto generico [ `Get` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DependencyService.Get{T}/p/Xamarin.Forms.DependencyFetchTarget/) metodo `DependencyService` per ottenere un'istanza della classe che implementa l'interfaccia della piattaforma.

Questa funzionalità viene illustrata la [ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) esempio.

## <a name="platform-specific-sound-generation"></a>Generazione di audio specifico della piattaforma

Il [ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) esempio aggiunge segnali acustici per il **MonkeyTap** programma mediante l'accesso alla funzionalità di generazione suono in ogni piattaforma.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 9 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Esempi di capitolo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
