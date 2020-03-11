---
title: Riepilogo del capitolo 9. Chiamate API specifiche della piattaforma
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 9. Chiamate API specifiche della piattaforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334561"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Riepilogo del capitolo 9. Chiamate API specifiche della piattaforma

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

In alcuni casi è necessario eseguire il codice che varia in base alla piattaforma. In questo capitolo illustra le tecniche.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pre-elaborazione nel progetto condiviso

Un progetto asset condiviso Novell. Forms può eseguire codice diverso per ogni piattaforma usando C# le direttive per il preprocessore `#if`, `#elif`e `endif`. Questa operazione è illustrata in [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Schermata tripla del paragrafo formattato variabile](images/ch09fg01-small.png "Modello di dispositivo e sistema operativo")](images/ch09fg01-large.png#lightbox "Modello di dispositivo e sistema operativo")

Tuttavia, il codice risultante può essere confusa e difficili da leggere.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classi parallele nel progetto di Asset condivisi

Un approccio più strutturato all'esecuzione di codice specifico della piattaforma in SAP è illustrato nell'esempio [**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) . Ognuno dei progetti della piattaforma contiene metodi e una classe con nome identico, ma implementato per quella piattaforma specifica. SAP quindi semplicemente crea un'istanza della classe e chiama il metodo.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e libreria di classi portabile

> [!NOTE] 
> Librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard.

Una libreria non è in genere accedere alle classi nei progetti di applicazione. Questa restrizione sembra impedire l'uso della tecnica illustrata in **PlatInfoSap2** in una libreria. Tuttavia, Novell. Forms contiene una classe denominata [`DependencyService`](xref:Xamarin.Forms.DependencyService) che usa la reflection .NET per accedere alle classi pubbliche nel progetto di applicazione dalla libreria.

La libreria deve definire un `interface` con i membri che deve usare in ogni piattaforma. Quindi, ognuna delle piattaforme contiene un'implementazione di tale interfaccia. La classe che implementa l'interfaccia deve essere identificata con un [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) a livello di assembly.

La libreria usa quindi il metodo generico [`Get`](xref:Xamarin.Forms.DependencyService.Get*) di `DependencyService` per ottenere un'istanza della classe Platform che implementa l'interfaccia.

Questa operazione è illustrata nell'esempio [**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) .

## <a name="platform-specific-sound-generation"></a>Generazione del suono specifico della piattaforma

L'esempio [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) aggiunge segnali acustici al programma **MonkeyTap** accedendo alle funzionalità di generazione di suoni in ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 9 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Esempi del capitolo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
