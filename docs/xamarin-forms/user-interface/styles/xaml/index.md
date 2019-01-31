---
title: Stile App xamarin. Forms usando gli stili XAML
description: Questa guida illustra come personalizzare l'aspetto di un'applicazione xamarin. Forms usando gli stili XAML.
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: ec41955ac15ab23579a5e63b9e17eed61a74e86f
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291986"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Stile App xamarin. Forms usando gli stili XAML

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Le applicazioni xamarin. Forms contengono spesso più controlli che hanno un aspetto identico. Impostazione dell'aspetto di ogni controllo singoli possono essere ricorrenti e tendente all'errore. Al contrario, gli stili è possibile creare che consentono di personalizzare l'aspetto del controllo il raggruppamento e impostare le proprietà disponibili per il tipo di controllo.

## <a name="explicit-stylesexplicitmd"></a>[Stili espliciti](explicit.md)

Un' *esplicite* stile di visualizzazione è quello che viene applicato in modo selettivo a controlli impostando loro [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà.

## <a name="implicit-stylesimplicitmd"></a>[Stili impliciti](implicit.md)

Un' *implicita* stile è quello usato da tutti i controlli dello stesso [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType), senza richiedere ogni controllo per fare riferimento allo stile.

## <a name="global-stylesapplicationmd"></a>[Stili globali](application.md)

Gli stili possono essere rese disponibili a livello globale vengono aggiunte all'applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Ciò consente di evitare la duplicazione degli stili nelle pagine o controlli.

## <a name="style-inheritanceinheritancemd"></a>[Ereditarietà degli stili](inheritance.md)

Stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo.

## <a name="dynamic-stylesdynamicmd"></a>[Stili dinamici](dynamic.md)

Gli stili non rispondere alle modifiche delle proprietà e rimangono invariati per la durata di un'applicazione. Tuttavia, le applicazioni possono rispondere alle modifiche di stile in modo dinamico in fase di esecuzione usando le risorse dinamiche.

## <a name="device-stylesdevicemd"></a>[Stili di dispositivo](device.md)

Xamarin. Forms include sei *dinamici* stili, noti come *dispositivo* gli stili, il [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) classe. Tutti i sei stili possono essere applicati a [ `Label` ](xref:Xamarin.Forms.Label) solo istanze.

## <a name="style-classesstyle-classmd"></a>[Classi di stile](style-class.md)

Le classi di stile di xamarin. Forms abilitano più stili da applicare a un controllo, senza dover ricorrere a ereditarietà degli stili.
