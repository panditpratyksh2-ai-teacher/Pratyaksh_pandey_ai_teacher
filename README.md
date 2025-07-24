import streamlit as st
import speech_recognition as sr
from gtts import gTTS
import os
import time

# 🎤 Voice Input
def get_voice_input():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        st.info("🎙️ बोलिए, मैं सुन रहा हूँ...")
        audio = r.listen(source, timeout=5)
    try:
        text = r.recognize_google(audio, language='hi-IN')
        st.success(f"आपने पूछा: {text}")
        return text
    except Exception as e:
        st.error("⚠️ कुछ समझ नहीं आया, कृपया दोबारा बोलें।")
        return ""

# 🔊 Text-to-Speech
def speak(text, lang='hi'):
    tts = gTTS(text=text, lang=lang)
    filename = "output.mp3"
    tts.save(filename)
    os.system("start output.mp3" if os.name == "nt" else f"mpg123 {filename}")
    time.sleep(2)

# 📚 Simple Question-Answer Bank (आप इसे बड़ा बना सकते हैं)
qa_bank = {
    "न्यूटन का पहला नियम": "यदि कोई वस्तु स्थिर है या गति कर रही है, तो वह तब तक वैसी ही रहेगी जब तक उस पर कोई बाहरी बल न लगे।",
    "गुरुत्वाकर्षण क्या है": "गुरुत्वाकर्षण वह बल है जो पृथ्वी हमें अपनी ओर खींचती है।",
    "पृथ्वी का उपग्रह": "चंद्रमा पृथ्वी का एकमात्र प्राकृतिक उपग्रह है।",
    "तापन के प्रकार": "तीन प्रकार के तापन हैं: चालन, संवहन और विकिरण।"
}

# 🎓 Streamlit UI
st.title("📚 AI टीचर — आपकी खुद की वर्चुअल क्लास!")
st.write("बोलकर सवाल पूछिए और जवाब सुनिए।")

# 🎤 Voice Question
if st.button("🎙️ सवाल पूछें (बोलें)"):
    question = get_voice_input()

    # 🤖 Match from Q-A Bank
    found = False
    for q in qa_bank:
        if q in question:
            answer = qa_bank[q]
            st.success(f"जवाब: {answer}")
            speak(answer)
            found = True
            break
    if not found:
        msg = "माफ़ कीजिए, यह सवाल मेरे पास अभी नहीं है।"
        st.warning(msg)
        speak(msg)
