Head into the `NUMBER-345ac8b236064b431fa43f53d91c98c4834ef8f3/` directory.  

The `index.html` file isn't promising -- realistically we need some PHP, which we identified as the webserver's back-end language in Task 31.

Let's look for PHP files using `find`:  
`find . -name "*.php"`  

Only one result:  
`./resources/index.php`

![[Website Code Analysis-20250128193015641.webp]]

If we're going to find a serious vulnerability, it's going to have to be here!

Read through the file.

> [!Question]
>What does Thomas have to phone Mrs Walker about?
>`Neighbourhood Watch Meetings`

This appears to be a file-upload point, so we might have the opportunity for a filter bypass here!

Additionally, the to-do list at the bottom of the page not only gives us an insight into Thomas' upcoming schedule, but it also gives us an idea about the protections around the page itself.

> [!Question]
>Aside from the filter, what protection method is likely to be in place to prevent people from accessing this page?
>`basic auth`

Let's turn our attention to the code itself now.

Reading through the PHP code, it appears that there are _two_ filters in place here, plus a simple check to see if the file already exists.

These filters are rolled together into one block of PHP code:  
```
$size = getimagesize($_FILES["file"]["tmp_name"]); 
if(!in_array(explode(".", $_FILES["file"]["name"])[1], $goodExts) || !$size){ 
	header("location: ./?msg=Fail");
    die();  
 }
```

The first line here uses a classic PHP technique used to see if a file is an image. In short, images have their dimensions encoded in their exif data. The `getimagesize()` method returns these dimensions if the file is genuinely an image, or the boolean value `False` if the file is not an image. This is more difficult to bypass than other filters, but it's far from impossible to do so.

The second line is an If statement which checks two conditions. If either condition fails (indicated by the "Or" operator: `||`) then the script will redirect with a Failure message. The second condition is easy: `!$size` just checks to see if the `$size` variable contains the boolean `False`. The first condition may need to be broken down a little.

`!in_array(explode(".", $_FILES["file"]["name"])[1], $goodExts)`

There are two functions in play here: `in_array()` and `explode()`. Let's start with the innermost function and work out the way:  
`explode(".", $_FILES["file"]["name"])[1]`

The `explode()` function is used to split a string at the specified character. Here it's being used to split the name of the file we uploaded at each period (`.`). From this we can (rightly) assume that this is a file-extension filter. As an example, if we were to upload a file called `image.jpeg`, this function would return a list: `["image", "jpeg"]`. As the filter only really needs the file-extension, it then grabs the second item from the list (`[1]`), remembering that lists start at 0.

This, unfortunately, leads to a big problem. What happens if there's more than one file extension? Let's say we upload a file called `image.jpeg.php`. The filename gets split into `["image", "jpeg", "php"]`, but only the `jpeg` (as the second element in the list) gets passed into the filter!

Looking at the outer function now (and replacing the inner function with a placeholder of `EXPLODE_RESULTS`):  
`!in_array(EXPLODE_RESULTS, $goodExts)`

This checks to see if the result returned by the `explode()` method is _not_ in an array called `$goodExts`. In other words, this is a whitelist approach where only certain extensions will be accepted. The accepted extension list can be found in line 5 of the file.

> [!Question]
>Which extensions are accepted (comma separated, no spaces or quotes)?
>`jpg,jpeg,png,gif`

Between lines 4 and 15:  

```
$target = "uploads/".basename($_FILES["file"]["name"]);   
...  

move_uploaded_file($_FILES["file"]["tmp_name"], $target);
```

We can see that the file will get moved into an `uploads/` directory with it's original name, assuming it passed the two filters.

In summary:

- We know how to find our uploaded files
- There are two file upload filters in play
- Both filters are bypassable

We have ourselves a vulnerability!

**Next step: ** [[Exploit PoC]]
