
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

# 3. miniRSA

> Let's decrypt this: ciphertext? Something seems a bit small.
## Solution:

- After doing the 1st challenge, this one seems easy because of the knowledge gained there, since we n is larger than the cipher itself, there is no repition etc. which means we can just take the cube root of the cipher to get the message no?
- This was Infact not easy:- some things I assumed wrong we have to use the mod too(as if there m^e > n then it will cipher will be m^e -kn ) for getting the ans, and what I am doing will always lead to unprecise result 

when I run this 
```py

N = 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673

a = 2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933 
x = int(pow(a,1/3))
b = bytes.fromhex(str(hex(x))[2:])
print(b)
```
I get output as  `b'picoCS\x08\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'` which means m^e < N and its being truncated becuse of loss of precision 

what we can do here is run a binary search from the value we got from doing int(pow(a,1/3)) -> 2*int(pow(a,1/3)) to get the value, since there will be no float value allowed and by using the start and end values like that we get fast runtime and also, we can do that because int(pow(a,1/3)) is just truncated actual a so for eg if the real value is 2333456456 we are getting 233300000 so that's good
```py
N = 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673

a = 2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933 
hi = int(pow(a,1/3))
bye = hi*2
mid = 0

while (hi+1<bye ):
    mid = (bye+hi)//2
    if mid**3 == a:
        break
    if mid**3 < a:
        hi = mid 
    elif (mid**3) > a :
        bye = mid
    else:
        bye = mid
b = bytes.fromhex(str(hex(mid))[2:])
print(b)
```


## Flag:

```
picoCTF{n33d_a_lArg3r_e_ccaa7776}
```

## Concepts learnt:

- RSA breaking techniques

## Notes:
```
a = 2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933 
b = bytes.fromhex(str(hex(int(pow(a,(1/3)))))[2:])
print(b.decode("utf-8"))
```
Did this first, realised am losing precision while taking the cuberoot becuase of the hint 
***

