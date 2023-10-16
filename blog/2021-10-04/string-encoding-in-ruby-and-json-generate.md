# String encoding in Ruby and JSON.generate()
Last week at work we ran into an interesting string encoding issue. The gist of the problem is that we had a string that was valid in Ruby but Ruby threw an exception when we used it in a call to `JSON.generate()`. Below is a simplified version of this scenario:

```
>> text = "\x81ru"
=> "\x81ru"

>> hash = { "language" => text }
=> {"language"=>"\x81ru"}

>> JSON.generate(hash)
=> [...] JSON::GeneratorError (source sequence is illegal/malformed utf-8)

>> puts text
?ru
=> nil
```

Notice how Ruby accepts the string and that I am able to use it in a hash, but then `JSON.generate()` throws an exception when it tries to use it. 

If I print the string to the console with `puts text` the output is `?ru` which is already suspicious, notice the "?" at the beginning. Something is amiss with this string but what is it? And why can I pass it around but then `JSON.generate()` throws an error?

## Digging into the issue
The exception reported makes it clear that this is an encoding issue, the text itself says "malformed utf-8". In looking for information about string encoding in Ruby I ran into the post [Guide to String Encoding in Ruby](https://tenderlovemaking.com/2020/01/13/guide-to-string-encoding-in-ruby.html) by Aaron Patterson that helped me get a better understanding of the problem. 

One of the things that Patterson makes clear is that the *encoding* of a string is a separate property of the string. The string itself is an array of bytes and the encoding is a separate object that is related to the string. This is why it's possible to have strings (i.e. sequence of bytes) assigned to the wrong encoding. 

Ruby provides some helpful methods to dig further into a string and its encoding. The method `valid_encoding?` for example checks the string against its current encoding and reports whether it's valid or not. Below is an example of how our problematic string is reported:

```
>> text = "\x81ru"
=> "\x81ru"

>> text.encoding
=> #<Encoding:UTF-8>

>> text.valid_encoding?
=> false
```

As we can see the string is assigned UTF-8 encoding by default but this particular string is **not** valid UTF-8.

The fact that the bytes and the encoding are separate explains why we can have a string with an incorrect encoding and why we were able to pass it around and use it (like inside a hash). The post by Patterson provides more information on why this is a good design and reviews other methods that we can use to handle encodings in Ruby strings.

## JSON.generate() error
So now we know why we were able to store our problematic string and pass it around. The last piece of the puzzle is to find out why does `JSON.generate()` throw an exception when it tries to use it. My guess is that `JSON.generate()` is somehow making sure the string is valid for the UTF-8 encoding and since it isn't it throws an exception. 

I can force Ruby to throw a similar (but not identical) exception with the following code:

```
>> text = "\x81ru"
=> "\x81ru"

>> text.encode("ASCII", "UTF-8")
[...] Encoding::InvalidByteSequenceError ("\x81" on UTF-8)
```

I found the text of the original exception "source sequence is illegal/malformed utf-8" inside the Ruby source code. It seems to me that the code for `JSON.generate()` relies on [this C code](https://github.com/ruby/ruby/blob/d92f09a5eea009fa28cd046e9d0eb698e3d94c5c/ext/json/generator/generator.c#L140-L143) to make sure the string is valid UTF-8 before using it, but I am not familiar with the Ruby source code so take this with a grain of salt.