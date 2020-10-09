---
title: "ما هو ال CBOW وكيفية تطويره من الصفر وتطبيقة على اللغة العربية"
tagline: "في هذا المقال سنتعرف اكثر على طريقة CBOW لصناعة Word2Vec وتطبيقها على اللغة العربية"
excerpt: "في هذا المقال سنتعرف اكثر على طريقة CBOW لصناعة Word2Vec وتطبيقها على اللغة العربية"
header:
  overlay_image: images/2020-10-09-Make-Your-Own-CBOW/header.png
  teaser: images/2020-10-09-Make-Your-Own-CBOW/header.png
categories:
  - Blog
tags:
    - Word2vec
    - NLP
    - Python
    - CBOW
toc: true
toc_sticky: true
---
<style>
.wrapper{
  direction: rtl;
}
.arabic {
    direction: rtl;
    }
</style>

{: .text-right}

## مقدمة

<p dir='rtl'>
في هذا المقال سنتعرف سويا على موضوع مهم في علم معالجة اللغات الطبيعية (NLP) وهو Word embeddings وكيفية تطبيقة باستخدام طريقة ال CBOW  والتي سنبرمجها معا من الصفر باستخدام  لغة البرمجة بايثون (Python)
</p>

{: .text-right}

## بعض المتطلبات المسبقة
<p dir='rtl'>
قبل ان نبدا معاً يجب ان نعرض بعض المتطلبات المسبقة لفهم المفال بشكل سهل وسلسل
</p>

- **Python**
- **Deep Learning**
- **Gradient descent**

{: .text-right}
## ترجمة بعض المصطلحات العلمية
<p dir='rtl'>
خلال هذا المقا سوف نستخدم المصطلحات العلمية بلغتها الاصلية حيث ان ترجمتها سيضع بعض الغموض وسيصعب عليك البحث والتعلم في هذا الموضوع فيما بعد لذلك ساكتبها كما هي وسأضع هنا بعض الترجمات التي امكنني العثور عليها 
</p>

- **Vector** : مُتجه
- **Word2Vec** : تمثيل الكلمات لمُتجهات
- **Continuous Bag of words (CBOW)** : واحدة من الطرق المستخدمة لتمثيل الكلمات كمتجهات
- **Natural language processing (NLP)** : معالجة اللغات الطبيية
- **Gradient descent** : هي الخوارزمية المستخدمة لتحسين الخطأ الناتج من التعلم قدر الامكان


##