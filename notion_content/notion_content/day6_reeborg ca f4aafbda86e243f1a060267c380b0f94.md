# day6_reeborg.ca

```python
##hurdlre 4
## https://reeborg.ca/reeborg.html?lang=en&mode=python&menu=worlds%2Fmenus%2Freeborg_intro_en.json&name=Hurdle%204&url=worlds%2Ftutorial_en%2Fhurdle4.json

# https://reeborg.ca/reeborg.html?lang=en&mode=python&menu=worlds%2Fmenus%2Freeborg_intro_en.json&name=Maze&url=worlds%2Ftutorial_en%2Fmaze1.json

def wall():
    turn_left()
    while wall_on_right() and front_is_clear():
        move()
    turn_around()
def turn_right():
    turn_left()
    turn_left()
    turn_left()
def turn_around_movement():
    turn_right()
    move()
    turn_right()
    move()
def turn_around():
    if front_is_clear() and right_is_clear():
        turn_around_movement()
    elif wall_in_front() and right_is_clear():
        turn_around_movement()

while not at_goal():
    if wall_on_right() and wall_in_front():
        wall()
    else:
        move()
```