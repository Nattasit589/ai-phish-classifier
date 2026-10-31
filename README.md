# AI Phish Classifier

ระบบตรวจจับและคัดกรองอีเมลฟิชชิ่งอัตโนมัติด้วยเทคนิคการเรียนรู้เชิงลึกและการประมวลผลภาษาธรรมชาติ โครงงาน Final Project รายวิชา CS461 มีเป้าหมายเพื่อเปรียบเทียบโมเดล **Bi-LSTM** และ **DistilBERT** พร้อมพัฒนาเว็บแอปพลิเคชันต้นแบบสำหรับตรวจสอบข้อความอีเมลและแสดงผลการทำนายพร้อมระดับความเสี่ยง

> **สถานะโครงงาน:** อยู่ในขั้นข้อเสนอโครงงาน ปัจจุบัน repository มีเอกสารข้อเสนอและ README ส่วนโมเดล ระบบฝึกสอน API และเว็บแอปพลิเคชันเป็นแผนการพัฒนา ยังไม่มีผลการทดลองหรือคำสั่งติดตั้งและรันระบบ

เอกสารข้อเสนอฉบับต้นฉบับ: [CS461_Finalpro.docx](docs/CS461_Finalpro.docx)

 AI Phish Classifier
[![Open In Colab](https://colab.research.google.com/drive/1jKUwxjd39M6qt14SK29DGoV83ulmVKfV?usp=sharing)]


## ที่มาและความสำคัญ

อีเมลฟิชชิ่งใช้การปลอมตัวและเทคนิค Social Engineering เพื่อหลอกขโมยรหัสผ่าน ข้อมูลบัญชีธนาคาร หรือชักจูงให้ติดตั้งมัลแวร์ รูปแบบภาษาที่ซับซ้อนทำให้ผู้ใช้งานแยกแยะได้ยาก และระบบกรองด้วยกฎหรือคำสำคัญอาจตรวจจับอีเมลรูปแบบใหม่ได้ไม่ครบถ้วน

โครงงานนี้เสนอการใช้ Deep Learning และ NLP เพื่อจำแนกข้อความอีเมล โดยมุ่งให้เป็นเครื่องมือช่วยตรวจสอบสำหรับผู้ใช้งานทั่วไป พนักงานองค์กร และทีม IT Security / SOC เพื่อลดเวลาในการตรวจสอบอีเมลและลดความเสี่ยงจากการโจรกรรมข้อมูล

## วัตถุประสงค์

1. ออกแบบและฝึกสอนโมเดลจำแนกอีเมลแบบ Binary Text Classification ระหว่างอีเมลฟิชชิ่งและอีเมลปกติ โดยตั้งเป้าหมาย Accuracy ไม่ต่ำกว่า 80%
2. เปรียบเทียบ Bi-LSTM ที่ออกแบบและฝึกตั้งแต่ต้นกับ DistilBERT ที่ทำ Fine-tuning รวมถึงศึกษาผลของ Hyperparameters และ Optimizers
3. พัฒนาเว็บต้นแบบด้วย Next.js และ FastAPI ให้ผู้ใช้กรอกข้อความอีเมลและดูผลการทำนายพร้อม Confidence และ Risk Score

## ชุดข้อมูล

ใช้ [Phishing Email Dataset บน Kaggle](https://www.kaggle.com/datasets/naserabdullahalam/phishing-email-dataset) ซึ่งรวบรวมข้อมูลจาก CEAS, Nazario, Nigerian Fraud, Enron และ SpamAssassin

รายละเอียดตามเอกสารข้อเสนอของไฟล์ `phishing_email.csv`:

| รายการ | รายละเอียด |
| --- | --- |
| จำนวนข้อมูลรวม | 82,486 รายการ |
| คลาส `1` | อีเมลฟิชชิ่ง/สแปม 42,891 รายการ ประมาณ 52% |
| คลาส `0` | อีเมลปกติ 39,595 รายการ ประมาณ 48% |
| `text_combined` | ข้อความที่รวมหัวข้อและเนื้อหาอีเมล |
| `label` | ป้ายกำกับคลาส `0` หรือ `1` |
| รูปแบบไฟล์ | CSV |

ชุดข้อมูลยังไม่ได้รวมไว้ใน repository นี้ ตัวเลขข้างต้นเป็นข้อมูลจากข้อเสนอและต้องตรวจสอบอีกครั้งในขั้นสำรวจข้อมูลจริง

## แนวทางพัฒนาโมเดล

### การเตรียมข้อมูล

- สำรวจข้อมูลเบื้องต้น (EDA) และตรวจสอบข้อมูลสูญหายกับข้อมูลซ้ำ
- ทำความสะอาดข้อความ เช่น จัดการอักขระพิเศษและแปลงเป็นตัวพิมพ์เล็ก
- เตรียม Tokenization, Padding และ Word Embedding ตามความเหมาะสมของแต่ละโมเดล
- แบ่งข้อมูลเป็น Train / Validation / Test set โดยเอกสารยังไม่ได้กำหนดสัดส่วน

### โมเดลที่เปรียบเทียบ

| โมเดล | แนวทาง | องค์ประกอบหลัก |
| --- | --- | --- |
| Bi-LSTM | โมเดลพื้นฐานที่ออกแบบและฝึกสอนตั้งแต่ต้น | Embedding, Bidirectional LSTM, Dropout และ Dense Layer |
| DistilBERT | Fine-tuning โมเดลตระกูล Transformer | การวิเคราะห์บริบทด้วย Self-Attention เพื่อจำแนกข้อความ |

### แผนการฝึกสอนและปรับแต่ง

| รายการ | ค่าหรือแนวทางเริ่มต้นตามข้อเสนอ |
| --- | --- |
| Optimizers | เปรียบเทียบอย่างน้อย 2 รูปแบบ ได้แก่ SGD with Momentum กับ Adam / AdamW |
| Loss function | Binary Cross-Entropy |
| Learning rate ของ Bi-LSTM | `1e-3` |
| Learning rate ของ DistilBERT | `2e-5` |
| Batch size | ทดลอง `32` และ `64` |
| Epochs | เริ่มต้น 10–20 รอบ |
| Learning rate scheduling | ทดลอง Scheduling / Decay |
| Regularization | Dropout `0.3–0.5` และ L2 / Weight Decay |
| Early stopping | หยุดเมื่อ Validation Loss ไม่ลดลง |

ค่าดังกล่าวเป็นจุดเริ่มต้นของการทดลอง และอาจปรับตามผลการฝึกสอน

## ระบบเว็บต้นแบบ

การใช้งานที่วางแผนไว้:

1. ผู้ใช้กรอกข้อความอีเมลผ่านหน้าเว็บ
2. เว็บส่งข้อความไปยัง REST API
3. API ประมวลผลด้วยโมเดลที่คัดเลือกจากผลการทดลอง
4. หน้าเว็บแสดงผลการจำแนกพร้อม Confidence และ Risk Score

| ส่วนงาน | เทคโนโลยีที่วางแผนใช้ |
| --- | --- |
| การเตรียมข้อมูลและพัฒนาโมเดล | Python, Google Colab, Pandas, NumPy, PyTorch, Hugging Face Transformers, Scikit-learn, NLTK |
| Backend และ Model Serving | FastAPI, Uvicorn |
| Frontend | Next.js, TypeScript, Tailwind CSS, shadcn/ui |

## การประเมินผล

| ตัวชี้วัด | จุดประสงค์ |
| --- | --- |
| Accuracy | ประเมินความถูกต้องโดยรวม โดยตั้งเป้าไม่ต่ำกว่า 80% |
| Precision | ประเมินความถูกต้องของการแจ้งเตือนฟิชชิ่ง เพื่อลด False Positive ที่กระทบอีเมลปกติ |
| Recall | ประเมินการตรวจจับฟิชชิ่ง เพื่อลด False Negative หรืออีเมลอันตรายที่หลุดรอด |
| F1-Score | ประเมินสมดุลระหว่าง Precision และ Recall เพื่อประกอบการเลือกโมเดล |
| Confusion Matrix | ตรวจสอบจำนวนและประเภทของการทำนายถูกและผิด |

ในด้านการใช้งาน ตั้งเป้าให้ API ประมวลผลภายใน **1–2 วินาทีต่อข้อความ** และควบคุม False Negative Rate ให้ต่ำที่สุดเพื่อลดความเสี่ยงขององค์กร เป้าหมายเหล่านี้ยังไม่ใช่ผลการวัดจริง

## แผนการดำเนินงาน

| ระยะ | สัปดาห์ | งานที่วางแผน |
| --- | --- | --- |
| Phase 1 | 7–8 | ส่งข้อเสนอโครงงาน ทำ EDA ตรวจสอบข้อมูลสูญหาย/ข้อมูลซ้ำ และทำ Text Preprocessing |
| Phase 2 | 9–10 | เตรียม Tokenization, Padding และ Word Embedding แบ่งชุดข้อมูล และพัฒนา Bi-LSTM |
| Phase 3 | 11–12 | ปรับ Hyperparameters เปรียบเทียบ Optimizers อย่างน้อย 2 รูปแบบ และนำเสนอ Project Check-in |
| Phase 4 | 13 | Fine-tune DistilBERT ใช้ Regularization เปรียบเทียบผลทั้งสองโมเดล และคัดเลือกโมเดล |
| Phase 5 | 14–15 | พัฒนา FastAPI และเว็บ Next.js เตรียม Live Demo รายงานฉบับสมบูรณ์ และสไลด์นำเสนอ |

## ผลงานที่วางแผนส่งมอบ

- โมเดล Bi-LSTM และ DistilBERT ที่ผ่านการฝึกสอนและประเมินผล
- ผลเปรียบเทียบโมเดล การปรับ Hyperparameters และ Optimizers
- Backend API สำหรับประมวลผลข้อความอีเมล
- เว็บแอปพลิเคชันต้นแบบสำหรับสาธิตการตรวจสอบอีเมล
- รายงานฉบับสมบูรณ์ที่สรุป Model Tuning & Optimization และสไลด์นำเสนอ

## ไฟล์ในโปรเจกต์

```text
ai-phish-classifier/
├── docs/
│   └── CS461_Finalpro.docx  # เอกสารข้อเสนอโครงงานต้นฉบับ
├── LICENSE                 # สัญญาอนุญาต MIT
└── README.md               # ภาพรวมและแผนการพัฒนาโครงงาน
```

## ผู้จัดทำ

| ชื่อ–สกุล | รหัสนักศึกษา |
| --- | --- |
| ณัฐสิทธิ์ สงวนธนากร | 1660701390 |
| ภาคิน เชาว์โคกสูง | 1660700046 |
| เดชาวัต รุ่งทิม | 1670700200 |
| ธนธัส คันทรินทร์ | 1670700663 |
| พัชรพล ชุลีประเสริฐ | 1660707462 |

## เอกสารอ้างอิง

1. Al-Subaiey, A., Al-Thani, M., Alam, N. A., Antora, K. F., Khandakar, A., & Zaman, S. A. U. (2024). *Novel Interpretable and Robust Web-based AI Platform for Phishing Email Detection*. [arXiv:2405.11619](https://arxiv.org/abs/2405.11619)
2. Alam, N. A. (2024). *Phishing Email Dataset* [Data set]. [Kaggle](https://www.kaggle.com/datasets/naserabdullahalam/phishing-email-dataset)
3. Sanh, V., Debut, L., Chaumond, J., & Wolf, T. (2019). *DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter*. [arXiv:1910.01108](https://arxiv.org/abs/1910.01108)

## สัญญาอนุญาต

โค้ดใน repository เผยแพร่ภายใต้ [MIT License](LICENSE) การใช้ชุดข้อมูลให้ตรวจสอบเงื่อนไขจากแหล่งข้อมูลต้นทาง
