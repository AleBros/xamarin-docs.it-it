---
title: Riepilogo del capitolo 9. Chiamate API specifiche della piattaforma
description: 'Creazione di App per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 9. Chiamate API specifiche della piattaforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334561"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Riepilogo del capitolo 9. Chiamate API specifiche della piattaforma

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Xamarin.Forms è diversa dal materiale presentato nel libro.

In alcuni casi è necessario eseguire il codice che varia in base alla piattaforma. In questo capitolo illustra le tecniche.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pre-elaborazione nel progetto condiviso

Un progetto Asset condivise Xamarin.Forms possono eseguire codice diverso per ogni piattaforma usando le direttive del preprocessore c# `#if`, `#elif`, e `endif`. Questa situazione è illustrata nel [ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Schermata triplo della variabile formattato paragrafo](images/ch09fg01-small.png "modello di dispositivo e sistema operativo")](images/ch09fg01-large.png#lightbox "modello di dispositivo e sistema operativo")

Tuttavia, il codice risultante può essere confusa e difficili da leggere.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classi parallele nel progetto di Asset condivisi

È illustrato un approccio più strutturato per l'esecuzione di codice specifico della piattaforma in SAP i [ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) esempio. Ognuno dei progetti della piattaforma contiene metodi e una classe con nome identico, ma implementato per quella piattaforma specifica. SAP quindi semplicemente crea un'istanza della classe e chiama il metodo.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e libreria di classi portabile

> [!NOTE] 
> Librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard.

Una libreria non è in genere accedere alle classi nei progetti di applicazione. Sembra che impediscono la tecnica illustrata in questa restrizione **PlatInfoSap2** perché non venga usato in una libreria. Tuttavia, Xamarin.Forms contiene una classe denominata [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) che usa la reflection .NET per accedere alle classi pubbliche nel progetto dell'applicazione dalla libreria.

La libreria è necessario definire un `interface` con i membri che deve essere utilizzato in ogni piattaforma. Quindi, ognuna delle piattaforme contiene un'implementazione di tale interfaccia. La classe che implementa l'interfaccia deve essere identificata con un [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) a livello di assembly.

La libreria Usa quindi il tipo generico [ `Get` ](xref:Xamarin.Forms.DependencyService.Get*) metodo `DependencyService` per ottenere un'istanza della classe che implementa l'interfaccia della piattaforma.

Questa funzionalità viene illustrata la [ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) esempio.

## <a name="platform-specific-sound-generation"></a>Generazione del suono specifico della piattaforma

Il [ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) esempio aggiunge segnali acustici per il **MonkeyTap** programma tramite l'accesso a funzionalità di generazione del suono in ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 9 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Esempi di capitolo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
