# Health Resource Chat Bot

## Overview

The **Health Resource Chat Bot** is a conversational AI application designed to provide companionship and mental health resources for patients in a hospital setting.

This project uses the Python **ChatterBot** library to train a chatbot with conversational data. The chatbot learns from predefined conversations and responds to users based on similar inputs.

This guide explains how to:

- Set up the development environment
- Install the required dependencies
- Train the chatbot
- Format custom training data
- Reset the chatbot's learned data
- Run and interact with the chatbot

This guide follows the steps from the **Real Python** tutorial *Building a Chatbot With Python*.

---

## Prerequisites

Before getting started, make sure you have:

- Python 3 installed
- pip
- Git (recommended)
- Visual Studio Code or another code editor
- A terminal or command prompt

---

## Table of Contents

1. [Chat Bot Structure](#1-chat-bot-structure)
2. [Creating Your Virtual Environment](#2-creating-your-virtual-environment)
3. [Preparing the ChatterBot Library](#3-preparing-the-chatterbot-library)
4. [Chat Bot Training](#4-chat-bot-training)
5. [Using the Chat Bot](#5-using-the-chat-bot)
6. [Additional Resources](#additional-resources)

## 1. Chat Bot Structure

The main chatbot program is contained in **`bot.py`**. This script performs three primary tasks:

1. Trains the chatbot using the available training data.
2. Reads user input from the terminal.
3. Generates and returns an appropriate response.

### How the Chatbot Responds

The chatbot is trained using conversations formatted as **input–response** pairs. During a conversation, it compares the user's input with its training data and selects the response associated with the most similar input.

For example:

```text
User: Hi
Bot: Hello there! What can I help you with?
```

Because the chatbot relies entirely on its training data, it **cannot generate responses for situations it has never learned**. Expanding the training dataset will generally improve the quality and variety of its responses.

### Conversation Flow

When `bot.py` is executed, the chatbot continuously waits for user input until an exit command is entered.

The interaction follows this process:

1. Prompt the user for input.
2. Read the user's message.
3. Compare the message against the chatbot's training data.
4. Return the closest matching response.
5. Repeat until the user exits.

The chatbot will terminate if the user enters one of the predefined exit commands, such as:

- `quit`
- `exit`
- `bye`
- `goodbye`


## 2. Creating Your Virtual Environment

A virtual environment isolates your project's dependencies from the rest of your system. This helps prevent version conflicts between packages and ensures that the chatbot uses the correct libraries without affecting other Python projects.

### Step 1: Create a Project Folder

Create a folder to store your chatbot project and virtual environment. Open this folder in **Visual Studio Code** (or another code editor), then open a terminal in that directory.

### Step 2: Create the Virtual Environment

Create a virtual environment named `chatbotenv` by running:

```powershell
python -m venv chatbotenv
```

### Step 3: Activate the Virtual Environment

Activate the virtual environment:

**Windows (PowerShell)**

```powershell
chatbotenv\Scripts\activate
```

After activation, your terminal prompt should look similar to:

```text
(chatbotenv) PS C:\YourProject>
```

> **Note**
>
> If you are using Command Prompt, Git Bash, or macOS/Linux, the activation command will be different. Refer to the Python documentation for the appropriate command for your operating system.

## 3. Preparing the ChatterBot Library

Install the required dependencies before running the chatbot:

```powershell
python -m pip install chatterbot==1.0.4 pytz
```

> **Warning**
>
> This project uses **ChatterBot 1.0.4**, which depends on several deprecated packages. When using newer versions of Python, you may encounter compatibility issues. If you receive one of the errors below, apply the corresponding fix.

### Common Compatibility Fixes

| Error Message | File | Line | Action |
|---------------|------|------|--------|
| `GeneralAttributeError: module 'time' has no attribute 'clock'` | `chatbotenv\Lib\site-packages\sqlalchemy\util\compat.py` | 264 | Replace `time.clock` with `time.perf_counter`. |
| `AttributeError: module 'collections' has no attribute 'Hashable'` | `chatbotenv\Lib\site-packages\yaml\constructor.py` | 8–9 | Update the import statement and add the compatibility line shown below. |

### Replace `time.clock`

Replace:

```python
time_func = time.clock
```

with:

```python
time_func = time.perf_counter
```

### Replace `collections.Hashable`

Replace:

```python
import collections, datetime, base64, binascii, re, sys, types
```

with:

```python
import datetime
import base64
import binascii
import re
import sys
import types
import collections
import collections.abc

collections.Hashable = collections.abc.Hashable
```

> **Note**
>
> The `collections.Hashable` alias was moved to `collections.abc` in newer Python versions. This compatibility fix allows ChatterBot to continue working without modifying the rest of the library.

After applying these changes, run the chatbot again. If you receive an error indicating that the training file does not exist, this is expected if you have not yet created your training data. The next section explains how to prepare and train the chatbot.



## 4. Chat Bot Training
This chatbot is a Natural Language Processing (NLP) chatbot. This type of chatbot needs to be provided with data to train off of if you want it to reply with useful responses. 

Each time ```bot.py``` is run, the training procedures are re-done.

We are using the chatterbot python library to make our bot which can be trained in many ways. The two main ways we will be using are the ```List_trainer``` and the ```Corpus_trainer```. These allow you to train your chatbot with personalized information in the form of 'input' and 'response'

### List Trainer
One way it can be trained is with the ```List_trainer```. This takes two arguments, a user input and the response that should be given. For example:

```
    List_trainer.train([
    "Hi",
    "Hello there! What can I help you with?",
    ])
```
This instructs the chatbot to respond to "Hi" with "Hello there! What can I help you with?"

### Corpus Trainer
A more efficient way to train the chatbot with large amounts of information is with the ```Corpus_trainer```. This function takes a ```.yml``` file filled with user input cases and responses and trains the bot on them all. 

There exist pre-made files containing training data in the chatterbot library (chatterbot_corpus\data), but you can also make your own training files using the same format as seen below.

```
Categories:
- Health Companion Training
Conversations:
- - User input
  - Chatbot response
- - I am not feeling well today.
  - I am sorry to hear that, would you like to tell me more about how you are feeling?
```
 These files can be implemented into your chatbot by running the ```Corpus_trainer``` function where each element in the list below is the path to the specific ```.yml``` file.

```
Corpus_trainer.train(
    "chatterbot.corpus.english.emotion",
    "chatterbot.corpus.english.conversations",
    "chatterbot.corpus.english.greetings",
    "chatterbot.corpus.custom"
)
```



### Format Training Data
Another way to gather data to train the chatbot is to import data from external sources in other forms. 

An example of this is done with [GPT generated therapist transcripts](https://www.kaggle.com/datasets/thedevastator/synthetic-therapy-conversations-dataset). Here, a extensive ```.csv``` file with user inputs and responses can be formatted, using a script, to the exact format needed for the corpus trainer.

Be careful when importing different files with conversational data since some special characters have different meanings when in a ```.yml``` file. It can be useful to write a line that removes these characters from your ```.csv``` file or removes specific phrases/text that you do not want to be included in the chatbot response:
```
import pandas as pd

# Read the CSV file
df = pd.read_csv('your_file.csv')

# Define the phrases to remove
phrases_to_remove = ['&', '@', 'another phrase']

# Remove the phrases from all string columns
df = df.applymap(
    lambda x: ''.join(x.replace(phrase, '') for phrase in phrases_to_remove)
    if isinstance(x, str) else x
)

# Save the cleaned DataFrame back to a CSV
df.to_csv('cleaned_file.csv', index=False)

```

The training file also must be formatted in a specific way that the trainer function can read. This format can be attained through something like this, where the text between two characters is extracted and formatted.

In [format_training_data.py](Chatbot_code/format_training_data.py), the text between "{" and "}" is saved into an array. The value of each array element is written into the new file alternating 'user input' (- -) and 'chatbot response'(   -).


Check out the provided example by running [format_training_data.py](Chatbot_code/format_training_data.py) to format [health_training_data2.csv](Chatbot_code/health_training_data2.csv) into a useable training file.




### Untraining
Everything that the chatbot learns is stored in its memory even after the program ends. If you are running the training programs and realize you trained the bot with something you do not want it to know/talk to users about, you can run the [```untrain.py```](Chatbot_code/untrain.py) file to reset its memory.


## 5. Using The Chat Bot
1. Run ```bot.py``` to initiate chat bot.
2. Allow training data to download completely.
3. Once the input prompt ">" appears you can type your question/into the terminal and hit "Enter" to send your input to the chatbot.
4. Keep on chatting!

### Tips to use
* Provide as much context as possible in your input (i.e. no single-word responses) since the bot does not have short-term memory of what was previously said in the conversation. Consider using variables to keep track of important information such as the users name, age, and a hobby for the chatbot to train the chatbot with. An example of this can be found in ```train.py```. In order to use this example, uncomment the code on line 21 of ```bot.py``` and run the chatbot.
* By default, the chatbot will 'learn' from it's interactions when you are testing/running it, allowing it to train off of what you say to it while it is running. If you want to disable this feature, set the the read_only parameter to be True when initializing the Chatbot on line 11 of ```train.py```.


<br><br><br><br>
# Additional Resources
* [Setting up a Python environment](https://code.visualstudio.com/docs/python/environments)
* [Free chatbot tools](https://www.edenai.co/post/top-free-chatbot-tools-apis-and-open-source-models): These are other resources that can be used to build a chatbot.
