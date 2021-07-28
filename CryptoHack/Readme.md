# CryptoHack
Flag format - crypto{y0ur_f1rst_fl4g}

https://cryptohack.org/user/Tacola/

## Introduction - tips
Several of the challenges are dynamic and require you to talk to our challenge servers over the network. This allows you to perform man-in-the-middle attacks on people trying to communicate, or directly attack a vulnerable service. To keep things consistent, our interactive servers always send and receive JSON objects.
Python makes such network communication easy with the ```telnetlib``` module. Conveniently, it's part of Python's standard library, so let's use it for now.
Connect at nc socket.cryptohack.org 11112

Useful scripts in Intrduction folder.
