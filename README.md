# git-101
short introduction to git/github

# What is Git and Github?

[Git](https://git-scm.com/) is an open-source version control system. It can be used on a local machine, independent of Github. 

[Github](https://github.com/) is a cloud-based Git repository hosting service.

Git can exist without Github. Github cannot exist without Git. 

# Downloading Git

First, download Git on your local machine. See the [Git website](https://git-scm.com/) for download links.

## Mac

If you have [homebrew](https://docs.brew.sh/Installation) installed, run the following in your terminal:

```
brew install git
```

## Windows

See instructions [here](https://git-scm.com/download/win).

## HPC 

Git should already be installed on the USC HPC. By default, an older version of Git should be automatically loaded when you log in.

Check the version:
```
git --version
```

As of March 14, 2022, this results in the following output:
```
git version 1.8.3.1
```

If you want to use the use a more recent version of Git on HPC, you will need to find the versions that are installed and then load the desired version. 

Run the following in the terminal to find available versions of Git: 
```
module spider git
```

For example, this should return something like this: 
```
-----------------------------------------------------------------------------------------------------
  git:
-----------------------------------------------------------------------------------------------------
     Versions:
        git/2.25.0
        git/2.31.1
     Other possible modules matches:
        libgit2  websockify-git

-----------------------------------------------------------------------------------------------------
  To find other possible module matches execute:

      $ module -r spider '.*git.*'

-----------------------------------------------------------------------------------------------------
  For detailed information about a specific "git" package (including how to load the modules) use the module's full name. Note that names that have a trailing (E) are extensions provided by other modules.
  For example:

     $ module spider git/2.31.1
-----------------------------------------------------------------------------------------------------
```
Say for example, you want to load Git version 2.31.1. 

First, check to see if there are any prerequisate modules that need to be loaded before git/2.31.1 can be loaded:
```
module spider git/2.31.1
```
This returns the following:
```
------------------------------------------------------------------------------------------------------------------------------------------
  git: git/2.31.1
------------------------------------------------------------------------------------------------------------------------------------------

    You will need to load all module(s) on any one of the lines below before the "git/2.31.1" module is available to load.

      gcc/11.2.0
 
    Help:
      Git is a free and open source distributed version control system
      designed to handle everything from small to very large projects with
      speed and efficiency.
```

This means that gcc/11.2.0 must first be loaded. 

```
module load gcc/11.2.0
```

Now, you should be able to load git/2.31.1:

```
module load git/2.31.1
```

Check that the newer version of  git is loaded: 

```
module list
```

It should return something like this: 

```
Currently Loaded Modules:
  1) usc   2) python/3.9.6   3) openblas/0.3.18   4) pmix/3.2.1   5) openmpi/4.1.1   6) gcc/11.2.0   7) git/2.31.1

Inactive Modules:
  1) cuda/10.1.243
```

Run 'module save' to save the modules to your default collection. 

This means that you can run 'module restore' next time you login to HPC to restore the saved modules. 

E.g.

```
(base) [kojoseph@discovery1 ~]$ module restore
Restoring modules from user's default
(base) [kojoseph@discovery1 ~]$ module list

Currently Loaded Modules:
  1) usc            3) openblas/0.3.18   5) openmpi/4.1.1   7) git/2.31.1
  2) python/3.9.6   4) pmix/3.2.1        6) gcc/11.2.0
```

# Git configuration 
After installing git, we need to modify our git configurations. 

There are three scopes of config: system, global, and local. 

>*System config* controls settings for all users and all repositories for the whole system. 

>*Global config* controls settings for the current user and all repositories for this user. 

>*Local config* controls settings for a specific repository. 

Settings at the smaller scope will supercede settings at the larger scope. For example, local config settings will take precedence over the global config settings in the case where there are two conflicting settings. 

Let's fist add user information to the global config file. 
```
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```
Note: The user info supplied here has no connection to your user info on github. It is just used for commit logs, so people can see who made the changes and what the contact info should be for that person.

Let's check to see that the global config has been modified by using: `git config --global --list`

It should return something like...
```
user.name=John Doe
user.email=johndoe@gmail.com
...
```

Let's change one more setting in global config. 

Unfortunately, Windows and Mac/Linux deal with newlines in text differently. This can cause issues when handing off files between users of different OS.

The [git documentation](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration) summarizes this issue as follows:
>This is because Windows uses both a carriage-return character and a linefeed character for newlines in its files, whereas macOS and Linux systems use only the linefeed character. This is a subtle but incredibly annoying fact of cross-platform work; many editors on Windows silently replace existing LF-style line endings with CRLF, or insert both line-ending characters when the user hits the enter key.

To deal with this, change the config as follows: 
>If Windows: `git config --global core.autocrlf true`
>If Mac/Linux: `git config --global core.autocrlf input`

For more details about this LF vs. CRLF issue, see details [here](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration) under "core.autocrlf"

Let's view the config file to make sure that this has been changed: `git config --global --edit`

Notice that the config file opens in your default text editor this time since we used the --edit flag instead of the --list flag. 
You should see something like this: 
```
[user]
        name = John Doe
        email = johndoe@gmail.com
[core]
        autocrlf = input
```

Exit the config file once you're satisfied (e.g., ':q' in vim). 

There are a lot more settings that you can alter in config, but this is good enough to get started. See the [git-config doc](https://git-scm.com/docs/git-config) for full details.

# Github authentication
In order to establish the connection between Git and Github, we must first authenticate our Github credentials using a secure protocol. This is done either through HTTPS or SSH. 

For sake of example, we will use HTTPS here and cache our credentials using (GitHub CLI)[https://docs.github.com/en/get-started/getting-started-with-git/caching-your-github-credentials-in-git] so we don't have to enter our authentication token (i.e., password) each time we interface with Github on the terminal. 

