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




  
