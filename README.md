# Coding Club

Cognitive and Systematic Musicology Laboratory, Ohio State University

Written by Hubert Léveillé Gauvin  
leveillegauvin.1@osu.edu

## Using APIs
### Who’s in Space?

To use an API, we’re going to use the `curl` function. In its most basic form, `curl` needs an URL to interact with something. Let’s use `curl` to see who’s in space right now using the following API: http://api.open-notify.org/astros.json.

    curl "http://api.open-notify.org/astros.json"

Yay! `curl` has a bunch of options. One of the useful one is `-s`, which enables silent mode and hides
the progress bar:

    curl -s "http://api.open-notify.org/astros.json"

`JSON` is a text format that is ideal data-interchange language. It is human readable, but as you can see, it can be a bit overwhelming. This is where `jq` comes handy. `jq` is a command-line tool that allows you to parse `JSON` file. Unfortunately, it is not preinstalled on your machine. To add it, I recommend you used `brew`. The brew package manager is like an app-store for the terminal. You can install `brew` on your machine following these instructions (OS X: https://brew.sh/ ; Ubuntu: http://linuxbrew.sh/).

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
    
This worked, but it’s pretty messy. This is because mintinlinebashjq is adding extra " " to make sure things are separated properly. But since our original data was already enclosed in " ", we don’t really need `jq` to do this. We can specify that we want the output to be "raw" using the `-r` option:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '.people[] | [.name, .craft] | @csv'
    
We can make our output even nicer by adding a header:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '["NAME", "CRAFT"], (.people[] | [.name, .craft]) | @csv'
    
We did it! Since this is `UNIX`, we can redirect our output to a `CSV` file if we want to:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '["NAME", "CRAFT"], (.people[] | [.name, .craft]) | @csv' > whosinspace.csv
    
The `CSV` file we just created can be used with other softwares, like `R` for example. But sometimes we just want to look at the data in the terminal. `csvkit` is a useful toolkit that allows you to view and manipulate `CSV` file directly in the terminal. If you have `brew` on your machine, you can install `csvkit` easily:

    brew install csvkit
    
One of the useful tool that is included in the `csvkit` is `csvlook`, which displays `CSV` files in a nice
table:

    curl -s "http://api.open-notify.org/astros.json" | jq -r '["NAME", "CRAFT"], (.people[] | [.name, .craft]) | @csv' | csvlook
    
Of course, `csvlook` can also open local files:

    csvlook whosinspace.csv
    
### Grabbing the Weather


The first API we used was pretty simple. It did not require any ID nor offered options. It really only did one thing: return information about who’s in space. But most of the time, APIs will be more sophisticated. In this exercise, we’ll be using the OpenWeatherMap API to fetch information about the weather. First, we’ll need to sign up for an API key at: http://openweathermap.org/appid.

After signing-up online, you’ll receive your API key by email. Since everyone’s API key is different, let’s assign our API key to a variable:

    my_id=6ff3595d244317ecf2a4a17976e7XXXX
    
__Note: You’ll need to replace 6ff3595d244317ecf2a4a17976e7XXXX with your own ID.__

We can now print our key anytime we need it:

    echo $my_id
    
__Note: This variable will be available as long as your session is running. If you close your terminal, your variable will disappear.__

We’re ready to use make our first request. Let’s start by looking for the current weather in Columbus, OH:

    curl -s "https://api.openweathermap.org/data/2.5/weather?q=columbus&APPID=$my_id" | jq
    
That’s a lot of information! Let’s see... There is something called .main.temp, but the value associated with it seems very high. Let’s look at the API documentation online: http://openweathermap.org/current. Ah! Let’s change the default unit to Fahrenheit. We can do this by modifying the API endpoint (i.e. then end of our url) to specify our preferred unit:

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
    
That didn’t work! That’s because `JSON` is a hierarchical format, and by using the `head` command, we destroyed its hiercharchy. However, if we were to use `jq` on the whole file, things would work normally. Now since `city.list.json` is a pretty big file, this will take a couple of seconds. We can calculate how long it will take by adding `time` before the command you want to measure:

    time jq '.' "city.list.json"
    
That took just a little bit more than 29 seconds on my machine! But it worked as expected. Now that we have a list of all the cities and their respective IDs, we need to find Paris, Ontario. We have a couple of ways to do this. Let’s have a look at our `JSON` file. Right above the city name is its ID, and right below the city name is its country. This means that we could use `grep` to search for the city we’re looking for, use the `-B1` option to tell `grep` to print one extra line before each match, and the `-A1` to print one extra line after our match:

    grep -B1 -A1 "Paris" "city.list.json"
    
That worked out okay. We can be even more specific by filtering our output file. Let’s see how many cities in Canada are named "Paris." We’ll use `grep` to search for the line `"country": "CA"` and specify that, this time, we want to print two extra lines before our match:

    grep -A1 -B1 "Paris" "city.list.json" | grep -B2 '"country": "CA"'
    
So we found the ID we we’re looking for using `grep`. Alternatively, `jq` has a built-in search function:

    jq '.[] | select(.name | match("paris";"i")) | select(.country | match("CA";"i"))' "city.list.json"
    
Let’s breakdown our last query. We asked `jq` to open the array using `[]`, search within `.name` for the case-insensitive (`"i"`) string `"paris"`, then within those results, search within `.country` for a case-insensitive string matching `"CA"`, the country code for Canada. Since we’re really interested in the city ID, let’s be even more specific:

    jq '.[] | select(.name | match("paris";"i")) | select(.country | match("CA";"i")) | .id' "city.list.json"
    
Awesome. We can now copy and paste this ID somewhere. But wouldn’t it be better to assign it to a variable? In `BASH`, you can assign the outcome of a command to a variable using the following method:

    city_id=$(jq '.[] | select(.name | match("paris";"i")) | select(.country | match("CA";"i")) | .id' "city.list.json")
    
If you’ve been following closely, you might wonder why we hard coded the string `"paris"` in the previous query, instead of using the shell variable `$city`. The answer is that shell variables cannot be used as is in a `jq` command. However, we can reassign a shell variables to `jq` variables. In the following command, we are using the `--arg` option to reassign the shell variable `$city` to the `jq` variable `$CITY`:

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

Since Canada is a bilingual country, it would be nice if we could translate our last ouptut to French. Fortunately, the `trans` command from the `translate-shell` package uses the Google Translate API (and others) to do just that. We can install it using `brew`:

    brew install translate-shell
    
We can see if it worked:

    which -a trans
    
If you have the `humdrum toolkit`installed on your computer, you already have another command called `trans`. That will be problematic. We can overcome this problem by renaming our newly installed `trans` command to `translate` using an `alias`:

    alias translate=/usr/local/bin/trans
    
This worked as a temporarily solution, but won’t work after we close our terminal. To make it
permanent, we can add the `alias` to our `bash_profile`:

    echo "alias translate=/usr/local/bin/trans" >> ~/.bash_profile
    source ~/.bash_profile
    
__Note: Since `man`is an independent program, we can’t do `man translate`, but `translate -h`will work.__

We can test our newly renamed command. We’ll used the `-to` option to specify the target language and `-b` to enable brief mode:

    translate -to "French" -b 'Hello'
    
It works! We can now translate our weather tweet to French:

    echo "The weather is currently "$current_temp"C in Paris, Ontario. "$description". Temperatures are expected to climb up to "$temp_max"C." | translate -to "French" -b
    
__Note: The Google Translate engine is getting pretty good, but it’s still not perfect. It works ok for this demo, but if we we’re to implement an actual Twitter bot, we would probably want the text to be translated by a human.__

### Using the Spotify API

So far we have encountered APIs requiring no authentication, and APIs requiring a simple API key. But sometimes, in addition to having a unique key, APIs will require that you use an access token. While your key is permanent, your access token will only be valid for a limited period of time. The OAuth protocol that Spotify uses for their API works that way.

__Note: Spotify has a Interactive API Web Console that allows you to use their API without having to code (but what’s the fun in that?). You can find more about it at: https://developer.spotify.com/web-api/console/__

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













  
