---
layout:     post
title:      "Create personalized, AI-driven experiences with an Azure free account"
subtitle:   "快来使用12个月的免费的Azure认知服务吧"
date:       2023-05-12 10:52:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - cognitive
    - Azure
---


>
> 之前在用OPenAI的免费账号使用openAI的chatgpt的服务，只给了18美金，并且6月1日就到期了，眼看着额度已经到期了，发现微软云提供了200美金的免费额度，而且可以用一年，快点来试试吧。
> 微软在应用准入方面还是很下功夫的。
> “Yeah It's on. ”
>  
>  

## portal登录

首先我们登录azure的congnitive的protal页面,[cognitive-service](https://azure.microsoft.com/en-us/free/cognitive-services/?azure-portal=true)

* An Azure subscription 订阅Azure服务

访问认知服务需要填写一个表单，里面主要告诉使用Azure的认知服务需要注意的限制

```
When evaluating which scenarios to onboard, we consider who will directly interact with the application, who will see the output of the application, whether the application will be used in a high-stakes domain (e.g., medical), and the extent to which the application’s capabilities are tightly scoped. In general, applications in high stakes domains will require additional mitigations and are more likely to be approved for applications with internal-only users and internal-only audiences. Applications with broad possible uses, including content generation capabilities, are more likely to be approved if 1) the domain is not high stakes and users are authenticated or 2) in the case of high stakes domains, anyone who views or interacts with the content is internal to your company.  
```

医疗属于高风险领域，会被禁止使用认知大模型，或者会限制用户范围只有内部用户可以使用。
哪些领域可以获批呢？
1. 非高风险领域，且用户经过认证了；
2. 高风险领域需要确保查看应用和与内容交互的人都是你公司内部的人员；


## cognitive Service onboarding
Sat, May 13, 12:00 AM,收到了MS 发来的onboarding 邮件

TODO


## 参考文献
1. >[ Azure OpenAI Service 注册申请与配置心得 ](https://atlassc.net/2023/04/25/azure-openai-service) 
