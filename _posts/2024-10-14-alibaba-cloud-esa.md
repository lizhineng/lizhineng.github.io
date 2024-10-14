---
layout: post
title: "Alibaba Cloud Edge Security Accelerator (ESA)"
date: 2024-10-14 15:27:00 +0800
---

Alibaba Cloud recently launched a new CDN service, Edge Security Accelerator
(ESA), which is very similiar to Cloudflare. ESA includes built-in DDoS
protection, page rules, and a web application firewall (WAF). It also
provides edge computing, so you can write Javascript applications that run
on edge servers close to your users.

Unlike Cloudflare, which doesn't charge for CDN traffic, ESA charges 0.198
RMB per gigabyte—lower than the DCDN rate. If I understand correctly, the data
charges are uniform regardless of the client's traffic region.

![The screenshot lists the DCDN traffic charges by region, with rates ranging from 0.15 Yuan/GB to 1.31 Yuan/GB.](/assets/2024-10-14-alibaba-cloud-esa/dcdn-data-charges.webp "Screenshot of Alibaba Cloud DCDN data charges")

The screenshot lists the DCDN data charges by region, with rates ranging
from 0.15 Yuan/GB to 1.31 Yuan/GB.

![ESA data overage charges: 0.198 Yuan/GB for Basic Plan, 0.75 Yuan/GB for Standard Plan, and 1.2 Yuan/GB for Advanced Plan.](/assets/2024-10-14-alibaba-cloud-esa/esa-overages.webp "Screenshot of Alibaba Cloud ESA overage charges")

ESA data overage charges: 0.198 Yuan/GB for Basic Plan, 0.75 Yuan/GB for
Standard Plan, and 1.2 Yuan/GB for Advanced Plan.

I couldn't find the data overage charges in the official documentation in
English, this screenshot is originally from the
[Chinese version](https://help.aliyun.com/document_detail/2701851.html)
and translated into English.

While ESA doesn't offer a free plan, the 9.9 RMB monthly subscription made
it tempting to give it a try. However, I found that setting up a site with
a subdomain requires the Enterprise plan—similar to Cloudflare's approach,
where partial setups also require an Enterprise plan, but ESA allows you
to add a site with CNAME setup on the Basic Plan.

![The website that you entered is a subdomain, for which you can only select the Enterprise plan.](/assets/2024-10-14-alibaba-cloud-esa/esa-subdomain-enterprise-plan.webp "Screenshot of adding a new site to Alibaba Cloud ESA")

So that may have to wait until my next project, with root domain.

## Useful Links

- [ESA Documentation - English](https://www.alibabacloud.com/help/en/dcdnnext)
- [ESA Plan Comparison - English](https://www.alibabacloud.com/help/en/dcdnnext/latest/package-comparison)
- [ESA Documentation - Chinese](https://help.aliyun.com/product/2673927.html)
- [ESA Plan Comparison - Chinese](https://help.aliyun.com/document_detail/2701845.html)
