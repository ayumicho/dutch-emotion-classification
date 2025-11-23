# Emotion Classification Pipeline

This notebook provides a complete end-to-end NLP pipeline that extracts audio from YouTube videos, transcribes Dutch speech, translates to English, and classifies emotions using fine-tuned deep learning models.

---

## Pipeline Steps

The pipeline executes the following steps:

1. Extracts audio from a Dutch YouTube video link.
2. Transcribes the audio into text using Whisper.
3. Segments the transcription into timestamped sentences.
4. Translates each sentence into English using the MarianMT model.
5. Analyzes the emotional tone of each sentence through a remote API.
6. Compiles and stores the results in a CSV file.
---

## Requirements

### System Requirements
This pipeline requires **Python 3.9+** and several system-level dependencies for audio and text processing.

**Core Dependencies**
- **Python 3.9 or higher** [Download Python](https://www.python.org/downloads/)
- **FFmpeg** - Required by Whisper for audio processing


## Installation & Environment Setup

### 1. Clone the Repository

``` 
git clone https://github.com/BredaUniversityADSAI/2024-25c-fai2-adsai-group-team_4_y2c
cd 2024-25c-fai2-adsai-group-team_4_y2c\Datalabtasks\Task11
```

### 2. Create and Activate Virtual Environment

```bash
#Create viritual environment
python -m venv environment

# Activate the environment
source environment/bin/activate     # macOS/Linux
environment\Scripts\activate        # Windows
```

### 4. Installing System Dependencies 

#### Installing `ffmpeg`
FFmpeg is required for Whisper to process audio files. It must be installed at the system level before running the pipeline.

**macOS (using Homebrew):**
```bash
brew install ffmpeg
```

**Windows:**
1. Download FFmpeg from [ffmpeg.org](https://ffmpeg.org/download.html)
2. Add the `bin` folder to your system PATH:

**Linux**
```bash
apt update
apt install ffmpeg lame
```

### 4. Install Python Dependencies
```bash
pip install -r requirements.txt
```
The `requirements.txt` includes the following Python packages:
   
   - `torch`
   - `transformers`
   - `openai-whisper`
   - `pytubefix`
   - `sentencepiece`
   - `sacremoses`
   - `protobuf`

Which can be found [here](https://github.com/BredaUniversityADSAI/2024-25c-fai2-adsai-group-team_4_y2c/blob/main/Datalabtasks/Task11/rrequirements.txt).

---

## API Configuration

**Important:** This pipeline specifically uses a local LLM API (Llama 3.3) from [http://194.171.191.228:30080/](http://194.171.191.228:30080/), which can only be accessed on BUAS campus network (on-campus or via VPN).

Update the `API_base` and `TOKEN` values in the `pipeline.ipynb` notebook with an API endpoint and token:


### Setting Up API Access

1. **Obtain API credentials**

2. **Update credentials in `pipeline.ipynb`:**
   
   Open the notebook and locate the API configuration cell:
```bash
   # Connect to the API
   TOKEN = "api-token"
   API_BASE = "http://api-endpoint.com"
```

3. **Replace with credentials:**
   - Update `TOKEN` with a personal API token
   - Update `API_BASE` with an assigned endpoint

**Note:** If API access is unavailable, emotion classification will default to "neutral" for all entries.

---

## Usage

### Running the Pipeline

1. **Open `pipeline.ipynb` Notebook:**

2. **Configure the YouTube URL:**
   
   In the last cell of the notebook, update the URL:
```bash
   youtube_url = "https://youtu.be/YOUR_VIDEO_ID"
```

3. **Run all cells** (Cell → Run All)

### Output Format

The resulting CSV includes:

| Start Time | End Time | Sentence | Translation | Emotion |
|------------|----------|----------|-------------|---------|
| 00:00:01,000 | 00:00:05,500 | Dutch text... | English text... | happiness |

Each row corresponds to a transcribed audio segment with timestamps, original Dutch text, English translation, and detected emotion.

---

## Design Considerations

This pipeline processes YouTube videos through a sequential workflow implemented in Jupyter Notebook format. The system is designed for Dutch-language content and outputs structured CSV data suitable for editorial review or further analysis.

**Processing Capabilities:**
- Handles Dutch audio transcription using Whisper (large-v3) model
- Translates to English using MarianMT (Helsinki-NLP)
- Classifies emotions into 7 categories using Llama 3.3
- Preserves timestamps for video alignment

**Limitations:**
- Pipeline runs as single workflow - individual stages cannot be toggled
- Notebook-based execution (not command-line interface)
- Requires manual URL configuration for each video
- API-dependent for emotion classification (defaults to "neutral" if unavailable)

---

## License & Attribution

Developed as part of the Natural Language Processing Project at Breda University of Applied Sciences for the Content Intelligence Agency (CIA). For academic and research purposes only.
