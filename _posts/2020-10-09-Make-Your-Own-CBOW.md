---
title: "ما هو ال CBOW وكيفية تطويره من الصفر وتطبيقه على اللغة العربية"
tagline: "في هذا المقال سنتعرف أكثر على طريقة CBOW لصناعة Word2Vec وتطبيقها على اللغة العربية"
excerpt: "في هذا المقال سنتعرف أكثر على طريقة CBOW لصناعة Word2Vec وتطبيقها على اللغة العربية"
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
.wrapper, .toc__menu, table, .ara{
  direction: rtl;
}
p {
  text-align: justify;
  text-justify: inter-word;
}
</style>
 
 
## مقدمة
{: .text-right}
 
<p dir='rtl'>
في هذا المقال سنتعرف سويا على موضوع مهم في علم معالجة اللغات الطبيعية (NLP) وهو Word embeddings وكيفية تطبيقة باستخدام طريقة ال CBOW  والتي سنبرمجها معا من الصفر باستخدام  لغة البرمجة بايثون (Python)
</p>
 
 
## بعض المتطلبات المسبقة
{: .text-right}

<p dir='rtl'>
قبل ان نبدا معاً يجب ان نعرض بعض المتطلبات المسبقة لفهم المفال بشكل سهل وسلسل
</p>
 
- **Python**
- **Deep Learning**
- **Gradient descent**
 
## ترجمة بعض المصطلحات العلمية
{: .text-right}

<p dir='rtl'>
خلال هذا المقال سوف نستخدم المصطلحات العلمية بلغتها الاصلية حيث ان ترجمتها سيضع بعض الغموض و سيصعب عليك البحث والتعلم في هذا الموضوع فيما بعد لذلك سأكتبها كما هي وسأضع هنا بعض الترجمات التي يمكن العثور عليها 
</p>
 
- **Vector** : مُتجه
- **Word2Vec** : تمثيل الكلمات لمُتجهات
- **Continuous Bag of words (CBOW)** : واحدة من الطرق المستخدمة لتمثيل الكلمات كمتجهات
- **Natural language processing (NLP)** : معالجة اللغات الطبيعية
- **Gradient descent** : هي الخوارزمية المستخدمة لتحسين الخطأ الناتج من التعلم قدر الإمكان
- **Deep Learning** : التعلم العميق، وهو فرع من فروع علم الذكاء الاصطناعي
- **Model**:
- **Integers**: عدد صحيح
 
## مشكلة تمثيل الكلمات 
{: .text-right}

<p dir='rtl'>
في ال <strong>Deep learning</strong> يجب أن تكون البيانات المستخدمة في التعلم على هيئة أرقام ،فعلى سبيل اذا اردت بناء model للتعرف على مدى رضا العملاء عن خدمة معينة من خلال تعليقاتهم، فستحتاج بلا شك لبيانات  مكونة من كلمات (نصوص التعليقات المكتوب بواسطة العملاء) ، ولكي تبدأ عملية التعلم فعليك تحويل تلك النصوص لأرقام حتى يستطيع ال model التعامل معها، وهنا سيظهر سؤال كيف يمكن تمثيل النصوص لأرقام؟
</p>

## الطرق المختلفة لتمثيل النصوص الي ارقام
{: .text-right}

<p dir='rtl'>
هناك العديد من الطرق لتميل النصوص سنعرض هنا بض منها ، وسنطبق في النهاية بطريقة ال CBOW
</p>

## Integers
{: .text-right}
<p dir='rtl'>
في هذة الطريقة نستخرج الكلمات من النصوص المتوافرة لدينا بدون تكرار،وبعذ ذلك نضع لها رقم صحيح مميز لها . على سبيل المثال لنتخيل ان النصوص الاتية هي البيانات والمطلوب تمثيلها بطريقة الاعداد الصحيحة
</p>

<div dir='rtl' class='notice--success'>
1-   أنا أحب تعلم البرمجة
<br>
2- البرمجة تفتح أفق جديدة للعالم
<br>
3- أنا أحب تطوير تطبيقات جديدة
<br>
<br>
</div>

<p dir='rtl'>
الكلمات الموجودة في المثال بدون تكرار :
</p>

<div dir='rtl' class='notice--success'>
أنا-أحب-تعلم-البرمجة-تفتح-أفق-جديدة-للعالم-تطوير-تطبيقات   
</div>

| الكلمة      | الرقم |
| ----------- | ----------- |
| أنا      | 1       |
| أحب   | 2        |
| تعلم   | 3        |
| البرمجة   | 4        |
| تفتح   | 5        |
| أفق   | 6        |
| جديدة   | 7        |
| للعالم   | 8        |
| تطوير   | 9        |
| تطبيقات   | 10        |

<p dir='rtl'>
قد تكون هذة الطريقة حلت مشكلت تمثيل البيانات ولكن تظل هناك مشكلة حقيقية في ان الارقام   التي تمثل الكلمات لاتحمل اي معني، فيمكن تبديل الارقم المقابلة للكمات باي رقم اخر ،كمثال يمكن التبديل بين كلمتين (أنا:1 ، تعلم:3) في الجدول بالاعلى لتصبح (انا :3 ، تعلم:1)
</p>


## One-hot Vectors
{: .text-right}
<p dir='rtl'>
فهذة الطريقة تاخد كل كلمة رقم خاص بها ، ولكن هنا يتم تمثيل الكلمة من خلال متجه (Vector)  جميع عناصره قمها صفر ماعدا العنصر في المكان القابل لرقم الكلمة فيكون قيمته بصفر ، شكل هذا المتجه $$M*1$$ حيث M عدد الكلمات في النص بدون تكرار. ليكون الامر اوضح دعونا نطبق على المثال بالاعلى تلك الطريقة
</p>

<div dir='rtl' class='notice--success'>
في المثال بالاعلى كلمة "أفق" تحمل الرقم 6 ،سنلاحظ هنا ان المتجه الخاص بكلمة "أفق" جميع عناصره قيمتها صفر ماعاد العنصر رقم 6 ،سنلاحظ ايضا ان هذا المتجه خاص فقط بهذه الكلمة
</div>

|        | أفق |
| ----------- | ----------- |
| 1      | 0       |
| 2   | 0        |
| 3   | 0        |
| 4   | 0        |
| 5   | 0        |
| 6   | 1        |
| 7   | 0        |
| 8   | 0        |
| 9   | 0        |
| 10   | 0        |

<p dir='rtl'>
مميزات تلك الطريقة
</p>
- بسيطة لاتحمل تعقيد لبرمجتها
{: .ara}
<p dir='rtl'>
عيوب تلك الطريقة
</p>
- حجم المتجاهات يمكن ان يكون ضخم فنتخيل مثلا عدد الكلمات 12M 
{: .ara}
- المتجهات في هذة الحاله لاتحمل ايضا معني خاص للكلمة يعبر عنها
{: .ara}


## Word Embeddings
{: .text-right}
<p dir='rtl'>
لتبسيط هذا المفهوم ، تخيل ان هناك خط يزيد فيه المعنى الايجابي كلما اتجهنا يمينا، ويزيد فيه المعنى السلبي كلما اتجهنا لليسار كما هو موضح بالاسفل . فبهذة الطريقة  يمكننا تمثيل الكلمة من خلال متجه ذات بعد واحد 1*1 يعبر عن مدي ايجابية او سلبية الكلمة فعلى سبيل المثال اذا اخترنا رقم عشوائي (مثلاً 3) فبالتأكيد سيكون هذا الرقم تمثيل لكلمة إيجابية وعلى العكس إذا اخترنا رقم اقل من الصفر.
</p>

![words line](/images/2020-10-09-Make-Your-Own-CBOW/words_line.png)

<p dir='rtl'>
بنفس الطريقة تخيل ان هناك بدل الخط، محورين محور راسي ومحور افقي كما هو موضح بالاسفل،بحيث تكون الكلمة تعبر عن شئ مادي كلما اتجهنا لاعلى، وتعبر الكلمة عن شئ اكثر معنوية كلما اتجهنا لاسفل. مكننا التعبير عن الكلمة في هذة الحالة بمتجه بشكل 2*1 ، كمثال يمكنن تمثيل كلمة كلب بالمتجه [1.09, 0.57] . في هذهة الحالة اصبح المتجه يعبر بشكل اكبر عن الكلمة ، فكلما زادت قيمة المحور الافقي كلما ذادت المني الايجابي للكلمة ،وكلما زادت قيمت المحور الرأسي اصبحت الكلمة اكثر مادية، وهكذا في باقي الاتجاهات. فبذلك اصبح التثيل يعبر بقوة اكثر عن الكلمة. وكلما زادت المحاور او كما نسميها الابعاد التي تمثل الكلمة كلما اصبح هذا التمثيل يعبر بقوة اكثر عن الكلمة.
</p>


![words line](/images/2020-10-09-Make-Your-Own-CBOW/words_2axis.png)

<p dir='rtl'>
مميزات تلك الطريقة
</p>

- أصبحت المتجهات في هذة الطريقة حجمها اقل فعادة تكون بين 100~1000 ولكن في الطريقة السابقة ربما تتجاوز المليون 
{: .ara}

- أصبح المتجه يحمل معني لكل كلمة 
  -  اي المسافة بين الكلمات القريبة في المعني اقصر
    - مثال : متجه كلمة "شجرة" تقريبا يساوى المتجه الممثل لكلمة "أشجار" ويختلف تماماً عن المتجه الممثل لكلمة "تذكرة"
{: .ara}
  - تستطيع استخراج التشابه الجزئي بين الكلمات
    - مثال: كلمة "باريس" بالنسبة لكلمة "فرنسا"  مثل كلمة "القاهرة بالنسبة لكلمة ؟ فاذا كنا نمتلك Word Embeddings صحيحة فسيكون الناتج كلمة "مصر"
{: .ara}

<p dir='rtl'>
والان بعد ان تعرفنا على الفكرة الاساسية التي تعتمد عليها ال word embeddings 
كيف يمكننا حسابها ؟ فبالطبع لن نرسم محاور ونضع كل كلمة على تلك المحاور ، وهنا ياتي دول الDeep learning ، سنتعرف في الفقرات القادمة على الشهر الطرق المستخدمة لحساب تلك المتجهات
</p>

## الطرق المختلفة حصول على ال Word Embeddings
{: .text-right}
<p dir='rtl'>
والان بعد ان تعرفنا على الفكرة الاساسية التي تعتمد عليها ال word embeddings 
كيف يمكننا حسابها ؟ فبالطبع لن نرسم محاور ونضع كل كلمة على تلك المحاور ، وهنا ياتي دول الDeep learning ، سنتعرف في الفقرات القادمة على الشهر الطرق المستخدمة لحساب تلك المتجهات
</p>

## ١- طرق بسيطة 
{: .text-right}
<p dir='rtl'>
المشترك في هذة الطرق الثلاثة ان كل كلمة لها متجه واحد فقط بغض النظر عن موقع الكلمة في الجملة (كمثال: رأيت عين ما)   
</p>

<div dir='rtl' class='notice--success'>
١- رأيت عين ماء في الصحراء
<br>
٢- يوجد للذبابة أكثر من عين
<br>
</div>

<p dir='rtl'>
معنى كلمة "عين" في الجملة الاولى تختلف عن معني "عين" في الجملة الثانية ولكن لهما نفس قيمة المتجه  
</p>

- Word2Vec (Google, 2013)
  - Continuous bag-of-word (CBOW)
  - Continuous Skip-gram/Skip-gram with negative sampling (SGNS)

- Global Vectors (GLoVe) (Stanford, 2014)

- fastText (Facebook, 2016)
  - المميز في هذهة الطريقة انها تستطيع حساب متجهات لكلمات لم تراها من قبل لانها تعمل على الحروف بدلاً عن الكلمات


## ٢- طرق أكثر تعقيداً
{: .text-right}
<p dir='rtl'>
المشترك في هذهة الطرق الثلاثة ان حساب المتجه للكلمة متغير بحسب معناها في الجملة، فإذا طبقنا على الثال السابق ، سيصبح لكلمة "عين" في الجملتين متجهين مختلفين 
</p>

- BERT (Goole, 2018)
- ELMo (Allen Institute for AI, 2018)
- GPT-2 (OpenAI, 2018)

## ما هي طريقة ال CBOW 
{: .ara}
{: .text-right}
<p dir='rtl'>
CBOW هو طريقة نستطيع بها حساب ال Word emmbeddings ، وتعتمد هذة الطريقة على  تدريب model للتنبؤ بالكلمة من مجموعة الكلمات المحيطة بتلك الكلمة. مثال : 
</p>

<div dir='rtl' class='notice--success'>
رأيت اليوم __؟__ صغير ينبح.
</div>

<p dir='rtl'>
الكلمات المتاحة لل model للتنبؤ :
</p>

<div dir='rtl' class='notice--success'>
[كلب - قطة - ورقة - ملعب]
</div>
<p dir='rtl'>
قيجب ان يختار ال model هنا كلمة كلب 
</p>

## كيفية تحضير الامثلة وال dataset
{: .ara}
{: .text-right}
<p dir='rtl'>
قبل البدء في شرح هذا بالتفصيل ، هناك بعض المفاهيم التي يجب معرفتها
</p>

<p dir='rtl'>
تسمى الكلمة المراد التنبؤ بها ب (center word)، وتسمى الكلمات المحيطة بالكلمة من اليمين و اليسار بال (Context Word) ، عدد الكلمات المستخدم على  اي جانب من بال (context half-size)، ويرمز لها بالرمز (C) ، ويطلق على مجموع (Cotext word + center word) بال (windows) ويتم حسابه باستخدام هذة المعادلة $$2*c + 1$$
مثال :
</p>

<div dir='rtl' class='notice--success'>
كما هو موضح بالصورة ال (context half-size) يساوي 2، لحساب ال (windows size)، نستخدم المعادلة  بالاعلى   
$$window size=2*5+1=5$$
</div>

![words line](/images/2020-10-09-Make-Your-Own-CBOW/cbow_ele.png)


## شكل ال Dataset
{: .ara}
{: .text-right}
<p dir='rtl'>
لنستطيع تطبيق طريقة ال CBOW يجب ان تكون النصوص بشكل معين، وهو ان تكون ال (Context words) هي المدخلات لل Model وتكون ال المخرجات او ال labels هي ال (Ccenter word) ، فلنفترض ان النصوص   بالاسفل سنستخدمها في عملية التدريب، وسنستخدم (context half-sizr) يساوي 1 
</p>

<div dir='rtl' class='notice--success'>
القراءة عملية معرفية تستند على تفكيك رموز تسمى حروفا لتكوين معنى، والوصول إلى مرحلة الفهم والإدراك.
</div>


<p dir='rtl'>
فستكون ال Dataset بهذا الشكل بالاسفل ، حيث سيرمز للمدخلات بالرمز X  والمخرجات بالرمز Y
</p>



| X      | Y |
| ----------- | ----------- |
| القراءة معرفية      | عملية       |
| عملية تستند   | معرفية        |
| معرفية على   | تستند        |
| تستند تفكيك   | على        |
| على رموز   | تفكيك        |
| تفكيك تسمى   | رموز        |
| رموز حروفا   | تسمى        |
| تسمى لتكوين   | حروفا        |
| حروفا معنى   | لتكوين        |
| لتكوين والوصول   | معنى        |
| معنى إالى   | والوصول        |
| والوصول مرحلة   | إالى        |
| إالى الفهم   | مرحلة        |
| مرحلة والإدراك   | الفهم        |
| الفهم  .   | والإدراك        |


<p dir='rtl'>
والآن بعد ان عرفنا ما هو الشكل المفترض لل Dataset ، هناك اسئلة حلو كيفية تحويل تلك النصوص لارقام ؟ وماهو الهيكل لشبكة التعلم العميق ؟ وكيفية استخراج ال Word Embeddings ? وكيفية تقيم جوتها ؟ ، كل هذا سنجيب عليه عمليا في الفقرات التالية
</p>


## معالجة النصوص (Data preprocesing)
{: .ara}

<p dir='rtl'>
دائما ما تحتوي البيانات على اشياء لانرعب بها ، او اشياء يجب تعديلها لتناسب ال modell الخاص بنا. في حالة النصوص قد تحتوي على علامات ترقيم مثلا (:,.!? ..etc), او قد تحتوي على رموز تعبيرية مثل (😀😂😌❤️), او هشاتاج مثلا (#NLP, #Python), او قد تحتوي على روابط مثل (gooogle.com - github.com)
</p>

<p dir='rtl'>
وبالنسبة للغة العربية بالإضافة للمشاكل السابقة قد تحتوي على علامات تشكيل مثلا (الكسرة والسكون و الشدة و...), وقد يكتب البعض الياء المنقوطة بدلا من الياء الغير منقوطة والعكس او الكاف الفارسية بدلا من الكاف وهذا سيخلق العديد من المشالك من ضمنها تكرار الكلام مثلا (التي - التى) ستتصبح كلمتين مختلفتين بسبب اختلاف الياء بالخطأ. و الحل ؟!
</p>


<p dir='rtl'>
يعتمد الحل عل التطبيق الذي تعمل عليه، ماالذي يجب عليك الاحتفاظ به و مالذي يجب عليك تغيره. مثلا لو افترضنا اننا نعمل ل انشاء word embeddings خاصة بالغة العربية الفصحى او اى تطبيق سيكون فيها علامات التشكيل فيجب ان نترك تلك العلامات. اما اذا افترض اننا نعمل على تطبيق سيستخدم لغة عامية فنستطيع ان نزيل علامات التشكيل،  فالعامية لا تحتاج علامات تشكيل بصورة كبيرة. ايضا يجب عليك ان تعرف هل سيكون مثلا الرموز التعبيرية مؤثرة في التطبيق ام لا. كل هذهة الامور يجب ان تكون في ذهنك قبل بدء عملية معالجة النص
</p>



<p dir='rtl'>
في هذا المقال سنستخدم فقط الحروف العربية والنقطة، وسنبسط اكثر اللغة اي سنزيل علامات الترقيم، وسنستبدل بعض الحروف التي ستعمل على تكرار كلمات بنفس المعني مثلا (أ-إ-آ) 
</p>


### ١- إزالة علامات الترقيم
{: .ara}

<p dir='rtl'>
سنستخدم هنا مكتبة re وهي اختصار ل (Regular expression) وهو مفهم مهم جدا يجب تلمه ، لانه يسهل العديد من العمليات على النصوص
</p>

```python
import re

arabic_diacritics = re.compile("""
                             ّ    | # Tashdid
                             َ    | # Fatha
                             ً    | # Tanwin Fath
                             ُ    | # Damma
                             ٌ    | # Tanwin Damm
                             ِ    | # Kasra
                             ٍ    | # Tanwin Kasr
                             ْ    | # Sukun
                             ـ     # Tatwil/Kashida
                         """, re.VERBOSE)

def remove_diacritics(text):
    text = re.sub(arabic_diacritics, '', text)
    return text
```

<p dir='rtl'>
مثال
</p>

```python
text = 'هَلْ غَادَرَ الشُّعَرَاءُ منْ مُتَـرَدَّمِ'
print(remove_diacritics(text))
```
<p dir='rtl'>
النتيجة
</p>

```python
'هل غادر الشعراء من متردم'
```

### ٢- تبسيط حروف اللغة
{: .ara}

<p dir='rtl'>
هنا سنستبدل الحروف التي يمكن كتابتها باكثر من شكل واستبدالها بشكل واحد فقط للتبسيط مثل    te (إ-أ-آ) سنستبدله ب [ا]
</p>

```python
def normalize_arabic(text):
    text = re.sub("[إأآا]", "ا", text)
    text = re.sub("ى", "ي", text)
    text = re.sub("ة", "ه", text)
    text = re.sub("گ", "ك", text)
    text = re.sub("وال", "و ال", text)
    return text
```

<p dir='rtl'>
مثال
</p>

```python
text = 'أحب لعب كرة القدم وگرة السلة'
print(normalize_arabic(text))
```
<p dir='rtl'>
النتيجة
</p>

```python
'احب لعب كره القدم وكره السله'
```

### ٣- النصوص المختلطة
{: .ara}
<p dir='rtl'>
تكون النصوص العربية احيانا بها حروف وعلامات ترقيم واحيانا بها ابجديات من لغات اخرى. لذلك هن سنستخدم المكتبة re لنستخرج فقط الحروف العربية والنقطة
</p>

![words line](/images/2020-10-09-Make-Your-Own-CBOW/arabic_rep.png)

<p dir='rtl'>
اذا استخدمنا الجدول بالصورة لنعرف الكود المقابل للحروف العربية، سنرى انها تقع بين الحرفين (ء-ي) اي بين الكود (0621-064A)
</p>

```python
def get_arabic_and_full_stop(text):
    text = re.findall(r'[\u0621-\u064a]+|\.', text)
    return text
```
<p dir='rtl'>
مثال
</p>

```python
text = 'أحب قيادة السيارات من نوع BMW M850 .'
print(get_arabic_and_full_stop(text))
```
<p dir='rtl'>
النتيجة
</p>

```python
['أحب', 'قيادة', 'السيارات', 'من', 'نوع', '.']
```

<p dir='rtl'>
سنضع كل النصوص التي عالجناها في هيئة list بحيث يسهل التعامل معها، واستخراج ال (center words) و (context words), وتحويهل الي الشكل المطلوب
</p>


### ٤- نوع النصوص المستخدمة في التدريب (Dataset)
{: .ara}

<p dir='rtl'>
هاك العديد من النصوص التي يمكن تحميلاها من المواقع المختلفة، اخترت هذة النصوص لسهولة التعام معها واستخدمامها. معظم هذة النصصوص من جرائد ومقالات. خلال العمل في هذا المشروع سنضع اي نصوص في المجلد (data) 
</p>

<p dir='rtl'>
تلك هي خريطة المجلدات في المجلد (data) بداخل كل مجلد يوجد العديد من الملفات المكتوبة باللغة العربية، والتي سنعالجها وكما راينا بالاعلى ، لتصبح جاهزة للخطوة القادمة
</p>

```
├───data
│   ├───articlesSports
│   ├───Echoroukonline
│   └───Khaleej-2004
│       ├───Economy
│       ├───International news
│       ├───Local News
│       └───Sports
```

<p dir='rtl'>
بعد ان عرفنا كيف سنعالج النصوص ، لنضع الآن كل ماتعلمناه سابقا، لنفتح الملفات ونعالج النصوص
</p>

```python
data = []
root = '.\\data'
# get all files
files_paths = [ os.path.join(path, name) for path, subdirs, files in os.walk(root) for name in files]

# loop over files and process them, then append them to the data list
for file_path in files_paths:

    f = open(file_path, 'r', encoding='utf-8')

    for line in f :
        text = remove_diacritics(line)
        text = normalize_arabic(text)
        text = get_arabic_and_full_stop(text)
        data += text
    f.close()
```

<p dir='rtl'>
والآن بعد حولنا جميع النصوص ووضعناها في list يجب علينا الآن ان نحولها ما شرحنا في هذهة  الخطوة   
<a href="#شكل-ال-dataset"> شكل ال Dataset</a> ولكن تحويلهاوتخزينها في الذكرة هكذا لن يون عملي حيث ان عدد الكلمات اكثر من 40M لذلك سنستخدم طريقة (Sliding window) او بالعربية إزاحة النافذة   وسنستخدم  ايضا ال generators وهو ميزة رائعة من بايثون ستساعدنا للتعامل مع تلك البيانات الكبيرة، اذا لم تسمع عنها من قبل يجب ان تتعلمها فهي جزء مهم.
</p>

```python
def get_windows(words, c):
    i = c
    while i < len(words) - c:
        center_word = words[i]
        context_words = words[(i-c):i] + words[(i+1):(i+c+1)]
        yield center_word, context_words
        i += 1
```
<p dir='rtl'>
مثال
</p>

```python
gen = get_windows(['احب'  ,'قيادة'  ,'السيارات'  ,'و' ,'احب' ,'قيادة'  ,'الدراجات'],1)
for center, context in gen :
    print(f'{center}\t{context}')
```

<p dir='rtl'>
النتيجة
</p>

```python
قيادة	['احب', 'السيارات']
السيارات	['قيادة', 'و']
و	['السيارات', 'احب']
احب	['و', 'قيادة']
قيادة	['احب', 'الدراجات']
```

## تحويل النصوصل الي one-hot vector
{: .ara}
<p dir='rtl'>
ولنبدا بتدريب ال model يجب تحويل اصورة في المثال السابق الي ارقام. سنستعمل في هذا طريقة one-hot vectors 
</p>

<p dir='rtl'>
اولا استخراج الكلمات بدون تكرار . مثال 
</p>

```python
"أحب  قيادة السيارات و أحب  قيادة الدراجات"
```

<p dir='rtl'>
الكلمات بدون تكرار
</p>


```python
أحب - قيادة - السيارات - و - الدراجات
```

<p dir='rtl'>
بد تحويلها ستون بهذا الشكل
</p>

![words line](/images/2020-10-09-Make-Your-Own-CBOW/one_hot.png)

<p dir='rtl'>
جيد بد حولنا الكلمات في النصوص الي one-hot vectors كيف شكل المدخلات (Cotext words) التي حولناعا الي ال model للتعلم، لنفترض ان لدين هذا المدخل
</p>

```python
['احب', 'السيارات']
```

<p dir='rtl'>
لتحويله الي vector واد فقط بدلا من اربعة، سنجنمع قيم ال vectors ثم نقسمها على عددها، لتصبح بذلك الشكل
</p>

![words line](/images/2020-10-09-Make-Your-Own-CBOW/average.png)

<p dir='rtl'>
ولتحويل ال (Center word) لن نحتاج لهذا ، فهي كلمة واحدة
</p>

<p dir='rtl'>
مثال : ال (center words) للمثال بالاعلى
</p>

```python
'قيادة'
```

<p dir='rtl'>
وقيمة المتجه الخاص بهل لن تتغير
</p>

![words line](/images/2020-10-09-Make-Your-Own-CBOW/center_words.png)



<p dir='rtl'>
الشكل النهائي لل Dataset
</p>

  
![words line](/images/2020-10-09-Make-Your-Own-CBOW/final_dataset.png)




## الهيكل البنائي لل model الخاص بال CBOW
{: .ara}



<p dir='rtl'>
يتكون ال momdel باستخدام ما يعرف بال (Shallow neural networks) او الشبكات التي تتون من عدد صغي جدا من الطبقات (layers)،
</p>

<p dir='rtl'>
ستكون هنا الشكبة الخاصة بال CBOW من ثلاثة مراحل (layers) فقط. المرحلة الاولى ستكون للمدخلات (input layer)، والمرحلة الثانية او الداخلية (hidden layers)، والمرحلةالثالثة للمخرجات (output layer).
</p>


<p dir='rtl'>
عدد العصبونات (node or neurons) في المرحلة الاولى والاخيرة سيكون متساوى ، حيث انها ستساوي عدد الكلمات بدون تكرار في ال (dataset)
</p>



<p dir='rtl'>
بالنسبة للمحلة الثانية سيكون عدد العصبونات فيها اختياري كما شرحنا سابقا،
</p>

<p dir='rtl'>
الابعاد الخاصة بكل مرحلة
</p>


- Vocabulary size = V
- input layer = V*1
- output layer = V*1
- hidden layer = N*1

<p dir='rtl'>
من تلك الابعاد يكمن استنتاج الابعاد الخاصة بال (weights) وتكون كلاتي
</p>

- W1 = N*v
- b1 = N*1
- W2 = V*N
- b2 = V*1

![words line](/images/2020-10-09-Make-Your-Own-CBOW/network_shape.png)


<p dir='rtl'>
ولن عند استخدام ال batch inputs ستتغير الابعاد قليلا ، لتناسب عدد ال batches
</p>


- Vocabulary size = V
- Batch size = m
- input layer = V*m
- output layer = V*m
- hidden layer = N*m


<p dir='rtl'>
هنا لن يتغير الابعد الخاصة ب (W1 , W2)
</p>


- b2 = V*m
- b2 = V*m

![words line](/images/2020-10-09-Make-Your-Own-CBOW/batch_shape.png)

## عملية التعلم
{: .ara}


<p dir='rtl'>
تنقسم عملية التعلم اللى ثلاث
</p>

- 1- Forward Propagation
<p dir='rtl'>
وهنا يتم ادخال البيانات خلال الشبكة والتنبؤ بالناتج
</p>

<p dir='rtl'>
في اول الامر نستخدم قيم عشوائة ل (W1, W2)
</p>


```python
def initialize_model(N,V, random_seed=1):

    np.random.seed(random_seed)

    # W1 has shape (N,V)
    W1 = np.random.rand(N,V)
    # W2 has shape (V,N)
    W2 = np.random.rand(V,N)
    # b1 has shape (N,1)
    b1 = np.random.rand(N,1)
    # b2 has shape (V,1)
    b2 = np.random.rand(V,1)

    return W1, W2, b1, b2
```

```python
def forward_prop(x, W1, W2, b1, b2):

    # Calculate h
    h = np.dot(W1,x)+b1
    # Apply the relu on h (store result in h)
    h = np.maximum(0,h)    
    # Calculate z
    z = np.dot(W2,h)+b2

    return z, h
```

- 2- Cost

<p dir='rtl'>
نحسب ال (cost) او ثمن الالاخطاء الناتجة من ال (forward propagation) ، فالهدف من عملية التدريب هو تقليل هذة الاخطاء قدر الامكان
</p>

```python
def compute_cost(y, yhat, batch_size):
    logprobs = np.multiply(np.log(yhat),y) + np.multiply(np.log(1 - yhat), 1 - y)
    cost = - 1/batch_size * np.sum(logprobs)
    cost = np.squeeze(cost)
    return cost
```

- Backpropagation an gradient descent

<p dir='rtl'>
فهذة المرحلة نستخدم ال (Gradient descent) ، لتعديل قيم ال (weights) لتقليل نسبة الخطأ
</p>

<p dir='rtl'>
ان كنت لم تسمع عن (Gradient descent) فانصح بقراءة هذة  
<a href="https://aliabdelaal.github.io/blog/gradient-descent-family/"> المقالة</a>
</p>

```python
def back_prop(x, yhat, y, h, W1, W2, b1, b2, batch_size):

    # Re-use it whenever you see W2^T (Yhat - Y) used to compute a gradient
    l1 = np.dot(W2.T,(yhat-y))
    # Apply relu to l1
    l1 = np.maximum(0,l1)
    # Compute the gradient of W1
    grad_W1 = (1/batch_size)*np.dot(l1,x.T)    
    # Compute the gradient of W2
    grad_W2 = (1/batch_size)*np.dot(yhat-y,h.T)
    # Compute the gradient of b1
    grad_b1 = np.sum((1/batch_size)*np.dot(l1,x.T),axis=1,keepdims=True)
    # Compute the gradient of b2
    grad_b2 = np.sum((1/batch_size)*np.dot(yhat-y,h.T),axis=1,keepdims=True)
    
    return grad_W1, grad_W2, grad_b1, grad_b2
```

<p dir='rtl'>
فيمكن تلخيص هذة العملية من خلال الصورة بالاسفل
</p>


![words line](/images/2020-10-09-Make-Your-Own-CBOW/train_process.png)

<p dir='rtl'>
الان بعد ان انتهينا من المفاهيم الاساسية اللازمة للتطبيق ، لنناقش الان بعض المتغيرات التي سيتوقف عليها نتائج الموديل
</p>

- ١- عدد كلمات ال (Context words)
{: .ara}

<p dir='rtl'>
عن تغير هذا المعامل ستتغير الكفاءة النهائية ، سنستخدم هنا  قيمة المعامل ب 2 (C)
</p>

- ٢- ابعاد ال (word embeddigs) 
{: .ara}

<p dir='rtl'>
ستستخدم هنا متجه  بشكل (300*1) 
</p>

- ٣- معامل التعلم 
{: .ara}

<p dir='rtl'>
ستسنستخدم هنا معامل قيمته .03 وسيقل بمقدار .66 في كل لفة ،
</p>

- ٤- عدد اللفات
{: .ara}

<p dir='rtl'>
سنستخدم هنا عدًد اللفات ب 20
</p>

```python
def gradient_descent(data, word2Ind, N, V, num_iters, C=2, batch_size=128, alpha=0.03):

    W1, W2, b1, b2 = initialize_model(N,V, random_seed=282)
    batch_size = batch_size
    iters = 0
    C = C
    for x, y in get_batches(data, word2Ind, V, C, batch_size):

        # Get z and h
        z, h = forward_prop(x, W1, W2, b1, b2)
        # Get yhat
        yhat = softmax(z)
        # Get cost
        cost = compute_cost(y, yhat, batch_size)
        if ( (iters+1) % 2 == 0):
            print(f"iters: {iters + 1} cost: {cost:.6f}")
   
        # Get gradients
        grad_W1, grad_W2, grad_b1, grad_b2 = back_prop(x, yhat, y, h, W1, W2, b1, b2, batch_size)
        
        # Update weights and biases
        W1 -= alpha*grad_W1 
        W2 -= alpha*grad_W2
        b1 -= alpha*grad_b1
        b2 -= alpha*grad_b2
        
        iters += 1 
        if iters == num_iters: 
            break
        if iters % 100 == 0:
            alpha *= 0.66
            
    return W1, W2, b1, b2
```

```python
C = 2
batch_size = 128

# getdict is o=holding the words and its index
word2Ind = get_dict(data)
V = len(word2Ind)
num_iters = 20

print("Call gradient_descent")
W1, W2, b1, b2 = gradient_descent(data, word2Ind, N, V, num_iters, C, batch_size)
```

<p dir='rtl'>
النتيجة
</p>

```python
Call gradient_descent
iters: 2 cost: 14.706067
iters: 4 cost: 0.004671
iters: 6 cost: 0.004275
iters: 8 cost: 0.003942
iters: 10 cost: 0.003657
iters: 12 cost: 0.003411
iters: 14 cost: 0.003196
iters: 16 cost: 0.003007
iters: 18 cost: 0.002839
iters: 20 cost: 0.002689
```
## استخراج ال Word Embeddings
{: .ara}

<p dir='rtl'>
يوجد ثلاث اختيارات يمكننا بيها استخراج ال word embeddings
</p>


- ١- من خلال W1
{: .ara}


- ٢- من خلال W2
{: .ara}


- ٣- من خلال متوسط القيم بين (W1 , W2)
{: .ara}




































