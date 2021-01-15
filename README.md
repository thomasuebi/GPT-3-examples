# GPT-3-examples

This repository shows a variety of GPT-3 prompts and its expected outputs. Note that "expected outputs" refer to the first completion I got when handing the prompt to GPT-3. Feel free to add your own completions via Pull Request.

Authors: Thomas Übellacker (https://github.com/thomasuebi/)
Last updated: 2021-01-15

## Email Classification

## Entity Recognition

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
