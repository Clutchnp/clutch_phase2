
# 1. rsa_oracle

> Can you abuse the oracle?
An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
Additional details will be available after launching your challenge instance.


## Solution:

- So I really didnt know anything about how rsa worked before the challenge, so I went on the web for the hunt and I found the formula $C^e mod n$ for decryption just as we look in the formula we realise something, if we multiply 2 ciphers we can get teh multiplication of the hex values of the two message strings from which we know one of the messages so we can divide it to get the ans
$$
(C_1*C_2)^e/modn = m_1*m_2
$$
$$
m_1 = (m_1*m_2)/(m_1)
$$
we can create a python script for the following like this 


```python
f = open("./password.enc")
a = f.read()
b = "2109099638245126168581441821909978352911824972407533480588333616018328593159570924774816172470503885858523106540866526427937237578866245179399409941010254" # this is the encrypted message for "help"
c = int(a)*int(b) # we multiply these numbers 
```

we decrypt the number we get and convert it to hex  where we get "28ef40743098ba14f0" we also have the hex for "help" which was "68656c70" we can divide them using an online tool which in my case was [calculator.net](https://www.calculator.net/hex-calculator.html) from where we get the new hex as "6461303939" then we convert this to string and get "da099" which we can use to decrypt the secret.enc using openssl as given in the question hint from which we get the correct flag

## Flag:

```
picoCTF{su((3ss_(r@ck1ng_r3@_da099d93}%                                        
```

## Concepts learnt:

- openssl basics
- working of rsa 

## Resources:

https://www.geeksforgeeks.org/computer-networks/rsa-algorithm-cryptography/


***


# 2. Custom Encryption

> Can you get sense of this code file and write the function that will decode the given encrypted file content.
Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

## Solution:

So looking at the below encryption code I saw
```
from random import randint
import sys


def generator(g, x, p):
    return pow(g, x) % p


def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher


def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True


def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text


def test(plain_text, text_key):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    a = randint(p-10, p)
    b = randint(g-10, g)
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    semi_cipher = dynamic_xor_encrypt(plain_text, text_key)
    cipher = encrypt(semi_cipher, shared_key)
    print(f'cipher is: {cipher}')


if __name__ == "__main__":
    message = sys.argv[1]
    test(message, "trudeau")
```

basically there are two steps in the encryption of the given program once in the dynamic_xor_encrypt() function and once in the encrypt function 
we are doing a partial encrypt  in the dynamic_xor_encrypt() and passing that to encrypt() for fully encrypting,

What we need to do is just reverse the logic as we are given the cipher array and values of a and b to use along with text_key (i.e "trudeau"), btw we also need to recalculate the keys using the generator function.

Here's the code I wrote to reverse this :
```
def generator(g, x, p):
    return pow(g, x) % p

def dynamic_xor_decrypt(cipher_text, text_key):
    plain = ""
    key_length = len(text_key)
    for i, char in enumerate(cipher_text):
        key_char = text_key[i % key_length]
        decrypted_char = chr(ord(char) ^ ord(key_char))
        plain += decrypted_char
    return plain[::-1]

def decrypt(cipher_text, key):
    plaintext = ""
    for element in cipher_text:
        plaintext+=chr(int(element/(key*311)))
    return plaintext

if __name__ == "__main__":
    p = 97
    g = 31
    a = 89
    b = 27
    cipher = [33588, 276168, 261240, 302292, 343344, 328416, 242580, 85836, 82104, 156744, 0, 309756, 78372, 18660, 253776, 0, 82104, 320952, 3732, 231384, 89568, 100764, 22392, 22392, 63444, 22392, 97032, 190332, 119424, 182868, 97032, 26124, 44784, 63444] 
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
    partial =  decrypt(cipher, shared_key)
    complete = dynamic_xor_decrypt(partial,"trudeau")
    print(complete)

```
## Flag:

```
picoCTF{custom_d2cr0pt6d_dc499538}
```

## Concepts learnt:

- about properties of xor 
## Notes:

- I did reverse of the cipher text in the xor decryption instead of the result of it, also didnt convert to float when dividing by 311

***

# 3. 

> Put in the challenge's description here

## Solution:

- Include as many steps as you can with your thought process
- You **must** include images such as screenshots wherever relevant.

```
put codes & terminal outputs here using triple backticks

you may also use ```python for python codes for example
```

## Flag:

```
picoCTF{}
```

## Concepts learnt:

- Include the new topics you've come across and explain them in brief
- 

## Notes:

- Include any alternate tangents you went on while solving the challenge, including mistakes & other solutions you found.
- 

## Resources:

- Include the resources you've referred to with links. [example hyperlink](https://google.com)


***

