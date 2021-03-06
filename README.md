#(1) Katal (0.3.2)
A (Python3/GPLv3/OSX-Linux-Windows/CLI) project, using no additional modules than the ones installed with Python3. This project requires the installation of **one** file (katal.py).

Create a catalogue from various source directories, without any duplicate. Add some tags to the files.

* license : GPL-3.0 (License :: OSI Approved :: GNU General Public License v3 (GPLv3))
* status : Beta (Development Status :: 5 - Production/Stable)

#(2) purpose and caveats
Read a directory, select some files according to a configuration file (leaving aside the duplicates), copy the selected files in a target directory.
Once the target directory is filled with some files, a database is added to the directory to avoid future duplicates. You can add new files to the target directory by using Katal one more time, with a different source directory.

Katal can also be used to create a "mirror database" from a source, without copying the files in the target directory (see the mode='nocopy' option, defined in the configuration file).

Caveats :

* Katal uses sqlite3 databases since sqlite3 is supported out-of-the-box by Python
* Katal is slow, very slow : you could speed up the execution by reducing the amount of console messages (see the --verbosity option) but the core is absolutely inefficient.
* Katal isn't immune to unexpected shutdowns/program's stops.
* please use the `--usentfsprefix` option if you read files from a NTFS volume.

#(3) installation and tests

    Don't forget : Katal is Python3 project, not a Python2 project !

    $ pip3 install katal

    or

    $ wget https://raw.githubusercontent.com/suizokukan/katal/master/katal/katal.py
    Since katal.py is a stand-alone file, you may place this file in the target directory.

    tests :
    Beware ! Due to the limitations of OSX and Windows systems, these tests are to be runned
    on Linux systems.
    
    $ python -m unittest tests/tests.py

    or
    
    $ nosetests
    
#(4) workflow
    
    $ katal -h
    ... will display all known parameters.
    $ katal --version
    ... wil display the version of the current script.
    
    note : using the --off option allows the user to use Katal without modifying the target 
    directory; with --off, no file will be written and the database will not change.

    note : Katal NEVER simply deletes a file : the script move ALWAYS the file to be deleted in its
    trash directory.

####Create the target directory :
    $ katal --new myworkingdirectory
    
    It may be convenient to go inside the target directory, e.g. :
    $ cd myworkingdirectory
    
    If Katal wasn't installed on the computer ($ pip3 install katal), it may be convenient to copy the 
    script katal.py inside the target directory, e.g. :
    $ cp katal.py myworkingdirectory
    
####Then, modify the .ini file (myworkingdirectory/.katal/katal.ini) and choose a source :

    Inside the .ini file, modify the following lines :
    
    [source]
    path : ~/src/

    [target]
    mode : copy   # 'copy', 'move' or 'nocopy'

    ->  'copy'   : source files are copied into the target directory .
    ->  'move'   : source files are moved into the target directory .
    ->  'nocopy' : no source file is copied into the target directory (the
                   target database being updated).
    
####Take a look at the files stored in the source directory :
    $ katal -si
    or
    $ katal --sourceinfos
    Some informations are displayed : how many files lie in the source directory, what are
    the extensions, and so on.
    
####Choose which files have to be selected; modify the .ini file :
    
    (example 1 : all .jpg (any size) and all .bmp greater then 5MB.)

    eval : filter1 or filter2

    [source.filter1]
    iname : .*\.jpg$
    
    [source.filter2]
    iname : .*\.bmp$
    size : >= 5MB
    
    (example 2 : all jpg,tif,bmp,png greater then 1MB.)
    eval : filter1 and (filter2 or filter3 or filter4 or filter5)
    [source.filter1]
    size : >1MB
    [source.filter2]
    iname : .*\.jpg$
    [source.filter3]
    iname : .*\.tif$
    [source.filter4]
    iname : .*\.bmp$
    [source.filter5]
    iname : .*\.png$
    
    Choose a name for the files in the target directory, e.g. :
    name of the target files : birthday__%%dd__%%i.%%e
    (%%dd : date (e.g. 2015_09_25_06_50)
    (%%i : database index, like "0", "1", "2"...)
    (%%e : extension, like "jpg")

####Let's see what would happen if the script tried to select the files :
    $ katal --select
    ... and answer 'yes' to the final question if all the details are ok to you.
    The files will be copied/moved/left (see the mode option) in the target directory.
    
    If you want a bit-to-bit check if two files have the same hashes, use the --strictcmp option.
    
####See the result (ti : target informations)
    $ katal -ti
    
    If want to start from scratch just keeping the configuration file, you may delete all target 
    files and flush the database with :
    $ katal --reset
    ... and modify the configuration file and use again --select
    
####Let's tag some files :
    $ katal --addtag=tree --to=*.jpg
    $ katal --addtag=birthday --to=myfile.bmp
    
####Let's search some tags :
    $ katal --findtag=tree
    
####Let's search some files and copy the selected files in new directory :
    $ katal --findtag=birthday --copyto=backup_birthday
    
####Let's remove all the files without any tag :
    $ katal --rmnotags
    ... the files will be copied in the trash directory (myworkingdirectory/.katal/trash)
    
####Let's remove a file :
    $ katal --targetkill=myfile.jpg

####Let's remove some files :
    $ katal --targetkill=file*.jpg
    
####Let's copy a target directory into another one, which didn't exist :
    $ katal --new=../target2
    
    modify the configuration file in target2 : e.g. you may change the name of the files.
    
    Then :
    $ katal --rebase=../target2

####Check if a file external to the target directory is already stored in it :
    $ katal --whatabout=myfile

####Check if the files external to the target directory are already stored in it :
    $ katal --whatabout=mydir/

####Display informations about a file belonging to the target directory :
    $ katal --whatabout=myfile

#(5) project's author and project's name
Xavier Faure (suizokukan / 94.23.197.37) : suizokukan @T orange D@T fr

The name Katal is derived from the Ancient Greek κατάλογος ("enrolment, register, catalogue").

#(6) arguments

    usage: katal.py [-h] [--add] [--addtag ADDTAG] [-cfg CONFIGFILE] [--cleandbrm]
                    [--copyto COPYTO] [-dlcfg {local,home}] [--findtag FINDTAG]
                    [--infos] [-n NEW] [--off] [--rebase REBASE] [--reset]
                    [--rmnotags] [--rmtags] [-s] [--settagsstr SETTAGSSTR] [-si]
                    [--strictcmp] [--targetpath TARGETPATH] [-ti] [-tk TARGETKILL]
                    [--to TO] [--usentfsprefix] [--verbosity {none,normal,high}]
                    [--version] [--whatabout WHATABOUT]

    optional arguments:
      -h, --help            show this help message and exit
      --add                 # Select files according to what is described in the
                            configuration file then add them to the target
                            directory. This option can't be used with the --select
                            one.If you want more informations about the process,
                            please use this option in combination with --infos .
                            (default: False)
      --addtag ADDTAG       # Add a tag to some file(s) in combination with the
                            --to option. (default: None)
      -cfg CONFIGFILE, --configfile CONFIGFILE
                            # Set the name of the config file, e.g. config.ini
                            (default: None)
      --cleandbrm           # Remove from the database the missing files in the
                            target path. (default: False)
      --copyto COPYTO       # To be used with the --findtag parameter. Copy the
                            found files into an export directory. (default: None)
      -dlcfg {local,home}, --downloaddefaultcfg {local,home}
                            # Download the default config file and overwrite the
                            file having the same name. This is done before the
                            script reads the parameters in the config file. Use
                            'local' to download in the current directory, 'home'
                            to download in the user's HOME directory. (default:
                            None)
      --findtag FINDTAG     # Find the files in the target directory with the
                            given tag. The tag is a simple string, not a regex.
                            (default: None)
      --infos               # Display informations about the source directory
                            given in the configuration file. Help the
                            --select/--add options to display more informations
                            about the process : in this case, the --infos will be
                            executed before --select/--add (default: False)
      -n NEW, --new NEW     # Create a new target directory (default: None)
      --off                 # Don't write anything into the target directory or
                            into the database, except into the current log file.
                            Use this option to simulate an operation : you get the
                            messages but no file is modified on disk, no directory
                            is created. (default: False)
      --rebase REBASE       # Copy the current target directory into a new one :
                            you rename the files in the target directory and in
                            the database. First, use the --new option to create a
                            new target directory, modify the .ini file of the new
                            target directory (modify [target]name of the target
                            files), then use --rebase with the name of the new
                            target directory (default: None)
      --reset               # Delete the database and the files in the target
                            directory (default: False)
      --rmnotags            # Remove all files without a tag (default: False)
      --rmtags              # Remove all the tags of some file(s) in combination
                            with the --to option. (default: False)
      -s, --select          # Select files according to what is described in the
                            configuration file without adding them to the target
                            directory. This option can't be used with the --add
                            one.If you want more informations about the process,
                            please use this option in combination with --infos .
                            (default: False)
      --settagsstr SETTAGSSTR
                            # Give the tag to some file(s) in combination with the
                            --to option. Overwrite the ancient tag string. If you
                            want to empty the tags' string, please use a space,
                            not an empty string : otherwise the parameter given to
                            the script wouldn't be taken in account by the shell
                            (default: None)
      -si, --sourceinfos    # Display informations about the source directory
                            (default: False)
      --strictcmp           # To be used with --add or --select. Force a bit-to-
                            bit comparisionbetween files whose hashid-s is equal.
                            (default: False)
      --targetpath TARGETPATH
                            # Target path, usually '.' . If you set path to .
                            (=dot character), it means that the source path is the
                            current directory (=the directory where the script
                            katal.py has been launched) (default: .)
      -ti, --targetinfos    # Display informations about the target directory
                            (default: False)
      -tk TARGETKILL, --targetkill TARGETKILL
                            # Kill (=move to the trash directory) one file from
                            the target directory.DO NOT GIVE A PATH, just the
                            file's name, without the path to the target directory
                            (default: None)
      --to TO               # Give the name of the file(s) concerned by
                            --settagsstr. wildcards accepted; e.g. to select all
                            .py files, use '*.py' . Please DON'T ADD the path to
                            the target directory, only the filenames (default:
                            None)
      --usentfsprefix       # Force the script to prefix filenames by a special
                            string required by the NTFS for long filenames, namely
                            \\?\ (default: False)
      --verbosity {none,normal,high}
                            # Console verbosity : 'none'=no output to the console,
                            no question asked on the console; 'normal'=messages to
                            the console and questions asked on the console;
                            'high'=display discarded files. A question may be
                            asked only by using the following arguments : --new,
                            --rebase, --reset and --select (default: normal)
      --version             # Show the version and exit
      --whatabout WHATABOUT
                            # Say if the file[the files in a directory] already in
                            the given as a parameter is in the target directory
                            notwithstanding its name. (default: None)

#(7) history / future versions

See the `roadmap.txt` file.

#(8) technical details

##(8.0) structure

see chart.txt

##(8.1) exit codes
                 0      : success
                -1      : can't read the parameters from the configuration file
                -2      : a KatalError exception has been raised
                -3      : an unexpected exception exception has been raised

##(8.2) configuration file
    The informations stored in the configuration file are written in the CFG_PARAMETERS global
    variable. CFG_PARAMETERS is filled by read_parameters_from_cfgfile() and is a
    configparser.Configparser object.
    The default name of the configuration file is set by the DEFAULT_CONFIGFILE_NAME global variable
    and may be set by the commande line (see --configfile option).

    [log file]    : parameters about the logfile
    use log file  : True/False; if False, all the messages are written only to the console.
    name          : no quotation mark here !

    [display]         : parameters about the way informations are displayed
    target filename.max length on console : (max length of the file names displayed)
    source filename.max length on console : (max length of the file names displayed)

    [source]          : parameters about source directory 

    [source.filterN]   : N is an integer greater or equal to  1; [source.filter1], [source.filter2], ...
    name/iname         : a regex; e.g. for all files with an .jpg extension : .*\.jpg$
                         (iname : case insensitive); "^c.5$" is different from "c.5" !
    size              : a symbol plus an integer
                        e.g., either >999, either <999, either =999, either <=999 either >=999

                        use the following suffixes :

                           "kB" : 1000
                           "KB" : 1000
                           "MB" : 1000**2
                           "GB" : 1000**3
                           "TB" : 1000**4
                           "PB" : 1000**5
                           "EB" : 1000**6
                           "ZB" : 1000**7
                           "YB" : 1000**8
                           "KiB": 1024
                           "MiB": 1024**2
                           "GiB": 1024**3
                           "TiB": 1024**4
                           "PiB": 1024**5
                           "EiB": 1024**6
                           "ZiB": 1024**7
                           "YiB": 1024**8

    [target]                  : parameters about target directory 
    path                      : a string without any quotation mark.

    name of the target files  : a string using keywords which will be replaced by their value
                                see the create_target_name() function.

                known keywords :

                %%h  : hashid (e.g. GwM5NKzoZ76oPAbWwX7od0pI66xZrOHI7TWIggx+xFk=)

                %%f  : source name (with the path), without the extension

                %%ff : source name (with the path), without the extension
                       [see below, no forbidden characters]

                %%p  : source path

                %%pp : source path
                       [see below, no forbidden characters]

                %%e  : source extension, without the dot character (e.g. jpg / no dot character !)

                %%ee : source extension, without the dot character (e.g. jpg / no dot character !)
                       [see below, no forbidden characters]

                %%s  : size (e.g. 123)

                %%dd : date (e.g. 2015_09_25_06_50)
                       [see below, no forbidden characters]

                %%i  : database index (e.g. 123)

                %%t  : timestamp of the file (integer, e.g. 1445584562)

                %%ht : timestamp of the file (hexadecimal, e.g. 5629DED0)

                n.b. : keywords with a reduplicated letter (%%pp, %%ff, ...) are builded against
                       a set of illegal characters, replaced by "_". 

##(8.3) logfile
Can be filled with many informations (verbosity="high") or less informations (verbosity="low"). See in documentation:configuration file the explanations about the log verbosity.

The logfile is opened by logfile_opening. The program writes in it via msg() if _for_logfile is
set to True. If the logfile becomes too large (see the "[log file]maximal size" value) the logfile
if backuped and flushed.

##(8.4) selection

    SELECT is filled by fill_select(), a function called by action__select(). SELECT is a dictionary with hashid as keys and SELECTELEMENT as values.

    Definition of SELECTELEMENT :
      SELECTELEMENT = namedtuple('SELECTELEMENT', ["complete_name",
                                                   "path",
                                                   "filename_no_extens",
                                                   "extension",
                                                   "size",
                                                   "date"])

      !!! an "extension" stored in SELECTELEMENT does not start with a dot (".") !!!

    FILTERS is a dictionary with a (int)filter_index as a key and a dict as values.
    This dict may be empty or contain the following keys/values : 
      FILTERS["name"] = re.compile(...)
      FILTERS["size"] = a string like ">999", initial symbol in ('=', '<', '>', '<=', '>=')
      FILTERS["date"] = a string like '>2015-09-17 20:01', initial symbol in ('=', '<', '>', '<=', '>=')
    FILTERS is filled by read_filters().

##(8.5) database
In every target directory a database is created and filled. Its name is set by the
global variable DATABASE_NAME.
TARGET_DB is initialized by read_target_db(); hashid:(partialhashid, size, fullname)
    
    o hashid varchar(44) PRIMARY KEY UNIQUE : hashid (of all the file)
    o partialhashid varchar(44)             : hashid (of the beginning of the file)
    o size integer                          : size
    o name text UNIQUE                      : (target) name
    o sourcename text                       : complete path + name + extension
    o sourcedate integer                    : epoch time
    o tagsstr text                          : a list of tags separated by the TAG_SEPARATOR
                                              symbol.

##(8.6) trash directory
the deleted files are placed in a trashed directory placed inside the target directory. The
trash name is defined in the configuration file.


##(8.7) the functions

    o  action__add()                        : add the source files to the target
                                              path.
    o  action__addtag()                     : add one tag to the tags' string of the given files
    o  action__cleandbrm()                  : remove from the database the missing files
    o  action__downloadefaultcfg()          : download the default configuration file
    o  action__findtag()                    : display the files tagged with the _tag parameter
                                              which is a simple string, not a regex.
    o  action__infos()                      : display informations about the source
                                              and the target directory
    o  action__new()                        : create a new target directory
    o  action__rebase()                     : copy a target directory into a new one
    o  action__rebase__files()              : --rebase : select the files to be copied.
    o  action__rebase__write()              : --rebase : write the files into the new target direc.
    o  action__reset()                      : --reset : remove the database and the files in the
                                              target directory
    o  action__rmnotags()                   : Remove all files if they have no tags.
    o  action__rmtags()                     : remove the entire tags' string of some files
    o  action__select()                     : fill SELECT and SELECT_SIZE_IN_BYTES and
                                              display what's going on.
    o  action__settagsstr()                 : modify the tags string in the target directory,
                                              overwriting ancient tags.
    o  action__target_kill()                : delete a filename from the target directory
                                              and from the database
    o  action__whatabout()                  : is a file/[are the files in a dir] already in the
                                              target directory ?
    o  add_keywords_in_targetstr()          : replace some keywords by the value given as parameters
                                              in order to make strings used to create the target files
    o  backup_logfile()                     : copy a logfile into a backuped file.
    o  check_args()                         : check the arguments of the command line.
    o  create_empty_db()                    : create an empty database.
    o  create_subdirs_in_target_path()      : create the expected subdirectories in ARGS.targetpath .
    o  create_target_name()                 : create the name of a file (a target file)
                                              from various information (filename, ...)
    o  create_target_name_and_tags()        : create the name and the tags of a file (a target file)
                                              from various information (filename, ...)
    o  create_target_tags()                 : create the tags of a file (a target file)
                                              from various information (filename, ...)
    o  draw_table()                         : draw a table with some <_rows> and fill it with _data.
    o  eval_filter_for_a_file()             : evaluate a file according to a filter
    o  fill_select()                        : fill SELECT and SELECT_SIZE_IN_BYTES from
                                              the files stored in SOURCE_PATH.
    o  fill_select__checks()                : final checks at the end of fill_select()
    o  get_disk_free_space()                : return the available space on disk
    o  get_database_fullname()              : return the full name of the db stored in ARGS.targetpath
    o  get_filename_and_extension()         : return (filename_no_extension, extension)
    o  get_logfile_fullname()               : return the logfile fullname.
    o  goodbye()                            : display the goodbye message
    o  hashfile64()                         : return the footprint of a file, encoded
                                              with the base 64.
    o  logfile_opening()                    : open the log file
    o  main()                               : main entry point
    o  main__actions()                      : call the different actions required by the arguments
    o  main__actions_tags()                 : call the different actions required by the arguments
    o  main__warmup()                       : initializations
    o  modify_the_tag_of_some_files()       : modify the tag(s) of some files
    o  msg()                                : display a message on console, write the
                                              same message in the log file.

    o  normpath()                           : return a human-readable, normalized version of a path
    o  possible_paths_to_cfg()              : return a list of the (str)paths to the config file
    o  read_command_line_arguments()        : read the command line arguments
    o  read_parameters_from_cfgfile()       : read the configuration file
    o  read_filters()                       : initialize FILTERS from the configuration file
    o  read_target_db()                     : read the database stored in the target
                                              directory and initialize TARGET_DB.
    o  remove_illegal_characters()          : replace some illegal characters by the
                                              underscore character.
    o  shortstr()                           : shorten a string
    o  show_infos_about_source_path()       : display informations about source path
    o  show_infos_about_target_path()       : display informations about target path
    o  size_as_str()                        : return a size in bytes as a human-readable
                                              string
    o  tagsstr_repr()                       : return an improved representation of a tags string
    o  is_ntfs_prefix_mandatory()           : return True if the _path is a path in a systemfile
                                              requiring the NTFS prefix for long filenames.
    o  thefilehastobeadded__db()            : return True if the file isn't already known in the
                                              database
    o  thefilehastobeadded__filters()       : return True if a file can be choosed and added to
                                            : the target directory
    o  thefilehastobeadded__filt_date()     : a part of thefilehastobeadded__filters()
    o  thefilehastobeadded__filt_name()     : a part of thefilehastobeadded__filters()
    o  thefilehastobeadded__filt_size()     : a part of thefilehastobeadded__filters()
    o  welcome()                            : display a welcome message on screen
    o  welcome_in_logfile()                 : display a welcome message in the log file
    o  where_is_the_configfile()            : return the config file name from ARGS.configfile or
                                              from the paths returned by possible_paths_to_cfg().
