# day55_flask learning_game: guess number

[https://replit.com/@chenjinghao/day54flask-learning](https://replit.com/@chenjinghao/day54flask-learning)

```python
from flask import Flask
import random

random_num = random.randrange(10)
print(random_num)

app = Flask(__name__)

@app.route('/')
def title():
    return '<div style="text-align: center">' \
           '<h1> Guess a number between 0 and 9 </h1>' \
           '<iframe src="https://giphy.com/embed/3o7aCSPqXE5C6T8tBC" width="480" height="480" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/animation-retro-pixel-3o7aCSPqXE5C6T8tBC">via GIPHY</a></p>' \
           '</div>'

@app.route(f'/<int:guess>')
def guess_num(guess):
    if guess == random_num:
        return '<div style="text-align: center">' \
               '<h1 style="color:green;"> You Found me! </h1>' \
               '<iframe src="https://giphy.com/embed/4T7e4DmcrP9du" width="458" height="480" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/puppy-biscuit-emerging-4T7e4DmcrP9du">via GIPHY</a></p>' \
               '</div>'
    elif guess < random_num:
        return '<div style="text-align: center">' \
               '<h1 style="color:red;"> Too low, try again! </h1>' \
               '<iframe src="https://giphy.com/embed/jD4DwBtqPXRXa" width="384" height="480" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/work-digging-jD4DwBtqPXRXa">via GIPHY</a></p>' \
               '</div>'
    elif guess > random_num:
        return '<div style="text-align: center">' \
               '<h1 style="color:blue;"> Too high, try again! </h1> ' \
               '<iframe src="https://giphy.com/embed/3o6ZtaO9BZHcOjmErm" width="480" height="453" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/dog-puppy-fly-3o6ZtaO9BZHcOjmErm">via GIPHY</a></p>' \
               '</div>'

if __name__ == '__main__':
    app.run(debug=True)
```