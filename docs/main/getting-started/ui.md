---
title: Building Your UI
description: Popular UI options for building great Capacitor mobile apps
slug: /getting-started/ui
---

# Building Your UI

Capacitor apps are web apps at the core. But it takes a lot more than just wrapping a website to deliver a great native-quality mobile app. 

Today, teams have a variety of options for their app UI. Let's explore some of the most popular options.

## Ionic Framework

[Ionic Framework](https://ionicframework.com/) is a mobile-focused UI kit and set of utilities that enable web developers using Capacitor to get a native-quality app experience that follows platform conventions. Ionic Framework is created by the same company that makes Capacitor and is designed specifically with Capacitor in mind.

Today, Ionic Framework is our recommended UI framework for Capacitor, because we believe it will help teams achieve the highest quality native app experience. However, it is _not_ required to use it in your Capacitor apps.

Ionic Framework comes with native-quality transitions and routing for [Angular](https://ionicframework.com/docs/angular/navigation), [React](https://ionicframework.com/docs/react/navigation), and [Vue](https://ionicframework.com/docs/vue/navigation) with deep integration into the most popular routing solution in each framework. Additionally, Ionic comes with powerful components like [Modals](https://ionicframework.com/docs/api/modal), [Menus](https://ionicframework.com/docs/api/menu), [Lists](https://ionicframework.com/docs/api/list) along with powerful item features like [Sliding Items](https://ionicframework.com/docs/api/item-sliding), [Form inputs](https://ionicframework.com/docs/api/input), [Datetime pickers](https://ionicframework.com/docs/api/datetime), [Cards](https://ionicframework.com/docs/api/card), [Tabs](https://ionicframework.com/docs/api/tabs), [iOS-style condensed headers](https://ionicframework.com/docs/api/header#condensed-header), and [so much more](https://ionicframework.com/docs/components).

Ionic Framework requires Angular, React, or Vue, so will only be a fit for teams using those technologies. 

To get started, view the [using Capacitor with Ionic](./with-ionic) docs to learn more.

## Tailwind CSS

[Tailwind CSS](https://tailwindcss.com/) is a popular CSS framework with companion UI template library that many Capacitor developers use to build great app experiences. Some of our favorite examples include [Reflect](https://reflect.app/) and [LogSnag](https://twitter.com/ImSh4yy/status/1615080429417103366?s=20&t=bmVrAb9PNFY6AQPNXwMFYA).

There are also some interesting Tailwind-focused Mobile UI frameworks, such as [Konsta UI](https://konstaui.com/).

When using Tailwind, it's important to keep in mind that Tailwind does not provide mobile-style navigation and routing primitives, so teams will need to take care to build a UX that fits platform conventions. One way to do this is to mix Tailwind with Ionic Framework, as shown in this [Next.js + Tailwind + Ionic Framework + Capacitor template](https://github.com/mlynch/nextjs-tailwind-ionic-capacitor-starter). Another would be to design a UX that avoids traditional forward/back navigation and instead uses tabs or modals. Finally, teams are free to build a custom navigation and routing experience if desired.

## Framework7

[Framework7](https://framework7.io/) is a popular mobile-focused UI library created by the developer of [Swiper](https://swiperjs.com/), a powerful mobile touch slider library.

## Quasar

[Quasar](https://quasar.dev/) is a Vue.js framework with mobile-focused components and [official support for Capacitor](https://quasar.dev/quasar-cli-vite/developing-capacitor-apps/introduction#introduction).

## Material UI

[Material UI](https://mui.com/) is a popular React-focused library implementing the Material Design guidelines.

## Roll your own

If you already have an existing UI kit or would like to implement your own, we recommend reviewing Ionic Framework and the other options presented here for inspiration. Capacitor provides a blank slate to build your dream, but if you choose to roll your own UI you are responsible for building a great experience that users expect. This can be challenging to do on top of building your app, so we generally recommend this only for very advanced teams or for web apps that are already mobile-optimized.
import os
import sys
import time
import pyttsx3
import speech_recognition as sr

# --- অফলাইন ভয়েস ইঞ্জিন সেটআপ ---
engine = pyttsx3.init()
engine.setProperty('rate', 170)     # কথা বলার গতি
engine.setProperty('volume', 1.0)   # সাউন্ড ভলিউম

# বাংলা ভয়েস থাকলে সেট করবে, না হলে ডিফল্ট ইংলিশ অ্যাসিস্ট্যান্ট ভয়েস নেবে
voices = engine.getProperty('voices')
if len(voices) > 1:
    engine.setProperty('voice', voices[1].id) # সাধারণত ১ নম্বরে ফিমেল ভয়েস থাকে

def shila_speak(text):
    """শিলা অ্যাসিস্ট্যান্টের মুখে কথা বলানোর ফাংশন"""
    print(f"[SHILA]: {text}")
    engine.say(text)
    engine.runAndWait()

def listen_offline_command():
    """সম্পূর্ণ অফলাইনে মাইক্রোফোন থেকে ভয়েস ইনপুট নেওয়ার ফাংশন"""
    recognizer = sr.Recognizer()
    
    with sr.Microphone() as source:
        print("\n[কোড স্ট্যাটাস] পারমিশন ওকে। মাইক্রোফোন সক্রিয়... কমান্ড দিন...")
        # আশেপাশের নয়েজ অ্যাডজাস্ট করার জন্য
        recognizer.adjust_for_ambient_noise(source, duration=1)
        audio = recognizer.listen(source)

    try:
        print("[প্রসেসিং] লোকাল অন-ডিভাইস মডেলে ভয়েস অ্যানালাইসিস চলছে...")
        # ইন্টারনেট ছাড়া অফলাইনে কাজ করার জন্য PocketSphinx ব্যবহার করা হয়েছে
        # আপনি চাইলে এটিকে ইংরেজি (en-US) বা ইনস্টল করা লোকাল ল্যাঙ্গুয়েজ মডেলে চালাতে পারেন
        command = recognizer.recognize_sphinx(audio)
        return command.lower()
    except sr.UnknownValueError:
        print("[ত্রুটি] ভয়েস স্পষ্ট ছিল না বা কোনো কমান্ড মেলেনি।")
        return ""
    except sr.RequestError as e:
        print(f"[ত্রুটি] অফলাইন ইঞ্জিন সমস্যা: {e}")
        return ""

def process_shila_system(command, current_state):
    """ভয়েস কমান্ড অনুযায়ী সিকিউরিটি ও হার্ডওয়্যার টগল করার লজিক"""
    print(f"[লগ] সনাক্তকৃত কমান্ড: '{command}'")
    
    # ১. ভয়েস বায়োমেট্রিক আনলক ও লক সিস্টেম (AES-256 স্টেট সিমুলেশন)
    if "unlock" in command or "open" in command:
        if current_state["is_locked"]:
            current_state["is_locked"] = False
            shila_speak("Device unlocked successfully. Biometric verification complete.")
        else:
            shila_speak("Device is already unlocked.")
            
    elif "lock" in command or "close" in command:
        if not current_state["is_locked"]:
            current_state["is_locked"] = True
            shila_speak("Device secured. AES-256 encryption activated.")
        else:
            shila_speak("Device is already locked.")

    # ডিভাইস লক থাকলে অন্য কোনো কমান্ড কাজ করবে না (সিকিউরিটি ফিল্টার)
    elif current_state["is_locked"]:
        shila_speak("Access denied. Please unlock the device first.")
        return current_state

    # ২. ব্লুটুথ কন্ট্রোল (Bluetooth ON/OFF)
    elif "bluetooth on" in command:
        current_state["bluetooth"] = True
        shila_speak("Bluetooth turned on.")
        # এখানে আপনার ওএস অনুযায়ী আসল সিস্টেম কমান্ড দিতে পারেন, যেমন:
        # os.system("rfkill unblock bluetooth") # Linux-এর জন্য
        
    elif "bluetooth off" in command:
        current_state["bluetooth"] = False
        shila_speak("Bluetooth turned off.")
        # os.system("rfkill block bluetooth")

    # ৩. মোবাইল ডেটা / নেটওয়ার্ক কন্ট্রোল
    elif "data on" in command or "network on" in command:
        current_state["data"] = True
        shila_speak("Mobile data connection enabled.")
        
    elif "data off" in command or "network off" in command:
        current_state["data"] = False
        shila_speak("Mobile data disconnected. Switched to core offline architecture.")

    else:
        shila_speak("Command not recognized in local offline engine. Please try again.")
        
    return current_state

# --- মূল প্রোগ্রাম রান ---
if __name__ == "__main__":
    # অ্যাপের প্রাথমিক স্ট্যাটাস (UI এর মতোই শুরুতে লকড এবং অফলাইন থাকবে)
    shila_app_state = {
        "is_locked": True,
        "bluetooth": False,
        "data": False
    }
    
    print("="*50)
    print("      SHILA OS - OFFLINE VOICE SYSTEM INITIALIZED      ")
    print("="*50)
    shila_speak("Shila Core Engine Ready. Security state active.")
    
    # ইনফিনিট লুপ যাতে অ্যাসিস্ট্যান্ট ব্যাকগ্রাউন্ডে চলতেই থাকে
    while True:
        print(f"\n[বর্তমান স্ট্যাটাস] লক করা: {shila_app_state['is_locked']} | ব্লুটুথ: {shila_app_state['bluetooth']} | ডেটা: {shila_app_state['data']}")
        
        # ভয়েস ইনপুট নেওয়া
        user_voice = listen_offline_command()
        
        if user_voice:
            if "exit app" in user_voice or "shutdown" in user_voice:
                shila_speak("Shutting down Shila OS. Goodbye.")
                sys.exit()
                
            # কমান্ড প্রসেস করা
            shila_app_state = process_shila_system(user_voice, shila_app_state)
        
        time.sleep(1)
        
