---
title: WTForms Install email_validator for email validation support
date: 2023-03-16 02:47:00 -0500
categories: [Walkthrough, Python]
tags: [python, WTForms, email_validator]
published: true
---


## Problem

Trying to use this code to validate a form but getting exception:

- `Exception: Install 'email_validator' for email validation support.`

```python
class LoginForm(FlaskForm):
    email = StringField(label='Email', validators=[Email(message="valid email pls")])
    password = PasswordField(label='Password', validators=[DataRequired(message="password")])
    submit = SubmitField(label='Log In')
```

According to the [documentation](https://wtforms.readthedocs.io/en/2.3.x/validators/#built-in-validators) on built in validators in order to use the function we can install it with `pip install wtforms[email]`

But already have the requirements.

```cmd
PS E:\Documents\GitHub\python\python\Python Projects\061 - WTForms and Authentication> pip install wtforms[email]
Requirement already satisfied: wtforms[email] in e:\appdata\local\programs\python\python310\lib\site-packages (3.0.1)
Requirement already satisfied: MarkupSafe in e:\appdata\local\programs\python\python310\lib\site-packages (from wtforms[email]) (2.1.2)
Requirement already satisfied: email-validator in e:\appdata\local\programs\python\python310\lib\site-packages (from wtforms[email]) (1.3.1)
Requirement already satisfied: dnspython>=1.15.0 in e:\appdata\local\programs\python\python310\lib\site-packages (from email-validator->wtforms[email]) (2.3.0)
Requirement already satisfied: idna>=2.0.0 in e:\appdata\local\programs\python\python310\lib\site-packages (from email-validator->wtforms[email]) (3.4)
```

And requirements are already satisfied for email_validator as well:

```cmd
PS E:\Documents\GitHub\python\python\Python Projects\061 - WTForms and Authentication> pip install email_validator
Requirement already satisfied: email_validator in e:\appdata\local\programs\python\python310\lib\site-packages (1.3.1)
Requirement already satisfied: dnspython>=1.15.0 in e:\appdata\local\programs\python\python310\lib\site-packages (from email_validator) (2.3.0)
Requirement already satisfied: idna>=2.0.0 in e:\appdata\local\programs\python\python310\lib\site-packages (from email_validator) (3.4)
```

## Solution - requirements.txt

Adding `email-validator == 1.0.5` to `requirements.txt` fixes the issue for me.

In order to keep errors like this when happening make sure to list dependencies inside of the requirements.txt when using a requirements.txt
