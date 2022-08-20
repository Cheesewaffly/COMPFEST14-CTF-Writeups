
## Overview

I'm assuming you know how a regular RSA and its components work. It starts with two files, ```pubkey.pem```, which can be read with a regular text editor, and ```message.enc``` which contains binary values. 

Reading the pubkey, we are greeted with this:

```
-----BEGIN PUBLIC KEY-----
MIIBHzANBgkqhkiG9w0BAQEFAAOCAQwAMIIBBwKB/wEtcArErCGnnmBPHi777bWX
iRHb969U78LdmQk/AQ58FiVA6LilGoKvxpkNRtGujjvTJDjCz7wCGRe2K9tp1OQ3
c0Trwws0QUXLsySUx6DRyjc6anhA3PmpAsLL193vzhJLzN/t7om+btjbIyVRe6yA
u4VVP2mSixufIi+zwV8T3M7yHZUITykk0fO+t562dyiLS9F72772KyaI3bPKpGdp
vcXtUMJhi0bcH7WOCV07+RBGp5cml2znpHn7/pMbUxeO3wi8eSptEjU1ZwqiiViE
mkIYxr8tiXauNFDnMCMENj6A9ijq9d9ADLt9d+jWcyzrEp9p54PZoksqJO/6VQID
AQAB
-----END PUBLIC KEY-----
```

It takes some experience to realise that this is an RSA certificate, which can be decoded online using a tool such as [dcode](https://www.dcode.fr/rsa-cipher) which outputs:

```
n = 580642391898843192929563856870897799650883152718761762932292482252152591279871421569162037190419036435041797739880389529593674485555792234900969402019055601781662044515999210032698275981631376651117318677368742867687180140048715627160641771118040372573575479330830092989800730105573700557717146251860588802509310534792310748898504394966263819959963273509119791037525504422606634640173277598774814099540555569257179715908642917355365791447508751401889724095964924513196281345665480688029639999472649549163147599540142367575413885729653166517595719991872223011969856259344396899748662101941230745601719730556631637
e = 65537
```

that n looks large, but we're in luck! factoring it in [factordb](http://factordb.com/) just in case returns results, but we have a problem, it has multiple prime factors.
In fact, it has 32 factors. A typical RSA has only two prime factors for its n, p and q, but these are special RSAs, called multiprime RSAs. According to this [stack overflow question](https://crypto.stackexchange.com/questions/74891/decrypting-multi-prime-rsa-with-e-n-and-factors-of-n-given),
the totient or phi value for a multiprime RSA can be calculated by the product of all of its factors decremented by 1. We can also use carmichael's function but its a little bit tricky to code. 
$$\phi = (p_1-1)(p_2-1)(p_3-1)\ldots(p_n-1)$$
Anyway, knowing the totient means we can find d.
$$d = e^{-1}\bmod\bigl((p-1)(q-1)(r-1)(s-1)\bigr)$$

## Python Implementation




