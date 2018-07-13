---
title: Modelli di dati di xamarin. Forms
description: Una classe DataTemplate viene usato per specificare l'aspetto dei dati nei controlli supportati e viene in genere associata ai dati da visualizzare.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 771ae22c3e28a4fce758bbfd6a3bd63bafb75e53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994971"
---
# <a name="xamarinforms-data-templates"></a>Modelli di dati di xamarin. Forms

_Una classe DataTemplate viene usato per specificare l'aspetto dei dati nei controlli supportati e viene in genere associata ai dati da visualizzare._

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

I modelli di dati di xamarin. Forms offrono la possibilità di definire la presentazione dei dati nei controlli supportati. Questo articolo fornisce un'introduzione ai modelli di dati, esaminare il motivo per cui sono necessari.

## <a name="creating-a-datatemplatecreatingmd"></a>[Creazione di un DataTemplate](creating.md)

I modelli di dati possono essere creati inline, in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), o da un tipo personalizzato o un tipo di cella di xamarin. Forms appropriato. Un modello inline deve essere utilizzato se non è necessario per riutilizzare il modello di dati in un' posizione. In alternativa, un modello di dati può essere riutilizzato da definirla come un tipo personalizzato, o come una risorsa a livello di controllo, a livello di pagina o a livello di applicazione.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Creazione di un DataTemplateSelector](selector.md)

Oggetto [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) consente di scegliere un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo, più `DataTemplate` istanze da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. Questo articolo illustra come creare e utilizzare un `DataTemplateSelector`.


## <a name="related-links"></a>Collegamenti correlati

- [Modelli di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
