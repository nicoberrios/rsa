# 11.3. RSA Encryption

As we learnt in the previous section, RSA security relies on the underlying difficulty of factoring large numbers, which is currently believed to be computationally hard – as in [US $100.000 hard](https://en.wikipedia.org/wiki/RSA_Factoring_Challenge) for 1024-bit keys.

Making use of this difficulty, Bob generates its first public key component $n$, by multiplying together two very large prime numbers $p$ and $q$.

$$n = p * q$$

The second element of his public key can be obtained by defining the auxiliary value $t = (p-1)(q-1)$ and choosing a nunmber $e$ under the following ruleset:

1. $2 < e < t$
2. $e$ is coprime to $t$

After that, Bob has successfully generated a key $PK = (n, e)$ that can be publicly broadcasted! Note that these are the only public values in RSA, and that our initial primes $p$ and $q$ must remain a secret.

Finally, the private key $SK = (d)$ can be obtained by the Extended Euclid algorithm, as follows.

$$d = e^{-1} mod \space t$$

Sanity check: The RSA key pair we just created is defined as $PK = (n, e), \space SK = (d)$. After our keys are generated, we can make use of this values to define an encryption scheme for a message $M$.

$$Enc(e, n, M) = M^e mod \space n = C$$

And a decryption algorithm that reverts the ciphertext $C$ back to our original message $M$.

$$Dec(d, C) = C^d mod \space n = (M^e)^d mod \space n = M$$

Although the underlying correctness proof of RSA is out-of-scope for this class, feel free to refer to [CS70 notes]() for a detailed explanation.

Now, going back to the context of providing confidentiality, try taking a look at the equations we just defined and see if you can spot any issues. Looking closer at our encryption scheme, we can see that no randomness is introduced at any point. As such, sending the same message multiple times would produce the exact same response, yielding this algorithm IND-CPA insecure. 

Additionally, some implementations of RSA encryption will also be vulnerable to side-channel attacks, as timing the decryption process will leak information regarding the message and private key. Note that changing public keys will not provide a fix for this problem, so an external source of randomness is required.

This is achieved by OAEP (Optimal Asymmetric Encryption Padding), which despite its name, it is analogous to the Initialization Vectors (IVs) in block ciphers, aiming to create a random looking input instead of extending a message, unlike what regular padding does. The process is boiled down to generating a random key and using it to scramble the message, before applying our encryption scheme to both the output message and the key itself.  
