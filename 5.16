import speech_recognition as sr
import os
import requests
import uuid
import azure.cognitiveservices.speech as speechsdk

# Set up Microsoft Translate API parameters
key = "cb63e81edc9b4f0d9c99f5c4fc6261a7"  # Replace with your API key
endpoint = "https://api.cognitive.microsofttranslator.com"
location = "westus2"

# Set up your Azure subscription key and region
subscription_key = "a2c9ce64e8a74f04a85b547dac85584b"
region = "westus2"

# Set up speech recognition
recognizer = sr.Recognizer()

# Function to translate text using Microsoft Translate API
def translate_text(text, source_lang='zh-CN', target_lang='yue'):
    path = '/translate'
    url = endpoint + path
    params = {
        'api-version': '3.0',
        'from': source_lang,
        'to': [target_lang]
    }
    headers = {
        'Ocp-Apim-Subscription-Key': key,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }
    body = [{'text': text}]
    response = requests.post(url, params=params, headers=headers, json=body)
    translated_text = response.json()[0]['translations'][0]['text']
    return translated_text

# Function to synthesize speech from text using Azure Speech SDK
def synthesize_speech(text, language='yue'):
    try:
        speech_config = speechsdk.SpeechConfig(subscription=subscription_key, region=region)
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config)
        result = speech_synthesizer.speak_text_async(text).get()
        if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
            filename = "temp.wav"
            with open(filename, "wb") as audio_file:
                audio_file.write(result.audio_data)
            return filename
        else:
            print("Speech synthesis failed: {}".format(result.reason))
            return None
    except Exception as e:
        print("Error during speech synthesis:", e)
        return None


# Capture audio from microphone and continuously recognize speech
while True:
    try:
        with sr.Microphone() as mic:
            recognizer.adjust_for_ambient_noise(mic, duration=0.2)
            audio = recognizer.listen(mic)
            text = recognizer.recognize_google(audio, language='yue')
            text = text.lower()
            print(f"Recognized: {text}")
            
            # Translate recognized text using Microsoft Translate API
            translated_text = translate_text(text)
            print(f"Translated: {translated_text}")
            
            # Synthesize speech from translated text
            temp_file = synthesize_speech(translated_text)
            if temp_file:
                print("Speech synthesized to file:", temp_file)
                os.remove(temp_file)
            else:
                print("Speech synthesis failed")
    except sr.UnknownValueError:
        recognizer = sr.Recognizer()
        continue
