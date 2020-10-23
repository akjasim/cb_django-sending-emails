# Sending Emails in Django(Gmail)

Part of YouTube Video.

English : https://youtu.be/TZL-WFzvDJg

Malayalam : https://youtu.be/3S326ioW3gE

## Usage

1) Enable Two Factor Authentication and setup an App Password in Gmail.
2) Set Email Configurations.

```python
#### settings.py
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_USE_TLS = True
EMAIL_PORT = 587
EMAIL_HOST_USER = 'your_account@gmail.com'
EMAIL_HOST_PASSWORD = 'your app password'
```
3. Make a simple Form.
```python
#### forms.py
from django import forms


class SubscribeForm(forms.Form):
    email = forms.EmailField()
```
4. Make a simple View to handle sending Emails.
```python
#### views.py
from django.contrib import messages
from django.core.mail import send_mail
from django.shortcuts import render, redirect
from django.conf import settings
from subscriptions.forms import SubscribeForm


def subscribe(request):
    form = SubscribeForm()
    if request.method == 'POST':
        form = SubscribeForm(request.POST)
        if form.is_valid():
            subject = 'Code Band'
            message = 'Sending Email through Gmail'
            recipient = form.cleaned_data.get('email')
            send_mail(subject, 
              message, settings.EMAIL_HOST_USER, [recipient], fail_silently=False)
            messages.success(request, 'Success!')
            return redirect('subscribe')
    return render(request, 'subscriptions/home.html', {'form': form})
```

5. Finally, the template
```html
<!-- home.html -->
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Subscribe</title>
</head>
<body>
<main>
    <h1>Subscribe</h1>
    {% if messages %}
        {% for message in messages %}
            <p style="color: red">{{ message }}</p>
        {% endfor %}
    {% endif %}
    <form action="{% url 'subscribe' %}" method="POST">
        {% csrf_token %}
        {{ form }}
        <input type="submit" value="Submit">
    </form>
</main>
</body>
</html>
```
