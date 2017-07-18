# predelex v1.0
Package for the pre-processing of text to be used with lexicoder, or other text analysis packages.


## Description

`predelex` is a tool for preparing text for analysis with `lexicoder` or other text analysis packages. The purpose is to reduce the amount of noise in a given text file, leaving only the words of interest.  By default, predelex will place each sentence on its own line, with end of line characters specified in `eol.lsd`. 

## Usage

`java Predelex --function file.txt`

`java Predelex --function directory`

Or, if you want to use the plx script (makes for cleaner code):
1. Make sure the script is executable: `chmod +x plx`
2. Make sure that the script points to the classpath where predelex is located
3. Make sure that plx is in your path, either explicitly by calling `./plx` or by altering the system path to include the directory where `plx` resides.

Now you should be able to run predelex like this:
`plx --function file.txt`

`plx --function directory`

## Basic Functions
### Implemented
`--upper` - converts text to upper case

`--lower` - converts all text to lower case

`--delete-quotes` - removes all single and double quotes and shifts text to the left (e.g. don't -> dont)

`--kill-quotes` - removes all single and double quotes, does not shift text (e.g. don't -> don t)

`--stop-words [optional-dictionary]` - removes common stop words specified in `.stop-words.lsd`, can be overridden by user-supplied dictionary.

`--delete-words [dictionary]` - removes all words found in [dictionary] and shifts the text to the left. 

`--delete-numbers` - removes all numbers and shifts the text to the left (e.g. In 1999, I went to the beach. -> In, I went to the beach) 

`--kill-numbers` - removes all numbers and does not shift the text (e.g. In 1999, I went to the beach. -> In     , I went to the beach) 

`--white-space` - removes all unnecessary white space (e.g. In    , I went to the beach -> In, I went to the beach). Note: Also removes new-lines.

`--delete-commas` - removes all commas and shifts the text to the left (e.g. The ship is at co-ordinates X,Y: 100,200 -> The ship is at co-ordinates XY: 100200)

`--kill-commas` - removes all commas and does not shift the text (e.g. The ship is at co-ordinates X,Y: 100,200 -> The ship is at co-ordinates X Y: 100 200)

`--delete-periods` - removes all periods and shifts the text to the left (e.g. www.lexicoder.com -> eg wwwlexicodercom)

`--kill-periods` - removes all periods and does not shift the text (e.g. -> e g www lexicoder com)

`--delete-titles` - removes common titles (defined in `.titles.lsd`) and shifts the text left (e.g. Go visit Mr. Smith -> Go visit Smith)

`--replace-titles` - replaces common titles (defined in `.titles.lsd`) to their full text (e.g. Mr. & Dr. Curie -> Mister & Doctor Curie)

### Coming soon

`--expand [optional-dictionary]` - expands common contractions (e.g. don't -> do not). Defaults in `.contractions.lsd`, overridden by user-supplied dictionary. 

`--replace-accents [optional-dictionary]` - removes all accents from the text and replaces them with unnaccented versions of the characters (e.g. é -> e)  Defaults specified in `.accents.lsd`, overridden by user-supplied dictionary.


## Advanced Functions
### Implemented

`--split-files ["optional-token"]` - splits the file (or files) specified by page-break (default) or by the contents of a string passed in quotes.  For example, --split-files "\n" would split the files at each line break. New filenames are generated based on the original filename.  For example, `predelex --split-files mydata.txt` would generate `mydata-1.txt` ... `mydata-n.txt`  Upon completion, the original file is deleted. As such, this command cannot be run with any other commands. It is advised that you run `--kill-backup` after you are confident that the new files are as desired, in order to minimize the amount of storage space used.

`--eol [optional-dictionary]` - `eol` will take end of line characters (defaults in `.eol.lsd`) and push them away from the words they are near.  For example, `Hello? Is it me you're looking for? No. No it is not.` would be replace with `Hello ?  Is it me you're looking for ?  No .  No it is not . `  Note the additional spaces.  This allows for pre-processing on sentence elements, and if desired allows for the tagging of sentences.  For example, one could edit the `.eol.lsd` dictionary to replace all end of line characters with `<eol>` (or some other marker) for later processing (specifically with `line-split`, to replace them with line breaks).  User can optionally specify a different dictionary for the end of line characters.

`--line-split [optional-dictionary]` - `line-split` replaces end of line characters with a line break (defaults in `eol.lsd`). The example sentence above would be converted to:

`Hello

Is it me you're looking for

No

No it is not

`
### Coming Soon
`--block-start | --block-end` - to be used together, specifices a block that is to be removed from the text.  Useful for removing meta-data, headers, and footers.  

`--extract-links [file-name]` - extracts all URL's and writes them to [file-name]. URL's are defined by any text that starts with "http://" or "https://"

`--extract-start | --extract-end | --extract-file` - to be used together, extracts a block from the text and writes it to file.  If extract-end is not specified, end of line is assumed.



## Backup Functions (currently disabled)
* Note: For version 1.0, the backup funcitons have been disabled.  Please make your own backup.

`predelex` (normally) automatically creates backup files of the original text you are cleaning. These are stored as hidden files in the same directory.  Sometimes (when you have a very large dataset, or when you have already made backups) you will not want to back these files up, but in general it is recommended. Note that this is a very basic backup procedure, and is really just a safeguard for new users who may make simple mistakes. Best practice is to always retain a backup of your original files

`--no-backup` - instructs predelex not to create backups of the files you are altering.

`--kill-backup` - instructs predelex to delete all backup files in the current directory.

`--restore` - instructs predelex to restore all files to their originally backed up state

## Order of Operations
`predelex` processes roughly in descending order of unit-size, and also in the order of how things are passed to it at the command line. In general, functions that apply to the whole document (e.g. `--kill-backup`, `--upper`) are done first, then operations at the sentence level (e.g. `--eol`, `--block-`, `extract-`), and then finally operations at the character level (`--kill-accents`, etc.) 

For new users, it is highly recommended to perform a series of predelex commands to be 100% confident in the order of operations. There is no warranty, and sometimes I'm pretty sure predelex will not behave as expected.
