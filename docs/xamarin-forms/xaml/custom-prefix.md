---
title: XAML Namespace consigliati i prefissi in xamarin. Forms
description: La classe XmlnsPrefixAttribute può essere utilizzata dagli autori di controlli per specificare un prefisso consigliato da associare a uno spazio dei nomi XAML, per l'utilizzo XAML.
ms.prod: xamarin
ms.assetid: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2019
ms.openlocfilehash: 33f18b3f9c9ddb6ab31ca92e2f192ffad783ec0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075101"
---
# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>XAML Namespace consigliati i prefissi in xamarin. Forms

Il `XmlnsPrefixAttribute` classe può essere utilizzata dagli autori di controlli per specificare un prefisso consigliato da associare a uno spazio dei nomi XAML, per l'utilizzo XAML. Il prefisso è utile quando il supporto di serializzazione dell'albero degli oggetti per XAML, o quando si interagisce con un ambiente di progettazione con funzionalità di modifica XAML. Ad esempio:

- Gli editor di testo XAML è possibile usare la `XmlnsPrefixAttribute` come hint per uno spazio dei nomi XAML iniziale `xmlns` mapping.
- Ambienti di progettazione XAML è possibile usare il `XmlnsPrefixAttribute` per aggiungere i mapping per il XAML quando si trascinano gli oggetti all'esterno di una casella degli strumenti e su una superficie di progettazione visiva.

Consiglia di prefissi dello spazio dei nomi devono essere definiti a livello di assembly con il `XmlnsPrefixAttribute` costruttore che accetta due argomenti: una stringa che specifica l'identificatore di uno spazio dei nomi XAML e una stringa che specifica un prefisso consigliato:

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

I prefissi devono usare stringhe brevi, poiché il prefisso viene in genere applicato a tutti gli elementi serializzati che provengono dallo spazio dei nomi XAML. Pertanto, la lunghezza della stringa di prefisso può avere un impatto negativo sulle dimensioni dell'output di XAML serializzato.

> [!NOTE]
> Più `XmlnsPrefixAttribute` può essere applicato a un assembly. Ad esempio, se si dispone di un assembly che definisce i tipi per più di uno spazio dei nomi XAML, è possibile definire i valori di prefisso diverso per ogni spazio dei nomi XAML.

## <a name="related-links"></a>Collegamenti correlati

- [Schemi spazio dei nomi personalizzati XAML](custom-namespace-schemas.md)
- [Spazi dei nomi XAML in xamarin. Forms](namespaces.md)
