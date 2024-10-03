---
title: "{{ search-api-full-name }} pricing policy"
description: "This article describes the {{ search-api-name }} pricing policy."
editable: false
---

# {{ search-api-name }} pricing policy



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

## What goes into the cost of using {{ search-api-name }} {#rules}

The cost of using {{ search-api-name }} is calculated based on the number of search requests initiated within a calendar month ([reporting period](../billing/concepts/reporting-period.md)).

## Prices for the Russia region {#prices}

{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

Free tier includes 1,000 requests per user per month during night hours^1^ from 00:00:00 to 07:59:59. The following pricing applies to requests beyond this threshold.



{% include [usd](../_pricing/search-api/usd.md) %}


The service has a quota of 30,000 requests per month (1,000 requests per day) for all new users. For more information on limits, see [Quotas and limits](concepts/limits.md). To request a change in quotas, contact [technical support]({{ link-console-support }}) or your account manager.

^1^ All times are [UTC+3](https://en.wikipedia.org/wiki/UTC%2B03:00).
