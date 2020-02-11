---
title: Modelli di dati Xamarin.Forms
description: Un oggetto DataTemplate viene usato per specificare l'aspetto dei dati nei controlli supportati e in genere è associato ai dati da visualizzare.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 5d130a6644af4e5831263c6de137513c021e0b6a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760794"
---
# <a name="xamarinforms-data-templates"></a>Modelli di dati Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Un oggetto DataTemplate viene usato per specificare l'aspetto dei dati nei controlli supportati e in genere è associato ai dati da visualizzare._

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

I modelli di dati Xamarin.Forms consentono di definire la presentazione dei dati nei controlli supportati. Questo articolo offre un'introduzione ai modelli di dati e spiega perché sono necessari.

## <a name="creating-a-datatemplatecreatingmd"></a>[Creazione di un oggetto DataTemplate](creating.md)

I modelli di dati possono essere creati inline, in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), o usando un tipo personalizzato o un tipo di cella Xamarin.Forms appropriato. Usare un modello inline se non è necessario usare di nuovo il modello di dati altrove. In alternativa, è possibile riusare un modello di dati definendolo come tipo personalizzato o come risorsa a livello di controllo, a livello di pagina o a livello di applicazione.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Creazione di un oggetto DataTemplateSelector](selector.md)

Un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) consente di scegliere un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) in fase di esecuzione in base al valore di una proprietà associata ai dati. Questo consente di applicare più istanze di `DataTemplate` allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. Questo articolo illustra come creare e usare un oggetto `DataTemplateSelector`.

## <a name="related-links"></a>Collegamenti correlati

- [Modelli di dati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
