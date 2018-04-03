# Coding Club

<!-- ![coding-club](https://github.com/hleveillegauvin/CodingClub/blob/master/images/musiccognition-451x266.jpg-01.svg)

Cognitive and Systematic Musicology Laboratory, Ohio State University

Written by Hubert Léveillé Gauvin  
leveillegauvin.1@osu.edu

# <a name="table-of-contents"></a> Table of Contents
* [0. The Shell -- A Primer](#shell-primer)
* [1. Writing Shell Scripts](#writing-shell-scripts)
    * [1.1. Saying Hello](#saying-hello)
    * [1.2. Counting Characters](#counting-characters)
    * [1.3. Printing Quotes](#printing-quotes)
    * [1.4. Writing Functions](#writing-functions)
* [2. Using APIs](#using-apis)
    * [2.1. Who's in Space?](#whos-in-space)
    * [2.2. Grabbing the Weather](#grabbing-the-weather)
    * [2.3. Using the Spotify API](#using-the-spotify-api)
* [3. Using the Humdrum Toolkit](#using-the-humdrum-toolkit)
    * [3.1. Basic Pitch Analysis](#basic-pitch-analysis)
    * [3.2. Basic Rhythmic Analysis](#basic-rhythmic-analysis)
    * [3.3. A Sample Problem](#humdrum-sample-problem)
* [4. References](#references)
    * [4.1. Online Resources](#online-resources)

## <a name="shell-primer"></a> 0. The Shell -- A Primer

The shell is a command line user interface for UNIX.

When you open your terminal, the shell should start you in your home directory. You can find out the name of your current directory by typing:

    pwd

You can use `ls` to list all the files and the directories of the directory you’re in:

    ls
    
The `cd` (change directory) command to navigate the shell. To move to a specic directory, type `cd` 
plus the name of the directory:

    cd Desktop

To move back to the parent dirctory, type:

    cd ..
    
If you simply type `cd` by itself, you'll can go back to your home directory:

    cd
    
Similarly, the `~` character is a shortcut to your home directory. So if you want to go to, say, your desktop from anywhere, simply type:

    cd ~/Desktop
    
You can view files using the `cat` command:

    cat myfile.txt
    
The `|`symbol is used to pipe the output of one command into another command. For example, we can list all the files and directories in our current directory using `ls` and then count the number of files using the `wc -l`(word count) command:

    ls | wc -l
 
[Back to table of contents](#table-of-contents)

## <a name="writing-shell-scripts"></a>1. Writing Shell Scripts

In this section, we'll learn how to write shell scripts through a series of short exercises lifted from the book [Exercises for Programmers: 57 Challenges to Develop Your Coding Skills](https://www.amazon.com/Exercises-Programmers-Challenges-Develop-Coding/dp/1680501224/ref=sr_1_1?ie=UTF8&qid=1519245259&sr=8-1&keywords=exercises+for+programmers). 

For all the examples in this section, type or paste the script into the text editor of your choice and save the file as `[nameofexercise].sh`. Once you have saved the file, type `[nameofexercise].sh` in Terminal to make it executable. Finally, run it with `/[nameofexercise].sh`.

### <a name="saying-hello"></a>1.1. Saying Hello
---

In this first exercise, we'll create a program that prompts for your name and prints a greeting using your name. Every shell script must start with a shebang line `#!/bin/sh`. We'll also use the `#` character to add comments to our script and document what we are doing. 

We'll use the `read -p` command to prompt a user response. The response will be stored under the variable `$name`. We'll that print that name and a greeting phrase using the `echo` command.

```
#!/bin/sh

# Exercises for Programmers
# 1 - Saying Hello
# Written by: Hubert Léveillé Gauvin
# Date: 15 December 2016, revised 13 September 2017

# Create a program that prompts for your name and prints a greeting using your name.

read -p "What is your name? " name
echo "Hello, $name, nice to meet you!"
```
[Back to table of contents](#table-of-contents)

### <a name="counting-characters"></a>1.2. Counting Characters
---

Next, we'll create a program that prompts for an input and returns the number of characters. Again we'll use the `read -p` command to prompt the user for an input, and we'll store the user input under the variable `$input`. We can create a `while loop` to test whether the user actually typed anything; `if` the input is empty, we'll prompt the user again, `else` (i.e. if the string is not empty), we'll count the number of characters. We'll use the `printf` command instead of `echo`, since `echo` adds a carriage return by default, which will add one extra invisible character to our string. We'll then use the `wc` command (word count) to count the number of characters. The standard `wc` output has 3 fields: number of lines, number of words, and number of characters. We can use `awk {'print $3'}` to print the third field (i.e. the number of characters). We'll save that number under the variable `$count`. We'll then print a sentence using `echo` that returns the number of characters. Finally, since we created a `while loop` at the beginning of our script, we'll need an `exit` statement to exit the loop.

```
#!/bin/sh

# Exercises for Programmers
# 2 - Counting the Number of Characters
# Written by: Hubert Léveillé Gauvin
# Date: 17 December 2016

# Create a program that prompts for an input and returns the number of characters.

while read -p "What is the input string? " input; 
do

	if [ -z "$input" ]; then	 #verify if $input is empty
	echo "Please enter a word."

	else

		count=$(printf $input | wc | awk {'print $3'})  # echo has a carriage return, which adds a character. Use printf instead.
		echo "$input has $count characters."
		exit		# only exit the while loop if the $input is not empty	
	fi

done
``` 

[Back to table of contents](#table-of-contents)

### <a name="printing-quotes"></a>1.3. Printing Quotes
---

Some characters have special meanings in the shell, meaning that by default, they are used for their literal meaning. For example, the `#` character is used to indicate a comment line, and the semi-colon (`;`) is a command separator. But sometimes, you'll need to use these characters for their literal meaning, as opposed to their special meaning. This is called "escaping," since you escape the special meaning of a character. In the shell, escaping is done by preceding a special character with a backslash (`\`). As such, `#` indicates a comment character, but `\#` indicates the traditional number sign (or hash sign).

In our next shell script, we'll create a program that prompts for a quote and an author. We will display the quotation using quotation marks. Since quotation marks have a special meaning, we'll need to escape them using the backslash character.  We'll start by writing two prompts using `read -p` and we'll store the user's input in the bash variables `$quote`and `$who`. Next, we'll use the `echo` command to print our quote, making sure to escape the double quotes:

```
#!/bin/sh

# Exercises for Programmers
# 3 - Printing Quotes
# Written by: Hubert Léveillé Gauvin
# Date: 17 December 2016

# Create a program that prompts for a quote and an author. Display the quotation using quotation marks.

read -p "What is the quote? " quote
read -p "Who said it? " who
echo "$who says, \"$quote\""
```
### <a name="writing-functions"></a>1.4. Writing Functions
---

Functions are a useful way to group pieces of code together. This can be especially useful if you plan on reusing the same lines of code often in a script. Let's write a simple function that prints a greeting message:

```
# Start by defining your function
hello() {
echo "Hello!"
}

# Then call your function by typing its name
hello
```

Functions can accept argument. The first argument passed to a function is automatically assigned to the variable `$1`:

```
hello() {
echo "Hello, "$1"!"
}

hello Hubert
```

Functions can also be used within a "for loop":

```
hello() {
echo "Hello, "$1"!"
}

for args in Hubert Andrew Nic Lindsay David Niels
do
hello "$args"
done
```

Similarly, "for loops" can be used inside a function:

```
hello() {
for args in $1 $2 $3 $4 $5 $6
do
echo "Hello, "$args"!"
done
}

hello Hubert Andrew Nic Lindsay David Niels
```

You can also write "if statements" within a function:

```
hello() {
if [[ $1 == "Dr. Huron" ]];
then
echo "Hide the beer!"
else
echo "Hello, "$1"!"
fi
}

for args in Hubert Andrew Nic Lindsay David Niels "Dr. Huron"
do
hello "$args"
done
```

Obviously, functions can be used within shell scripts:

```
#!/bin/bash

# Greetings script
# Written by: Hubert Léveillé Gauvin
# Date: 23 March 2018

# Create a program that prompts for a list of names and print a specific message based on the name

# Start by defining your function
hello() {
if [[ "$1" == "Dr. Huron" ]];
then
echo "Hide the beer!"
else
echo "Hello, "$1"!"
fi
}

# Then write your main script
read -p "Who's there? " name
hello "$name"
``` 
__Note: This will only work if you type Dr. Huron without " ".__

You can also call a function within another function. This can be useful to organize longer, more complex scripts into smaller parts:

```
#!/bin/bash

# Greetings script
# Written by: Hubert Léveillé Gauvin
# Date: 23 March 2018

# Create a program that prompts for a list of names and print a specific message based on the name

# Start by defining your function
hello() {
if [[ "$1" == "Dr. Huron" ]];
then
echo "Hide the beer!"
else
echo "Hello, "$1"!"
fi
}

# Then create a function for your main script
main() {
read -p "Who's there? " name
hello "$name"
}

# Finally, call your main function
main
```
__Note: This will only work if you type Dr. Huron without " ".__

As you write more and more shell scripts, you'll realize that you keep reusing the same functions over and over again. Something that save all your functions in a single shell script, that you can than import evrytime your write a new script. Think of this as your personal "function" package. Let's create a shell script called `bash_functions.sh`:

```
#!/bin/sh

# Collection of useful functions
# Written by: Hubert Léveillé Gauvin
# Date: 23 March 2018

check_dependency_command() {
# Check if a unix tool is installed. If not, print error message and exit
if command -v $1 >/dev/null 2>&1 ; then
:
else
    echo -e "\033[0;31m'$1': command not found.\033[0m"
    exit
fi
}

lowercase() {
# Translates string to lowercase only
tr [:upper:] [:lower:] <<< "$1"
}

random_integer_between() {
# Generates a random number between two numbers. Note: this is not pure bash.
python -S -c "import random; print random.randint($1,$2)"
}

ceil(){
# Rounds up to next integer (e.g. 2.1 becomes 3)
awk '{print ($0-int($0)>0)?int($0)+1:int($0)}' <<< "$1"
}

floor(){
# Rounds down to next integer (e.g. 2.9 becomes 2)
awk '{print int($0)}' <<< "$1"
}
```

Let's see how we can import this collection of funtions into a shell script. We'll use the `source` command to do so. If you haven't done so yet, make sure you change the permissions associated with the script we just did: `chmod +x bash_functions.sh`:

```
#!/bin/sh

# My cool research script
# Written by: Hubert Léveillé Gauvin
# Date: 23 March 2018

# Start by sourcing your function script to import its functions
source bash_functions.sh

# You now have all those new functions available to you
echo "This is a random number between 1 and 10"
random_integer_between 1 10

echo "This is the ceil function applied to 2.1"
ceil 2.1

echo "This is the floor function applied to 2.9"
floor 2.9
```

[Back to table of contents](#table-of-contents)

## <a name="using-apis"></a>2. Using APIs
### <a name="whos-in-space"></a>2.1. Who's in Space?
---


To use an API, we’re going to use the `curl` function. In its most basic form, `curl` needs an URL to interact with something. Let’s use `curl` to see who’s in space right now using the following API: http://api.open-notify.org/astros.json.

    curl "http://api.open-notify.org/astros.json"

Yay! `curl` has a bunch of options. One of the useful ones is `-s`, which enables silent mode and hides
the progress bar:

    curl -s "http://api.open-notify.org/astros.json"

`JSON` is a text format that is ideal data-interchange language. It is human readable, but as you can see, it can be a bit overwhelming. This is where `jq` comes handy. `jq` is a command-line tool that allows you to parse `JSON` file. Unfortunately, it is not preinstalled on your machine. To add it, I recommend you use `brew`. The brew package manager is like an app-store for the terminal. You can install `brew` on your machine following these instructions (OS X: https://brew.sh/ ; Ubuntu: http://linuxbrew.sh/).

__Note: I haven’t tried the Ubuntu version.__

Once `brew` is installed, you can easily add new tools to your machine. To add `jq`:

    brew install jq
    
__Note: For more information about jq, visit: https://stedolan.github.io/jq/manual/v1.5/__

Let’s see what `jq` does:

    curl -s "http://api.open-notify.org/astros.json" | jq
    
`JSON` files are organized as name/value pairs. For example, to know how many people are in space, we can call the name "number":

    curl -s "http://api.open-notify.org/astros.json" | jq '.number'
    
`jq` follows the UNIX philosophy, meaning that it can interact with other commands. Above, we saw that it can receive input from another command. We can also send its output elsewhere. For example, let’s use the stream-editor `sed` to convert "6" to "six":

    curl -s "http://api.open-notify.org/astros.json" | jq '.number' | sed 's/6/six/g'
    
Just like with any other UNIX tools, we can assign the above command to a variable and call that variable later:

    number_of_astronauts_in_space=$(curl -s "http://api.open-notify.org/astros.json" | jq '.number' | sed 's/6/six/g')
    echo "There are $number_of_astronauts_in_space astronauts in space"
    
Now, imagine we want to create a list with the name of everyone in space right now. Let’s try with
something like this:

    curl -s "http://api.open-notify.org/astros.json" | jq '.people.name'
    
That didn’t work! `jq` gives us the following error message: jq: error (at <stdin>:0): Cannot index array with string "name".
  
This is because the values stored in "people" are stored as an array. Arrays are represented by square brackets in `JSON`. To access arrays with `jq`, we can use the following syntax:

    curl -s "http://api.open-notify.org/astros.json" | jq '.people[].name'
    
This is useful, but it would be even better if we could create a `CSV` file with the name of the
astronaut and their craft. Let’s try something like this:

    curl -s "http://api.open-notify.org/astros.json" | jq '.people[].name, .people[].craft'

This gave us a list of all the people followed by a list of all the crafts. But we want to have name then craft for everyone in space. Let’s re-work our query:

    curl -s "http://api.open-notify.org/astros.json" | jq '.people[] | .name, .craft'
    
Better. Notice how were are using pipes within `jq`. We know we’re still in `jq` because we haven’t closed that single quote yet. This is still a list, but at least it’s in the right order. Thankfully for us, `jq` has a built-in function to create `CSV` file called `@csv`. It does, however, require that we organize our data has an array. We can do that using square brackets:

    curl -s "http://api.open-notify.org/astros.json" | jq '.people[] | [.name, .craft] | @csv'
    
This worked, but it’s pretty messy. This is because jq is adding extra " " to make sure things are separated properly. But since our original data was already enclosed in " ", we don’t really need `jq` to do this. We can specify that we want the output to be "raw" using the `-r` option:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '.people[] | [.name, .craft] | @csv'
    
We can make our output even nicer by adding a header:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '["NAME", "CRAFT"], (.people[] | [.name, .craft]) | @csv'
    
We did it! Since this is `UNIX`, we can redirect our output to a `CSV` file if we want to:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '["NAME", "CRAFT"], (.people[] | [.name, .craft]) | @csv' > whosinspace.csv
    
The `CSV` file we just created can be used with other softwares, like `R` for example. But sometimes we just want to look at the data in the terminal. `csvkit` is a useful toolkit that allows you to view and manipulate `CSV` file directly in the terminal. If you have `brew` on your machine, you can install `csvkit` easily:

    brew install csvkit
    
One of the useful tools that is included in the `csvkit` is `csvlook`, which displays `CSV` files in a nice
table:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '["NAME", "CRAFT"], (.people[] | [.name, .craft]) | @csv' | csvlook
    
Of course, `csvlook` can also open local files:

    csvlook whosinspace.csv
    
[Back to table of contents](#table-of-contents)

### <a name="grabbing-the-weather"></a>2.2. Grabbing the Weather
---

The first API we used was pretty simple. It did not require any ID nor offered options. It really only did one thing: return information about who’s in space. But most of the time, APIs will be more sophisticated. In this exercise, we’ll be using the OpenWeatherMap API to fetch information about the weather. First, we’ll need to sign up for an API key at: http://openweathermap.org/appid.

After signing-up online, you’ll receive your API key by email. Since everyone’s API key is different, let’s assign our API key to a variable:

    my_id=6ff3595d244317ecf2a4a17976e7XXXX
    
__Note: You’ll need to replace 6ff3595d244317ecf2a4a17976e7XXXX with your own ID.__

We can now print our key anytime we need it:

    echo $my_id
    
__Note: This variable will be available as long as your session is running. If you close your terminal, your variable will disappear.__

We’re ready to use make our first request. Let’s start by looking for the current weather in Columbus, OH:

    curl -s "https://api.openweathermap.org/data/2.5/weather?q=columbus&APPID=$my_id" | jq
    
That’s a lot of information! Let’s see... There is something called .main.temp, but the value associated with it seems very high. Let’s look at the API documentation online: http://openweathermap.org/current. Ah! Let’s change the default unit to Fahrenheit. We can do this by modifying the API endpoint (i.e. the end of our url) to specify our preferred unit:

    curl -s "https://api.openweathermap.org/data/2.5/weather?q=columbus&units=imperial&APPID=$my_id" | jq
    
Just like we created a variable to store our ID, we can create a variable called `$city` to store the name of the city we’re interested in:

    city="columbus"
    curl -s "https://api.openweathermap.org/data/2.5/weather?q=$city&units=imperial&APPID=$my_id" | jq
    
Now, imagine we’re interested in finding out what the weather is like in Paris, we can simply update our variable `$city` and keep the same query:

    city="paris"
    curl -s "https://api.openweathermap.org/data/2.5/weather?q=$city&units=imperial&APPID=$my_id" | jq
    
We now know what the weather is like in Paris, France. But what if we were interested in knowing the weather in Paris, Ontario, "the Prettiest Little Town in Canada"? According to the online documentation, cities can be specified by city names (as we did previously), but also using either ISO 3166 country codes, city ID, geographic coordinates, or ZIP codes. Let’s use city ID. Open Weather Map has a `JSON` file with all the cities available and their unique ID. You can download that file from the following url: http://bulk.openweathermap.org/sample/city.list.json.gz. We can also use `curl` to download the file, and `gunzip` to extract it:

    curl -L "http://bulk.openweathermap.org/sample/city.list.json.gz" > "city.list.json.gz"
    gunzip -kv "city.list.json.gz"
    
__Note: curl can be pretty slow.__

Let’s look at the file we just downloaded. Since this is a pretty big file, we’ll use the `head` command to only display the first 50 lines:

    head -n 50 "city.list.json"

Since this is a `JSON` file, we might want to use `jq` command to display it in a nice way. Let’s try it:

    head -n 50 "city.list.json" | jq
    
That didn’t work! That’s because `JSON` is a hierarchical format, and by using the `head` command, we destroyed its hierarchy. However, if we were to use `jq` on the whole file, things would work normally. Now since `city.list.json` is a pretty big file, this will take a couple of seconds. We can calculate how long it will take by adding `time` before the command you want to measure:

    time jq '.' "city.list.json"
    
That took just a little bit more than 29 seconds on my machine! But it worked as expected. Now that we have a list of all the cities and their respective IDs, we need to find Paris, Ontario. We have a couple of ways to do this. Let’s have a look at our `JSON` file. Right above the city name is its ID, and right below the city name is its country. This means that we could use `grep` to search for the city we’re looking for, use the `-B1` option to tell `grep` to print one extra line before each match, and the `-A1` to print one extra line after our match:

    grep -B1 -A1 "Paris" "city.list.json"
    
That worked out okay. We can be even more specific by filtering our output file. Let’s see how many cities in Canada are named "Paris." We’ll use `grep` to search for the line `"country": "CA"` and specify that, this time, we want to print two extra lines before our match:

    grep -A1 -B1 "Paris" "city.list.json" | grep -B2 '"country": "CA"'
    
So we found the ID we’re looking for using `grep`. Alternatively, `jq` has a built-in search function:

    jq '.[] | select(.name | match("paris";"i")) | select(.country | match("CA";"i"))' "city.list.json"
    
Let’s break down our last query. We asked `jq` to open the array using `[]`, search within `.name` for the case-insensitive (`"i"`) string `"paris"`, then within those results, search within `.country` for a case-insensitive string matching `"CA"`, the country code for Canada. Since we’re really interested in the city ID, let’s be even more specific:

    jq '.[] | select(.name | match("paris";"i")) | select(.country | match("CA";"i")) | .id' "city.list.json"
    
Awesome. We can now copy and paste this ID somewhere. But wouldn’t it be better to assign it to a variable? In `BASH`, you can assign the outcome of a command to a variable using the following method:

    city_id=$(jq '.[] | select(.name | match("paris";"i")) | select(.country | match("CA";"i")) | .id' "city.list.json")
    
If you’ve been following closely, you might wonder why we hardcoded the string `"paris"` in the previous query, instead of using the shell variable `$city`. The answer is that shell variables cannot be used as is in a `jq` command. However, we can reassign shell variables to `jq` variables. In the following command, we are using the `--arg` option to reassign the shell variable `$city` to the `jq` variable `$CITY`:

    city_id=$(jq --arg CITY "$city" '.[] | select(.name | match($CITY;"i")) | select(.country | match("CA";"i")) | .id' "city.list.json")

Let’s make sure it worked by using `echo` to print the value of `$city_id` to our terminal:

    echo $city_id
    
Great! We can now use our variable `$city_id` within our API query. Since Canada uses the metric system, let’s also change the system were a using:

    curl -s "https://api.openweathermap.org/data/2.5/weather?id=$city_id&units=metric&APPID=$my_id" | jq
    
Now, let’s imagine that we are employees working for the city of Paris, Ontario, and that we want to write a small weather command that displays a one-sentence summary of the weather. Something that we could automatically tweet every morning for example. For example, it could be something like this:

_The weather is currently -3.67C in Paris, Ontario. Overcast clouds. Temperatures are expected to climb up to -3C._

To do this, we’ll need to retrieve three bits of information: `.temp`; `.temp_max`; .`description`. Notice how for `.description`, we’re removing the " " from the string using `sed`, and calling `python` to capitalize the string:

    current_temp=$(curl -s "https://api.openweathermap.org/data/2.5/weather?id=$city_id &units=metric&APPID=$my_id" | jq '.main.temp')
    temp_max=$(curl -s "https://api.openweathermap.org/data/2.5/weather?id=$city_id&units=metric&APPID= $my_id" | jq '.main.temp_max')
    description=$(curl -s "https://api.openweathermap.org/data/2.5/weather?id=$city_id &units=metric&APPID=$my_id" | jq '.weather[].description' | sed 's/"//g' | python -c "print raw_input().capitalize()")
    echo "The weather is currently "$current_temp"C in Paris, Ontario. "$description ". Temperatures are expected to climb up to "$temp_max"C."

Since Canada is a bilingual country, it would be nice if we could translate our last output to French. Fortunately, the `trans` command from the `translate-shell` package uses the Google Translate API (and others) to do just that. We can install it using `brew`:

    brew install translate-shell
    
We can see if it worked:

    which -a trans
    
If you have the `humdrum toolkit` installed on your computer, you already have another command called `trans`. That will be problematic. We can overcome this problem by renaming our newly installed `trans` command to `translate` using an `alias`:

    alias translate=/usr/local/bin/trans
    
This worked as a temporary solution, but won’t work after we close our terminal. To make it
permanent, we can add the `alias` to our `bash_profile`:

    echo "alias translate=/usr/local/bin/trans" >> ~/.bash_profile
    source ~/.bash_profile
    
__Note: Since `man` is an independent program, we can’t do `man translate`, but `translate -h` will work.__

We can test our newly renamed command. We’ll use the `-to` option to specify the target language and `-b` to enable brief mode:

    translate -to "French" -b 'Hello'
    
It works! We can now translate our weather tweet to French:

    echo "The weather is currently "$current_temp"C in Paris, Ontario. "$description". Temperatures are expected to climb up to "$temp_max"C." | translate -to "French" -b
    
__Note: The Google Translate engine is getting pretty good, but it’s still not perfect. It works ok for this demo, but if we were to implement an actual Twitter bot, we would probably want the text to be translated by a human.__

[Back to table of contents](#table-of-contents)

### <a name="using-the-spotify-api"></a>2.3. Using the Spotify API
---

So far we have encountered APIs requiring no authentication, and APIs requiring a simple API key. But sometimes, in addition to having a unique key, APIs will require that you use an access token. While your key is permanent, your access token will only be valid for a limited period of time. The OAuth protocol that Spotify uses for their API works that way.

__Note: Spotify has an interactive API Web Console that allows you to use their API without having to code (but what’s the fun in that?). You can find more about it at: https://developer.spotify.com/web-api/console/__

To use the Spotify API, you’ll first need to register on https://developer.spotify.com/. Use your regular Spotify username and password to create your developer app. The app can be called anything. You will be granted a Spotify developer client ID and client secret.

Our next step is to use our new credentials to get a temporary access token. An important coding skill is to be able to look online for existing bits of code. For example, let’s see if we can find an existing script that will allow us to get a Spotify access token. Let’s Google: "get spotify api access token script". One of the first results was this link: https://gist.github.com/ahallora/4aac6d048742d5de0e65, which had the following script:

```
<?php

$client_id = '<insert your spotify app client id>'; 
$client_secret = '<insert your spotify app client secret>'; 

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL,            'https://accounts.spotify.com/api/token' );
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1 );
curl_setopt($ch, CURLOPT_POST,           1 );
curl_setopt($ch, CURLOPT_POSTFIELDS,     'grant_type=client_credentials' ); 
curl_setopt($ch, CURLOPT_HTTPHEADER,     array('Authorization: Basic '.base64_encode($client_id.':'.$client_secret))); 

$result=curl_exec($ch);
echo $result;

?>
```
__Note: if you're on OS X, `PHP` is pre-installed on your computer. If you're running Ubuntu (or something similar), you might have to install it manually.__

We are going to use this existing script to get an access token. First, we’ll modify the script by adding our client id and secret:


```
<?php

$client_id = '5cf8945daa8d4d2ca7862aef2bc4XXXX'; 
$client_secret = '69dae24b3fe44a6990171e5d7e78XXXX'; 

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL,            'https://accounts.spotify.com/api/token' );
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1 );
curl_setopt($ch, CURLOPT_POST,           1 );
curl_setopt($ch, CURLOPT_POSTFIELDS,     'grant_type=client_credentials' ); 
curl_setopt($ch, CURLOPT_HTTPHEADER,     array('Authorization: Basic '.base64_encode($client_id.':'.$client_secret))); 

$result=curl_exec($ch);
echo $result;

?>
```

Second, we’ll add a `PHP` she-bang line at the very beginning of our script to tell our shell that this is written in `PHP`:

```
#!/usr/bin/php
<?php

$client_id = '5cf8945daa8d4d2ca7862aef2bc4XXXX'; 
$client_secret = '69dae24b3fe44a6990171e5d7e78XXXX'; 

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL,            'https://accounts.spotify.com/api/token' );
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1 );
curl_setopt($ch, CURLOPT_POST,           1 );
curl_setopt($ch, CURLOPT_POSTFIELDS,     'grant_type=client_credentials' ); 
curl_setopt($ch, CURLOPT_HTTPHEADER,     array('Authorization: Basic '.base64_encode($client_id.':'.$client_secret))); 

$result=curl_exec($ch);
echo $result;

?>
```

We’ll save this file as `spotifyToken.php`. Finally, our last step is to change the permissions associated with this script to make it executable:

    chmod +x spotifyToken.php
    
Let’s see what the script does:

    ./spotifyToken.php
    
By now, you should recognize that the data we received is in the `JSON` format. Let’s use `jq` to make
it look pretty:

    ./spotifyToken.php | jq .
    
This tells us what our token is ("BQCU3...IMXXXX"), its type ("Bearer"), and when it expires (in 3600 seconds, i.e. 1 hour). Since the token is so long, it might be useful to assign it to a variable. We’ll use `jq` to print the value associated with `.access_token` and then `sed` to remove the " ":

    token=$(./spotifyToken.php | jq '.access_token' | sed 's/"//g')
    
__Note: This token will expire in 1 hour. If it expires, simply re-run the command above to get a new access token.__

Let’s see if it worked:

    echo $token
    
Now that we have a valid token, we can make our first query. Spotify’s API documentation is very detailed and can be found here: https://developer.spotify.com/web-api/endpoint-reference/ (and information about audio analysis can be found here: https://web.archive.org/web/20160528174915/http://developer.echonest.com/docs/v4/_static/AnalyzeDocumentation.pdf). In addition to our access token, each query will need 1) an access method, 2) an endpoint, and 3) a Spotify ID, (e.g. a track ID, album ID, and artist ID, etc).

1. The method of authentication is indicated on the Spotify website. For researchers, we will almost exclusively use `GET`. `GET` is also the default method used by `curl`, so we won’t have to worry about this too much.

2. Endpoints are used to indicate what type of information you want to retrieve. Simply copy the endpoint from the website and paste appropriately in the URL. For example: `/v1/albums/{id}/tracks`

__Note: All endpoints should begin with a forward slash (/). On the Spotify website, the forward slash for "Audio Analysis for a Track" endpoint is missing. You will need to manually add the forward slash at the beginning of this endpoint to access this information.__

3. Spotify IDs can be found through the Spotify app. Simply select the track, album, or artist, click share, and select URI to copy the ID.

__Note: By default, if you copy a Spotify ID from the app, it will come in this format: spotify:track:4BRkPBUxOYffM2QXVlq7aC. To use this ID with the Spotify API, you will need to trim the beginning part and only keep 4BRkPBUxOYffM2QXVlq7aC. We can do this using the following sed command: `echo "spotify:track:4BRkPBUxOYffM2QXVlq7aC" | sed 's/.*://g'.`__

__Note: Some endpoints require two types of IDs. For example, to get access to a user’s specific playlist, one would need `user_id` and `playlist_id`.__

Okay, let’s try to have a list of all the songs on the Beatles’ album "Revolver" (Spotify ID: 3PRoXYsngSwjEQWR5PsHWR):

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR" -H "Authorization: Bearer $token " | jq .
    
By skimming this file, it looks like the information we’re looking for is under .`tracks`, then `.items[]`, then `.name`. Since `.item[]` is an array, we’ll need to use `[]` (square brackets) in our query:

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq '.tracks.items[].name'

Sometimes, it can be hard to get around `JSON` files, especially if they are very long. The following command (which I found on an online forum), although very long and convoluted, can be really useful to get a big picture of how one file is organized:

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq '[
    path(..)
    | map(
    if type == "number" then
    "[]"
    else
    tostring
    end
    )
    | join(".")
    | split(".[]")
    | join("[]")
    ]
    | unique
    | map("." + .)
    | .[]'
    
Let’s make this into an `alias` and add it to our `.bash\_profile` so we never have to type this monstrosity ever again. Open `~/.bash_profile` with your favourite text editor (I’ll use TextEdit in this example):

    open -a TextEdit ~/.bash_profile
    
Copy and paste the following command as the last line of you `~/.bash_profile`, then save and quit:

    alias jq_overview='jq '"'"'[path(..) | map(if type == "number" then "[]" else tostring end)
    | join(".") | split(".[]") | join("[]")] | unique | map("." + .) | .[]'"'"''
    
Finally run source `~/.bash_profile`:

    source ~/.bash_profile
    
Ok, so we managed to find print a list of all the song. Let’s make it a little bit more interesting by also retrieving the duration of each song, and make it into a `CSV` file:

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq -r '.tracks.items[] | [.name, .duration_ms] | @csv'
    
Now, imagine we wanted to make our `CSV` file more useful by adding a column for artist (`.tracks.items[].artists.name`) and for album name (`.name`). The `@csv` filter included with `jq` is designed to convert arrays into `CSV`. However, since the value for album name is not part of the `.item` array, we’ll need to think outside the box a little bit. One solution would be to simply hardcode the name of the album in our query. For example:

curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq -r '.tracks.items[] | [.artists[].name, "Revolver", .name, .duration_ms] | @csv'

That works ok, but the fact that it’s hardcoded makes it harder for us to reuse the code. A better solution would be to assign the value of the album’s name stored under `.name` to a variable, and then call that variable when needed:

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq -r '.name as $album_name | .tracks.items[] | [.artists[].name, $album_name, .name, .duration_ms] | @csv'

__Note: In the line above, we created a `jq` variable, not a `BASH` variable. This explains why the syntax to create a variable is different than the one we saw previously.__

By default, the `v1/albums/{id}` endpoint will return a maximum of 20 songs. We can push that limit to 50 using the `limit` option:

    curl -s "https://api.spotify.com/v1/albums/3exqrnwvtUAEVCwar8xIcs/tracks?limit=50" -H "Authorization: Bearer $token" | jq '.items[].name'
    
Some albums, however, have more than 50 tracks. In order to get around this limitation, we will need to make more than one query. For example, we can get tracks 51-100 using a combination of `limit` and `offset`:

    curl -s "https://api.spotify.com/v1/albums/3exqrnwvtUAEVCwar8xIcs/tracks?offset=50&limit=50" -H "Authorization: Bearer $token" | jq '.items[].name'

Let’s see what else we can do with the Spotify API. One of the endpoints is called "audio analysis" and gives us some information about single songs. Let’s try it out:

    curl -s "https://api.spotify.com/v1/audio-analysis/2vEQ9zBiwbAVXzS2SOxodY" -H "Authorization: Bearer $token" | jq '.'
    
Again, since this is a big file, let’s use the `jq_overview` alias we created above to have a quick overview of the file’s organization:

    curl -s "https://api.spotify.com/v1/audio-analysis/2vEQ9zBiwbAVXzS2SOxodY" -H "Authorization: Bearer $token" | jq_overview
    
One of the keys is called `.bars[].start`. We can use this to estimate how many measures are in a song. First, let’s start by having a look at the data:

    curl -s "https://api.spotify.com/v1/audio-analysis/2vEQ9zBiwbAVXzS2SOxodY" -H "Authorization: Bearer $token" | jq '.bars[].start'
    
This gives us a series of timestamps representing the estimated beginning of each measure. We can count how many measures there are by using the `wc -l` command:

    curl -s "https://api.spotify.com/v1/audio-analysis/2vEQ9zBiwbAVXzS2SOxodY" -H "Authorization: Bearer $token" | jq '.bars[].start' | wc -l

We can also use the Spotify API to retrieve information like the tempo of a song. We will do this using the "audio features" endpoint:

    curl -s "https://api.spotify.com/v1/audio-features/2vEQ9zBiwbAVXzS2SOxodY" -H "Authorization: Bearer $token" | jq '.tempo'

Now imagine we wanted to get the tempo of all the songs on the Beatles’ album "Revolver." The audio features endpoint allows you to retrieve information for many songs at once, as long as your Spotify IDs are comma-separated. First, we’ll use the album endpoint to retrieve the Spotify ID for all the songs on the album:

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq -r '.tracks.items[].uri'
    
Our next step is to clean our IDs to create a nice comma-separated list. First, let’s remove the first part of the ID. We’ll use `sed` to replace any string of character ending with a colon with nothing:

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq -r '.tracks.items[].uri' | sed 's/.*://g'

Next, we’ll need to convert the newlines (represented by \n) to commas. We’ll use `tr` to do that:

    curl -s "https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/" -H "Authorization: Bearer $token" | jq -r '.tracks.items[].uri' |  sed 's/.*://g' | tr "\n" ","

Let’s assign this list of IDs to a variable:

    list_IDs=$(curl -s https://api.spotify.com/v1/albums/3PRoXYsngSwjEQWR5PsHWR/ -H "Authorization: Bearer $token" | jq -r '.tracks.items[].uri' | sed 's/.*://g' | tr "\n" ",")
    
Great. Now we can go back to the audio features endpoint and retrieve the tempo information for all those Spotify IDs:

    curl -s "https://api.spotify.com/v1/audio-features?ids=$list_IDs" -H "Authorization: Bearer $token" | jq '.audio_features[].tempo'

We can do the same thing for mode (where 1 is major and 0 is minor):

    curl -s "https://api.spotify.com/v1/audio-features?ids=$list_IDs" -H "Authorization: Bearer $token" | jq '.audio_features[].mode'

And of course, we can create a `CSV` file that combines both:

    curl -s "https://api.spotify.com/v1/audio-features?ids=$list_IDs" -H "Authorization: Bearer $token" | jq -r '.audio_features[] | [.mode, .tempo] | @csv'
    
[Back to table of contents](#table-of-contents)
  
## <a name="using-the-humdrum-toolkit"></a>3. Using the Humdrum Toolkit
### <a name="basic-pitch-analysis"></a>3.1. Basic Pitch Analysis
---
    
In the following exercises, we will familiarize ourselves with the [Humdrum Toolkit](http://www.humdrum.org/). We will loosely follow the structure of the [Humdrum User Guide](http://www.humdrum.org/guide/). Since our exercises are time-limited, I strongly encourage you to look at the [Humdrum User Guide](http://www.humdrum.org/guide/) and the [Humdrum Reference Manual](http://www.humdrum.org/man/) for more detailed explanations. 

Humdrum can refer to two things. 1) A music encoding syntax, and 2) a series of Unix command (commonly referred to as the Humdrum Toolkit) designed to parse Humdrum files.

When you [install Humdrum](http://www.humdrum.org/install/github/), a small sample of Humdrum files (often referred to as "kern" files) are downloaded on your machine. You can find those in the `humdrum-tools` directory:

    cd ~/humdrum-tools/data; ls

For this exercise, we'll make use of a small collection of unaccompanied folk songs from Nova Scotia:

    cd ~/humdrum-tools/data/songs/unaccompanied/nova-scotia; ls

We see that there are three items in this directory: 1) a readme file, 2) a directory named kern containing the kern files, and 3) a script called Makefile. Let's have a look at the readme file:

    cat README.md

OK, so we know that these songs were collected by Helen Creighton in 1932. Let's change directory and have a look at the kern files. Humdrum has a command called `census`, which provides statistical information about Humdrum files. Let's use it to get an overview of our whole sample. We'll use the wildcard character `*` to get information about all the `.krn` files in the `kern` directory:

    cd ./kern
    census *.krn

The `census` command also has a `-k` option (kern), which provides additional information about individual kern spines:

    census -k *.krn

But what if we wanted to get stats for every single kern file? We can create a `for loop` to run `census` on every single file. We'll also use `echo` to print the name of the file at the beginning of the loop so we know which file the stats correspond to:

```
for i in *.krn; do
    echo
    echo "-----------"
    echo $i
    echo "-----------"
    census -k $i
    echo
done
```

Reference records in Humdrum are usually indicated with `!!!`. We can use the `grep` command to retrieve reference information. For example, we can retrieve the name of every melody in our Nova Scotia sample:

    grep '!!!OTL:' *.krn

Imagine we wanted to save a clean version of our list of titles. We want to clean our previous output to keep only the names, sort the list alphabetically, and then save it as a text file. We can use the `-h` option of `grep` to get rid of the files' headers, then use `sed` to get rid of `!!!OTL:`, and then use the `sort` command:

    grep -h '!!!OTL:' *.krn | sed 's/!!!OTL: //g' | sort > titles.txt

Let's now have a closer look at single song:

    cat nova001.krn
 
Sometimes, you'll want to get rid of the reference records and only keep the music. We can do this using `grep -v`. The `-v` option is for invert-match, meaning that the selected lines are those not matching any of the specified patterns. The `grep` command allows us to use [regular expressions](https://en.wikipedia.org/wiki/Regular_expression) (sometimes referred to as regex or regexp) to search for specific strings. If you want to learn more about regex, I recommend you [watch this series of short videos](http://youtu.be/GVZOJ1rEnUg?list=PLfdtiltiRHWGRPyPMGuLPWuiWgEI9Kp1w). When writing a regular expression, the caret (`^`) can be used to specify that the pattern must start a line:

    grep -v '^!' nova001.krn

One of the most common things you'll want to do is extract some specific information from a spine. For example, imagine we were only interested in pitch information. We can easily extract pitch-related information in a kern file using the `kern -x` command. 

    kern -x nova001.krn 

The Humdrum Toolkit offers a series of commands to convert one type of notation to another.  For example, the `solfa` command can be used to find the moveable-do syllables associated with a specific melody. Just like the `kern`command above, `solfa` has a `-x` option:

    solfa -x nova001.krn
    
__Note: Do not confuse `solfa`, which converts a kern spine to moveable-do syllables, to `solfg`, which converts a kern spine to fixed-do syllables.__

Imagine we were interested in calculating the number of occurrences of the tonic pitch in a specific song. We can use `grep -c` to look for lines with the string "do." Since we're only interested in matches in data records (as opposed to comment or interpretation records), we'll want to eliminate all lines starting with the character `!` (for comment records) or `*` (for interpretation records). We'll use `grep -v '^[!*]'` to invert our search pattern and display lines that do not start (`^`) with either `!` nor `*`:

    solfa -x nova001.krn | grep -v '^[!*]' | grep -c do

Alternatively to `grep -v '^[!*]'`, we can use the `rid -GLId` humdrum command to eliminate specified Humdrum record types: `-G` removes all global comments, `-L` removes all local comments, `-I` removes null local comments, and `-d` removes null data records:

    solfa -x nova001.krn | rid -GLId | grep -c do

The proportion of tonic pitches can be manually calculated by simply comparing the resulting pattern count with the number of notes identified by census.

    census -k nova001.krn

We can also calculate it automatically using bash variables. We'll assign the number of tonic pitches to the variable `$tonic`, the total number of notes to the variable `$total`, and then we'll calculate the proportion using the `bc` command:

```
tonic=$(solfa -x nova001.krn | rid -GLId | grep -c do)
total=$(census -k nova001.krn | grep 'Number of notes:' | sed 's/[^0-9]//g')
bc -l <<< "($tonic/$total)*100"
```

The `-x` option is very useful, but sometimes we'll want to be more specific about the type of information we want to keep. For example, imagine we were interested in the relationship between pitches and phrases. The `solfa -x` command gets rid of all the phrase markings in our file (`{}`), making it useless for this problem. Instead, we can use `humsed` to edit our file and only keep the relevant information. The `humsed` command is a special version of `sed` designed specifically to manipulate Humdrum files. In contrast to `sed`, Humdrum interpretations and comments are not affected by `humsed`; only Humdrum data records will be modified:

    humsed '/^[^=]/ s/[^A-Ga-gr}{]//g; s/^$/./' nova001.krn 

Let's break down this query:

  * `/^[^=]/` is used to tell `humsed` to ignore lines representing barlines. `[^=]` means 'not =' meaning all characters except =. The initial caret (`^`) is an anchor used to indicate the start of a string. As such, we are telling `humsed` to only work on lines that start (`^`) with 'not =' (`[^=]`).

  * `s/[^A-Ga-gr}{]//g` is used to substitute characters. The initial caret means 'not', meaning substitute all characters except the ones specified within the square brackets. `A-G` means any uppercase letters between A-G, `a-g` means any lower case letters between a and g, and `r`, `}`, and `{` represents unique characters. Since `A-Ga-g` is used to represent pitches, `r` is used to represent rests, and `{ }` are used to represent phrase markings, we are effectively asking `humsed` to find all the characters that do no represent pitches, rests, or phrase markings, and replace them with nothing. Notice how in the query, we typed `}{` instead of `{}`. Since parentheses and brackets often have special meanings, writing it this way, i.e. `}{`, prevents the terminal to assume that the brackets are used to enclose something.

  * `;` is a command separator in Unix. It is the equivalent of pressing `[return]` on your keyboard in the terminal. We are using it because we want to run two `humsed` commands one after another.

  * `s/^$/./` is used to find empty lines (which are illegals in Humdrum) and replace them with a dot (`.`) character. The dot character is a null token. The caret (`^`) is an anchor representing the start of a line, and the dollar sign (`$`) is an anchor representing the end of a line. When used together (`^$`), they represent an empty line.

Imagine that we want to know which pitches begin and end phrases in a song. We'll use `grep` to search for all the lines containing either the beginning of a phrase or the end of a phrase:

    humsed '/^[^=]/ s/[^A-Ga-gr}{]//g; s/^$/./' nova001.krn | solfa | rid -GLId | grep '[}{]'

We can sort our results using the `sort` command:

    humsed '/^[^=]/ s/[^A-Ga-gr}{]//g; s/^$/./' nova001.krn | solfa | rid -GLId | grep '[}{]' | sort

Now imagine we want to tabulate each instance. We'll start by getting rid of octave distinctions (represented by numbers) using `sed`. We'll then use the `sortcount` humdrum extra command to tabulate our results.

    humsed '/^[^=]/ s/[^A-Ga-gr}{]//g; s/^$/./' nova001.krn | solfa | rid -GLId | grep '[}{]' | sed 's/[0-9]//g' | sortcount

We can then use `grep` to see the results only for phrase beginnings:

    humsed '/^[^=]/ s/[^A-Ga-gr}{]//g; s/^$/./' nova001.krn | solfa | rid -GLId | grep '[}{]' | sed 's/[0-9]//g' | sortcount | grep '{'

Or for phrase endings:

    humsed '/^[^=]/ s/[^A-Ga-gr}{]//g; s/^$/./' nova001.krn | solfa | rid -GLId | grep '[}{]' | sed 's/[0-9]//g' | sortcount | grep '}'

Suppose we wanted to get information about melodic intervals. An easy way to do this is to use the `mint` command:

    mint nova001.krn

Once again, we can tabulate all the melodic intervals. We'll get rid of all the non-data records using `rid -GLId`. We'll then get rid of barlines and rests using `grep -v '^[=r]'`. You may have noticed that the `mint` command echoes the first pitch token in square bracket (e.g. `[f]`). We will also get rid of that line using `grep -v '^\['`. (Since square brackets have a special meaning in regex, we'll need to escape them using the backslash character (`\`).) Finally, we'll use `sortcount` to tabulate the results.

    mint nova001.krn | rid -GLId | grep -v '^[=r]' | grep -v '^\[' | sortcount

By default, `mint` makes a distinction between ascending and descending intervals. We can overide this function using the `-a` option (for "absolute")

    mint -a nova001.krn | rid -GLId | grep -v '^[=r]' | grep -v '^\[' | sortcount

The  `sortcount` command has a `-p` option, which converts the results of `sortcount` into a percentage value, which can also be useful:

    mint -a nova001.krn | rid -GLId | grep -v '^[=r]' | grep -v '^\[' | sortcount -p

We've learned about the `solfa` command and the `mint` command. Now, let's try to create a file that would combine the original `**kern` spine, the `**solfa` spine, and the `**mint` spine. We'll start by creating two temporary files: one for `**solfg` and one for `**mint`. We'll make sure to keep all three types of records (i.e. comment, interpretation, and data records) so the spines properly align with one another. We'll then use the `assemble` command to create our new file. Finally, we'll use the `rm` command to delete our temporary files

```
kern -x nova001.krn | solfa | sed 's/[0-9]//g' > temp_solfa
mint -a nova001.krn > temp_mint

assemble nova001.krn temp_solfa temp_mint

rm temp_solfa
rm temp_mint
```

[Back to table of contents](#table-of-contents)

### <a name="basic-rhythmic-analysis"></a>3.2. Basic Rhythmic Analysis
---

In the last exercise, we learned how to do basic pitch analysis using the Humdrum Toolkit. In this exercise, we'll be focusing on rhythm. 
We'll make use of a small collection of unaccompanied folk songs from Nova Scotia:

    cd ~/humdrum-tools/data/songs/unaccompanied/nova-scotia/kern

Rhythmic information in Humdrum is encoded using the `**recip` representation. `**recip` is a subset of the `**kern` representation. The following command can be used to generate a `**recip` spine using a `**kern` spine:

    humsed '/^[^=]/s/[^0-9.r ]//g; s/^$/./' nova001.krn | sed 's/\*\*kern/**recip/'
    
Let's break down this query:

  * `/^[^=]/` is used to tell `humsed` to ignore lines representing barlines. `[^=]` means 'not =' meaning all characters except =. The initial caret (`^`) is an anchor used to indicate the start of a string. As such, we are telling `humsed` to only work on lines that start (`^`) with 'not =' (`[^=]`).

  * `s/[^0-9.r ]//g` is used to substitute characters. The initial caret means 'not', meaning substitute all characters except the ones specified within the square brackets. `0-9` means any number, `.` means any dot character, and `r` represents rests. Since `0-9.` is used to represent rhytms and  `r` is used to represent rests, we are effectively asking `humsed` to find all the characters that do not represent rhythms or rests, and replace them with nothing.

  * `;` is a command separator in Unix. It is the equivalent of pressing `[return]` on your keyboard in the terminal. We are using it because we want to run two `humsed` commands one after another.

  * `s/^$/./` is used to find empty lines (which are illegals in Humdrum) and replace them with a dot (`.`) character. The dot character is a null token. The caret (`^`) is an anchor representing the start of a line, and the dollar sign (`$`) is an anchor representing the end of a line. When used together (`^$`), they represent an empty line.
  
  * `sed 's/\*\*kern/**recip/'` is used to to change the exclusive interpretation from `**kern` to `**recip`.
  
One of the things we might be interested in doing is looking for rhythmic patterns. The `context`command can be used to amalgamate one or more successive input data records into single records according to user-defined criteria. For example, imagine we wanted to determine the most common rhythmic pattern spanning a single measure:

    humsed '/^[^=]/s/[^0-9.r ]//g; s/^$/./' nova001.krn | sed 's/\*\*kern/**recip/' | context -b ^= -o ^= | rid -GLId | sortcount
    
Again, let's break down this query:  

  * `humsed '/^[^=]/s/[^0-9.r ]//g; s/^$/./' nova001.krn | sed 's/\*\*kern/**recip/'` converts `**kern` to `**recip`, as described above.
  
  * `context -b ^= -o ^=` amalgamates all the data within one measure and prints it on a single line. The `-b ^=` option indicates to begin amalgamating when a line that starts (`^`) with the `=` character (i.e. a barline). The `-o ^=` option indicates that lines starting (`^`) with a `=` character should be omitted (`-o`).
  
  * `rid -GLId` eliminates specific Humdrum records: `-G` removes all global comments, `-L` removes all local comments, `-I` removes null local comments, and `-d` removes null data records.
  
  * `sortcount` tabulates the results.
  
Determining the most common rhythmic pattern spanning a single measure is a bit unmusical. Perhaps a more interesting query would be to compare rhythmic patterns spanning musical phrases. In Humdrum, phrases are identified using `{ }`. Currently, phrase markings were deleted from our `**recip` spine. We can change that by modifying our original query:

    humsed '/^[^=]/s/[^0-9.r }{]//g; s/^$/./' nova001.krn | sed 's/\*\*kern/**recip/'
  
Now that we have phrase markings, let's used the context command again to search for rhythmic patterns 
  
    humsed '/^[^=]/s/[^0-9.r }{]//g; s/^$/./' nova001.krn | sed 's/\*\*kern/**recip/' | context -b { -o ^= | humsed 's/[}{]//g' | rid -GLId | sortcount

Perhaps unsurprisingly, all the phrases in this song have a different rhythmic structure. Let's try the same thing on the whole collection of Nova Scotian songs. We'll use `cat` to concatenate all the songs in our directory together:

    cat *.krn | humsed '/^[^=]/s/[^0-9.r }{]//g; s/^$/./' | sed 's/\*\*kern/**recip/' | context -b { -o ^= | humsed 's/[}{]//g' | rid -GLId | sortcount

Another useful command is the Humdrum extra command `beat`. The `beat` command can be used to identify the metrical beat on which a line in a Humdrum `**kern` score occurs. By default, `beat` assumes that quarter notes (i.e. 4) are the definition of a beat (this can be overriden using the `-u` option):

     beat nova001.krn

By default, `beat` will output a single `**beat`spine. To append the `**beat` spine the to original `**kern` spine, simply use the `-a` option:

    beat -a nova001.krn
    
Similarly, you can also prepend the `**beat` spine to the original `**kern` spine:

    beat -p nova001.krn
    
Now imagine we were interested in comparing the average length of notes that fall on downbeats. We'll start by using the `dur` command to convert rhythmic notation into time in seconds. We'll use the `-T` option to specify the duration in second of a quarter note. The duration we choose is irrelavant, as long as we are consistent. In this example, we'll use 1 second. We'll also use the `-x` option to suppress all non-duration output. Let's save the output of our command as `nova001.dur`:

    dur -T 1 -x nova001.krn > nova001.dur
    
Let's now create a beat spine to using the `beat` comand we learned above. We'll need to use the `-n` option to keep all null records, to make sure we can realign our spines properly. Let's save this file as `nova001.beat`:

    beat -n nova001.krn > nova001.beat
    
Finally, let's create a `**recip` spine:

    humsed '/^[^=]/s/[^0-9.r ]//g; s/^$/./' nova001.krn | sed 's/\*\*kern/**recip/' > nova001.recip

We can assemble our two files using the `assemble` function:

    assemble nova001.beat nova001.dur nova001.recip
    
Okay, back to our initial problem. We're only interested in the duration of notes that fall on downbeats. We'll start by using `rid -GLId` to get rid of all non-music data. We'll then delete all rests. Since the our `**recip` spine is the furthest to the right, we can simply use `grep` to print all lines that do not (`-v`) end (`$`) with `r`. We can then use grep to select only the notes that fall on a down beat. Since our `**beat` spine is the left most, we'll look for lines that start (`^`) with either `1` or `2` (`[1-2]`) (our two downbeats in 2/4), followed by any type of vertical space (`[[:space:]]`): 

    assemble nova001.beat nova001.dur nova001.recip | rid -GLId | grep -v "r$" | grep "^[1-2][[:space:]]"
    
The only thing left to do is to calculate the mean of the second column. We can use `awk` to filter out the information we want. The `awk` command allows you to define custom field separator using the `-F` option. For this example, we'll define any vertical whitespace (meaning spaces, tabs, carriage returns, and newlines) as our field separator. In `BASH`, the bracket expression `[[:space:]]` can be used to represent any vertical space. In `awk`, the fields are represented by a dollar sign (`$`) followed by the cardinal position of the field. We'll use `$2` to print the second column. We'll then use the `stats` command to get the mean duration:

    assemble nova001.beat nova001.dur nova001.recip | rid -GLId | grep -v "r$" | grep "^[1-2][[:space:]]" |  awk -F[[:space:]] '{ print $2 }' | stats

Based on our last command, the mean duration notes falling on a downbeat in this song is 0.693548. Remember that we previously assigned an arbitrary duration of 1 second to a quarter note. This meanse that a quarter note would be 1, an eight note would be 0.5, and that a dotted eight note would be 0.75. So the mean duration of a note falling on a downbeat is a little be shorter than a dotted eight note. Let's now calculate the average duration of upbeat notes in the same piece. The only thing we need to change is our `grep` command. Instead of looking for lines starting with either 1 or 2, we'll look for lines that include a float (e.g. 1.50):

    assemble nova001.beat nova001.dur nova001.recip | rid -GLId | grep -v "r$" | grep "^[1-2].[0-9]*[[:space:]]" |  awk -F[[:space:]] '{ print $2 }' | stats

The mean duration of notes on upbeat is 0.50, meaning that every notes on upbeats are eight notes. It looks like, in this song, notes on upbeats are shorter than notes on downbeats. Once we're done, we can delete our temporary files using `rm`:

    rm nova001.beat nova001.dur nova001.recip

Sometimes it can be useful to estimate the duration of a piece based on its tempo. For example, imagine we wanted to record a collection of piano rags by Scott Joplin. Let's start by changing our current working directory:

    cd ~/humdrum-tools/data/joplin/kern ; ls
	
Th `dur` command converts rhythmic notation to durations in seconds based on the tempo of a song. The `-x` option is used to suppress all non-duration output for processed spines. `rid -GLId` eliminates specific Humdrum records: `-G` removes all global comments, `-L` removes all local comments, `-I` removes null local comments, and `-d` removes null data records. `grep -v '='` eliminates barlines, and `stats` calculates basic statistics on the first column of our output. 

    dur -x antoinette.krn | rid -GLId | grep -v '=' | stats
	
Alternatively, we can use the Humdrum extra command `gettime`, which creates an absloute timing spine to indicate the playing time of `**kern` data.  The `-T` option displays the total time that a `**kern` score takes to be performed instead of the attack times of the individual `**kern` lines, and `--simple` option displays the total time values (using the `-T` option) in terms of plain seconds rather than hours:minutes:seconds.

    gettime -T --simple antoinette.krn
    
We can use the same command to calculate the total duration of a collection of `**kern` files. This time, we'll simply use the `-T` option without the `--simple` flag:

    gettime -T  *.krn
    
By default, `gettime` will return the performance length of every single song, in addition to the total duration. If we're only interested in total duration, we can use the `tail -n 1` command to only print the last line of the `gettime` output:

    gettime -T  *.krn | tail -n 1

Currently, our output looks like this: `Total time:	1:38:00.962881 hours`. Now imagine you were writing a script to get the total duration of all the collections of songs you currently have on your computer. Maybe you want to save your information in a CSV format. Ideally, you only want to save the duration it self, and not the whole output. We can use `awk` to filter out the information we want. The `awk` command allows you to define custom field separator using the `-F` option. For this example, we'll define any vertical whitespace (meaning spaces, tabs, carriage returns, and newlines) as our field separator. In `BASH`, the bracket expression `[[:space:]]` can be used to represent any vertical space. In `awk`, the fields are represented by a dollar sign (`$`) followed by the cardinal position of the field. For example, `$1` would print "Total", and `$2` would print "time:". We'll use `$3` to print the duration:

    gettime -T  *.krn | tail -n 1 | awk -F[[:space:]] '{ print $3 }'

[Back to table of contents](#table-of-contents)

### <a name="humdrum-sample-problem"></a>3.3. A Sample Problem
---

In one of the [very firt issues of MTO, Jon Wild](http://www.mtosmt.org/issues/mto.96.2.7/mto.96.2.7.wild.html) wrote a review of the Humdrum Toolkit. This review included the following sample problem: _In what proportion are leading-tones in Bach chorale melodies approached from beneath, and in what proportion from above?_ In this section we'll loosely follow the instructions given in [Wild (1996)](http://www.mtosmt.org/issues/mto.96.2.7/mto.96.2.7.wild.html). First, let's find the Bach chorales that came with the Humdum Toolkit. 

    cd ~/humdrum-tools/data/bach-js/371chorales/kern
    
We'll start by concatenating all the chorales:

    cat *.krn
    
For this exercise, we'll assume that the melody is always in the soprano. We can extract the soprano part using `extract -p 4`. The `-p 4` option indicates that we want to extract the fourth (4) spine, which corresponds to the soprano part:

    cat *.krn | extract -p 4
    
We'll then use the `deg` command to convert pitches into scale degrees based on the key info encoded in each file. For minor-mode pieces, the minor harmonic scale is assumed, such that, in C minor, A-flat will be represented as `6 `, B-flat as `7-`, and B natural as `7`. `deg` makes use of the key indication encoded in each file to translate pitches into scale degrees. Let's Look at all the key indications in our concatenated file to make sure that everything looks fine:

    cat *.krn | extract -p 4 | grep \*.*: 
    
Mmmm. Looks like some key signatures were encoded according to modes rather than major/minor. Since this is not "standard" Humdrum practice, it will make the `deg` command stop abruptly. To make sure this doesn't happen, we can get rid of these modal indications using `sed`. We'll then pipe the `sed` output to `deg`:

    cat *.krn | extract -p 4 | sed 's/:.*/:/' | deg 

If you look at the output, you'll see that each scale degree is preceded by either `^` or `v`. The `v` signifies “lower than previous note” and `^` signifies “higher than previous note.” As such, the token `1` followed by `^5` means that the ensuing dominant pitch is above rather than below the preceding tonic pitch. Since we're only interested in leading tones, we'll use the `grep ^[v^]7` command to print only the lines with a leading tone. The first `^` is an anchord meaning that our search pattern must start a line, the square brackets `[ ]` mean either `v` or `^`. Note that the order is important here, as `[^v]` would mean _not_ `v`: 

    cat *.krn | extract -p 4 | sed 's/:.*/:/' | deg | grep ^[v^]7
    
Notice that some token have a minus sign (`-`) to their right. These represent lowered seventh scale-degrees, not leading tones. We'll use `grep -v [-]` to print all the lines that _do not_ contain the `-` character:

     cat *.krn | extract -p 4 | sed 's/:.*/:/' | deg | grep ^[v^]7 | grep -v [-]
     
Finally, we'll use `sortcount -p` to calculate the percentage of leading tones approached from above and the percentage of leading tones approached from below:
 
    cat *.krn | extract -p 4 | sed 's/:.*/:/' | deg | grep ^[v^]7 | grep -v [-] | sortcount -p
    
[Back to table of contents](#table-of-contents)

## <a name="references"></a>4. References
### <a name="online-resources"></a>4.1. Online Resources

#### Humdrum
  * http://www.humdrum.org/
  * http://extras.humdrum.org/
  * http://humdrum.ccarh.org/
  * http://wiki.ccarh.org/images/b/b5/Harmony1.pdf
  * http://humdrum.ccarh.org/
  * http://kern.ccarh.org/
  * http://verovio.humdrum.org/
  * http://www.mtosmt.org/issues/mto.96.2.7/mto.96.2.7.wild.html

#### Regular Expressions
  * https://regexr.com/
  
#### Shell Scripting
  * [https://developer.apple.com/](https://developer.apple.com/library/content/documentation/OpenSource/Conceptual/ShellScripting/Introduction/Introduction.html#//apple_ref/doc/uid/TP40004268-TP40003516-SW1)
  
#### Other
  * https://www.datascienceatthecommandline.com/
  
[Back to table of contents](#table-of-contents)
