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

## Asking Questions That Can Not Be Answered Ad-Hoc

```python
restart_sequence = "]"
response = openai.Completion.create(
  engine="davinci",
  prompt="To be able to quickly respond to emails, we find questions that we might want to look up in our documents before responding:\n\nDear Mr Boon,\nwas there already a deadline announced for the philosophy of science paper? In that case I probably missed it and would be curious where to find it.\nThank you in advance.\nThomas Übellacker\n\nIn order to respond to this email, we need to look up in our internal documents the following explicit, objective questions:\n[\"What is the deadline for the paper in 'Philosophy of Science'?\"]\n-----------\nDear Rachel,\nin our group the question occurred whether at the final exam in calculus we are allowed to use technology/a calculator or not at all.\nThank you in advance.\nKind regards,\nThomas Übellacker\n\nIn order to respond to this email, we need to look up in our internal documents the following explicit, objective questions:\n[\"Is it allowed to use technology in the final exam of Rachel's calculus course?\", \"Is it allowed to use a calculator in the final exam of Rachel's calculus course?\"]\n-----------\nHi Adi,\ntomorrow 10:15 works for me.\nHere's the link: meet.google.com/vnx-dwwc-xiy\nSee you then.\nThomas\n\nIn order to respond to this email, we need to look up in our internal documents the following explicit, objective questions:\n[]\n-----------\nDear Dr. Smirnov,\n\nI have written you about three weeks ago regarding a waiver for your UCM course SCI2033 Datamining that I really would like to attend in period 4.\n\nUnfortunately, I do not satisfy all prerequisites formally, but after carefully reading through the course material and talking with former course attendees, I am sure to be able to follow your course without any problem.\n\nBefore entering UCM I have worked as a software engineer at an Austrian TV channel group and am still working for a tech-startup where I am used to deal with a lot of data. I have a good understanding in data structures and databases plus their querying languages and have a solid knowhow in data preparation, data visualization, data crawling and applying basic machine learning models which I think will help me make the best out of your course.\n\nTherefore I kindly ask for a waiver to be able to attend your course next semester.\n\nI will then of course be pleased to answer any additional questions you wish to raise regarding this waiver request. Thank you in advance!\n\nYours sincerely,\n\nThomas Übellacker\ni6254894\n\nIn order to respond to this email, we need to look up in our internal documents the following explicit, objective questions:\n[",
  temperature=0,
  max_tokens=100,
  top_p=1,
  stop=["]", "\n"]
)
# Expected outcome:
# ["Is there a waiver for the course 'Data Mining'?"
```
