# digi-key_api_access
Implements Digi-Key API access using plain Python 2.7 with no external dependencies.  Human interface is via command line.

## Motivation
I was cleaning up the KiCad BOM for a project and wanted to:
1. Check part availability
1. Get direct replacement
1. Verify pad assignments (do I have a THT part number for a component that should be SMT)
1. Get an idea of the BOM cost
1. See if I can maximize return by utilizing price breaks on parts
1. Most importantly I wanted all of the above to be automated.

Since I am a complete fanboy of Digi-Key and buy all of my parts from there, their API seemed like the perfect fit.  What was not the perfect fit was the very boring mandatory diddling of magic strings that’s required to actually use the API.  Hence this little project.

## Limitations
This project is intended to be used purely locally by a single person.  It takes all of the various magic strings, stores them in a file, and uses them as needed to implement the above.

The very first use of the API may require manual intervention by manually navigating using your favorite browser to the URL generated by STR_M1 (see [Program Usage] bellow).  To be honest I’m kind of fuzzy on what magic does what.

## Dependencies
1. Python – I wrote this using 2.7. 
1. Requests – (http://requests.readthedocs.io/en/master/).  My system came with it.  Seems like it would be a fairly popular package.
1. Digi-Key account – the kind you use for buying stuff.
1. Digi-Key API account – different account than then one you use for buying stuff.  See: (https://api-portal.digikey.com/)

## Quickstart
+ Download the the Python script
+ Create the initial state/config file `~/.digi-key_api_state.json` with the following contents.  Naturally replace the stuff in brackets with values specific to you

```
{
 	"API_CLIENT_ID": "<MAGIC STRING>",
 	"API_SECRET": "<SUPER SECRET MAGIC STRING>",
 	"API_REDIRECT_URI": "https://localhost",
 	"LOGIN_PASSWORD" : "<SUPER SECRET PASSWORD>",
 	"LOGIN_NAME" : "<LESS SECRET LOGIN NAME>",
 	"CONTEXT": { }
}
```

The API_* keys are the values you get from getting Digi-Key API access via  (https://api-portal.digikey.com/).  The LOGIN_* keys are the login/password key combo for the account you use to buy stuff off their website.

NOTE: You should probably check this downloaded script to make sure that super devious hackers didn’t alter my code to send your login credentials to themselves.

## Program usage
The most frustrating part is getting past all of this Web2.0 security through annoyance.  Once all of the magic beans are collected and stored things should ‘just work’.  The very first time you try to use the API you may have to generate a magic URL using the STR_M1 command and paste that into the browser.  After that AUTH_NEW and AUTH_REFRESH should work automagically.

### Command line parameters
+ -h – Prints  brief help text
+ -d – Disables extra debug output overwriting the -D flag or the application configuration
+ -D – Enables extra debug output regardless of the application configuration.  Is overridden by the -d parameter.
+ -P – Parameter.  If searching for parts then the Digi-Key part number goes here.  If navigating the wonderful world of magical web strings then an auth code goes here.  More on that later.
+ -C – Count.  The quantity of parts specified by -P you’re looking for.  Defaults to 1 if omitted.
+ -Jc – Produces compact JSON output when dumping search results to stdout.  If omitted the output if formatted all pretty like.

### Main command
The main command is one of the following:
+ INVOKE_M1 – Invokes part one of the magical process using the information in the configuration file.  Outputs a new magic code.
+ INVOKE_M2 – Invokes part two of the magical process using the magic code generated by INVOKE_M1.  Supply the magic code using -P parameter.
+ STR_M1 – Generates the URL that is required to make part one of the magic to work.  Outputs the URL.
+ STR_M2 – Generates the URL that is required to make part two of the magic work.  Requires the magic code produced by INVOKE_M1.  Supply the magic code using the -P parameter.  Outputs the URL.
+ AUTH_NEW – Initiates a new authentication process using the information in the state file that you created under the Quickstart section.  Essentially calls INVOKE_M1 and INVOKE_M2 back to back.  If everything goes well the magic beans will be stored in the state file.
+ AUTH_REFRESH – Refreshes the magic beans and if everything goes well updates the state file.  If you call this with a period of less than 24 hours your magic tokens will remain valid and your life will be less annoying.
+ PART_SEARCH – The command that’s the steak behind all this sizzle.  Searches for the part specified in the -P parameter using the Digi-Key API.


