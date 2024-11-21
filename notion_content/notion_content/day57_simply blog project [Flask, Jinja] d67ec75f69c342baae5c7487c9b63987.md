# day57_simply blog project [Flask, Jinja]

[20230722221826.mp4](20230722221826.mp4)

```python
from flask import Flask, render_template
import requests as re

app = Flask(__name__)

all_posts = re.get(url='https://api.npoint.io/e95cfc39f09932520a69').json()

@app.route('/')
def home():
    return render_template("index.html", posts=all_posts)

@app.route('/post/<int:post_id>')
def get_post(post_id):
    post_location = post_id - 1
    post_body = all_posts[post_location]['body']
    post_title = all_posts[post_location]['title']
    post_subtitle = all_posts[post_location]['subtitle']
    return render_template('post.html',
                           post_body=post_body,
                           post_title=post_title,
                           post_subtitle=post_subtitle)

if __name__ == "__main__":
    app.run(debug=True)
```