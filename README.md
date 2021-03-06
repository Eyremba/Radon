# Radon Java Obfuscator
Yet another Java obfuscator. If I forgot to credit you, just make a pull-request or open an issue.

Licenses get to go with the manifest in src/META-INF (yay!)

Usage: ```java -jar Radon.jar --config exampleconfig.yml```

Alternatively, you can also use ```java -jar Radon.jar --help``` for help.

Example config:
```yaml
Input: ClearChat.jar
Output: ClearChat-OBF.jar
StringEncryption: Light
InvokeDynamic: Normal
FlowObfuscation: Normal
LocalVariableObfuscation: Remove
LineNumberObfuscation: Remove
SourceNameObfuscation: Obfuscate
SourceDebugObfuscation: Obfuscate
HideCode: True
Crasher: True
StringPool: True
NumberObfuscation: True
Shuffler: True
InnerClassRemover: True
TrashClasses: 50
Renamer: True
WatermarkType: ConstantPool
WatermarkMessage: ItzSomebody
WatermarkKey: PASSWORD
ExpiryTime: 1/18/2018
ExpiryMessage: "YOUR SOFTWARE TRIAL HAS ENDED!!! YOU MUST NOW PAY $100000000 FOR THE FULL VERSION LULZ"
Libraries:
    - "C:/Program Files/Java/jdk1.8.0_131/jre/lib/rt.jar"
Exempt:
    - "me/itzsomebody/clearchat/ClearChat"
    - "me/itzsomebody/clearchat/config/Config"
```

Valid config options you can use:

| Option | Expected Value(s) | Desc |
| --- | --- | --- |
| Input | String | Input file to obfuscate |
| Output | String | Output file to dump result of obfuscation |
| Libraries | String List | Libraries used to compile the input |
| Exempts | String List | Exempted classes, methods, or fields from obfuscation |
| StringEncryption | String (Light/Normal/Heavy) | Type of string encryption to apply |
| FlowObfuscation | String (Light/Normal) | Type of flow obfuscation to apply |
| InvokeDynamic | String (Light/Normal/Heavy) | Type of invokedynamic obfuscation to apply |
| LocalVariableObfuscation | String (Obfuscate/Remove) | Type of local variable obfuscation to apply |
| Crasher | Boolean | Determines if the decompiler crasher should be applied |
| HideCode | Boolean | Determines if synthetic modifiers should be applied |
| StringPool | Boolean | Determines if strings should be pooled |
| LineNumberObfuscation | String (Obfuscate/Remove) | Type of line number obfuscation to apply |
| NumberObfuscation | Boolean | Determines if integers should be split into simple math expressions |
| SourceNameObfuscation | String (Obfuscate/Remove) | Type of source name obfuscation to apply |
| SourceDebugObfuscation | String (Obfuscate/Remove) | Type of source debug obfuscation to apply |
| TrashClasses | Integer | Number of trash classes to generate |
| WatermarkMessage | String | Message to watermark into the output |
| WatermarkType | String (ConstantPool/Signature) | Type of watermark to apply |
| WatermarkKey | String | Key used to encrypt watermarks |
| SpigotPlugin | String | Determines if input should be treated as a spigot/bungee plugin |
| Renamer | Boolean | Determines if obfuscator should rename classes and methods |
| Shuffler | Boolean | Determines if obfuscator should re-arrange class members |
| InnerClassRemover | Boolean | Determines if obfuscator should remove inner-class information |
| ExpiryTime | String | Message to insert for expiry obfuscation (useful for trialware) |
| ExpiryMessage | String | Message to show when set your trialware goes past expiration date (rip) |

## Obfuscation description
This table describes the current obfuscation settings Radon has.

| Obfuscation Type | Description |
| --- | --- |
| StringEncryption | This takes the strings in your program and encrypts them. Each of the current settings are symmetrical and will NOT prevent reverse-engineers from figuring out what your program does. The light setting uses a fairly well known XOR algorithm which is fast but very unsecure due to the fact it can be stuck into an IDE and the strings can be decrypted manually. The normal string encryption encrypts strings using AES. This can still be decrypted manually and should not be relied upon. Strings encrypted with the heavy string encryption cannot be decrypted manually because it is stacktrace-backed. While slightly more secure than the other options, this is still easily removed. |
| FlowObfuscation | This attempts to confuse the program flow to make the decompiled code harder to understand. The light setting replaces all gotos in the program with conditionals which always evaluate to true. The light setting usually doesn't affect decompiler results by much unless you have methods with very heavy flow control operations. The normal flow obfuscation attempts to confuse the stack rather than the decompiler output. Most of the added code can still be easily removed so you should not rely on this. |
| InvokeDynamic | This abuses Java 7's new opcode, the invokedynamic. This replaces certain member access with dynamic invocations. The light invokedynamic transformer is very simple and replaces invokestatic, invokevirtual and invokeinterface with invokedynamics. This is easily reversed by experienced reverse-engineers so don't rely on this. The normal invokedynamic protection is a very slight improvement to the light settings, but still is easy to reverse. The heavy invokedynamic hides invokestatic, invokevirtual, getstatic, putstatic, getfield and putfield opcodes with invokedynamics. Fields with primitive descriptions are not replaced with invokedynamics. |
| LocalVariableObfuscation | This attempts to prevent local variable analysis. The obfuscation setting changes the names of the local variables to hard-to-read UTF-8 characters. The remove setting removes the local variable information completely destroying the ability to recover the local variable names. Removal setting tends to shrink jar size since it removes information in the code. |
| Crasher | This adds an invalid signature to classes. The intention of this transformer is to crash a majority of decompiles which attempt to take class signatures into account of the output. This crashes JD-GUI, Procyon, CFR and Javap. |
| HideCode | This adds certain access flag modifiers to members of your code. Synthetic modifiers are added to classes, methods and fields while bridge modifiers are added to methods which aren't initializer methods (<init> and <clinit>). |
| StringPool | This adds a method into the class which takes all the strings in the class and pools them into the added method. |
| LineNumberObfuscation | This attempts to obscure stacktrace output by changing line number debug information. The obfuscation setting sets line numbers to random ones. The removal setting removes them from your code entirely. The removal setting also shrinks the size of the jar. |
| NumberObfuscation | This splits integers into some arithmetic expressions making it difficult to determine what number is being used. This is defeated by Krakatau. |
| SourceNameObfuscation | This attempts to obscure stacktrace output by removing sourcefile debug information. The obfuscation setting sets all the source file names to random ones. The removal setting removes them entirely from your code. The removal setting also shrinks the size of the jar. |
| SourceDebugObfuscation | This attempts to obscure source debug information. The obfuscation setting sets all the source debug values to random ones. The removal setting removes them entirely from your code. The removal setting also shrinks the size of the jar. |
| TrashClasses | This generates garbage classes which aren't used to attempt to hide the ones that are actually used. This also prevents JByteEdit usage. |
| Watermark | This marks a message into the classes which is intended for customer-identification. The constant pool option marks the message into the constant pool of each class. The signature option marks the messages into the class signature. |
| Renamer | This renames classes, methods and fields from their original names to random UTF-8 strings. |
| Shuffler | This simply changes the order of class members (methods and fields). |
| InnerClassRemover | This removes innerclass information. |
| ExpirationObfuscation | This adds a block of expiration code into each class initializer to prevent usage of jar after the expiration date has passed. |

## Credits

* [OW2 ASM](http://asm.ow2.org) - ObjectWeb ASM.
* [SnakeYaml](http://www.snakeyaml.org) - SnameYAML.
* [LordPancake](https://www.youtube.com/user/LordPankake) - Maker of SkidSuite
* [samczsun](https://github.com/samczsun) - Author of Java-Deobfuscator and the 20 (or so) ways to crash the various decompilers which is no longer open-source.
* [VincBreaker](https://github.com/Vinc0682) - Author of Smoke obfuscator which I stole some stuff from xD.
* [Coolman](https://github.com/c001man) - I stole his Zip-writing algorithm xD.
* [WindowBuilder by Eclipse](https://www.eclipse.org/windowbuilder/) - Used to make GUI (yes I know it's Java Swing, I didn't feel like remaking it in JavaFX)
* [Licel](https://licelus.com) - Makers of Stringer Java Obfuscator.
* [Allatori Dev Team](http://www.allatori.com) - Makers of Allatori Java Obfuscator.
* [Caleb Whiting](https://github.com/CalebWhiting/java-asm-obfuscator) - Author of Java ASM Obfuscator.

## License

GNU General Public License v3.0