# voice-assistant
My python voice assiatant project
import speech_recognition as sr
import pyttsx3
import datetime
import webbrowser
import os
import subprocess
import pywhatkit
import pyautogui

# Initialize
engine = pyttsx3.init()
recognizer = sr.Recognizer()
WAKE_WORD = "spec"
HISTORY_FILE = "history.txt"

# Speak
def speak(text):
    print("Assistant:", text)
    engine.say(text)
    engine.runAndWait()

# Listen
def listen_command():
    with sr.Microphone() as source:
        print("🎙️ Listening...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)
        try:
            command = recognizer.recognize_google(audio).lower()
            print("You said:", command)
            return command
        except sr.UnknownValueError:
            speak("Sorry, I didn't catch that.")
            return ""
        except sr.RequestError:
            speak("Network error.")
            return ""

# Save command history
def save_history(command):
    with open(HISTORY_FILE, "a") as file:
        file.write(command + "\n")

# Open websites
def open_website(command):
    if "youtube" in command:
        webbrowser.open("https://youtube.com")
        speak("Opening YouTube.")
    elif "google" in command:
        webbrowser.open("https://google.com")
        speak("Opening Google.")
    else:
        website = command.replace("open", "").strip().replace(" ", "")
        url = f"https://{website}.com"
        webbrowser.open(url)
        speak(f"Opening {website}")

# Search on web
def search_web(command):
    search_term = command.replace("search", "").strip()
    webbrowser.open(f"https://www.google.com/search?q={search_term}")
    pywhatkit.search(search_term)
    speak(f"Searching for {search_term} on Google.")

# Play YouTube video
def play_youtube(command):
    song = command.replace("play", "").replace("on youtube", "").strip()
    speak(f"Playing {song} on YouTube.")
    pywhatkit.playonyt(song)

# Open Spotify
def open_spotify():
    path = r"C:\Users\Karthik\AppData\Roaming\Spotify\Spotify.exe"
    if os.path.exists(path):
        subprocess.Popen([path])
        speak("Opening Spotify.")
    else:
        speak("Spotify not found.")

# Volume control
def control_volume(action):
    if action == "up":
        for _ in range(5): pyautogui.press("volumeup")
        speak("Volume increased.")
    elif action == "down":
        for _ in range(5): pyautogui.press("volumedown")
        speak("Volume decreased.")
    elif action == "mute":
        pyautogui.press("volumemute")
        speak("Volume muted.")

# Close browser
def close_browser():
    os.system("taskkill /im chrome.exe /f")
    os.system("taskkill /im msedge.exe /f")
    speak("Browser closed.")

# Main logic
def main():
    speak(f"Hello! My name is {WAKE_WORD}. I am ready.")
    while True:
        command = listen_command()
        if WAKE_WORD in command:
            command = command.replace(WAKE_WORD, "").strip()
            save_history(command)

            if "time" in command:
                now = datetime.datetime.now().strftime("%I:%M %p")
                speak(f"The time is {now}.")

            elif "open" in command:
                open_website(command)

            elif "search" in command:
                search_web(command)

            elif "play" in command and "youtube" in command:
                play_youtube(command)

            elif "spotify" in command:
                open_spotify()

            elif "volume up" in command:
                control_volume("up")
            elif "volume down" in command:
                control_volume("down")
            elif "mute" in command:
                control_volume("mute")

            elif "close browser" in command or "close tabs" in command:
                close_browser()

            elif "exit" in command or "stop" in command:
                speak("Goodbye! See you next time.")
                break

            else:
                speak("Sorry, I didn't understand that.")
        else:
            print(" Waiting for wake word...")

main()
