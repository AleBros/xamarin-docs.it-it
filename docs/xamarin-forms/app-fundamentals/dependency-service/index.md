---
title: Xamarin.Forms DependencyService
description: La classe DependencyService di Xamarin.Forms è un localizzatore di servizi che consente alle applicazioni Xamarin.Forms di richiamare la funzionalità della piattaforma nativa dal codice condiviso.
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: ea259d1ee9dc4a94322c38b3e96bee654197bb87
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650447"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

La classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) è un localizzatore di servizi che consente alle applicazioni Xamarin.Forms di richiamare la funzionalità della piattaforma nativa dal codice condiviso.

## <a name="registration-and-resolutionregistration-and-resolutionmd"></a>[Registrazione e risoluzione](registration-and-resolution.md)

Le implementazioni della piattaforma devono essere registrate con [`DependencyService`](xref:Xamarin.Forms.DependencyService) e quindi risolte dal codice condiviso per richiamarle.

## <a name="picking-a-photo-from-the-libraryphoto-pickermd"></a>[Selezione di una foto dalla raccolta](photo-picker.md)

Questo articolo illustra come usare la classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) di Xamarin.Forms per scegliere una foto dalla raccolta immagini del telefono.
