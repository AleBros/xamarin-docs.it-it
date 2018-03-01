---
title: Animazione
description: "Xamarin. Forms include la propria infrastruttura animazione che è molto semplice per la creazione di semplici animazioni, pur sufficientemente versatile per creare animazioni complesse."
ms.topic: article
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: e50419eaa6466e94fc5192a77ffd7cb89ca9d965
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="animation"></a>Animazione

_Xamarin. Forms include la propria infrastruttura animazione che è molto semplice per la creazione di semplici animazioni, pur sufficientemente versatile per creare animazioni complesse._

Le classi di animazione xamarin. Forms destinazione diverse proprietà degli elementi visivi, con un'animazione tipico progressivamente modifica di una proprietà da un valore a un altro in un periodo di tempo. Si noti che non è disponibile alcuna interfaccia XAML per le classi di animazione xamarin. Forms. Tuttavia, è possibile incapsulare animazioni [comportamenti](~/xamarin-forms/app-fundamentals/behaviors/index.md) e quindi farvi riferimento da XAML.

## <a name="simple-animationssimplemd"></a>[Animazioni semplice](simple.md)

Il [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe fornisce metodi di estensione che possono essere usati per costruire le animazioni semplice ruotare, ridimensionare, convertono e dissolvenza [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) istanze. In questo articolo illustra la creazione e l'annullamento di animazioni mediante la `ViewExtensions` classe.

## <a name="easing-functionseasingmd"></a>[Funzioni di interpolazione](easing.md)

Xamarin. Forms include un [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) classe che consente di specificare una funzione di trasferimento che controlla come animazioni velocizzare o rallentare come vengono eseguiti. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate.

## <a name="custom-animationscustommd"></a>[Animazioni personalizzate](custom.md)

Il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe è il blocco predefinito di tutte le animazioni di xamarin. Forms, con i metodi di estensione di [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe creazione di uno o più `Animation` oggetti. In questo articolo viene illustrato come utilizzare la `Animation` classe per creare e annullare le animazioni, sincronizzare più animazioni e creare animazioni personalizzate che animare le proprietà che non sono animate dai metodi di animazione esistente.

