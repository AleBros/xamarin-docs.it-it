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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "67650447"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introduction"></a>[Introduzione](introduction.md)

La [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe è un localizzatore di servizi che consente alle applicazioni Xamarin.Forms di richiamare la funzionalità della piattaforma nativa dal codice condiviso.

## <a name="registration-and-resolution"></a>[Registrazione e risoluzione](registration-and-resolution.md)

Le implementazioni della piattaforma [`DependencyService`](xref:Xamarin.Forms.DependencyService)devono essere registrate con , quindi risolte dal codice condiviso per richiamarle.

## <a name="picking-a-photo-from-the-library"></a>[Selezione di una foto dalla raccolta](photo-picker.md)

In questo articolo viene illustrato come utilizzare [`DependencyService`](xref:Xamarin.Forms.DependencyService) la classe Xamarin.Forms per selezionare una foto dalla raccolta immagini del telefono.
