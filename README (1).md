# 🐄 Livestock Helper | لائیو سٹاک معاون

A bilingual (Urdu + English) RAG chatbot for the **Livestock & Dairy Development Department, Punjab**.
Livestock farmers ask questions in **Urdu or English** and get answers in **both languages**, based on the department's FAQ documents.

**Tech:** Python · Streamlit · Groq API (open-source GPT-OSS models) · FastEmbed (multilingual embeddings) · FAISS · tiktoken · Google Colab (testing) · GitHub · Streamlit Cloud

---

## ✨ Features

| Feature | Details |
|---|---|
| Bilingual Q&A | Ask in Urdu, English or mixed; answers in Urdu + English (or one language) |
| Technical level | Simple (farmers) · Moderate (trainees) · Technical (veterinary staff) |
| Response size | Short · Medium · Detailed |
| Urdu display | Nastaliq font, right-to-left; Urdu and English one below the other or side by side |
| Smart search | Urdu and follow-up questions are turned into an English search query before searching |
| Sources | Each answer shows the document pages used, with a match score (🟢 🟡 🔴) |
| Safety rules | No invented fees or dates, no medicine doses unless documented, emergency advice to call helpline 0800-09211 |
| Local context | Punjab breeds (Sahiwal, Cholistani, Nili-Ravi…), seasonal problems, department services |
| Sample questions | One-click example questions in Urdu and English |
| Save chat | Download the conversation as a text file |
| Admin panel | Add more PDFs, change chunk size, overlap and number of chunks |

## ⚙️ How it works (RAG pipeline)

```
PDF → extract text (pypdf) → clean → token chunks (tiktoken, 400 tokens, 60 overlap)
    → multilingual embeddings (paraphrase-multilingual-MiniLM-L12-v2)
    → FAISS vector index
Question (Urdu/English) → English search query (Groq) → FAISS search (top 5 chunks)
    → Groq model writes the answer from the chunks → Urdu + English answer + sources
```

The knowledge base is built **once per server** and shared by all users, so farmers get fast answers.

---

## 📁 Files to upload to GitHub

```
livestock-helper/
├── app.py              # the full application
├── requirements.txt    # Python packages
├── README.md           # this file
└── Data_Chatbot.pdf    # your FAQ PDF (any .pdf next to app.py is loaded automatically)
```

> You can also put PDFs in a `data/` folder. More PDFs can be added later from the sidebar → **Admin**.

---

## 🚀 Step-by-step setup

### Step 1: Get a free Groq API key
1. Open https://console.groq.com and sign in.
2. Go to **API Keys** → **Create API Key** → copy the key.

### Step 2 (optional): Test in Google Colab
Run each block in a new Colab cell:

```python
!pip install -q streamlit groq pypdf tiktoken fastembed faiss-cpu
```
Upload `app.py` and your PDF using the 📁 Files panel on the left, then:
```python
import os
os.environ["GROQ_API_KEY"] = "paste-your-key-here"
!wget -q -O - ipv4.icanhazip.com      # copy this IP: it is the tunnel password
!streamlit run app.py &>/content/log.txt & npx -y localtunnel --port 8501
```
Open the `https://....loca.lt` link, paste the IP as the password, and test the app.

### Step 3: Create the GitHub repository
1. On https://github.com click **+** → **New repository** → name it `livestock-helper` → **Public** → **Create**.
2. Click **Add file** → **Upload files**, and upload `app.py`, `requirements.txt`, `README.md` and your PDF.
3. Click **Commit changes**.

### Step 4: Deploy on Streamlit Cloud
1. Open https://share.streamlit.io and sign in with GitHub.
2. Click **Create app** → **Deploy a public app from GitHub**.
3. Repository: `your-name/livestock-helper` · Branch: `main` · Main file: `app.py`.
4. **Advanced settings**:
   - Python version: **3.12**
   - Secrets:
     ```
     GROQ_API_KEY = "paste-your-key-here"
     ```
5. Click **Deploy**. The first build takes 3–5 minutes, and the first question takes about 1 minute while the embedding model downloads.

### Step 5: Update later
Edit or upload files on GitHub, and Streamlit Cloud redeploys automatically. Replacing the PDF rebuilds the knowledge base.

---

## ⚠️ Important note about Urdu PDFs

In the current FAQ PDF, the **Urdu pages store text as font shapes, not real Urdu letters**, so no program can read them as text. The app automatically skips these pages and uses the English pages, which cover the same topics. The AI then answers in both Urdu and English.

To also use the Urdu FAQ text directly, export the Urdu file again from **Microsoft Word as PDF** (or save as a Unicode `.pdf` from Google Docs) and check that you can **copy-paste** the Urdu text from the PDF correctly. Then upload it.

## 🛠️ Troubleshooting

| Problem | Solution |
|---|---|
| "No documents found" | Upload the PDF to the repo next to `app.py` |
| "Groq API key is missing" | Add `GROQ_API_KEY = "..."` in Streamlit Cloud → App → Settings → Secrets |
| Model error | Groq may retire a model: update `GROQ_MODELS` and `TRANSLATE_MODEL` in `app.py` (see https://console.groq.com/docs/models) |
| App slow on first question | Normal: the embedding model downloads once (~220 MB) |
| Build error | Streamlit Cloud → Manage app → Logs; check the red error lines |

---

**Disclaimer:** This assistant gives general guidance from department documents. For sick animals, always consult a qualified veterinary doctor or call the **Livestock Helpline 0800-09211**.
