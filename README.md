# Demo Magic

demo-magic.sh is a handy shell script that enables you to script repeatable demos in a bash environment so you don't have to type as you present. Rather than trying to type commands when presenting you simply script them and let demo-magic.sh run them for you.

## Features
- Simulates typing. It looks like you are actually typing out commands
- Allows you to actually run commands or pretend to do so.
- Can hide commands from presentation. Useful for behind the scenes stuff that doesn't need to be shown.

## Functions

### pe
Print and Execute.

1. Waits for you to press <kbd>ENTER</kbd> (unless -n is passed).
1. Then simulates typing the command you gave it.
1. Then pauses until you press <kbd>ENTER</kbd>.
1. Then runs the command.

```bash
#!/bin/bash

pe "ls -l"
```

### pei
Print and Execute immediately.

1. Simulates typing the command you gave it.
1. Then pauses until you press <kbd>ENTER</kbd>.
1. Then runs the command.

```bash
#!/bin/bash

pei "ls -l"
```

### p
Print only.

1. Waits for you to press <kbd>ENTER</kbd> (unless -n is passed).
1. Then simulates typing the command you gave it.
1. Then pauses until you press <kbd>ENTER</kbd>.

```bash
#!/bin/bash

p "ls -l"
```

### wait
Waits for the user to press <kbd>ENTER</kbd>.

If `PROMPT_TIMEOUT` is defined and > 0 the demo will automatically proceed after the amount of seconds has passed.

```bash
#!/bin/bash

# Will wait until user presses enter
PROMPT_TIMEOUT=0
wait

# Will wait max 5 seconds until user presses
PROMPT_TIMEOUT=5
wait

```

With the argument `--prompt` the prompt is used for the input command, useful when used with `p` command. Example:

```bash
#!/bin/bash

p "# Test for the wait with prompt..."
PROMPT_TIMEOUT=5 wait --prompt
pei "ls -l"
```

Without the `--prompt` option a similar effect (but less nice) can be obtained with:
```bash
p "# Test for the wait with prompt..."
P ""
PROMPT_TIMEOUT=5 wait
pei "ls -l"
```


### cmd
Enters script into interactive mode and allows newly typed commands to be executed within the script
```
#!/bin/bash

cmd
```

## Getting Started
Create a shell script and include demo-magic.sh

```bash
#!/bin/bash

########################
# include the magic
########################
. demo-magic.sh

# hide the evidence
clear

# Put your stuff here
```

Then use the handy functions to run through your demo.

## Command line usage
demo-magic.sh exposes some options to your script.
- `-d` - disable simulated typing. Useful for debugging
- `-h` - prints the usage text
- `-n` - set no default waiting after `p` and `pe` functions
- `-w` - set no wait timeout after `p` and `pe` functions

```bash
$ ./my-demo.sh -h

Usage: ./my-demo.sh [options]

  Where options is one or more of:
  -h  Prints Help text
  -d  Debug mode. Disables simulated typing
  -n  No wait
  -w  Waits max the given amount of seconds before proceeding with demo (e.g. `-w5`)
```

## Useful Tricks

### Faking network connections
Network connections during demos are often unreliable. Try and fake whatever commands would rely on a network connection. For example: Instead of trying to install node modules in a node.js application you can fake it. You can install the node_modules at home on your decent network. Then rename the directory and pretend to install it later by symlinking. If you want to be thorough you can capture the output of npm install into a log file then cat it out later to simulate the install.

```bash
#!/bin/bash

########################
# include the magic
########################
. demo-magic.sh

# hide the evidence
clear

# this command is typed and executed
pe "cd my-app"

# this command is merely typed. Not executed
p "npm install"

# this command runs behind the scenes
ln -s cached_node_modules node_modules

# cat out a log file that captures a previous successful node modules install
cat node-modules-install.log

# now type and run the command to start your app
pe "node index.js"
```

### No waiting
The -n _no wait_ option can be useful if you want to print and execute multiple commands.

```bash
# include demo-magic
. demo-magic.sh -n

# add multiple commands
pe 'git status'
pe 'git log --oneline --decorate -n 20'
```

However this will oblige you to define your waiting points manually e.g.
```bash
...
# define waiting points
pe 'git status'
pe 'git log --oneline --decorate -n 20'
wait
pe 'git pull'
pe 'git log --oneline --decorate -n 20'
wait
```
