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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760794"
---
# <a name="xamarinforms-data-templates"></a>Modelli di dati Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Un oggetto DataTemplate viene usato per specificare l'aspetto dei dati nei controlli supportati e in genere è associato ai dati da visualizzare._

## <a name="introduction"></a>[Introduzione](introduction.md)

I modelli di dati Xamarin.Forms consentono di definire la presentazione dei dati nei controlli supportati. Questo articolo offre un'introduzione ai modelli di dati e spiega perché sono necessari.

## <a name="creating-a-datatemplate"></a>[Creazione di un oggetto DataTemplate](creating.md)

I modelli di dati possono [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)essere creati inline, in un oggetto o da un tipo di cella Xamarin.Forms appropriato o appropriato. Usare un modello inline se non è necessario usare di nuovo il modello di dati altrove. In alternativa, è possibile riusare un modello di dati definendolo come tipo personalizzato o come risorsa a livello di controllo, a livello di pagina o a livello di applicazione.

## <a name="creating-a-datatemplateselector"></a>[Creazione di un DataTemplateSelector](selector.md)

Un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) oggetto può essere [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) utilizzato per scegliere un in fase di esecuzione in base al valore di una proprietà con associazione a dati. Questo consente di applicare più istanze di `DataTemplate` allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. Questo articolo illustra come creare e usare un oggetto `DataTemplateSelector`.

## <a name="related-links"></a>Collegamenti correlati

- [Modelli di dati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
