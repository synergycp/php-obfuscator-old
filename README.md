Forked from [YAK Pro - Php Obfuscator](https://github.com/pk-fr/yakpro-po)

### Main Features:

- Removes all comments, indentation, and generates a single line program file.
- Obfuscates **if, else, elseif, for, while, do while** by replacing them with **if goto** statements.
- Obfuscates string literals.
- Scrambles names for:
  - Variables, Functions, Constants.
  - Classes, Interfaces, Traits.
  - Properties, Methods.
  - Namespaces.
  - Labels.
- Shuffles Statements.

- Recursivly obfuscates a project's directory.
- Makefile like, timestamps based mechanism, to re-obfuscate only files that were changed since last obfuscation.
- Many configuration options that let you have **full control** of what is obfuscated within your project!


## Setup:
    1. Prerequisites: git and php-cli (command line interface) packages. 
       on ubuntu: (adapt according your linux distribution) 
       # apt-get install git 
       # apt-get install php5-cli
       do not forget to install all other php modules that you are using within your software:
            for example: apt-get install php5-mysql if you are using mysql... 
    2. Navigate to the directory where you want to install yakpro-po (/usr/local is a good idea): 
       # cd /usr/local 
    3. Then retrieve from GitHub: 
       # git clone --recursive https://github.com/pk-fr/yakpro-po.git
    4. Go to the yakpro-po directory: 
       # cd yakpro-po 
    5. Check that yakpro-po.php has execute rights, otherwise:
                                            # chmod a+x yakpro-po.php 
    6. Create a symbolic link in the /usr/local/bin directory
       # cd /usr/local/bin 
       # ln -s /usr/local/yakpro-po/yakpro-po.php yakpro-po 
    7. You can now run yakpro-po 
       # yakpro-po --help 
       # yakpro-po test.php 

    Modify a copy of the yakpro-po.cnf to fit your needs...
    Read the "Configuration file loading algorithm" section of this document
    to choose the best location suiting your needs!

    That's it! You're done!

####

## Usage:

`yakpro-po`
Obfuscates according configuration file!
(See configuration file loading algorithm)

`yakpro-po source_filename`
Obfuscates code to stdout

`yakpro-po source_filename -o target_filename`
Obfuscates code to target_filename

`yakpro-po source_directory -o target_directory`
Recursivly obfuscates code to target_directory/yakpro-po (creates it if it does not already exist).

`yakpro-po --config-file config_file_path`
According to config_file_path.

`yakpro-po --clean`
Requires target_directory to be present in your config file!
Recursivly removes target_directory/yakpro-po


## Other command line options:
(override config file settings)

    --silent                            do not display Information level messages.
    --debug                             (internal debugging use) displays the syntax tree.

    -s or
    --no-strip-indentation              multi line output
    --strip-indentation                 single line output

    --no-shuffle-statements             do not shuffle statements
    --shuffle-statements                       shuffle statements

    --no-obfuscate-string-literal       do not obfuscate string literals
    --obfuscate-string-literal                 obfuscate string literals

    --no-obfuscate-loop-statement       do not obfuscate loop statements
    --obfuscate-loop-statement                 obfuscate loop statements

    --no-obfuscate-if-statement         do not obfuscate if statements
    --obfuscate-if-statement                   obfuscate if statements

    --no-obfuscate-constant-name        do not obfuscate constant names
    --obfuscate-constant-name                  obfuscate constant names

    --no-obfuscate-variable-name        do not obfuscate variable names
    --obfuscate-variable-name                  obfuscate variable names

    --no-obfuscate-function-name        do not obfuscate function names
    --obfuscate-function-name                  obfuscate function names

    --no-obfuscate-class_constant-name  do not obfuscate class constant names
    --obfuscate-class_constant-name            obfuscate class constant names

    --no-obfuscate-class-name           do not obfuscate class names
    --obfuscate-class-name                     obfuscate class names

    --no-obfuscate-interface-name       do not obfuscate interface names
    --obfuscate-interface-name                 obfuscate interface names

    --no-obfuscate-trait-name           do not obfuscate trait names
    --obfuscate-trait-name                     obfuscate trait names

    --no-obfuscate-property-name        do not obfuscate property names
    --obfuscate-property-name                  obfuscate property names

    --no-obfuscate-method-name          do not obfuscate method names
    --obfuscate-method-name                    obfuscate method names

    --no-obfuscate-namespace-name       do not obfuscate namespace names
    --obfuscate-namespace-name                 obfuscate namespace names

    --no-obfuscate-label-name           do not obfuscate label names
    --obfuscate-label-name                     obfuscate label names

    --scramble-mode     identifier|hexa|numeric         force scramble mode
    --scramble-length   length ( min=2; max = 16 for scramble_mode=identifier,
                                        max = 32 for scramble_mode = hexa or numeric)

    --whatis scrambled_name             retrieves original symbol from obfuscation context.
                                        (usefull for debugging your code when you give away
                                        obfuscated code, and keep the same obfuscation context).
                                        Tip: do not include the $ symbol, or use \$ because
                                        $ has special meaning in shell.

    -h or
    --help                              displays help.

####

## YOU MUST BE AWARE OF THE FOLLOWING:
    If your obfuscated software makes use of external libraries
    that you do not obfuscate along with your software:

    if the library consists of functions:
            set the $conf->obfuscate_function_name to false in your yakpro-po.cnf config file,
            or declare all the functions names you are using in $conf->t_ignore_functions
            example : $conf->t_ignore_functions = array('my_func1','my_func2');

    if the library consists of classes :
            set the $conf->obfuscate_class_name,
                    $conf->obfuscate_property_name,
                    $conf->obfuscate_method_name
            to false in your yakpro-po.cnf config file...
            ... or declare all the classes, properties, methods names you are using in
                    $conf->t_ignore_classes,
                    $conf->t_ignore_properties,
                    $conf->t_ignore_methods.

    This is also true for PDO::FETCH_OBJ that retrieves properties from external source
    (i.e. database columns).

## Hints for preparing your Software to be run obfuscated

    At first you can test obfuscating only variable names...


    If you obfuscate functions, do not use indirect function calls like
        $my_var = 'my_function';
        $my_var();
    or put all the function names you call indirectly in the $conf->t_ignore_functions array!


    Do not use indirect variable names!
        $$my_var = something;
    or put all the variable names you use indirectly in the $conf->t_ignore_variables array!


    Do not use PDO::FETCH_OBJ  but use PDO::FETCH_ASSOC instead!
    or disable properties obfuscation in the config file.


    If you use the define function for defining constants, the only allowed form is when the
    define function has exactly 2 arguments, and the first one is a litteral string!
    You MUST disable constants obfuscation in the config file, if you use any other forms
    of the define function!
    There is no problem with the const MY_CONST = something; form!


## Performance considerations

    Except for the statements shuffling obfuscation option,
    the obfuscated program speed is almost the same than the original one.

    $conf->shuffle_stmts    is set to true by default.

    If you encounter performance issues, you can either set the option to false,
    or fine tune the shuffle parameters with the associated options.

    You must know that the lesser the chunk size, the better the obfuscation,
    and the lower your software performance!

    (during my own tests, the maximum of obfuscation costs me about 13% of performance)

    You can tune it as you wish!




