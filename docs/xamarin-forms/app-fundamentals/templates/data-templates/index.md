---
title: Xamarin.FormsModelli di dati
description: Un oggetto DataTemplate viene usato per specificare l'aspetto dei dati nei controlli supportati e in genere è associato ai dati da visualizzare.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0961fad18ccd961a5b84b2a5535bca70781dd8d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136123"
---
# <a name="xamarinforms-data-templates"></a>Xamarin.FormsModelli di dati

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Un oggetto DataTemplate viene usato per specificare l'aspetto dei dati nei controlli supportati e in genere è associato ai dati da visualizzare._

## <a name="introduction"></a>[Introduzione](introduction.md)

Xamarin.Formsi modelli di dati offrono la possibilità di definire la presentazione dei dati nei controlli supportati. Questo articolo offre un'introduzione ai modelli di dati e spiega perché sono necessari.

## <a name="creating-a-datatemplate"></a>[Creazione di un oggetto DataTemplate](creating.md)

I modelli di dati possono essere creati inline, in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) o da un tipo personalizzato o da un Xamarin.Forms tipo di cella appropriato. Usare un modello inline se non è necessario usare di nuovo il modello di dati altrove. In alternativa, è possibile riusare un modello di dati definendolo come tipo personalizzato o come risorsa a livello di controllo, a livello di pagina o a livello di applicazione.

## <a name="creating-a-datatemplateselector"></a>[Creazione di un DataTemplateSelector](selector.md)

Un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) può essere usato per scegliere un oggetto in fase di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) esecuzione in base al valore di una proprietà con associazione a dati. Questo consente di applicare più istanze di `DataTemplate` allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. Questo articolo illustra come creare e usare un oggetto `DataTemplateSelector`.

## <a name="related-links"></a>Collegamenti correlati

- [Modelli di dati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
