---
title: Riepilogo del capitolo 9. Chiamate di API specifiche della piattaforma
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 9. Chiamate di API specifiche della piattaforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8feb636057f1e11c7df90236dee44697203d51c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136857"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Riepilogo del capitolo 9. Chiamate di API specifiche della piattaforma

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

Talvolta è necessario eseguire codice che varia in base alla piattaforma. In questo capitolo vengono esaminate le tecniche.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pre-elaborazione nel progetto asset condiviso

Un Xamarin.Forms progetto asset condiviso può eseguire codice diverso per ogni piattaforma usando le direttive per il preprocessore C# `#if` , `#elif` e `endif` . Questa operazione è illustrata in [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Schermata tripla del paragrafo formattato variabile](images/ch09fg01-small.png "Modello di dispositivo e sistema operativo")](images/ch09fg01-large.png#lightbox "Modello di dispositivo e sistema operativo")

Tuttavia, il codice risultante può essere brutto e difficile da leggere.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classi parallele nel progetto asset condiviso

Un approccio più strutturato all'esecuzione di codice specifico della piattaforma in SAP è illustrato nell'esempio [**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) . Ogni progetto di piattaforma contiene una classe e metodi con nome identico, ma implementata per la piattaforma specifica. SAP crea quindi semplicemente un'istanza della classe e chiama il metodo.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e la libreria di classi portabile

> [!NOTE] 
> Le librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio del libro è stato convertito in modo da usare le librerie .NET standard.

Una libreria non è in genere in grado di accedere alle classi nei progetti di applicazione. Questa restrizione sembra impedire l'uso della tecnica illustrata in **PlatInfoSap2** in una libreria. Contiene tuttavia Xamarin.Forms una classe denominata [`DependencyService`](xref:Xamarin.Forms.DependencyService) che usa la reflection .NET per accedere alle classi pubbliche nel progetto di applicazione dalla libreria.

La libreria deve definire un `interface` con i membri che deve usare in ogni piattaforma. Ognuna delle piattaforme contiene quindi un'implementazione di tale interfaccia. La classe che implementa l'interfaccia deve essere identificata con un [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) a livello di assembly.

La libreria usa quindi il [`Get`](xref:Xamarin.Forms.DependencyService.Get*) metodo generico di `DependencyService` per ottenere un'istanza della classe Platform che implementa l'interfaccia.

Questa operazione è illustrata nell'esempio [**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) .

## <a name="platform-specific-sound-generation"></a>Generazione di suoni specifici della piattaforma

L'esempio [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) aggiunge segnali acustici al programma **MonkeyTap** accedendo alle funzionalità di generazione di suoni in ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 9 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Esempi del capitolo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servizio di dipendenza](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
