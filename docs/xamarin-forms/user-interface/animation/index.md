---
title: Animazione in Xamarin.Forms
description: Xamarin.Forms include la propria infrastruttura di animazione semplice per la creazione di semplici animazioni, garantendo tuttavia sono sufficientemente versatili per creare animazioni complesse.
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: bebb3e32f298a2079069787dba3453e1817cf64f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158602"
---
# <a name="animation-in-xamarinforms"></a>Animazione in Xamarin.Forms

_Xamarin.Forms include la propria infrastruttura di animazione semplice per la creazione di semplici animazioni, garantendo tuttavia sono sufficientemente versatili per creare animazioni complesse._

Le classi di animazione di Xamarin.Forms di destinazione diverse proprietà di elementi visivi, con un'animazione tipica progressivamente si modifica una proprietà da un valore a un altro in un periodo di tempo. Si noti che non vi sia alcuna interfaccia XAML per le classi di animazione di Xamarin.Forms. Tuttavia, è possibile incapsulare le animazioni [comportamenti](~/xamarin-forms/app-fundamentals/behaviors/index.md) cui viene fatto riferimento da XAML.

## <a name="simple-animationssimplemd"></a>[Animazioni semplici](simple.md)

Il [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fornisce metodi di estensione che possono essere utilizzati per costruire semplici animazioni per ruotano, ridimensionano, traducono e dissolvenza [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) istanze. Questo articolo illustra la creazione e l'annullamento di animazioni tramite le `ViewExtensions` classe.

## <a name="easing-functionseasingmd"></a>[Funzioni di interpolazione](easing.md)

Xamarin.Forms include un' [ `Easing` ](xref:Xamarin.Forms.Easing) classe che consente di specificare una funzione di trasferimento che consente di controllare come le animazioni velocizzare o rallentare poiché sono in esecuzione. Questo articolo illustra come usare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate.

## <a name="custom-animationscustommd"></a>[Animazioni personalizzate](custom.md)

Il [ `Animation` ](xref:Xamarin.Forms.Animation) classe è il blocco predefinito di tutte le animazioni di Xamarin.Forms, con i metodi di estensione nel [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe di creazione di uno o più `Animation` oggetti. Questo articolo illustra come usare il `Animation` classe per creare e annullare le animazioni e sincronizzare le animazioni più creare animazioni personalizzate per animare le proprietà che non sono animate da metodi di animazione esistenti.
