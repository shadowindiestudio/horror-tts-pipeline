# Horror TTS Pipeline: Free Offline Dialogue Generation for UE5.3.2

Professional-grade text-to-speech (TTS) pipeline for Unreal Engine 5.3.2 using multilingual XTTS v2 voice cloning, Bark nonverbals, and UE5 audio routing. **Zero cost. Fully offline. RTX 4060 compatible.**

## 🎯 Overview

Generate broadcast-quality game dialogue from .docx scripts:
- **XTTS v2**: Multilingual dialogue synthesis with voice cloning (17 languages)
- **Bark**: Nonverbal sound effects (sighs, gasps, breaths, whispers)
- **Audacity**: Optional polish with free plugins (TDR Nova, Valhalla Supermassive)
- **UE5.3.2**: Submix-based audio routing with sidechain ducking and reverb

**Expected output for 30-minute chapter:** 250-300 dialogue lines in 2-4 hours (automated).

## 📋 Requirements

- **OS:** Windows 11
- **GPU:** NVIDIA RTX 4060 (8GB VRAM) or equivalent
- **Python:** 3.11
- **CUDA:** 12.1 (auto-configured)
- **Storage:** ~8GB for models, ~5GB for output audio

## 🚀 Quick Start

### 1. Clone & Setup

git clone https://github.com/YOUR_USERNAME/horror-tts-pipeline.git
cd horror-tts-pipeline
python -m venv tts_env
tts_env\Scripts\activate # Windows
python -m pip install --upgrade pip
pip install -r requirements.txt

### 2. Download Models

python scripts/1_environment_setup.py

### 3. Add Data
- Place voice references in `data/voice_refs/` (one .wav per character)
- Place dialogue .docx files in `data/dialogues/`

### 4. Generate Audio

python scripts/5_docx_parser.py # Parse .docx → CSV
python scripts/4_batch_dialogue_processor.py # Generate WAVs

### 5. Import to UE5.3.2
- Copy `outputs/manifest.json` to UE5 project
- Follow `docs/UE5_Audio_Integration.md`

## 📁 Project Structure


horror-tts-pipeline/
├── scripts/ # Python pipeline scripts
├── data/
│ ├── voice_refs/ # Character voice samples (6-30s .wav)
│ └── dialogues/ # Source .docx scripts
├── outputs/
│ ├── dialog/ # Generated dialogue WAVs
│ ├── nonverbal/ # Bark sound effects
│ └── manifest.json # UE5 import file
├── config/ # Configuration files
├── docs/ # Documentation
├── tests/ # Unit tests
└── requirements.txt # Dependencies

## 📖 Documentation

- [**Implementation Guide**](docs/Implementation_Guide.md): Step-by-step setup & batch processing
- [**UE5 Audio Integration**](docs/UE5_Audio_Integration.md): Submix routing, ducking, reverb configuration
- [**Audacity Workflow**](docs/Audacity_Workflow.md): Optional audio polishing with free plugins
- [**Troubleshooting**](docs/Troubleshooting.md): RTX 4060 optimization, common errors

## 🎤 Voice References Format

Place in `data/voice_refs/`:
- **Filename:** `{character_name}_ref.wav` (lowercase)
- **Duration:** 6-30 seconds
- **Format:** Mono or stereo, 22050 Hz or higher
- **Content:** Clear speech, no background noise

Example:

data/voice_refs/
├── protagonist_ref.wav
├── antagonist_ref.wav
└── narrator_ref.wav

## 📝 Dialogue Format (.docx)

Place in `data/dialogues/`:


SCENE 1
PROTAGONIST: The darkness was absolute. Not a single light.
ANTAGONIST: [sighs] Where are you hiding?
PROTAGONIST: ... I don't know. I'm afraid.

**Rules:**
- CHARACTER NAME in ALL CAPS
- Dialogue on next line after colon
- Optional tokens: `[sighs]`, `[gasps]`, `[clears throat]`, `[whispers]`
- Pauses: `...` (ellipsis) or `—` (em-dash)

## ⚙️ Configuration

Edit `config/config.json`:

{
"device": "cuda",
"gpu_available": true,
"vram_gb": 8.0,
"batch_size": 1,
"chunk_size_seconds": 3,
"target_sr": 48000,
"default_language": "en",
"supported_languages": ["en", "es", "fr", "de", "it", "pt", "pl", "tr", "ru", "nl", "cs", "ar", "zh-cn", "ja", "hu", "ko", "hi"]
}

## 🔧 Pipeline Stages

### Stage 1: Environment Setup

python scripts/1_environment_setup.py
Downloads XTTS v2 & Bark models (~5GB), verifies GPU.

### Stage 2: Parse Dialogue

python scripts/5_docx_parser.py
Converts .docx → `outputs/dialogue_data.csv`

### Stage 3: Batch Synthesis

python scripts/4_batch_dialogue_processor.py
Generates WAVs, nonverbals, and `manifest.json`

### Stage 4: Optional Polishing (Audacity)
Follow `docs/Audacity_Workflow.md` for de-essing and reverb.

### Stage 5: UE5 Integration
Follow `docs/UE5_Audio_Integration.md` for submixes and ducking.

## 📊 Performance (RTX 4060)

| Stage | Lines | Time |
|---|---|---|
| Setup (models) | - | 5-10 min |
| Parsing | 250 | 30 sec |
| XTTS synthesis | 250 | 2-4 hours |
| Bark nonverbals | 50 | 5 min |
| Optional polish | 250 | 1-4 hours |
| UE5 import | - | 45 min |
| **Total** | **250** | **5-11 hours** |

## 🆓 Free Software Stack

- **XTTS v2** (coqui-tts): Multilingual TTS
- **Bark** (suno-ai): Generative audio
- **PyTorch + CUDA:** GPU inference
- **Audacity:** Audio editing (optional)
- **TDR Nova:** Free dynamic EQ (optional)
- **Valhalla Supermassive:** Free reverb (optional)
- **UE5.3.2:** Game engine

**Total cost: $0**

## 🐛 Troubleshooting

### CUDA Out of Memory
→ Set `batch_size: 1` in config.json

### Voice Reference Not Found
→ Check `data/voice_refs/` naming: `{character}_ref.wav` (lowercase)

### Bark Inconsistent
→ Lock seed: `torch.manual_seed(42)`, reduce temperature to 0.5

### UE5 Ducking Not Working
→ Verify Ambience_Submix has Dynamics Processor keyed by Dialogue_KeySource bus

See [Troubleshooting.md](docs/Troubleshooting.md) for detailed solutions.

## 📚 Advanced Topics

- **Multilingual synthesis**: Change `language` code in batch processor (en, es, fr, de, etc.)
- **Voice cloning quality**: Longer reference clips (20-30s) = better results
- **Pause control**: Edit silence duration in `silence_ms` parameter (400-1000ms recommended)
- **Reverb zones**: Per-location reverb sends in UE5 Blueprint
- **Batch optimization**: Process one character at a time if VRAM pressure

## 🤝 Contributing

Contributions welcome! Areas:
- XTTS inference optimization
- Bark prompt engineering
- UE5 Blueprint improvements
- Audacity automation scripts

## 📄 License

MIT License – See [LICENSE](LICENSE) file

## 🎓 Attribution

- **XTTS v2**: [Coqui TTS](https://github.com/coqui-ai/TTS)
- **Bark**: [Suno AI](https://github.com/suno-ai/bark)
- **TDR Nova**: [Tokyo Dawn Labs](https://www.tokyodawn.net/)
- **Valhalla Supermassive**: [Valhalla DSP](https://valhalladsp.com/)

## 📞 Support

Issues? Check [Troubleshooting.md](docs/Troubleshooting.md) or open a GitHub issue.

---

**Last updated:** November 2025  
**Status:** Production-ready  
**Test environment:** Windows 11, RTX 4060 Laptop, Python 3.11, UE5.3.2