# git-101
short introduction to git/github

# What is Git and Github?

[Git](https://git-scm.com/) is an open-source version control system. It can be used on a local machine, independent of Github. 

[Github](https://github.com/) is a cloud-based Git repository hosting service.

Git can exist without Github. Github cannot exist without Git. 

# Downloading Git

First, download Git on your local machine. See the [Git website](https://git-scm.com/) for download links.

## Mac

If you have [homebrew](https://docs.brew.sh/Installation) installed, simply run the following in your terminal:

```
brew install git
```

## Windows

See instructions [here](https://git-scm.com/download/win).

## HPC 

Git should already be installed on the USC HPC. By default, an older version of Git be automatically loaded when you log in.

Check the version:
```
git --version
```

As of March 14, 2022, this results in the following output:
```
git version 1.8.3.1
```

If you want to use the use a more recent version of Git on HPC, you will need to find the version that is installed and then load it up as a module. 

To find the available version of Git: 
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
