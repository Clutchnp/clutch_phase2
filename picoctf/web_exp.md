# 1. Web Gaunlet

> Can you beat the filters?
Additional details will be available after launching your challenge instance.
## Solution:

- This challenge was easy
- We had been given the filter.php and we coud see which couuld be blocked 
I just used `--` and `/*` to get through all the filters


## Flag:

```
 picoCTF{y0u_m4d3_1t_79a0ddc6}
```

## Concepts learnt:

- about sql injections (didn't know about /* and ;)


## Resources:

- https://ctf101.org/web-exploitation/sql-injection/what-is-sql-injection/

***

<!-- # 1. SSTI -->
<!---->
<!-- > Put in the challenge's description here -->
<!---->
<!-- ## Solution: -->
<!---->
<!-- - Include as many steps as you can with your thought process -->
<!-- - You **must** include images such as screenshots wherever relevant. -->
<!---->
<!-- ``` -->
<!-- put codes & terminal outputs here using triple backticks -->
<!---->
<!-- you may also use ```python for python codes for example -->
<!-- ``` -->
<!---->
<!-- ## Flag: -->
<!---->
<!-- ``` -->
<!-- picoCTF{} -->
<!-- ``` -->
<!---->
<!-- ## Concepts learnt: -->
<!---->
<!-- - Include the new topics you've come across and explain them in brief -->
<!-- -  -->
<!---->
<!-- ## Notes: -->
<!---->
<!-- - Include any alternate tangents you went on while solving the challenge, including mistakes & other solutions you found. -->
<!-- -  -->
<!---->
<!-- ## Resources: -->
<!---->
<!-- - Include the resources you've referred to with links. [example hyperlink](https://google.com) -->
<!---->
<!---->
<!-- *** -->
<!---->



# 1. Cookies

> Who doesn't love cookies? Try to figure out the best one. http://mercury.picoctf.net:6418/
## Solution:

- Since the challenge name was cookies, I went directly to Dev tools -> Application->Cookies
- There was a cookie named name which was set to -1
- Also when I entered the defualt value I would notice that it would go to /check and the name cookie will become 0
- Also I noticed if I entered sth wrong it would remain at -1 
- So what I probably need to do is iterate through all the numbers which are available (guess 50)
```
import requests
for i in range(50):
    a = requests.get("http://mercury.picoctf.net:54219/check", cookies={"name": f"{i}"}).text
    print(a)

```
then I run the scirpt like this `python script.py | grep pico` which gave us the flag

## Flag:

```
picoCTF{3v3ry1_l0v3s_c00k135_96cdadfd}
```

