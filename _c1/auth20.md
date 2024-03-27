
---
title: 第一章：google，github auth2.0 权鉴登录
author: liuyuqi
date: 2024-03-27
category: nlp
layout: post
---

### google，github auth2.0 权鉴登录

```
# utils/authenticate.py
import streamlit as st
import requests
import json

from config import FE_HOST, CAS_LOGIN_URL, ALLOWED_USERS


# ------------------------------------
# Initialise Streamlit state variables
# ------------------------------------
def initialise_st_state_vars():
    if "auth_code" not in st.session_state:
        st.session_state["auth_code"] = ""
    if "authenticated" not in st.session_state:
        st.session_state["authenticated"] = False
    if "user_cognito_groups" not in st.session_state:
        st.session_state["user_cognito_groups"] = []


# ----------------------------------
# Get authorization code after login
# ----------------------------------
def get_auth_code():
    auth_query_params = st.experimental_get_query_params()
    try:
        auth_code = dict(auth_query_params)["sid"][0]
    except (KeyError, TypeError):
        auth_code = ""

    return auth_code

# -----------------------------
# Set Streamlit state variables
# -----------------------------
def set_st_state_vars():
    initialise_st_state_vars()
    auth_code = get_auth_code()
    user_info = get_auth_user(sid=auth_code) or {}

    if user_info.get("user"):
        st.session_state["auth_code"] = auth_code
        st.session_state["authenticated"] = True
        st.session_state["user"] = user_info.get("user")
        st.session_state["email"] = user_info.get("mail")
        st.session_state["display"] = user_info.get("display")


# -----------------------------
# Login/ Logout HTML components
# -----------------------------
login_link = f"{CAS_LOGIN_URL}?ref={FE_HOST}"


html_css_login = """
<style>
.button-login {
  background-color: skyblue;
  color: white !important;
  padding: 1em 1.5em;
  text-decoration: none;
  text-transform: uppercase;
}

.button-login:hover {
  background-color: #555;
  text-decoration: none;
}

.button-login:active {
  background-color: black;
}

</style>
"""

html_button_login = (
    html_css_login
    + f"<a href='{login_link}' class='button-login' target='_self'>Log In</a>"
)


def button_login():
    """

    Returns:
        Html of the login button.
    """
    _, col, _ = st.columns(3)
    return col.markdown(f"{html_button_login}", unsafe_allow_html=True)


def button_logout():
    """

    Returns:
        Html of the logout button.
    """
    def logout_click():
        st.session_state["authenticated"] = False
    st.sidebar.button("Logout", on_click=logout_click)
    print(st.session_state)


def get_auth_user(sid, ref=FE_HOST):
    cas_url = f"{CAS_LOGIN_URL}?sid=%s&ref=%s" % (sid, ref)
    if not sid or not ref:
        return

    user_info = requests.get(cas_url ).text
    try:
        user_dict = json.loads(user_info)
    except json.decoder.JSONDecodeError:
        return
    else:
        return user_dict


def is_allowed_user():
    if st.session_state["email"] in ALLOWED_USERS:
        return True
    return False
```

在页面使用 authenticate，所有页面都需要添加权鉴：

```
import streamlit as st

from utils import authenticate
from utils.authenticate import is_allowed_user


st.set_page_config(
    page_title="Welcome",
    page_icon="👋",
)

# 初始化鉴权变量
authenticate.set_st_state_vars()

# Add login/logout buttons，点击可跳转
if not st.session_state.get("authenticated"):
    st.warning("Please login!")
    authenticate.button_login()
else:
    authenticate.button_logout()
    if not is_allowed_user():
        st.error("You do not have access. Please contact the administrator.")
    else:
        # else,页面展示代码位于通过鉴权后
        st.title("欢迎使用XX仪表盘 👋")

        st.markdown(
            """
           
            该项目为streamlit跳转登陆测试项目\n
            **👈 请从侧边栏进入功能页**
            ### 官方参考文档
            - Streamlit: [Streamlit](https://docs.streamlit.io/)
            - 表单登陆: [streamlit-authenticator](https://blog.streamlit.io/streamlit-authenticator-part-1-adding-an-authentication-component-to-your-app/)
        
            ### 实现跳转登陆参考文档
            - [参考文档](https://levelup.gitconnected.com/building-a-multi-page-app-with-streamlit-and-restricting-user-access-to-pages-using-aws-cognito-89a1fb5364a3)
    
        """
        )
```
