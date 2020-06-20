---
title: Xamarin.FormsApplicazione di stili alle app usando gli stili XAML
description: In questa guida viene illustrato come personalizzare l'aspetto di un' Xamarin.Forms applicazione utilizzando gli stili XAML.
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 72effe15d3456b5a48cbf5d09e889600134ac686
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84903090"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Xamarin.FormsApplicazione di stili alle app usando gli stili XAML

## <a name="introduction"></a>[Introduzione](introduction.md)

Xamarin.Formsle applicazioni contengono spesso più controlli che hanno un aspetto identico. L'impostazione dell'aspetto di ogni singolo controllo può essere ripetitiva e soggetta a errori. È invece possibile creare stili che personalizzano l'aspetto del controllo mediante il raggruppamento e l'impostazione delle proprietà disponibili nel tipo di controllo.

## <a name="explicit-styles"></a>[Stili espliciti](explicit.md)

Uno stile *esplicito* è quello che viene applicato selettivamente ai controlli impostando le relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Proprietà.

## <a name="implicit-styles"></a>[Stili impliciti](implicit.md)

Uno stile *implicito* è quello usato da tutti i controlli dello stesso [`TargetType`](xref:Xamarin.Forms.Style.TargetType) , senza richiedere a ogni controllo di fare riferimento allo stile.

## <a name="global-styles"></a>[Stili globali](application.md)

Gli stili possono essere resi disponibili a livello globale aggiungendoli all'applicazione [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Ciò consente di evitare la duplicazione degli stili in pagine o controlli.

## <a name="style-inheritance"></a>[Ereditarietà degli stili](inheritance.md)

Gli stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo.

## <a name="dynamic-styles"></a>[Stili dinamici](dynamic.md)

Gli stili non rispondono alle modifiche delle proprietà e rimangono invariati per la durata di un'applicazione. Tuttavia, le applicazioni possono rispondere dinamicamente alle modifiche di stile in fase di esecuzione tramite risorse dinamiche.

## <a name="device-styles"></a>[Stili di dispositivo](device.md)

Xamarin.Formsinclude sei stili *dinamici* , noti come stili di *dispositivo* , nella [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) classe. Tutti i sei stili possono essere applicati [`Label`](xref:Xamarin.Forms.Label) solo alle istanze.

## <a name="style-classes"></a>[Classi di stile](style-class.md)

Xamarin.Formsle classi di stile consentono di applicare più stili a un controllo senza ricorrere all'ereditarietà dello stile.
