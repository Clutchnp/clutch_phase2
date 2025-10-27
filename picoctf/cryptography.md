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


