# Hashing - Crypto 101

An introduction to Hashing,

## Task 1 Key Terms 

Before we start, you need to get some jargon out of the way.

Encoding - This is NOT a form of encryption, just a form of data representation like base64 or hexadecimal. Immediately reversible.

Hash - A hash is the output of a hash function. Hashing can also be used as a verb, "to hash", meaning to produce the hash value of some data.

Brute force - Attacking cryptography by trying every different password or every different key

Cryptanalysis - Attacking cryptography by finding a weakness in the underlying maths
This room will likely involve some research. Get good at using search engines, it's crucial to infosec.



#### Read the words, and understand the meanings!
Is base64 encryption or encoding?

` ans: encoding `

<br><br><br>

# Task 2 What is a hash function? 

What's a hash function?

Hash functions are quite different from encryption. There is no key, and it’s meant to be impossible (or very very difficult) to go from the output back to the input.

A hash function takes some input data of any size, and creates a summary or "digest" of that data. The output is a fixed size. It’s hard to predict what the output will be for any input and vice versa. Good hashing algorithms will be (relatively) fast to compute, and slow to reverse (Go from output and determine input). Any small change in the input data (even a single bit) should cause a large change in the output.

The output of a hash function is normally raw bytes, which are then encoded. Common encodings for this are base 64 or hexadecimal. Decoding these won’t give you anything useful.

Why should I care?

Hashing is used very often in cyber security. When you logged into your computer, that also used hashing to verify your password. You interact indirectly with hashing more than you would think, mostly in the context of passwords.

What's a hash collision?

A hash collision is when 2 different inputs give the same output. Hash functions are designed to avoid this as best as they can, especially being able to engineer (create intentionally) a collision. Due to the pigeonhole effect, collisions are not avoidable. The pigeonhole effect is basically, there are a set number of different output values for the hash function, but you can give it any size input. As there are more inputs than outputs, some of the inputs must give the same output. If you have 128 pigeons and 96 pigeonholes, some of the pigeons are going to have to share.

MD5 and SHA1 have been attacked, and made technically insecure due to engineering hash collisions. However, no attack has yet given a collision in both algorithms at the same time so if you use the MD5 hash AND the SHA1 hash to compare, you will see they’re different. The MD5 collision example is available from https://www.mscs.dal.ca/~selinger/md5collision/ and details of the SHA1 Collision are available from https://shattered.io/. Due to these, you shouldn't trust either algorithm for hashing passwords or data.

#### What is the output size in bytes of the MD5 hash function?

 
 ` ans: 16`
 
 #### Can you avoid hash collisions? (Yea/Nay)
 
 ` ans: Nay  `
 
 #### If you have an 8 bit hash output, how many possible hashes are there?

` ans: 256  `


<br><br><br>

# Task 3 Uses for hashing 

What can we do with hashing?

Hashing is used for 2 main purposes in Cyber Security. To verify integrity of data , or for verifying passwords.


Hashing for password verification

Most webapps need to verify a user's password at some point. Storing these passwords in plain text would be bad. You've probably seen news stories about companies that have had their database leaked. Knowing some people, they use the same password for everything including their banking, so leaking these would be really really bad.

Quite a few data breaches have leaked plaintext passwords. You’re probably familiar with “rockyou.txt” on Kali as a password wordlist. This came from a company that made widgets for MySpace. They stored their passwords in plaintext and the company had a data breach. The txt file contains over 14 million passwords (although some are *unlikely* to have been user passwords

Adobe had a notable data breach that was slightly different. The passwords were encrypted, rather than hashed and the encryption that was used was not secure. This meant that the plaintext could be relatively quickly retrieved. If you want to read more about this breach, this post from Sophos is excellent: https://nakedsecurity.sophos.com/2013/11/04/anatomy-of-a-password-disaster-adobes-giant-sized-cryptographic-blunder/

Linkedin also had a data breach. Linkedin used SHA1 for password verification, which is quite quick to compute using GPUs. 

You can't encrypt the passwords, as the key has to be stored somewhere. If someone gets the key, they can just decrypt the passwords.

This is where hashing comes in. What if, instead of storing the password, you just stored the hash of the password? This means you never have to store the user's password, and if your database was leaked then an attacker would have to crack each password to find out what the password was. That sounds fairly useful.

There's just one problem with this. What if two users have the same password? As a hash function will always turn the same input into the same output, you will store the same password hash for each user. That means if someone cracks that hash, they get into more than one account. It also means that someone can create a "Rainbow table" to break the hashes.

A rainbow table is a lookup table of hashes to plaintexts, so you can quickly find out what password a user had just from the hash. A rainbow table trades time taken to crack a hash for hard disk space, but they do take time to create.
Here's a quick example so you can try and understand what they're like.


Hash 	Password
02c75fb22c75b23dc963c7eb91a062cc 	zxcvbnm
b0baee9d279d34fa1dfd71aadb908c3f 	11111
c44a471bd78cc6c2fea32b9fe028d30a 	asdfghjkl
d0199f51d2728db6011945145a1b607a 	basketball
dcddb75469b4b4875094e14561e573d8 	000000
e10adc3949ba59abbe56e057f20f883e 	123456
e19d5cd5af0378da05f63f891c7467af 	abcd1234
e99a18c428cb38d5f260853678922e03 	abc123
fcea920f7412b5da7be0cf42b8c93759 	1234567

Websites like Crackstation internally use HUGE rainbow tables to provide fast password cracking for hashes without salts. Doing a lookup in a sorted list of hashes is really quite fast, much much faster than trying to crack the hash.

Protecting against rainbow tables

To protect against rainbow tables, we add a salt to the passwords. The salt is randomly generated and stored in the database, unique to each user. In theory, you could use the same salt for all users but that means that duplicate passwords would still have the same hash, and a rainbow table could still be created specific passwords with that salt.

The salt is added to either the start or the end of the password before it’s hashed, and this means that every user will have a different password hash even if they have the same password. Hash functions like bcrypt and sha512crypt handle this automatically. Salts don’t need to be kept private


#### Crack the hash "d0199f51d2728db6011945145a1b607a" using the rainbow table manually.

![basketball](https://user-images.githubusercontent.com/41240719/152651563-113c1ece-0c33-4abb-a36b-85f1e6316835.jpg)


` ans: basketball `

####  Crack the hash "5b31f93c09ad1d065c0491b764d04933" using online tools

I used https://md5decrypt.net/en/#answer

![md5](https://user-images.githubusercontent.com/41240719/152651895-8eac9304-d2e0-4631-bdf2-309586bed43f.jpg)


` ans: tryhackme`

#### Should you encrypt passwords? Yea/Nay

` ans: Nay`

