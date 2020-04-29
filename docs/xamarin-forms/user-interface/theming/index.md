---
title: Definizione del tema di un'applicazione Xamarin.Forms
description: Le applicazioni Novell. Forms supportano la creazione di un oggetto ResourceDictionary per ogni tema e quindi il caricamento delle risorse con l'estensione di markup DynamicResource.
ms.prod: xamarin
ms.assetId: BF92AEDD-EF23-4D08-A972-B089066E75F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
ms.openlocfilehash: 5988437b40ac875b8b59f9af0f25d4b5c60ded97
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517134"
---
# <a name="theming-a-xamarinforms-application"></a>Temi di un'applicazione Novell. Forms

## <a name="theme-an-application"></a>[Tema di un'applicazione](theming.md)

Il tema può essere implementato nelle applicazioni Novell. Forms creando un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) per ogni tema e quindi caricando le risorse con l' `DynamicResource` estensione di markup.

## <a name="respond-to-system-theme-changes"></a>[Rispondi alle modifiche del tema di sistema](system-theme-changes.md)

I dispositivi in genere includono temi chiaro e scuro, che fanno riferimento a un ampio set di preferenze di aspetto che possono essere impostate a livello di sistema operativo. Le applicazioni devono rispettare questi temi di sistema e rispondere immediatamente quando il tema del sistema cambia.
