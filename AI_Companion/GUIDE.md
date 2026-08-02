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

# 1. Chat Bot Structure

```bot.py``` is the main file that launches the chatbot. This script does 3 main things to make the chatbot work:
1. Training the chatbot.
2. Reading user input.
3. Replying to the user with a line from training data.

The chatbot is trained using conversations formatted into 'input' and 'response'. It then uses these cases to base its answers on, so if a similar input is made to that in its training data, it will respond with a similar response.

The chatbot runs on a "while loop", prompting a user input, reading the input, and providing a response. If the user input contains one of the pre-defined end conditions (such as 'quit', 'exit', 'goodbye', or 'bye') the loop will end, closing the chatbot. Otherwise, the chatbot will compare the user input to its training data and output the response of the trained input that is most similar to that of the user.

Please note that this means the chatbot is unable to give a response that it has not seen in the training. If you want your chatbot to have more realistic conversations, it is important to provide more training data for it to use. 


# 2. Creating Your Virtual Environment
Virtual environments are used in software development since they manage packages, dependencies, and versions to avoid conflicts between other package versions. Conflicting package versions can cause these packages to be uninstalled to allow for the others to run properly, causing problems for other coding files later on.

First, create a folder to store all your chatbot files and virtual environment. Open that folder in VS code (or another source code editor) and open your terminal.<br><br>
Create your virtual environment named "chatbotenv" using:
```
PS> python -m venv chatbotenv
```
Activate your virtual environment using:
```
PS> chatbotenv\Scripts\activate
```
You should now see the virtual environment name in brackets in your terminal. 

# 3. Preparing chatterbot Library
Install the chatterbot library using:
```
(chatbotenv) PS> python -m pip install chatterbot==1.0.4 pytz
```

Since some of the libraries are out of date, some manual changes need to be made in the files. When you first run ```bot.py``` some of these errors will pop up.

Use the following table to edit them by selecting the link in the terminal where the problem is occurring and making the following changes.

Error Message |File Path| Line #  | Wrong | Right |
--------------|---------|---------| ------|-------|
```GeneralAttributeError: module 'time' has no attribute 'clock'``` |chatbotenv\Lib\site-packages\sqlalchemy\util\compat.py|264   |```time_func = time.clock``` | ```time_func = time.perf_counter```
```AttributeError: module 'collections' has no attribute 'Hashable'``` | chatbotenv\Lib\site-packages\yaml\constructor.py | 8,9 |```import collections, datetime, base64, binascii, re, sys, types``` | ```import datetime, base64, binascii, re, sys, types, collections, collections.abc``` <br><br> ```collections.Hashable = collections.abc.Hashable```

Once these changes are complete the only error you will get is that your training file does not exist...yet! That is your next step after learning how this chatbot script works.



# 4. Chat Bot Training
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


# 5. Using The Chat Bot
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
