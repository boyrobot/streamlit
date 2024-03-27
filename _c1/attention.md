---
title: 第一章：入门介绍
author: SHENG XU
date: 2022-05-04
category: nlp
layout: post
---

# streamlit 介绍

streamlit 是一个用于构建数据应用的开源 Python 库。它提供了一个简单的 API，可以让你在几行代码中构建数据应用。streamlit 会自动处理数据的加载、交互和可视化，让你可以专注于数据分析和应用的构建。

无需写html, css,js等前端代码。

```
poetry new streamlit
poetry add streamlit

sreamlit hello
```

demo.py

```
import streamlit as st
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

st.markdown("demo")
st.title("demo")

st.header("1、标题")
st.text("文本")
st.write("文本")
st.code("print('hello world')",language="python")

st.header("2、表格")
st.subheader("2.1、表格")


df = pd.DataFrame(
    np.random.randn(10, 5),
    columns=('第 %d 列' % (i+1) for i in range(5))
)

st.table(df)
# 高级图表：排序，高亮
st.dataframe(df.style.highlight_max(axis=0))


#监控面板
col1, col2, col3=st.columns(3)
col1.metric("指标1", 100, 200)
col2.metric("指标2", 200, 100)
col3.metric("指标3", 300, 300)

# 折线图,生成3列随机数
chart_data=pd.DataFrame(
    np.random.randn(20,3),
    columns=['a','b','c']
)
st.line_chart(chart_data)

# 柱状图
st.bar_chart(chart_data)

# 面积图
st.area_chart(chart_data)

# 交互式图表

# 地图
map_data=pd.DataFrame(
    np.random.randn(1000,2)/[50,50]+[37.76,-122.4],
    columns=['lat','lon']
)
st.map(map_data)


# 散点图

def scatter_plot(data, color):
    fig, ax=plt.subplots()
    ax.scatter(data['lat'], data['lon'], color=color)
    ax.set_xlabel('lat')
    ax.set_ylabel('lon')
    ax.set_title('scatter plot')
    return fig

fig = scatter_plot(map_data, 'red')
st.pyplot(fig)
```

运行：
```

if __name__=='__main__'
    streamlit_runner.run(run, 'demo.py')

streamlit run demo.py
```


Streamlit 的一些原生图表组件，虽然做到了傻瓜式，但仅能输入数据、高度和宽度，如果你想更漂亮的图表，就像 matplotlib.pyplot、Altair、vega-lite、Plotly、Bokeh、PyDeck、Graphviz 那样，streamlit 也提供了支持


# 交互，表单

-   button：按钮
-   download_button：文件下载
-   file_uploader：文件上传
-   checkbox：复选框
-   radio：单选框
-   selectbox：下拉单选框
-   multiselect：下拉多选框
-   slider：滑动条
-   select_slider：选择条
-   text_input：文本输入框
-   text_area：文本展示框
-   number_input：数字输入框，支持加减按钮
-   date_input：日期选择框
-   time_input：时间选择框
-   color_picker：颜色选择器

```
import streamlit as st

@st.cache_data
def expensive_computation(a, b):
    # 模拟一些很复杂的计算
    return a * b

input_a = st.number_input("输入a", value=1)
input_b = st.number_input("输入b", value=1)

output = expensive_computation(input_a, input_b)
st.write("计算结果（：", output)


name = st.text_input("请输入您的名字", value="John Doe")
occupation = st.selectbox("请选择您的职业", ("学生", "教师", "工程师"))
age = st.slider("请选择您的年龄", min_value=0, max_value=100, value=30, step=1)

st.write(f"您好，{name}！您是一位{occupation}，今年{age}岁。")


```

## 多媒体组件

-   image：图片 st.image
-   audio：音频 st.audio
-   video：视频 st.video

## 状态进度条

-   progress：进度条，如游戏加载进度
-   spinner：等待提示
-   balloons：页面底部飘气球，表示祝贺
-   error：显示错误信息
-   warning：显示报警信息
-   info：显示常规信息
-   success：显示成功信息
-   exception：显示异常信息（代码错误栈）

## 页面，侧边栏，布局

-   st.sidebar：侧边栏
-   st.empty：空白占位
-   st.markdown：Markdown
-   st.columns：多列布局
-   st.expander：展开收起
-   st.beta_expander：展开收起
-   st.container：容器


```
color = st.sidebar.color_picker('Pick A Color', '#00f900')
```

### 多页面交互，数据传递

```

main.py
----pages
---------home.py
---------about.py

import streamlit as st
session_state=st.session_state
session_state['page]='Home'

sidebar_page=st.sidebar.radio('Page', ['Home', 'About'])

if page =='Home':
    st.title('Home')
elif page =='About':
    st.title('About')

```

## 

st.stop
st.form
st.form_submit_button
st.echo

# 缓存
st.cache

```
read_and_cache_csv=st.cache(pd.read_csv)
df=read_and_cache_csv("data.csv", nrows=1000)
desired_label=st.selectbox("Label", df.columns)
st.write(df[desired_label].value_counts())

```


## streamlit 与其他库的结合

### streamlit + matplotlib


### steamlit + plotly


### streamlit + FastAPI + mysql

streamlit 登录等功能较弱，为此可以结合fastapi框架，实现登录验证等功能。

```python
import streamlit as st
import requests

```


### 登录验证 + firebase

每次操作互动组件时（比如输入新问题），都会触发重新运行（rerun）。rerun（重新运行）是streamlit的一个特色，指的是将应用代码从头到尾重新运行一遍。

初始化firebase：
```python
#配置firebaseConfig
firebaseConfig = {...}
#然后进行初始化
firebase = pyrebase.initialize_app(firebaseConfig)
auth = firebase.auth()
# Database
db = firebase.database()
storage = firebase.storage()

```

登录按钮：
```
if st.session_state['logged_in']:
    st.sidebar.write(f"Hello, {st.session_state['username']}!")
    if st.sidebar.button('Logout'):
        st.session_state['logged_in'] = False
        st.session_state['username'] = ''
        st.session_state['history'] = []
        st.session_state['generated'] = [] 
        st.session_state['past'] = []  
        st.experimental_rerun()
else:
    auth_option = st.sidebar.radio("Choose an option", ['Login', 'Register'])
    email = st.sidebar.text_input("Email")
    password = st.sidebar.text_input("Password", type='password')

    if auth_option == 'Login':
        if st.sidebar.button('Login'):
            try:
                user = auth.sign_in_with_email_and_password(email, password)
                st.session_state['user'] = user
                st.session_state['logged_in'] = True
                st.session_state['username'] = email
                st.session_state['email'] = email
                st.success("Logged in successfully")
            except:
                error_message = "Invalid email or password"
                st.error(error_message)  # Display the error message
            st.experimental_rerun()

    elif auth_option == 'Register':
        if st.sidebar.button('Register'):
            try:
                user = auth.create_user_with_email_and_password(email, password)
                st.session_state['logged_in'] = True
                st.session_state['username'] = email
                st.success("Registered and logged in successfully")
                #st.experimental_rerun()
            except:
                error_message = "Failed to create account"
                st.error(error_message)  # Display the error message
```


```
if 'total_tokens_used' not in st.session_state:
    st.session_state.total_tokens_used = 0


with get_openai_callback() as cb:
                output = conversational_chat(user_input)
                token_cost = cb.total_tokens
                st.session_state.total_tokens_used += token_cost
                tokens_remaining -= token_cost
```


### streamlit + Flask

## deploy

默认部署到 https://share.streamlit.io/ ，也可以部署到自己的服务器上。

Resources per app 1 GB
Private apps 1 app
Public apps Unlimited


docker 部署， heroku.com 














