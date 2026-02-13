

 Final GitHub Project Structure
azure-speech-live-translator/
│
├── .env
├── translator.py
├── requirements.txt
├── README.md
└── audio/
    └── station.wav

📄 requirements.txt
azure-cognitiveservices-speech==1.42.0
python-dotenv==1.0.0

.env
SPEECH_KEY=YOUR_AZURE_SPEECH_KEY
SPEECH_REGION=YOUR_AZURE_REGION

 translator.py (Live + File Support)
import os
from dotenv import load_dotenv
import azure.cognitiveservices.speech as speech_sdk

# Load environment variables
load_dotenv()
speech_key = os.getenv("SPEECH_KEY")
speech_region = os.getenv("SPEECH_REGION")

if not speech_key or not speech_region:
    raise ValueError("Missing SPEECH_KEY or SPEECH_REGION in .env file")

print("Azure Speech Service Region:", speech_region)

# Configure translation
translation_config = speech_sdk.translation.SpeechTranslationConfig(
    subscription=speech_key,
    region=speech_region
)

translation_config.speech_recognition_language = "en-US"

# Supported languages
supported_languages = {
    "fr": "French",
    "es": "Spanish",
    "hi": "Hindi"
}

for lang in supported_languages:
    translation_config.add_target_language(lang)

# Configure speech synthesis
speech_config = speech_sdk.SpeechConfig(
    subscription=speech_key,
    region=speech_region
)

voices = {
    "fr": "fr-FR-HenriNeural",
    "es": "es-ES-ElviraNeural",
    "hi": "hi-IN-MadhurNeural"
}

def translate_from_file(target_language):
    print("\n🎧 Using Audio File Input")

    audio_config = speech_sdk.AudioConfig(
        filename="audio/station.wav"
    )

    translator = speech_sdk.translation.TranslationRecognizer(
        translation_config,
        audio_config=audio_config
    )

    result = translator.recognize_once_async().get()

    if result.reason == speech_sdk.ResultReason.TranslatedSpeech:
        print("Original:", result.text)
        translation = result.translations[target_language]
        print(f"Translated ({target_language}):", translation)
        synthesize_speech(translation, target_language)
    else:
        print("Translation failed:", result.reason)


def translate_from_microphone(target_language):
    print("\n🎤 Speak now...")

    audio_config = speech_sdk.AudioConfig(
        use_default_microphone=True
    )

    translator = speech_sdk.translation.TranslationRecognizer(
        translation_config,
        audio_config=audio_config
    )

    result = translator.recognize_once_async().get()

    if result.reason == speech_sdk.ResultReason.TranslatedSpeech:
        print("Original:", result.text)
        translation = result.translations[target_language]
        print(f"Translated ({target_language}):", translation)
        synthesize_speech_live(translation, target_language)
    else:
        print("Translation failed:", result.reason)


def synthesize_speech(text, language):
    print(" Saving translated speech to output.wav")

    speech_config.speech_synthesis_voice_name = voices.get(language)

    audio_config = speech_sdk.audio.AudioConfig(
        filename="output.wav"
    )

    synthesizer = speech_sdk.SpeechSynthesizer(
        speech_config=speech_config,
        audio_config=audio_config
    )

    result = synthesizer.speak_text_async(text).get()

    if result.reason == speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print("Saved successfully ")
    else:
        print("Synthesis failed:", result.reason)


def synthesize_speech_live(text, language):
    print("🔊 Speaking translation...")

    speech_config.speech_synthesis_voice_name = voices.get(language)

    audio_config = speech_sdk.audio.AudioOutputConfig(
        use_default_speaker=True
    )

    synthesizer = speech_sdk.SpeechSynthesizer(
        speech_config=speech_config,
        audio_config=audio_config
    )

    result = synthesizer.speak_text_async(text).get()

    if result.reason == speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print("Speech completed ")
    else:
        print("Synthesis failed:", result.reason)


def main():
    print("\n===== Azure Live Speech Translator =====")
    print("Supported Languages:")
    for code, name in supported_languages.items():
        print(f"{code} - {name}")

    target_language = input("\nEnter target language code: ").strip()

    if target_language not in supported_languages:
        print("Invalid language selected.")
        return

    mode = input("Choose mode (1 = File, 2 = Microphone): ").strip()

    if mode == "1":
        translate_from_file(target_language)
    elif mode == "2":
        translate_from_microphone(target_language)
    else:
        print("Invalid mode selected.")


if __name__ == "__main__":
    main()

README.md (Professional AI Engineer Version)
#  Azure AI Live Speech Translator

End-to-end Speech-to-Speech Translation system using Azure AI Speech SDK.

## Features
-  Live microphone translation
-  File-based translation
- Real-time speech synthesis
-  English → French / Spanish / Hindi
- Production-ready modular structure

## Architecture

Speech → SpeechTranslationConfig → TranslationRecognizer → Text Translation  
Text → SpeechSynthesizer → Audio Output

## Setup

1. Install dependencies


pip install -r requirements.txt


2. Create `.env`


SPEECH_KEY=YOUR_KEY
SPEECH_REGION=YOUR_REGION


3. Run the app


python translator.py


## Demo Modes

| Mode | Input | Output |
|------|--------|--------|
| File | station.wav | output.wav |
| Live | Microphone | Speaker |

## Example Output



Azure Speech Service Region: eastus
===== Azure Live Speech Translator =====
Enter target language code: fr
Choose mode (1 = File, 2 = Microphone): 2

 Speak now...
Original: Where is the train station?
Translated (fr): Où est la gare ?
Speaking translation...
Speech completed 


## Skills Demonstrated

- Azure AI Speech SDK
- Real-time speech translation
- Text-to-Speech synthesis
- Event-driven architecture
- Secure credential management
- Production CLI design

## Author

Aram Radif AI Engineer Portfolio Project

