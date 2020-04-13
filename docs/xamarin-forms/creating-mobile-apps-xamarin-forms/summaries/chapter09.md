---
title: Riassunto del capitolo 9. Chiamate di API specifiche della piattaforma
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 9. Chiamate di API specifiche della piattaforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334561"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Riassunto del capitolo 9. Chiamate di API specifiche della piattaforma

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

A volte è necessario eseguire codice che varia in base alla piattaforma. Questo capitolo esplora le tecniche.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pre-elaborazione nel progetto di asset condiviso

Un progetto di asset condiviso Xamarin.Forms può eseguire codice diverso `#if`per `#elif`ogni `endif`piattaforma utilizzando le direttive per il preprocessore di C, e . Questo è dimostrato in [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Tripla schermata del paragrafo in formato variabile](images/ch09fg01-small.png "Modello di dispositivo e sistema operativo")](images/ch09fg01-large.png#lightbox "Modello di dispositivo e sistema operativo")

Tuttavia, il codice risultante può essere brutto e difficile da leggere.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classi parallele nel progetto di risorse condiviseParallel classes in the Shared Asset Project

Un approccio più strutturato all'esecuzione di codice specifico della piattaforma in SAP è illustrato nell'esempio [**PlatInfoSap2.A**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) more structured approach to executing platform-specific code in the SAP is demonstrated in the PlatInfoSap2 sample. Ognuno dei progetti di piattaforma contiene una classe e metodi con nome identico, ma implementato per quella particolare piattaforma. Il SAP crea quindi semplicemente un'istanza della classe e chiama il metodo.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService and the Portable Class Library

> [!NOTE] 
> Librerie di classi portabili sono state sostituite da librerie .NET Standard.Portable Class Libraries have been replaced by .NET Standard libraries. Tutto il codice di esempio del libro è stato convertito per utilizzare librerie standard .NET.

Una libreria in genere non può accedere alle classi nei progetti di applicazione. Questa restrizione sembra impedire l'utilizzo della tecnica illustrata in **PlatInfoSap2** in una libreria. Tuttavia, Xamarin.Forms contiene [`DependencyService`](xref:Xamarin.Forms.DependencyService) una classe denominata che utilizza la reflection .NET per accedere alle classi pubbliche nel progetto di applicazione dalla libreria.

La libreria deve `interface` definire un con i membri che deve utilizzare in ogni piattaforma. Quindi, ognuna delle piattaforme contiene un'implementazione di tale interfaccia. La classe che implementa l'interfaccia deve essere identificata con un [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) a livello di assembly.

La libreria utilizza [`Get`](xref:Xamarin.Forms.DependencyService.Get*) quindi `DependencyService` il metodo generico di per ottenere un'istanza della classe della piattaforma che implementa l'interfaccia.

Ciò è illustrato nell'esempio [**DisplayPlatformInfo.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) is demonstrated in the DisplayPlatformInfo sample.

## <a name="platform-specific-sound-generation"></a>Generazione di suoni specifici della piattaforma

L'esempio [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) aggiunge segnali acustici al programma **MonkeyTap** accedendo alle strutture di generazione del suono in ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 9 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Esempi del capitolo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
