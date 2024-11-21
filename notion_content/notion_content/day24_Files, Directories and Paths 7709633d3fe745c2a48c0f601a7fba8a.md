# day24_Files, Directories and Paths

[https://replit.com/@chenjinghao/day24sname-game-ver2-save-highest-score](https://replit.com/@chenjinghao/day24sname-game-ver2-save-highest-score)

code for a small challenge in the end lesson

```python
#to generate multiple invitation letter for each individual on the list
with open('./Input/Names/invited_names.txt', 'r') as file:
    names = file.readlines()
    names = [name.strip() for name in names]
    print(names)

for name in names:
    with open('./Input/Letters/starting_letter.txt') as file:
        sample_letter = file.read()
        individual_letter = sample_letter.replace('[name]', name)
        with open(f'./Output/ReadyToSend/letter_for_{name}.txt', mode='w') as file:
            file.write(individual_letter)
```