# OCR Plat Nomor Kendaraan menggunakan VLM (LM Studio + Python)

Program OCR plat nomor kendaraan Indonesia menggunakan Visual Language Model (VLM)
yang dijalankan via LM Studio, diintegrasikan dengan Python, dievaluasi dengan
Character Error Rate (CER).

## 1. Setup LM Studio
1. Install LM Studio dari https://lmstudio.ai
2. Download model vision via terminal, mis:
   ```bash
   lms get qwen2-vl-2b-instruct
   ```
   (atau model VLM lain: llava, moondream2)
3. Pastikan LM Studio dalam keadaan running (server backend aktif di background,
   tidak perlu setting manual Local Server karena script pakai SDK resmi `lmstudio-python`,
   bukan REST API OpenAI-compatible)

## 2. Setup Python
```bash
pip install -r requirements.txt
```

## 3. Download dataset
```bash
python src/download_dataset.py
```
Dataset akan ter-cache otomatis via `kagglehub` (tidak disimpan di repo ini).
Salin/arahkan folder `test` hasil download ke `data/test`, atau ubah path
`TEST_FOLDER` di `src/infer.py`.

## 4. Jalankan inferensi
Pastikan LM Studio Local Server sudah aktif, lalu:
```bash
python src/infer.py
```
Output: `results/raw_predictions.csv` (image, ground_truth, prediction)

## 5. Evaluasi CER
```bash
python src/evaluate.py
```
Output: `results/prediction_results.csv` (image, ground_truth, prediction, CER_score)
+ ringkasan rata-rata CER dan contoh sukses/gagal di terminal.

## Struktur Project
```
plat-nomor-ocr-vlm/
├── src/
│   ├── download_dataset.py
│   ├── infer.py
│   └── evaluate.py
├── results/
├── requirements.txt
├── .gitignore
└── README.md
```

## Catatan
- `ground_truth` diambil dari nama file gambar; sesuaikan `default_ground_truth_from_filename()`
  di `src/infer.py` jika dataset punya file label terpisah.
- CER dihitung dengan `jiwer.cer` (Levenshtein-based, sesuai formula CER = (S+D+I)/N).
- Integrasi LM Studio-Python memakai SDK resmi `lmstudio-python`, sesuai referensi:
  https://lmstudio.ai/docs/python/llm-prediction/image-input