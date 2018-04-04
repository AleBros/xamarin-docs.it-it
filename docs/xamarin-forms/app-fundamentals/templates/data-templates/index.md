---
title: Modelli di dati
description: DataTemplate utilizzato per specificare l'aspetto dei dati in controlli supportati e viene in genere associata ai dati da visualizzare.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 14de42acd1bde00df146a9fe5d772366735ed295
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="data-templates"></a>Modelli di dati

_DataTemplate utilizzato per specificare l'aspetto dei dati in controlli supportati e viene in genere associata ai dati da visualizzare._

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

I modelli di xamarin. Forms dati consentono di definire la presentazione dei dati in controlli supportati. In questo articolo viene fornita un'introduzione ai modelli di dati, esaminare il motivo per cui sono necessari.

## <a name="creating-a-datatemplatecreatingmd"></a>[Creazione di un elemento DataTemplate](creating.md)

È possibile creare modelli di dati inline in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), o da un tipo personalizzato o un tipo di cella appropriato xamarin. Forms. Un modello inline deve essere utilizzato se non è necessario riutilizzare il modello di dati in un' posizione. In alternativa, un modello di dati può essere riutilizzato da definendola come un tipo personalizzato o come una risorsa a livello di pagina o a livello di applicazione del livello di controllo.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Creazione di un DataTemplateSelector](selector.md)

Oggetto [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) consente di scegliere un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) in fase di esecuzione in base al valore di una proprietà con associazione a dati. In questo modo, più `DataTemplate` istanze da applicare allo stesso tipo di oggetto, per personalizzare l'aspetto di oggetti specifici. In questo articolo viene illustrato come creare e utilizzare un `DataTemplateSelector`.


## <a name="related-links"></a>Collegamenti correlati

- [Modelli di dati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
