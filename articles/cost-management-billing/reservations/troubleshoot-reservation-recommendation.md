---
title: Troubleshoot Azure reservation recommendations
description: This article helps you understand and troubleshoot Azure reservation recommendations shown in the Azure portal.
author: pri-mittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: primittal
ms.reviewer: primittal
ms.topic: troubleshooting
ms.date: 07/08/2025
---

# Troubleshoot Azure reservation recommendations

This article helps you understand and troubleshoot Azure reservation recommendations shown in the Azure portal. You might see no recommendations or recommendations that don't match your expectations. For example, you might already have a reserved instance for a specific VM configuration. You might expect to see a recommendation for a similar VM configuration.

## Symptoms

1. Sign in to the [Azure portal](https://portal.azure.com/) and navigate to **Reservations**.
2. Select **+ Add** and then select a product.
3. On the **Recommended** tab, you might see no recommendations or recommendations that don't meet your expectations.

## Cause

The list of recommended products is generated by the amount of usage you have for your scope (shared or single) compared to the cost of a reservation for the product. If the recommendation engine detects savings, it recommends a product for purchase. However, when the engine doesn't identify any savings, it doesn't recommend a product.

When you're running a resource with a given configuration, there's no guarantee that you'll save money by purchasing a reservation for that configuration. You might have sporadic usage for example. If so, the total cost of the reservation might not save you money over the lifetime of the reservation period.

It's also important to understand how the scope selection affects recommendations. When the scope is set to **Shared**, recommendations in the list show reserved instances where Azure finds savings for the entire enrollment that's associated with the billing subscription. When the scope is set to **Single**, recommendations in the list only apply to resources that run in the subscription. It's possible that some VM sizes get recommended for one scope but not another. For example, you might have aggregated usage of **Standard_B1ls** across your enrollment that's high enough to justify the cost of purchasing a reservation at the enrollment scope. However, a single subscription in the enrollment might not have enough usage to justify the cost of purchasing a reservation in the scope. Changing the scope between **Shared** and **Single** might produce different recommendations.

Azure might recommend purchasing a reservation for certain terms, and not for others, based on the cost savings identified. Specifically, three-year terms have larger discounts than one-year terms. It's more likely that Azure will find savings for a three-year term than it will for a one-year term.

Azure classic compute resources such as classic VMs are explicitly excluded from reservation recommendations. Microsoft recommends that users avoid making long-term commitments to legacy services that are being deprecated.

If you want to understand why Azure recommends a specific resource size and quantity, select **\<Quantity\> See details** for an in-depth, visualization showing potential savings over time.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Screenshot showing the reservation recommendation See details link." lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## Solution

You can buy any reservation quantity for a term that you like. Buying a reservation with a quantity and a term that differ from the recommendation will likely result in less-than optimum savings and use.

## Next steps

- For more information about reservation recommendations, see [Reservation purchase recommendations](determine-reservation-purchase.md).
