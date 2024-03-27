
---
title: 第一章：google，github auth2.0 权鉴登录
author: liuyuqi
date: 2024-03-27
category: nlp
layout: post
---

### streamlit-Authenticator身份认证

```
poetry add streamlit-authenticator
```

```python
import streamlit as st
import streamlit_authenticator as stauth

passwords=["123456","1234567"]
hashed_passwords = stauth.Hasher(passwords).generate()

authenticator =stauth.Authenticator(names, usernames, hashed_passwords,cookie_name="authenticator",cookie_expire_days =30)

if stauth.login(authenticator):
    st.write("You are logged in")
else:
    st.write("You are not logged in")
```



