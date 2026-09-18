# بخش اول — تعریف مسئله

## اسلاید 1 — Drug–Target Interaction چیست؟

### عنوان

**برهم‌کنش دارو–هدف (Drug–Target Interaction | DTI)**

### محتوای اسلاید

در فرآیند کشف دارو، یکی از مسائل اساسی شناسایی ارتباط میان:

**Drug ↔ Target Protein**

است.

هدف DTI این است که مشخص کند:

> آیا یک دارو می‌تواند با یک پروتئین هدف برهم‌کنش داشته باشد یا خیر؟

نمایش ساده:

```text
Drug D
   +
Target T
   ↓
Interaction?
   ↓
Yes / No
```

### نکته شفاهی

اینجا باید DTI را به عنوان **مسئله پایه** معرفی کنی.

یعنی:

> «اگر بخواهیم فضای جستجوی drug discovery را ساده کنیم، یکی از اولین سؤالات این است که از میان تعداد زیادی دارو و پروتئین، کدام جفت‌ها احتمالاً با یکدیگر interaction دارند.»

Co-VAE هم Introduction خود را تقریباً از همین نقطه شروع می‌کند و می‌گوید بسیاری از روش‌های computational برای تعیین وجود یا عدم وجود interaction توسعه یافته‌اند. ([PubMed][1])

---

# اسلاید 2 — محدودیت DTI: Interaction فقط یک جواب بله/خیر نیست

### عنوان

**از تشخیص Interaction به اندازه‌گیری قدرت Interaction**

### محتوای اسلاید

DTI:

```text
Drug + Target
      ↓
Interaction?
      ↓
Yes / No
```

اما در واقعیت ممکن است دو دارو هر دو با یک target برهم‌کنش داشته باشند ولی:

* یکی اتصال بسیار قوی داشته باشد
* دیگری اتصال ضعیف‌تری داشته باشد.

بنابراین سؤال دقیق‌تر این است:

> **قدرت اتصال دارو به هدف چقدر است؟**

اینجاست که مسئله **Drug–Target Binding Affinity (DTA)** مطرح می‌شود.

---

# اسلاید 3 — چرا DTA؟

### عنوان

**چرا پیش‌بینی Binding Affinity مهم‌تر از صرفاً Interaction است؟**

### محتوای اصلی

مقایسه:

| DTI                        | DTA                          |
| -------------------------- | ---------------------------- |
| آیا interaction وجود دارد؟ | interaction چقدر قوی است؟    |
| Binary                     | Continuous / quantitative    |
| Yes / No                   | مقدار affinity               |
| مناسب برای screening اولیه | اطلاعات دقیق‌تر برای ranking |

### مثال

فرض کن:

```text
Drug A → Target X → Interaction = Yes
Drug B → Target X → Interaction = Yes
```

DTI هر دو را مشابه می‌بیند.

اما DTA:

```text
Drug A → X → Strong binding
Drug B → X → Weak binding
```

را از یکدیگر تفکیک می‌کند.

### جمله مهم برای ارائه

> **بنابراین DTA اطلاعات غنی‌تری نسبت به DTI فراهم می‌کند، زیرا علاوه بر وجود interaction، شدت آن را نیز مشخص می‌کند.**

البته بهتر است نگویی DTA «همیشه بهتر» از DTI است؛ علمی‌تر این است که بگویی **برای هدف این پژوهش، اطلاعات کمیِ affinity ارزشمندتر و informative‌تر است.**

Co-VAE نیز دقیقاً binding affinity را به عنوان نوعی داده معرفی می‌کند که **strength of the binding interaction** را نشان می‌دهد و پیش‌بینی آن را چالش‌برانگیزتر از تشخیص interaction می‌داند. ([PubMed][1])

---

# اسلاید 4 — Binding Affinity چگونه اندازه‌گیری می‌شود؟

این اسلاید را حتماً داشته باش.

### عنوان

**معیارهای متداول برای اندازه‌گیری Binding Affinity**

چهار اصطلاح اصلی:

### 1. Kd

### 2. pKd

### 3. Ki

### 4. pKi

### 5. IC50

---

# اسلاید 5 — Kd و pKd

### عنوان

**Kd و pKd — Dissociation Constant**

### Kd چیست؟

**Kd = Equilibrium Dissociation Constant**

معیاری برای اندازه‌گیری affinity یک ligand نسبت به target.

از نظر مفهومی:

> Kd غلظتی از ligand است که در شرایط تعادل، تقریباً نیمی از targetها را در حالت bound قرار می‌دهد.

رابطه تعادلی:

$$
K_d = \frac{[D][T]}{[DT]}
$$

که در آن:

* \(D\): داروی آزاد
* \(T\): target آزاد
* \(DT\): complex دارو–target

### تفسیر

**Kd پایین‌تر → binding قوی‌تر**

**Kd بالاتر → binding ضعیف‌تر**

مثلاً:

```text
Drug A: Kd = 1 nM
Drug B: Kd = 100 nM

A → affinity بالاتر
B → affinity پایین‌تر
```

این رابطه با تعریف pharmacological affinity نیز سازگار است: Kd معیار متداول affinity است و مقدار کمتر Kd نشان‌دهنده affinity بالاتر است. ([BPS Journals][2])

---

### pKd چیست؟

برای تبدیل مقادیر بسیار کوچک Kd به مقیاس مناسب‌تر:

$$
pK_d=-\log_{10}(K_d[M])
$$

بنابراین:

**Kd پایین‌تر → pKd بالاتر**

مثلاً اگر:

$$
K_d=10^{-9} M
$$

آنگاه:

$$
pK_d=9
$$

پس:

```text
Kd ↓
Affinity ↑
pKd ↑
```

---

# اسلاید 6 — Ki و pKi

### عنوان

**Ki و pKi — Inhibition Constant**

Ki بیشتر در زمینه **inhibitor–target** مطرح می‌شود.

### Ki چیست؟

**Inhibition Constant**

معیاری برای بیان affinity یک inhibitor نسبت به target/enzyme.

به‌صورت مفهومی:

> هرچه Ki کوچک‌تر باشد، inhibitor برای اتصال مؤثر به target تمایل بیشتری دارد.

بنابراین:

```text
Ki ↓ → Affinity ↑
```

### pKi

مشابه pKd:

$$
pK_i=-\log_{10}(K_i[M])
$$

پس:

```text
Ki ↓
pKi ↑
Affinity ↑
```

### نکته مهم

**Ki و Kd کاملاً یک چیز نیستند.**

* Kd مستقیماً equilibrium dissociation را در binding system بیان می‌کند.
* Ki معمولاً در زمینه inhibition و potency/affinity inhibitor استفاده می‌شود.

ادبیات DTA نیز Ki را به عنوان شاخص affinity inhibitor و IC50 را به عنوان غلظت لازم برای ایجاد 50٪ inhibition تعریف می‌کند. ([PubMed Central (PMC)][3])

---

# اسلاید 7 — IC50

### عنوان

**IC50 — Half Maximal Inhibitory Concentration**

IC50 یعنی:

> غلظت inhibitor که باعث **50٪ کاهش فعالیت** یک سیستم/آنزیم می‌شود.

مثلاً:

```text
Inhibitor concentration ↑

0.1 μM → 10% inhibition
1 μM   → 30%
10 μM  → 50%  ← IC50
100 μM → 80%
```

### اما یک نکته بسیار مهم:

**IC50 مستقیماً binding affinity نیست.**

بلکه یک معیار **functional inhibition / potency** است.

این مقدار به شرایط آزمایش و عواملی مانند substrate concentration وابسته است.

بنابراین:

```text
Kd / Ki
      ↓
Binding affinity

IC50
      ↓
Functional inhibition / potency
```

این تمایز را حتماً در ارائه بگو؛ چون از نظر علمی مهم است. ادبیات مقایسه ابزارهای DTA نیز صراحتاً تأکید می‌کند که IC50 شاخص مستقیم affinity نیست، در حالی که Ki بیانگر binding affinity inhibitor است. ([Frontiers][4])

---

# اسلاید 8 — جمع‌بندی معیارهای Affinity

### عنوان

**مقایسه معیارهای Binding / Inhibition**

| Metric   | مفهوم                         | مقدار کمتر یعنی |
| -------- | ----------------------------- | --------------- |
| **Kd**   | Dissociation constant         | Affinity بیشتر  |
| **pKd**  | −log₁₀(Kd)                    | Affinity بیشتر  |
| **Ki**   | Inhibition constant           | Affinity بیشتر  |
| **pKi**  | −log₁₀(Ki)                    | Affinity بیشتر  |
| **IC50** | غلظت لازم برای 50٪ inhibition | Potency بیشتر   |

و پایین اسلاید:

> **در بسیاری از benchmarkهای DTA، مقادیر affinity به شکل لگاریتمی مانند pKd یا pKi مورد استفاده قرار می‌گیرند.**

این موضوع مستقیماً به DCGAN-DTA هم مربوط است: نویسندگان برای BindingDB از نسخه Kd استفاده کرده‌اند و affinityها را به **pKd** تبدیل کرده‌اند؛ PDBBind نیز شامل مقادیر log-transformed از Ki و Kd است. ([Springer][5])

---

# بخش دوم — مسیر پژوهشی

# اسلاید 9 — مسیر تاریخی پژوهش در DTA

### عنوان

**مسیر تکامل روش‌های پیش‌بینی Drug–Target Affinity**

اینجا به جای bullet list، پیشنهاد می‌کنم **حتماً درخت پژوهشی** بگذاری.

```text
              Drug–Target Affinity Prediction
                         │
             ┌───────────┴───────────┐
             │                       │
      Classical Methods       Machine Learning
             │                       │
      Similarity / Kernel      Feature-based ML
             │                       │
             └───────────┬───────────┘
                         │
                  Deep Learning
                         │
          ┌──────────────┼──────────────┐
          │              │              │
        CNN         Sequence-based     Graph
          │              │              │
          └──────────────┼──────────────┘
                         │
              Representation Learning
                         │
                  Generative Models
                         │
              ┌──────────┴──────────┐
              │                     │
             GAN                   VAE
              │                     │
         DCGAN-DTA               Co-VAE
```

این **یکی از مهم‌ترین اسلایدهای کل ارائه** خواهد بود.

---

# اسلاید 10 — مرحله اول: روش‌های کلاسیک

### عنوان

**روش‌های کلاسیک و Similarity-Based**

ایده اصلی:

اگر دو دارو شبیه هم باشند، احتمالاً رفتار مشابهی نسبت به targetها دارند.

و بالعکس.

استفاده از:

* Molecular similarity
* Protein similarity
* Kernel methods
* Matrix-based methods

نمونه:

**KronRLS**

### محدودیت

این روش‌ها شدیداً به:

> **تعریف similarity و quality of handcrafted representations**

وابسته هستند.

---

# اسلاید 11 — مرحله دوم: Machine Learning

### عنوان

**حرکت به سمت Machine Learning**

ایده:

به جای تعیین مستقیم رابطه similarity، مدل از داده یاد بگیرد.

```text
Drug Features
       +
Protein Features
       ↓
Machine Learning Model
       ↓
Affinity
```

روش‌های این مرحله می‌توانستند از:

* fingerprints
* sequence features
* physicochemical features
* similarity matrices

استفاده کنند.

### مشکل باقی‌مانده:

**Feature Engineering**

یعنی انسان باید تصمیم بگیرد:

> چه ویژگی‌هایی برای دارو و پروتئین مهم هستند؟

---

# اسلاید 12 — مرحله سوم: Deep Learning

### عنوان

**Deep Learning و Representation Learning**

ایده اصلی تغییر می‌کند:

به جای اینکه featureهای نهایی را دستی تعریف کنیم:

```text
Raw Representation
       ↓
Deep Neural Network
       ↓
Learned Representation
       ↓
Affinity
```

نمونه مهم:

### DeepDTA

استفاده از:

* SMILES برای drug
* Protein sequence برای target
* CNN برای استخراج feature

سپس prediction affinity.

---

# اسلاید 13 — مرحله چهارم: Graph و Representationهای پیچیده‌تر

### عنوان

**حرکت به سمت نمایش ساختاری‌تر**

در روش‌های مبتنی بر graph:

```text
Drug
 ↓
Molecular Graph
 ↓
Graph Neural Network
 ↓
Drug Representation
```

به جای اینکه molecule صرفاً یک string باشد، ساختار:

**Atom → Node**

**Bond → Edge**

مدل می‌شود.

هم‌زمان روش‌های sequence-based برای protein representation نیز توسعه پیدا کردند.

### نتیجه

تمرکز پژوهش از:

**Feature Engineering**

به:

**Representation Learning**

منتقل شد.

---

# اسلاید 14 — مرحله پنجم: Generative Models

### عنوان

**حرکت به سمت Generative Representation Learning**

اینجا باید پل به دو مقاله ایجاد شود.

### چرا Generative Models؟

چالش‌های باقی‌مانده:

* محدودیت داده برچسب‌دار
* پیچیدگی representation
* نیاز به latent representation مناسب
* نیاز به generalization بهتر

بنابراین:

```text
Discriminative Learning
        ↓
Representation Learning
        ↓
Generative Representation Learning
```

و سپس:

```text
                Generative Models
                       │
             ┌─────────┴─────────┐
             │                   │
            GAN                 VAE
             │                   │
        DCGAN-DTA             Co-VAE
```

DCGAN-DTA مشخصاً استفاده از DCGAN را برای استخراج hierarchical representations از داده‌ها، از جمله استفاده از داده‌های بدون برچسب، به‌عنوان انگیزه مطرح می‌کند. ([Springer][5])

Co-VAE نیز دو VAE را برای drug و target و یک بخش co-regularization برای affinity prediction معرفی می‌کند. ([PubMed][1])

---

# بخش سوم — مقاله اول

# اسلاید 15 — DCGAN-DTA

### عنوان

**DCGAN-DTA: استفاده از GAN برای DTA**

### مسئله مقاله

چالش‌های مورد اشاره نویسندگان:

* Limited training data
* Feature selection / engineering
* نیاز به validation قوی‌تر
* generalization به داده‌های unseen

### ایده:

استفاده از:

**Deep Convolutional Generative Adversarial Network**

برای یادگیری representation دارو و پروتئین.

مقاله در سال 2024 در BMC Genomics منتشر شده است. ([Springer][5])

---

# اسلاید 16 — معماری DCGAN-DTA

### عنوان

**Architecture of DCGAN-DTA**

این را بهتر است به صورت diagram نشان بدهی:

```text
             Drug SMILES
                  │
          Label Encoding
                  │
              Embedding
                  │
            CNN / DCGAN
                  │
             Drug Latent
                  │
                  ├──────────┐
                  │          │
                  │        Merge
                  │          │
                  │          ↓
                  │     Affinity
                  │     Prediction
                  │          ↑
                  │          │
             Protein        │
             Sequence       │
                  │          │
             Encoding       │
                  │          │
             Embedding      │
                  │          │
             CNN / DCGAN ───┘
```

و یک نکته مهم:

DCGAN-DTA برای protein از **BLOSUM encoding** نیز برای وارد کردن ویژگی‌های evolutionary استفاده می‌کند. ([Springer][5])

---

# بخش چهارم — مقاله دوم

# اسلاید 17 — Co-VAE

### عنوان

**Co-VAE: یادگیری مشترک Drug، Target و Affinity**

ایده اصلی مقاله:

دو VAE:

```text
Drug SMILES
     ↓
   VAE₁
     ↓
Drug Latent


Protein Sequence
     ↓
   VAE₂
     ↓
Target Latent
```

سپس:

```text
Drug Latent
      +
Target Latent
      +
Co-Regularization
      ↓
Binding Affinity
```

Co-VAE در IEEE TPAMI در سال 2022 منتشر شده و هدف آن پیش‌بینی DTA با استفاده از دو VAE و یک بخش co-regularization است. ([PubMed][1])

---

# اسلاید 18 — معماری Co-VAE

### عنوان

**Architecture of Co-VAE**

Diagram:

```text
                 ┌──────────────┐
                 │  Drug SMILES │
                 └──────┬───────┘
                        ↓
                    Encoder
                        ↓
                 Latent Distribution
                        ↓
                    Decoder
                        ↓
                 Reconstructed Drug
                        │
                        │
                        ├──────────────┐
                                       ↓
                                  Co-Regularization
                                       ↑
                        ┌──────────────┤
                        │
                 Latent Target
                        ↑
                    Encoder
                        ↑
              Protein Sequence
```

و سپس:

```text
Drug latent + Target latent
              ↓
       Affinity Prediction
```

نکته جالب مقاله این است که مدل فقط برای prediction طراحی نشده؛ authors نشان می‌دهند می‌تواند برای **generation of new drugs sharing similar targets** نیز استفاده شود. ([PubMed][1])

---

# اسلاید 19 — DCGAN vs Co-VAE

### عنوان

**مقایسه دو رویکرد اصلی**

| ویژگی                 | DCGAN-DTA                 | Co-VAE                         |
| --------------------- | ------------------------- | ------------------------------ |
| نوع مدل               | GAN                       | VAE                            |
| ایده اصلی             | Adversarial Learning      | Variational Learning           |
| Components            | Generator + Discriminator | Encoder + Decoder              |
| Latent Representation | Generative/adversarial    | Probabilistic                  |
| Drug                  | SMILES                    | SMILES                         |
| Target                | Protein sequence          | Protein sequence               |
| Affinity              | Prediction                | Prediction + co-regularization |
| هدف جانبی             | representation learning   | generation + representation    |

### جمله کلیدی:

> **تفاوت اصلی این دو مقاله فقط در architecture نیست؛ آن‌ها دو دیدگاه متفاوت نسبت به یادگیری representation دارند.**

---

# بخش پنجم — نوآوری پیشنهادی

اینجا باید خیلی مراقب باشی که **نوآوری را بیش از حد ادعا نکنی**.

من پیشنهاد می‌کنم این بخش را به دو قسمت تقسیم کنی.

---

# اسلاید 20 — خلأ پژوهشی

### عنوان

**Research Gap**

دو مقاله:

**DCGAN-DTA**

تمرکز بر:

> GAN-based representation learning + DTA

**Co-VAE**

تمرکز بر:

> Variational representation + co-regularization + DTA

اما:

> مقایسه مستقیم و کنترل‌شده این دو پارادایم تحت یک experimental protocol مشترک، مسئله‌ای است که می‌توان در این پایان‌نامه بررسی کرد.

---

# اسلاید 21 — نوآوری پیشنهادی پایان‌نامه

### عنوان

**نوآوری‌های پیشنهادی**

من پیشنهاد می‌کنم 4 مورد را مطرح کنی:

### 1. مقایسه دو پارادایم Generative

مقایسه:

**Adversarial Learning**

در برابر

**Variational Learning**

برای DTA.

---

### 2. Experimental Protocol یکسان

تا حد امکان:

* Dataset مشترک
* Preprocessing مشترک
* Representationهای مشخص
* Split یکسان
* Metrics یکسان
* Seedهای کنترل‌شده

تا تفاوت performance واقعاً به architecture نسبت داده شود.

---

### 3. بررسی Generalization

مقایسه در شرایط مختلف:

```text
Warm-start
        vs
Cold-start
```

چون عملکرد مدل روی random split الزاماً نشان‌دهنده توانایی آن در پیش‌بینی برای داروها/targets واقعاً جدید نیست.

DCGAN-DTA نیز همین مسئله را با warm-start و physicochemical cold-start مورد توجه قرار داده است. ([Springer][5])

---

### 4. تحلیل فراتر از Accuracy

فقط نگوییم:

> کدام مدل عدد بهتری دارد؟

بلکه بررسی کنیم:

```text
Performance
    +
Generalization
    +
Training Stability
    +
Computational Cost
    +
Data Efficiency
```

---

# اسلاید 22 — سؤال اصلی پژوهش

در نهایت ارائه باید به یک سؤال ختم شود:

### **سؤال اصلی**

> **آیا نوع روش Generative Learning، یعنی Adversarial یا Variational Learning، می‌تواند بر کیفیت یادگیری representation و عملکرد پیش‌بینی Binding Affinity در مسئله DTA تأثیر معناداری داشته باشد؟**

و سؤالات فرعی:

1. کدام روش در warm-start عملکرد بهتری دارد؟
2. کدام روش در cold-start بهتر generalize می‌کند؟
3. حساسیت هر مدل نسبت به حجم داده چقدر است؟
4. کدام مدل از نظر computational cost مناسب‌تر است؟
5. آیا برتری مشاهده‌شده ناشی از architecture است یا dataset/split/preprocessing؟

---

## در نهایت روایت کل ارائه

من برای ارائه تو دقیقاً این داستان را پیشنهاد می‌کنم:

```text
DTI
│
│  آیا Drug و Target با هم interaction دارند؟
↓
DTA
│
│  این interaction چقدر قوی است؟
↓
Binding Affinity
│
├── Kd / pKd
├── Ki / pKi
└── IC50
│
↓
روش‌های کلاسیک
│
↓
Machine Learning
│
↓
Deep Learning
│
├── CNN / Sequence
│
├── Graph
│
└── Representation Learning
│
↓
Generative Models
│
├───────────────┐
↓               ↓
GAN             VAE
↓               ↓
DCGAN-DTA       Co-VAE
│               │
└───────┬───────┘
        ↓
   مقایسه کنترل‌شده
        ↓
Research Gap
        ↓
نوآوری پایان‌نامه
```

**این ساختار به نظرم برای جلسه استاد خیلی بهتر از نسخه قبلی است**، چون پایان ارائه از قبل در ذهن استاد ساخته شده: *«مسئله چیست → چرا DTA → affinity دقیقاً چیست → پژوهشگران چه مسیری را طی کرده‌اند → چرا GAN/VAE → این دو مقاله چه می‌کنند → من دقیقاً چه چیزی می‌خواهم به این مسیر اضافه کنم.»*

یک اصلاح علمی کوچک هم پیشنهاد می‌کنم: در ارائه از عبارت **«DTA بهتر از DTI است»** استفاده نکن؛ بگو **«برای هدف این پژوهش، DTA اطلاعات کمی و غنی‌تری از شدت اتصال ارائه می‌کند.»** این از نظر علمی قابل دفاع‌تر است، چون DTI و DTA دو formulation متفاوت از مسئله‌اند، نه لزوماً یکی «بهتر» از دیگری.

[1]: https://pubmed.ncbi.nlm.nih.gov/34652996/?utm_source=chatgpt.com "Co-VAE: Drug-Target Binding Affinity Prediction by Co-Regularized Variational Autoencoders - PubMed"
[2]: https://bpspubs.onlinelibrary.wiley.com/doi/10.1111/bph.16222?utm_source=chatgpt.com "Defining and unpacking the core concepts of pharmacology: A global initiative - Guilding - 2024 - British Journal of Pharmacology - Wiley Online Library"
[3]: https://pmc.ncbi.nlm.nih.gov/articles/PMC6879652/?utm_source=chatgpt.com "Comparison Study of Computational Prediction Tools for Drug-Target Binding Affinities - PMC"
[4]: https://www.frontiersin.org/journals/chemistry/articles/10.3389/fchem.2019.00782/full?utm_source=chatgpt.com "Frontiers | Comparison Study of Computational Prediction Tools for Drug-Target Binding Affinities"
[5]: https://link.springer.com/article/10.1186/s12864-024-10326-x?utm_source=chatgpt.com "DCGAN-DTA: Predicting drug-target binding affinity with deep convolutional generative adversarial networks | BMC Genomics | Springer Nature Link"
