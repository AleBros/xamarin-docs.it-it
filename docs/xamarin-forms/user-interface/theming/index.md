---
title: Applicazione di un tema a un' Xamarin.Forms applicazione
description: Xamarin.Formsle applicazioni supportano la creazione di un oggetto ResourceDictionary per ogni tema e quindi il caricamento delle risorse con l'estensione di markup DynamicResource.
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80660ae7d3af0fe5948a5ae4ffdb35d2f9c2a40f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136136"
---
# <a name="theming-a-xamarinforms-application"></a>Applicazione di un tema a un' Xamarin.Forms applicazione

## <a name="theme-an-application"></a>[Tema di un'applicazione](theming.md)

Il tema può essere implementato nelle Xamarin.Forms applicazioni creando un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) per ogni tema e quindi caricando le risorse con l' `DynamicResource` estensione di markup.

## <a name="respond-to-system-theme-changes"></a>[Rispondi alle modifiche del tema di sistema](system-theme-changes.md)

I dispositivi in genere includono temi chiaro e scuro, che fanno riferimento a un ampio set di preferenze di aspetto che possono essere impostate a livello di sistema operativo. Le applicazioni devono rispettare questi temi di sistema e rispondere immediatamente quando il tema del sistema cambia.
