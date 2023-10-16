# Encrypt and Decrypt a string in C#
This blog posts presents an easy to use C# class to encrypt and decrypt strings in .NET.

Although the [System.Security.Cryptography](http://msdn.microsoft.com/en-us/library/system.security.cryptography.aspx) namespace in the .NET Framework provides a wealth of classes to encrypt and decrypt values, it seems that MSDN falls short of providing a good and simple example on how to use these classes for the most common request: encrypt a string.

The class presented in this blog allows you to encrypt and decrypt a string with the following lines of code: 

```code
string encryptionPassword = "supersecret";
string textToEncrypt = "the quick brown fox jumps over the lazy dog";
string encrypted = Crypto.Encrypt(textToEncrypt, encryptionPassword);
string original = Crypto.Decrypt(encrypted, encryptionPassword);
```

Disclaimer: The code presented in this blog post is just one of the many ways to encrypt and decrypt strings in .NET. If you need to encrypt incredible confidential information (like the nuke launch codes) you should check specific books on Cryptography. Having said that, you probably arrived to this post because you are in search of a simple example on encryption with .NET. If that's the case then this post is for you.

Below is the code of a simple C# class to encrypt and decrypt strings. All in all it's 60 lines of code including curly braces. You can just copy and paste this code to your project and voilà you are ready to encrypt and decrypt strings. If you are bit curious the remainder of this post explains how the code works. 

```code
// Code based on the book "C# 3.0 in a nutshell by Joseph Albahari" (pages 630-632)
// and from this StackOverflow post by somebody called Brett
// http://stackoverflow.com/questions/202011/encrypt-decrypt-string-in-net/2791259#2791259
static public class Crypto
{
  private static readonly byte[] salt = Encoding.ASCII.GetBytes("Ent3r your oWn S@lt v@lu# h#r3");
  
  public static string Encrypt(string textToEncrypt, string encryptionPassword)
  {
    var algorithm = GetAlgorithm(encryptionPassword);

    byte[] encryptedBytes;
    using (ICryptoTransform encryptor = algorithm.CreateEncryptor(algorithm.Key, algorithm.IV))
    {
      byte[] bytesToEncrypt = Encoding.UTF8.GetBytes(textToEncrypt);
      encryptedBytes = InMemoryCrypt(bytesToEncrypt, encryptor);
    }
    return Convert.ToBase64String(encryptedBytes);
  }

  public static string Decrypt(string encryptedText, string encryptionPassword)
  {
    var algorithm = GetAlgorithm(encryptionPassword);

    byte[] descryptedBytes;
    using (ICryptoTransform decryptor = algorithm.CreateDecryptor(algorithm.Key, algorithm.IV))
    {
      byte[] encryptedBytes = Convert.FromBase64String(encryptedText);
      descryptedBytes = InMemoryCrypt(encryptedBytes, decryptor);
    }
    return Encoding.UTF8.GetString(descryptedBytes);
    }

    // Performs an in-memory encrypt/decrypt transformation on a byte array.
    private static byte[] InMemoryCrypt(byte[] data, ICryptoTransform transform)
    {
      MemoryStream memory = new MemoryStream();
      using (Stream stream = new CryptoStream(memory, transform, CryptoStreamMode.Write))
      {
        stream.Write(data, 0, data.Length);
      }
      return memory.ToArray();
    }

    // Defines a RijndaelManaged algorithm and sets its key and Initialization Vector (IV) 
    // values based on the encryptionPassword received.
    private static RijndaelManaged GetAlgorithm(string encryptionPassword)
    {
      // Create an encryption key from the encryptionPassword and salt.
      var key = new Rfc2898DeriveBytes(encryptionPassword, salt);

      // Declare that we are going to use the Rijndael algorithm with the key that we've just got.
      var algorithm = new RijndaelManaged();

      int bytesForKey = algorithm.KeySize / 8;
      int bytesForIV = algorithm.BlockSize / 8;
      algorithm.Key = key.GetBytes(bytesForKey);
      algorithm.IV = key.GetBytes(bytesForIV);
      return algorithm;
    }
}
```


## The Crypto class

The Crypto class presented in this post has four methods. Methods `Crypt` and `Decrypt` are public and are the ones that your code calls to encrypt and decrypt values. The other two methods (`InMemoryCrypt` and `GetAlgorithm`) are used internally by the class.


## Encrypt()

The process of encrypting values in .NET requires a few steps that are not evident to first comers. In a nutshell this process involves setting up the algorithm that will be used for encryption and pushing the data to encrypt through it. In our case since we want to encrypt strings and the .NET cryptography classes don't provide overloads for strings out of the box we'll need to take a few extra steps to account for this. The `Encrypt()` method in our `Crypto` class takes care of these steps. Let's review this method in detail.

```code
  public static string Encrypt(string textToEncrypt, string encryptionPassword)
  {
    var algorithm = GetAlgorithm(encryptionPassword);

    byte[] encryptedBytes;
    using (ICryptoTransform encryptor = algorithm.CreateEncryptor(algorithm.Key, algorithm.IV))
    {
      byte[] bytesToEncrypt = Encoding.UTF8.GetBytes(textToEncrypt);
      encryptedBytes = InMemoryCrypt(bytesToEncrypt, encryptor);
    }
    return Convert.ToBase64String(encryptedBytes);
  }
```

The first thing we do in the `Encrypt()` method is **define what algorithm** we want to use for encryption. In our case the we will use the *Rijndael* algorithm (more on this later.) This algorithm class has the ability to provide either an encryptor or a decryptor. For the `Encrypt()` method we obviously need an encryptor.

The second thing we do is **convert to bytes the string** that we received with the value to encrypt. This is an important step as the .NET encryption classes work with bytes, not strings. 

As you probably already know [strings in .NET are encoded in Unicode](http://csharpindepth.com/Articles/General/Strings.aspx) and as such they can hold characters from pretty much any language in the world. Some of these characters need more than one byte to be stored and therefore the number of *bytes* that you get on line 15 might be larger than the number of *characters* in your string.

If you only deal with ASCII values you probably don't give this much thought as most of the characters in your strings translate to a single byte (for example character "A" can be represented by one byte with value of 65 - its ASCII value.) However, strings in .NET can hold characters that need multiple bytes to be stored. For example, the Japanese character &#12453; maps to three bytes with values 227, 130, and 166.

So now that we have defined an algorithm to use (along with its corresponding encryptor) and converted to bytes the information to encrypt we are ready to pass this information to the `InMemoryCrypt` method to finally **perform the encryption**. We'll talk about the details of the `InMemoryCrypt` method in the section below but for now let's just say that it encrypts the bytes that we pass to it and gives us back the corresponding encrypted bytes.

Finally, we **convert the encrypted bytes back to a .NET string** since that's what we want as an end result.

As you can see, although the Encrypt method is only a few lines long there is a lot going on. This is not exactly what I would call a self evident process and it is no wonder this is one of the most commonly asked questions in .NET message boards!


## GetAlgorithm()

The `GetAlgorithm()` method declares what algorithm will be used for encryption and sets up the corresponding encryption keys. The .NET framework supports several algorithms and each of them has different strengths and weaknesses. On this particular example we are using the [Rijndael algorithm](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard). You can find a lot of information about this algorithm on the web but for the purpose of this blog post let's just say that it uses a pair of values (known as key and initialization vector) to encrypt a set of bytes. In our case both, the key and the initialization vector (IV), will be calculated from an `encryptionPassword` that we provide.

Let's look in detail at the code of this method.

```code
    // Defines a RijndaelManaged algorithm and sets its key and Initialization Vector (IV) 
    // values based on the encryptionPassword received.
    private static RijndaelManaged GetAlgorithm(string encryptionPassword)
    {
      // Create an encryption key from the encryptionPassword and salt.
      var key = new Rfc2898DeriveBytes(encryptionPassword, salt);

      // Declare that we are going to use the Rijndael algorithm with the key that we've just got.
      var algorithm = new RijndaelManaged();

      int bytesForKey = algorithm.KeySize / 8;
      int bytesForIV = algorithm.BlockSize / 8;
      algorithm.Key = key.GetBytes(bytesForKey);
      algorithm.IV = key.GetBytes(bytesForIV);
      return algorithm;
    }
```

First we call `Rfc2898DeriveBytes` to get a **generator of pseudo-random bytes** based on our `encryptionPassword` and `salt` values.

We then **declare an instance of the Rijndael algorithm** and **set the key and initialization vector (IV) values** needed by this algorithm. Notice how we set these two values with as many bytes required depending on the `KeySize` and `BlockSize` of the algorithm.

Technically you could skip the call to `Rfc2898DeriveBytes` and manually set the values used for key and initialization vector. However if you do this you need to make sure that you have enough bytes to fill the key and initialization vector. This can be a problem if the `encryptionPassword` value was too short (say it's only 10 bytes long but the algorithm expects 32 bytes values.) The `Rfc2898DeriveBytes` takes care of this problem as it can generate enough bytes even if the `encryptionPassword` and `salt` are too short.


## InMemoryCrypt()

The classes on the .NET Framework use streams to perform the encryption process. This allows the encryption of very large blocks of text while consuming very little memory. This is a nice feature of the framework as it allows you to encrypt entire documents without having to load the entire document in memory in one chunk. The drawback is that you must go through these streams even when you encrypt small sets of data like in our example.

The `InMemoryCrypt()` method hides the process of setting up the streams to read through the bytes to encrypt and dump the resulting bytes into another stream.


## Encryption password and Salt

The 1encryptionPassword1 value that the `Encrypt()` and `Decrypt()` methods receive should be a value that only you know. This 1encryptionPassword1 alone is enough to decrypt a value encrypted with this code.

On the other hand the `salt` value defined at the class level and used in `GetAlgorithm()` on does not need to be kept secret as it is useless without the encryption password. It is important however that you use the exact same `salt` value to decrypt as you used to encrypt which is why I declared it as a static member of the class rather than make it a parameter to `Encrypt` and `Decrypt`. Feel free to update this value or make it configurable on your own implementation.


## A few final words

The `Decrypt()` method is pretty much the reverse of the `Encrypt()` method so I won't elaborate on it.

There are many things to consider when choosing an encryption algorithm:

* Some algorithms are one-way only meaning that they can encrypt a value but you cannot decrypt it. These algorithms are known as *hashing algorithms* and are perfect for storing password in a database.
* Other algorithms do allow for encryption and decryption. Within these algorithms some of them are symmetrical (like Rijndael) meaning they use the same key for encryption and decryption whereas others are asymmetrical and use different keys known as private and public keys.

Keep this in mind when deciding what algorithm to use in your application.

### References

As indicated at the top of the `Crypto` class most of the code for this blog post was taken from the excellent book [C# 3.0 in a Nutshell](http://www.amazon.com/3-0-Nutshell-Desktop-Reference-OReilly/dp/0596527578) by Joseph Albahari and Ben Albahari (pages 630-632) and from this [post](http://stackoverflow.com/questions/202011/encrypt-decrypt-string-in-net/2791259#2791259) in StackOverflow by somebody called Brett.

There is a good summary of the different encryption options in the book "C# 3.0 in a Nutshell" (see pages 627-638)