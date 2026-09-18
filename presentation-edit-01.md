**روش‌های کلاسیک → ML → DL → GNN → مدل‌های مولد**

به یک **درخت تکاملی چندشاخه** تبدیل کنیم؛ چون در واقع GNN، Attention/Transformer، مدل‌های مولد و مدل‌های پیش‌آموزش‌دیده الزاماً مراحل پشت‌سرهم نیستند، بلکه چند مسیر موازی برای حل محدودیت‌های نسل قبلی‌اند.

برای مرجع اصلی این بازطراحی، یک Review بسیار جدید در سال 2026 پیدا کردم:

> **Wang et al., “A unified survey on drug-target interaction and binding affinity prediction: Models, representations, and challenges”, Biotechnology Advances, 2026, 88:108843.**

این مقاله در فوریه 2026 به‌صورت آنلاین منتشر شده و در نسخه‌ی 2026 مجله‌ی *Biotechnology Advances* قرار گرفته است. نکته‌ی مهم برای ارائه‌ی شما این است که نویسندگان صراحتاً مسیر تکامل را از **similarity/feature-driven → matrix/network → sequence/structure → large-scale pretrained models** دنبال می‌کنند و مدل‌های pretrained را به‌عنوان یک پارادایم مستقل بررسی می‌کنند. ([PubMed][1])

---

# 1. روایت اصلی مسیر پژوهشی DTA

### مسیر کلی پیشنهادی

**Similarity & Kernel Methods**
↓
**Feature-based Machine Learning**
↓
**Deep Learning & Representation Learning**
↙︎ ↓ ↘︎
**Sequence/CNN** | **Graph/GNN** | **Attention/Transformer**
↘︎ ↓ ↙︎
**Multi-modal / Cross-modal Learning**
↓
**Generative Representation Learning**
↙︎ ↘︎
**GAN** | **VAE**
↓
**Pre-trained Protein/Chemical Language Models**
↓
**Multimodal + Foundation/Task-adaptive Models**

اما نکته‌ی مهم:

> این نمودار را نباید به‌صورت «هر نسل جایگزین کامل نسل قبلی شد» توضیح دهیم؛ بلکه هر شاخه پاسخی به یک محدودیت متفاوت در نمایش دارو، پروتئین یا تعامل آن‌هاست.

---

# 2. مرحله اول — روش‌های مبتنی بر شباهت و Kernel

### ایده‌ی اصلی

در نخستین نسل روش‌های DTA، فرض اصلی این بود:

> اگر دو دارو از نظر شیمیایی شبیه باشند، احتمالاً رفتار affinity مشابهی دارند؛ همچنین پروتئین‌های مشابه احتمالاً الگوی binding مشابهی دارند.

بنابراین مدل مستقیماً representation را از داده خام یاد نمی‌گرفت؛ بلکه انسان ابتدا **شباهت داروها و شباهت پروتئین‌ها** را تعریف می‌کرد.

### مدل مهم: KronRLS

یکی از مهم‌ترین مدل‌های این دوره:

**KronRLS — Kronecker Regularized Least Squares**

است.

KronRLS شباهت دارو–دارو و target–target را ترکیب می‌کند و مسئله‌ی DTA را با یک چارچوب regularized least squares حل می‌کند. این مدل به‌عنوان یکی از baselineهای کلاسیک مهم در DTA باقی مانده و DeepDTA نیز مستقیماً آن را به‌عنوان baseline مقایسه می‌کند. ([OUP Academic][2])

### محدودیت

مشکل اساسی این نسل:

**مدل فقط به اندازه‌ی کیفیت similarity تعریف‌شده توسط انسان می‌توانست خوب باشد.**

یعنی:

```text
Drug similarity ─┐
                 ├──> Kernel / KronRLS ──> Affinity
Target similarity┘
```

در نتیجه، representation هنوز **دستی و از پیش تعریف‌شده** بود.

---

# 3. مرحله دوم — Feature-based Machine Learning

در مرحله‌ی بعد، به‌جای تکیه‌ی صرف بر similarity، پژوهشگران شروع کردند به ساختن ویژگی‌های صریح برای دارو، پروتئین و pair.

### ویژگی‌های متداول

برای دارو:

* Molecular fingerprints
* Chemical descriptors
* 2D structural features

برای پروتئین:

* Sequence similarity
* Physicochemical features
* Evolutionary information

و سپس این ویژگی‌ها وارد الگوریتم‌های Machine Learning می‌شدند.

---

## مدل شاخص: SimBoost — 2017

یکی از نقاط مهم این مرحله:

**SimBoost**

است که در سال 2017 معرفی شد.

SimBoost به‌جای یک مدل ساده‌ی kernel، از **Gradient Boosting Machines** استفاده کرد و ویژگی‌های مربوط به similarity و شبکه‌ی drug–target را برای پیش‌بینی مقدار پیوسته‌ی affinity ترکیب کرد. ([Springer][3])

بنابراین مسیر تقریباً این بود:

```text
Drug descriptors ──────┐
Drug similarity ───────┤
Target features ───────┼──> SimBoost ──> Affinity
Target similarity ─────┤
Interaction features ──┘
```

### پیشرفت نسبت به KronRLS

مدل توانست روابط **غیرخطی‌تر** را نسبت به روش‌های kernel-based مدل کند.

اما یک مشکل همچنان وجود داشت:

> ویژگی‌ها هنوز عمدتاً توسط پژوهشگر طراحی و انتخاب می‌شدند.

پس bottleneck از:

**Similarity Engineering**

به:

**Feature Engineering**

منتقل شد.

---

# 4. مرحله سوم — ورود Deep Learning

این مرحله نقطه‌ی عطف اصلی مسیر DTA است.

## DeepDTA — 2018

مدل:

**DeepDTA — Öztürk et al., 2018**

یکی از مهم‌ترین نقاط عطف در DTA است.

DeepDTA نشان داد که می‌توان به‌جای استفاده از featureهای دستی، مستقیماً از:

* **SMILES دارو**
* **Protein Sequence**

استفاده کرد و representation را با شبکه‌ی عصبی یاد گرفت. این مدل از **1D CNN** برای استخراج representation از هر دو ورودی استفاده می‌کند. ([OUP Academic][4])

ساختار مفهومی:

```text
SMILES ──> Embedding ──> CNN ──┐
                               ├──> Fusion ──> Affinity
Protein ─> Embedding ──> CNN ──┘
```

### تغییر بنیادی

اینجا یک تغییر بسیار مهم اتفاق می‌افتد:

> **Representation دیگر به‌طور کامل توسط انسان تعریف نمی‌شود؛ شبکه آن را از داده یاد می‌گیرد.**

این همان نقطه‌ای است که در اسلاید فعلی شما باید پررنگ‌تر شود.

---

# 5. مرحله‌ی چهارم — توسعه‌ی Sequence-based Deep Learning

بعد از DeepDTA، مسئله دیگر فقط «استفاده از Deep Learning» نبود؛ بلکه پژوهشگران شروع کردند به سؤال کردن:

> آیا representation ساده‌ی sequence برای دارو و پروتئین کافی است؟

یکی از پاسخ‌ها:

## WideDTA — 2019

WideDTA اطلاعات بیشتری را وارد مدل کرد، از جمله:

* Protein sequence
* Ligand SMILES
* Protein domains/motifs
* Maximum common substructure information

هدف این بود که representation غنی‌تری از دارو و پروتئین ایجاد شود. ([arXiv][5])

بنابراین:

```text
             ┌── Protein sequence
             │
             ├── Protein domains
             │
Input ───────┼── Drug SMILES
             │
             └── Molecular substructures
                       ↓
                 Deep Network
                       ↓
                    Affinity
```

---

# 6. مرحله‌ی پنجم — Graph Neural Networks

یک محدودیت مهم DeepDTA این بود که:

> SMILES یک **sequence representation** از molecule است، در حالی که خود molecule ذاتاً یک **graph** است.

یعنی:

* اتم → Node
* پیوند → Edge

از اینجا مسیر Graph-based DTA شکل گرفت.

---

## GraphDTA — 2021

مدل بسیار مهم:

**GraphDTA — Nguyen et al., 2021**

به‌جای نمایش دارو به‌صورت sequence، آن را به شکل molecular graph نمایش داد.

GraphDTA چند نوع GNN را بررسی کرد:

* GCN
* GAT
* GAT-GCN
* GIN

و نشان داد که نمایش گرافی می‌تواند اطلاعات ساختاری دارو را مستقیماً وارد مدل کند. ([OUP Academic][6])

مسیر:

```text
                  ┌── Protein Sequence ──> CNN
                  │
Drug ──> Molecular Graph ──> GNN ──┐
                                    ├──> Fusion ──> Affinity
                                    │
Protein ────────────────────────────┘
```

### اهمیت این مرحله برای ارائه

اینجا می‌توانی بگویی:

> «مسئله فقط عمیق‌تر کردن شبکه نبود؛ نوع representation نیز تغییر کرد.»

یعنی تکامل از:

**Sequence representation**

به:

**Structure-aware representation**

---

# 7. مرحله‌ی ششم — Attention و Feature Fusion

بعد از CNN و GNN، یک مشکل جدید برجسته شد:

حتی اگر representation خوبی داشته باشیم، هنوز باید مشخص کنیم:

> کدام بخش از دارو و کدام بخش از پروتئین برای binding مهم‌تر است؟

اینجا **Attention mechanisms** اهمیت پیدا کردند.

### نمونه مهم: FusionDTA

**FusionDTA — 2022**

روی مسئله‌ی feature aggregation تمرکز داشت و از attention-based feature polymerization و knowledge distillation استفاده کرد. ([PubMed][7])

در این مرحله تمرکز از:

> «چه ویژگی‌ای استخراج کنیم؟»

به سمت:

> «چگونه ویژگی‌های استخراج‌شده را به شکل مؤثر با هم تعامل دهیم؟»

حرکت کرد.

---

# 8. مرحله‌ی هفتم — Transformer و مدل‌سازی وابستگی‌های دوربرد

در ادامه، Transformer و attention-based architectures وارد DTA شدند.

ایده‌ی اصلی:

CNN بیشتر برای local patterns مناسب است، در حالی که attention می‌تواند ارتباط میان بخش‌های دور از هم در sequence یا بین modalityها را مدل کند.

در این مرحله مدل‌هایی مانند:

* Transformer-based DTA
* AttentionDTA
* ELECTRA-DTA
* CPInformer
* DoubleSG-DTA

به وجود آمدند.

برای مثال، مدل‌های Transformer-based تلاش کردند روابط global میان اجزای drug و target را بهتر مدل کنند. مرورهای جدید نیز Transformer را در کنار CNN، RNN و GNN به‌عنوان یکی از خانواده‌های اصلی معماری‌های DL برای DTA دسته‌بندی می‌کنند. ([PubMed Central (PMC)][8])

---

# 9. مرحله‌ی هشتم — Multi-modal / Hybrid Representation

در این مرحله مشخص شد که احتمالاً **یک representation به‌تنهایی کافی نیست.**

مثلاً:

```text
Drug
 ├── SMILES
 ├── Molecular Graph
 └── Chemical descriptors

Protein
 ├── Sequence
 ├── Structure
 ├── Binding site
 └── Evolutionary information
```

بنابراین مدل‌ها شروع کردند به ترکیب چند modality.

این مسیر به مدل‌هایی مانند:

* DeepFusionDTA
* FusionDTA
* MDF-DTA
* PocketDTA
* MGF-DTA
* GraphTransDTA

منتهی شد.

برای نمونه، **MDF-DTA** از multi-dimensional feature fusion استفاده می‌کند و در مقایسه‌های خود مدل‌هایی مانند KronRLS، SimBoost، DeepDTA، WideDTA، GraphDTA و AttentionDTA را در یک زنجیره‌ی تاریخی کنار هم قرار می‌دهد. ([American Chemical Society Publications][9])

در 2026 نیز **GraphTransDTA** نمونه‌ای از ادامه‌ی این مسیر است که Graph Transformer را برای fusion داده‌های multimodal به‌کار می‌گیرد. ([DOI][10])

---

# 10. مرحله‌ی نهم — Generative Representation Learning

اینجا دقیقاً جایی است که **موضوع پایان‌نامه‌ی شما** وارد داستان می‌شود.

تا اینجا عمدتاً مدل‌ها:

> یک representation می‌گیرند → affinity را پیش‌بینی می‌کنند.

اما سؤال جدید:

> آیا می‌توان از **مدل‌های مولد** برای یادگیری representation بهتر استفاده کرد؟

این مسیر حداقل دو شاخه‌ی مهم دارد:

```text
                 Generative Models
                  /             \
                GAN             VAE
                 |               |
             DCGAN-DTA         Co-VAE
```

---

# 11. شاخه‌ی GAN — DCGAN-DTA

مدل مورد مطالعه‌ی شما:

**DCGAN-DTA**

با عنوان:

> *Predicting drug-target binding affinity with deep convolutional generative adversarial networks*

در سال 2024 منتشر شده است. ([Springer][11])

ایده‌ی آن این است که GAN صرفاً برای تولید molecule استفاده نشود، بلکه فرایند generative/adversarial برای یادگیری representationهای مفید در DTA به کار گرفته شود.

در DCGAN-DTA:

```text
Drug representation ──> DCGAN pretraining ──> Latent representation
                                                   │
Protein representation ─> DCGAN pretraining ──────┤
                                                   ↓
                                             DTA predictor
                                                   ↓
                                               Affinity
```

در مقاله، فرایند کلی شامل encoding/embedding، feature extraction، ادغام latent vectorهای drug و protein و در نهایت DTA prediction است. ([PubMed Central (PMC)][12])

### جایگاه پژوهشی آن

پس DCGAN-DTA را نباید صرفاً «یک مدل CNN دیگر» معرفی کنیم.

جمله‌ی بهتر برای ارائه:

> **DCGAN-DTA تلاش می‌کند ظرفیت یادگیری نمایش مدل‌های مولد را وارد مسئله‌ی DTA کند و از pretraining برای استخراج representation استفاده کند.**

---

# 12. شاخه‌ی VAE — Co-VAE

شاخه‌ی موازی دیگر:

**Co-VAE — Drug-Target Binding Affinity Prediction by Co-Regularized Variational Autoencoders**

است.

این مقاله توسط:

**Tianjiao Li, Xing-Ming Zhao, Limin Li**

ارائه شده و نسخه‌ی ژورنالی آن در IEEE TPAMI منتشر شده است. ([PubMed][13])

ایده:

```text
Drug ──────> VAE ──────> Drug latent representation
                           \
                            \
                             > Co-regularization
                            /
                           /
Protein ────> VAE ─────> Protein latent representation
                           |
                           ↓
                      Affinity prediction
```

بنابراین تفاوت مفهومی مهم با DCGAN-DTA این است که:

### DCGAN-DTA

تمرکز:

**Adversarial / GAN-based representation learning**

### Co-VAE

تمرکز:

**Variational representation learning + co-regularization**

این دقیقاً دلیل خوبی است که این دو مقاله برای یک **comparative audit** کنار هم قرار بگیرند.

---

# 13. مرحله‌ی دهم — Protein/Chemical Pre-trained Models

این مرحله در نسخه‌ی فعلی اسلاید شما تقریباً غایب است و به نظرم **حتماً باید اضافه شود.**

در سال‌های اخیر، مسیر DTA فقط به CNN/GNN/Transformerهای اختصاصی محدود نمانده است.

مدل‌های بزرگ از قبل روی حجم زیادی از:

* Protein sequences
* Molecular structures
* Chemical text
* Biological sequences

آموزش دیده‌اند.

سپس embeddingهای آن‌ها برای DTA استفاده می‌شود.

مثلاً:

* Protein language models
* ESM family
* ProtBERT
* ChemBERT
* Molecular language models

در یک Review سال 2026 نیز **large-scale pretrained models** به‌عنوان یک paradigm مستقل در مسیر تکامل DTI/DTA مطرح شده‌اند. ([PubMed][1])

این یعنی مسیر جدید تقریباً:

```text
Protein sequence
      ↓
Pre-trained Protein LM
      ↓
Protein embedding
      ↓
                         ┌──> DTA
Drug ──> Molecular LM ──┤
                         ↓
                    Cross-modal
                     interaction
```

---

# 14. مرحله‌ی فعلی در 2026 — Foundation / Multimodal / Task-adaptive Models

در 2026، مسیر پژوهش دیگر فقط «CNN بهتر یا GNN بهتر» نیست.

تمرکز جدیدتر روی مواردی مانند:

### 1. Pre-trained representations

استفاده از representationهای از قبل آموخته‌شده.

### 2. Multimodal learning

ترکیب:

> sequence + graph + structure + language representation

### 3. Cross-modal interaction

مدل به‌جای concatenate ساده‌ی drug/protein embeddings، تعامل میان دو modality را یاد می‌گیرد.

### 4. Cold-start generalization

آیا مدل برای:

* drug جدید
* target جدید
* یا هر دو

واقعاً generalize می‌کند؟

### 5. Task adaptation / Meta-learning

نمونه‌ی مهم در 2026:

**A meta learning and task adaptive approach for drug target affinity prediction**

که در *Nature Communications* در مارس 2026 منتشر شده است. ([Nature][14])

### 6. Structure-aware / binding-site-aware models

مانند مدل‌های جدیدی نظیر:

**DCI-SiteDTA**

که binding-site information را وارد مسئله می‌کنند. ([Springer][15])

---

# 15. بنابراین مسیر تکامل کامل‌تر شما

من برای ارائه‌ی شما این hierarchy را پیشنهاد می‌کنم:

```text
                         DTA Prediction
                               │
                               ▼
              ┌────────────────────────────────┐
              │ 1. Similarity / Kernel Methods │
              │    KronRLS                     │
              └────────────────┬───────────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │ 2. Feature-based ML            │
              │    SimBoost                     │
              │    RF / SVM / GBM               │
              └────────────────┬───────────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │ 3. Deep Representation Learning│
              │                                │
              │    Sequence-based              │
              │      DeepDTA                   │
              │      WideDTA                   │
              │                                │
              │    Graph-based                 │
              │      GraphDTA                  │
              │      MGraphDTA                 │
              │                                │
              │    Attention / Transformer     │
              │      AttentionDTA              │
              │      ELECTRA-DTA               │
              └───────────────┬────────────────┘
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
       Multi-modal       Generative       Pre-trained
       / Fusion          Learning         Models
             │             │                  │
             │        ┌────┴────┐       Protein LM
             │        ▼         ▼       Chemical LM
             │       GAN       VAE           │
             │        │         │            │
             │   DCGAN-DTA   Co-VAE           │
             │                              │
             └──────────────┬───────────────┘
                            ▼
             Cross-modal / Multimodal Learning
                            │
                            ▼
             Foundation / Task-adaptive Models
                            │
                            ▼
                 Robust DTA Prediction
              + Cold-start Generalization
              + Interpretability
              + Structure Awareness
```

---

# 16. اما برای ارائه‌ی شما یک نکته‌ی خیلی مهم وجود دارد

من **GAN و VAE را به‌عنوان «مرحله‌ی بعد از GNN» قرار نمی‌دهم.**

این از نظر تاریخی دقیق نیست.

بهتر است درخت شما این شکلی باشد:

```text
                         DTA
                          │
             Classical / ML-based
                          │
                     Deep Learning
                          │
          ┌───────────────┼────────────────┐
          │               │                │
       Sequence         Graph          Attention
          │               │                │
        CNN              GNN          Transformer
          │               │                │
          └───────────────┼────────────────┘
                          │
                   Representation
                       Learning
                          │
        ┌─────────────────┼──────────────────┐
        │                 │                  │
     Fusion           Generative        Pre-trained
        │                 │                  │
        │            ┌────┴────┐             │
        │            │         │             │
        │           GAN       VAE            │
        │            │         │             │
        │       DCGAN-DTA    Co-VAE          │
        │                                      │
        └────────────────┬─────────────────────┘
                         │
                  Multimodal / Cross-modal
                         │
                         ▼
             Foundation / Adaptive Models
```

این از نظر علمی **خیلی بهتر** از درخت فعلی اسلاید شماست.

---

# 17. داستانی که در ارائه باید تعریف کنی

اگر بخواهیم کل این مسیر را در حدود **۲–۳ دقیقه** توضیح دهیم، متن مفهومی آن این است:

> «در ابتدا، پیش‌بینی affinity عمدتاً بر اساس شباهت میان داروها و پروتئین‌ها انجام می‌شد. مدل‌هایی مانند KronRLS از similarityهای از پیش تعریف‌شده برای پیش‌بینی affinity استفاده می‌کردند. سپس روش‌هایی مانند SimBoost با استفاده از ویژگی‌های مهندسی‌شده و Gradient Boosting تلاش کردند روابط غیرخطی‌تری را یاد بگیرند.
>
> نقطه‌ی عطف بعدی ورود Deep Learning بود. DeepDTA نشان داد که می‌توان مستقیماً از SMILES و توالی پروتئین استفاده کرد و representation را به‌صورت خودکار با CNN یاد گرفت. پس از آن، پژوهش‌ها به سمت representationهای غنی‌تر حرکت کردند؛ از جمله WideDTA، مدل‌های graph-based مانند GraphDTA و سپس attention و Transformerها.
>
> در ادامه، مشخص شد که یک نوع representation به‌تنهایی برای توصیف کامل drug و target کافی نیست؛ بنابراین روش‌های hybrid و multimodal شکل گرفتند.
>
> در کنار این مسیر discriminative، یک شاخه‌ی دیگر نیز شکل گرفت: استفاده از مدل‌های generative برای یادگیری representation. در این شاخه، GAN و VAE دو مسیر مهم هستند. DCGAN-DTA در مسیر GAN و Co-VAE در مسیر VAE قرار می‌گیرند.
>
> در سال‌های اخیر، مسیر پژوهش به سمت مدل‌های pre-trained، protein language models، multimodal learning، cross-modal interaction و روش‌های task-adaptive و cold-start حرکت کرده است.
>
> بنابراین، این پایان‌نامه در یک نقطه‌ی مشخص از این درخت قرار می‌گیرد: بررسی دو رویکرد generative یعنی DCGAN-DTA و Co-VAE و مطالعه‌ی تفاوت آن‌ها در نحوه‌ی یادگیری representation برای DTA.»

---

# 18. جایگاه دقیق پایان‌نامه‌ی شما

این قسمت برای دفاع شما خیلی مهم است.

نباید بگویی:

> «روش‌های قبلی ضعیف بودند و من می‌خواهم مدل بهتری بسازم.»

چون این ادعا نیاز به آزمایش گسترده دارد.

بهتر است بگویی:

> **«پژوهش حاضر بر شاخه‌ی Generative Representation Learning در تکامل روش‌های DTA تمرکز می‌کند.»**

و سپس:

```text
Deep Learning
      │
      └── Representation Learning
               │
               ├── Sequence
               ├── Graph
               ├── Attention
               ├── Multimodal
               │
               └── Generative Representation
                        │
                        ├── GAN
                        │    └── DCGAN-DTA
                        │
                        └── VAE
                             └── Co-VAE
```

این framing برای پایان‌نامه‌ی شما خیلی دفاع‌پذیرتر است، چون دقیقاً نشان می‌دهد **چرا این دو مقاله را انتخاب کرده‌اید**.

---

# 19. منابع کلیدی که برای اسلاید مسیر پژوهشی پیشنهاد می‌کنم

برای خود اسلاید لازم نیست ۱۵ رفرنس بگذاری. این ۸ مورد کفایت می‌کنند:

1. **Wang et al., 2026 — Unified survey on DTI/DTA**
   مرجع اصلی مسیر تکامل. ([PubMed][1])

2. **Pahikkala et al., 2015 — KronRLS**
   نسل similarity/kernel. ([PubMed][16])

3. **He et al., 2017 — SimBoost**
   نسل feature-based ML. ([Springer][3])

4. **Öztürk et al., 2018 — DeepDTA**
   نقطه‌ی عطف Deep Learning/sequence. ([OUP Academic][4])

5. **Öztürk et al., 2019 — WideDTA**
   توسعه‌ی representation sequence/text. ([arXiv][5])

6. **Nguyen et al., 2021 — GraphDTA**
   ورود جدی molecular graph/GNN. ([OUP Academic][6])

7. **Li et al., 2022 — Co-VAE**
   شاخه‌ی VAE. ([PubMed][13])

8. **Kalemati et al., 2024 — DCGAN-DTA**
   شاخه‌ی GAN و دقیقاً یکی از دو محور پایان‌نامه‌ی شما. ([Springer][11])

و برای نشان دادن اینکه مسیر در **2026 متوقف نشده**، می‌توانیم در نسخه نهایی یک یا دو نمونه‌ی 2026 مثل **meta-learning/task adaptation** و **pre-trained/Transformer-based DTA** را در انتهای درخت قرار دهیم. ([Nature][14])

---

### یک اصلاح مهم نسبت به اسلاید فعلی شما

در فایل فعلی، مسیر با **«روش‌های کلاسیک → یادگیری ماشین → یادگیری عمیق → یادگیری نمایش → مدل‌های مولد»** نمایش داده شده و در نهایت DCGAN-DTA و Co-VAE به‌عنوان دو برگ آخر آمده‌اند.  

این روایت **برای شروع خوب است، اما برای ارائه‌ی نهایی شما بیش از حد خطی است**. با توجه به Review سال 2026، بهتر است «Generative» را یک شاخه‌ی مهم از Representation Learning نشان دهیم، نه اینکه القا کنیم بعد از GNN به وجود آمده است. همچنین **Pre-trained models، multimodal learning و Transformerها** باید در سمت راست درخت به‌عنوان مسیرهای متأخرتر اضافه شوند.

اگر بخواهیم در مرحله‌ی بعد اسلاید را بسازیم، پیشنهاد من این است که **یک اسلاید اصلی با یک درخت افقی از 2015 تا 2026** داشته باشیم و **DCGAN-DTA و Co-VAE را با رنگ/کادر متفاوت به‌عنوان محل ورود پایان‌نامه برجسته کنیم**؛ این از اسلاید فعلی بسیار حرفه‌ای‌تر و از نظر روایت پژوهشی قابل‌دفاع‌تر خواهد بود.

[1]: https://pubmed.ncbi.nlm.nih.gov/41690335/?utm_source=chatgpt.com "A unified survey on drug-target interaction and binding affinity prediction: Models, representations, and challenges - PubMed"
[2]: https://academic.oup.com/bib/article/16/2/325/246479?utm_source=chatgpt.com "Toward more realistic drug–target interaction predictions | Briefings in Bioinformatics | Oxford Academic"
[3]: https://link.springer.com/article/10.1186/s13321-017-0209-z?utm_source=chatgpt.com "SimBoost: a read-across approach for predicting drug–target binding affinities using gradient boosting machines | Journal of Cheminformatics | Springer Nature Link"
[4]: https://academic.oup.com/bioinformatics/article/34/17/i821/5093245?utm_source=chatgpt.com "DeepDTA: deep drug–target binding affinity prediction | Bioinformatics | Oxford Academic"
[5]: https://arxiv.org/abs/1902.04166?utm_source=chatgpt.com "WideDTA: prediction of drug-target binding affinity"
[6]: https://academic.oup.com/bioinformatics/article/37/8/1140/5942970?utm_source=chatgpt.com "GraphDTA: predicting drug–target binding affinity with graph neural networks | Bioinformatics | Oxford Academic"
[7]: https://pubmed.ncbi.nlm.nih.gov/34929738/?utm_source=chatgpt.com "FusionDTA: attention-based feature polymerizer and knowledge distillation for drug-target binding affinity prediction - PubMed"
[8]: https://pmc.ncbi.nlm.nih.gov/articles/PMC11019008/?utm_source=chatgpt.com "A comprehensive review of the recent advances on predicting drug-target affinity based on deep learning - PMC"
[9]: https://pubs.acs.org/doi/10.1021/acs.jcim.4c00310?utm_source=chatgpt.com "MDF-DTA: A Multi-Dimensional Fusion Approach for Drug-Target Binding Affinity Prediction | Journal of Chemical Information and Modeling | ACS Publications"
[10]: https://doi.org/10.1007/s12539-026-00823-w?utm_source=chatgpt.com "GraphTransDTA: Drug-Target Affinity Prediction with Graph Transformer for Multimodal Data Fusion | Interdisciplinary Sciences: Computational Life Sciences | Springer Nature Link"
[11]: https://link.springer.com/article/10.1186/s12864-024-10326-x?utm_source=chatgpt.com "DCGAN-DTA: Predicting drug-target binding affinity with deep convolutional generative adversarial networks | BMC Genomics | Springer Nature Link"
[12]: https://pmc.ncbi.nlm.nih.gov/articles/PMC11080241/?utm_source=chatgpt.com "DCGAN-DTA: Predicting drug-target binding affinity with deep convolutional generative adversarial networks - PMC"
[13]: https://pubmed.ncbi.nlm.nih.gov/34652996/?utm_source=chatgpt.com "Co-VAE: Drug-Target Binding Affinity Prediction by Co-Regularized Variational Autoencoders - PubMed"
[14]: https://www.nature.com/articles/s41467-026-70554-5?utm_source=chatgpt.com "A meta learning and task adaptive approach for drug target affinity prediction | Nature Communications"
[15]: https://link.springer.com/article/10.1186/s12859-026-06446-8?utm_source=chatgpt.com "DCI-SiteDTA: drug-target affinity prediction based on binding sites detection and site-aware dual cross-interaction block | BMC Bioinformatics | Springer Nature Link"
[16]: https://pubmed.ncbi.nlm.nih.gov/24723570/?utm_source=chatgpt.com "Toward more realistic drug-target interaction predictions - PubMed"
