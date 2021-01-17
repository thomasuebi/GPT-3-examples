# GPT-3-examples

This repository shows a variety of GPT-3 prompts and its expected outputs. Note that "expected outputs" refer to the first completion I got when handing the prompt to GPT-3. Feel free to add your own completions via Pull Request.

Authors: Thomas Übellacker (https://github.com/thomasuebi/)

## Email Classification

```python
# classification of an email from the Enron Dataset https://www.cs.cmu.edu/~enron/
response = openai.Completion.create(
  engine="davinci-instruct-beta",
  prompt="Classify the email into one of the following categories: [\"Company Business, Strategy, etc. \", \"Purely Personal\", \"Personal but in professional context (e.g., it was good working with you)\", \"Logistic Arrangements (meeting scheduling, technical support, etc)\",  \"Employment arrangements (job seeking, hiring, recommendations, etc)\", \"Document editing/checking (collaboration)\", \"Empty message\"]\nEmail: \"Please review the attachment and let me know if you have any changes. The final document will provide a starting place for the year end review process.\"\nClassification: \"",
  temperature=0,
  max_tokens=64,
  top_p=1
)
# Expected outcome:
# Document editing/checking (collaboration)"
```
Token length: 97 + email length + output length
(can vary depending on the length of categories)

## Named-Entity Recognition

Extracting event information from email:

```python
restart_sequence = "}"
response = openai.Completion.create(
  engine="davinci",
  prompt="this is an incoming email:\n\"How about an after-work beer in Amstetten? Friday?\nSee you,\nJohn\"\nCalendar events are in the format {eventTitle: String,participants: Array<String>, location: String, when: String}\nThe calendar event extracted from the email:\n{",
  temperature=0,
  max_tokens=100,
  top_p=1,
  stop=["}"]
)
# Expected outcome:
# "eventTitle": "After-work beer",
# "participants": ["John"],
# "location": "Amstetten",
# "when": "Friday"

```
Token length: 47 + incoming email length + output length

Extracting order information from email:

```python
restart_sequence = "}"
response = openai.Completion.create(
  engine="davinci",
  prompt="this is an email:\n\"Hi Thomas,\n\nfor the following season I want to order\n- 5 pieces of the coat with the product number 34534\n- 3 jeans with the number 43544\n\nThank you!\nJohn\"\nOrders are in the format {buyer: string, orderedItems: Array<{productDescription: string, productNr: number, amount: number}>}\nThe order extracted from the email:\n{",
  temperature=0.3,
  max_tokens=100,
  top_p=1,
  stop=["]}", "]\n}"]
)
# Expected outcome:
# "buyer": "John",
# "orderedItems": [
# {
# "productDescription": "Coat",
# "productNr": "34534",
# "amount": 5
# },
# {
# "productDescription": "Jeans",
# "productNr": "43544",
# "amount": 3
# }
```
Token length: 50 + email length + output length

## Email Generation

Email generation by input the preceding email and a list of bullet points

```python
response = openai.Completion.create(
  engine="davinci-instruct-beta",
  prompt="Create email from bullet points\n[[Preceding email:]]\nHi Thomas,\n\nfor the following season I want to order\n\n- 5 pieces of the coat with the product number 34534\n- 3 jeans with the number 43544\n\nThank you!\nJohn\n[[Bullets:]]\n- delivered by sunday\n[[Response Draft:]]",
  temperature=0,
  max_tokens=200,
  top_p=1
)
# Expected outcome:
# Hi John,
#
# I am happy to confirm that we will be able to deliver your order by Sunday.
#
# Thank you!
# Thomas
```
Token length: 25 + preceding email length + bullet points length + outcome email length

## Asking Questions That Can Not Be Answered Ad-Hoc

```python
import os
import openai

openai.api_key = os.environ["OPENAI_API_KEY"]

response = openai.Completion.create(
  engine="davinci-instruct-beta",
  prompt="Dear Mr Boon,\n\nwas there already a deadline announced for the philosophy of science paper? In that case I probably missed it and would be curious where to find it.\n\nThank you in advance.\nThomas Übellacker\n\nTo be able to quickly respond to the email, we find question(s) and key concept(s) that we might want to look up in our documents before responding:\n-",
  temperature=0,
  max_tokens=64,
  top_p=1,
  stop=["\n\n"]
)
# Expected outcome:
# Deadline for philosophy of science paper
# - Philosophy of science
```
Token length: 38 + length of email + length of output
