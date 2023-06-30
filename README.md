# AI-Desktop-Assistant-Using-Python
LAKURO AI, an interactive desktop assistant, runs on Python and leverages OpenAI's GPT-3 library. This advanced chatbot offers users a seamless conversational experience. It employs voice recognition for input, integrates external APIs for live weather updates and news retrieval, and employs a text-to-speech engine for clear responses.



**source code:**

import os  # Importing the os module to interact with the operating system
import random  # Importing the random module for generating random numbers
import sys  # Importing the sys module to access system-specific parameters and functions
import pyttsx3  # Importing the pyttsx3 module for text-to-speech conversion
import requests  # Importing the requests module for making HTTP requests
import speech_recognition as sr  # Importing the speech_recognition module for speech recognition
import webbrowser  # Importing the webbrowser module for opening websites
from datetime import datetime  # Importing the datetime module for working with dates and times
from config import apikey, weatherapikey, newsapikey  # Importing API keys from a configuration file, choose your API keys 
import openai  # Importing the OpenAI library for AI language processing

def say(text):
    # Function to convert text to speech using pyttsx3 module
    engine = pyttsx3.init()
    engine.say(text)
    engine.runAndWait()

chatString = ""  # Initializing an empty string for storing chat history

def get_weather(city):
    # Function to retrieve weather information for a given city
    weather_url = "http://api.weatherstack.com/current"
    params = {
        'access_key': weatherapikey,
        'query': city
    }
    api_result = requests.get(weather_url, params=params)  # Sending an HTTP GET request to the weather API
    api_response = api_result.json()  # Parsing the JSON response

    if 'error' in api_response:
        return "Weather information not found for the specified city."
    else:
        temperature = api_response['current']['temperature']
        return f"Current temperature in {api_response['location']['name']} is {temperature}℃"

def get_news():
    # Function to retrieve the latest news headlines
    news_url = "https://newsapi.org/v2/top-headlines"
    params = {
        "country": "in",
        "apiKey": newsapikey
    }
    response = requests.get(news_url, params=params).json()  # Sending an HTTP GET request to the news API
    articles = response["articles"]
    news_articles = []
    for article in articles:
        title = article["title"]
        source = article["source"]["name"]
        news_articles.append(f"{title} - {source}")
    return "\n".join(news_articles)

def get_current_time():
    # Function to get the current time
    current_time = datetime.now().strftime("%I:%M %p")
    return f"The current time is {current_time}"

def chat_with_lakuro_ai(query):
    # Function to chat with the LAKURO AI using the OpenAI GPT-3 model
    global chatString
    print(chatString)
    openai.api_key = apikey
    chatString += f"LalithKumar: {query}\n LAKURO: "
    response = openai.Completion.create(
        model="text-davinci-003",
        prompt=chatString,
        temperature=0.7,
        max_tokens=256,
        top_p=1,
        frequency_penalty=0,
        presence_penalty=0
    )
    say(response["choices"][0]["text"])  # Convert the AI response to speech
    chatString += f"{response['choices'][0]['text']}\n"
    return response["choices"][0]["text"]

def ai_files(prompt):
    # Function to generate AI response for a prompt and save it to a text file
    openai.api_key = apikey
    text = f"OpenAI response for Prompt: {prompt} \n *********\n\n"

    response = openai.Completion.create(
        model="text-davinci-003",
        prompt=prompt,
        temperature=0.7,
        max_tokens=256,
        top_p=1,
        frequency_penalty=0,
        presence_penalty=0
    )
    text += response["choices"][0]["text"]
    if not os.path.exists("AI Files"):
        os.mkdir("AI Files")
    with open(f"AI Files/{''.join(prompt.split('intelligence')[1:]).strip()}.txt", "w") as f:
        f.write(text)

error_path = r"C:\Users\LALITK~1\Downloads\Enti_Comedy_Ah.m4a"  # Path to an error audio file

def takeuserinput():
    # Function to take user input through microphone and perform speech recognition
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        r.pause_threshold = 0.6
        audio = r.listen(source)
    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language="en-in")  # Perform speech recognition using Google Speech Recognition API
        print(f"User said: {query}")
        return query
    except Exception as e:
        os.system(f"start {error_path}")  # Play the error audio file if speech recognition fails

if __name__ == '__main__':
    print("LAKURO")
    say("Hello, I'm LAKURO AI")

    while True:
        print("Listening...")
        query = takeuserinput()
        websites = [
            ["youtube", "https://www.youtube.com/"],
            ["my HCL", "https://www.myhcl.com/bprhome/Home/Index"],
            ["Google", "https://www.google.co.in"],
            ["ServiceNow", "https://aptiv.service-now.com/navpage.do"],
            ["Outlook", "https://outlook.live.com/"]
        ]
        for site in websites:
            if f"open {site[0]}".lower() in query.lower():
                say(f"Opening {site[0]} sir... ")
                webbrowser.open(site[1])  # Open the specified website in a web browser
        music_path = r"C:\Users\LALITK~1\Downloads\downfall-21371.mp3"  # Path to a music file
        docpath = r"C:\Users\LALITK~1\Documents\Aptiv_Docs\SupportModel.xlsx"  # Path to a document file
        filepath = r"C:\Users\LALITK~1\Documents\Aptiv_Docs\template_email.txt"  # Path to a file
        TeamViewer_path = r"C:\PROGRA~1\TeamViewer\TeamViewer.exe"  # Path to a desktop application
        FireFox_Path = r"C:\PROGRA~2\MOZILL~1\firefox.exe"  # Path to a desktop application
        Chrome_Path = r"C:\PROGRA~1\Google\Chrome\Application\chrome.exe"  # Path to a desktop application
        CPUOS_Path = r"C:\PROGRA~2\CPU_OS_Simulator\CPUSimulator.exe"  # Path to a desktop application
        Onedrive_Path = r"C:\PROGRA~1\Microsoft_OneDrive\OneDrive.exe"  # Path to a desktop application
        VSCode_Path = r"C:\Users\LALITK~1\AppData\Local\Programs\MICROS~1\Code.exe"  # Path to a desktop application

        DesktopApps = [
            ["music", music_path],
            ["support model list", docpath],
            ["template email", filepath],
            ["TeamViewer", TeamViewer_path],
            ["Firefox", FireFox_Path],
            ["Chrome", Chrome_Path],
            ["CPU OS simulator", CPUOS_Path],
            ["One drive", Onedrive_Path],
            ["VS Code", VSCode_Path],
        ]
        for apps in DesktopApps:
            if f"open {apps[0]}".lower() in query.lower():
                say(f"Opening {apps[0]} sir ...")
                os.system(f"start {apps[1]}")  # Open the specified desktop application
        if "quit".lower() in query:
            exit()  # Exit the program
        elif "reset chat".lower() in query:
            chatString = " "  # Reset the chat history
        elif "weather" in query.lower():
            say("Tell me your city name for knowing its temperature:")
            r = sr.Recognizer()
            with sr.Microphone() as source:
                print("Speak the city name:")
                audio = r.listen(source)
            try:
                city = r.recognize_google(audio)
                print("You said:", city)
            except sr.UnknownValueError:
                print("Sorry, I could not understand your voice.")
            except sr.RequestError:
                print("Sorry, I'm having trouble accessing the speech recognition service.")
            weather_info = get_weather(city)  # Get the weather information for the specified city
            say(weather_info)
            print(weather_info)
        elif "news" in query.lower():
            news_response = get_news()  # Get the latest news headlines
            say("Here are some of the latest news headlines:")
            say(news_response)
        elif "time now" in query.lower():
            current_time = get_current_time()  # Get the current time
            say(current_time)
        elif "using artificial intelligence".lower() in query.lower():
            ai_files(prompt=query)  # Generate AI response for the given prompt and save it to a file
        else:
            chat_with_lakuro_ai(query)  # Chat with the LAKURO AI based on user input
