# git-101
short introduction to git/github

[**GIT CHEAT SHEET**](https://education.github.com/git-cheat-sheet-education.pdf)

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
git config --global user.name "John Doe"
git config --global user.email "johndoe@gmail.com"
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

For sake of example, we will use HTTPS here and cache (i.e. store) our credentials using [GitHub CLI](https://docs.github.com/en/get-started/getting-started-with-git/caching-your-github-credentials-in-git) so we don't have to enter our authentication token (i.e., password) each time we interface with Github on the terminal. 

First, generate a personal access token on Github. Follow the instructions [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) and copy/paste the token somewhere accessible to you. 

Next, we install [Github CLI](https://github.com/cli/cli#installation) so that we cache our credentials. On HPC, we can do this via conda:  
>First, create a new conda environment: `conda create --name gh`  
>Activate the new environment: `conda activate gh`  
>Install Github CLI: `conda install gh --channel conda-forge`  

Now, we should be able to use Github CLI (i.e. 'gh') to [cache the authentication](https://docs.github.com/en/get-started/getting-started-with-git/caching-your-github-credentials-in-git):
In the command line, enter: `gh auth login`

Answer the series of questions that appear in the terminal. 
- When asked for preferred protocol, select HTTPS. 
- When asked how you would like to authenticate GitHub CLI, select authentication token. 
- The authentication token is the "personal access token" you generated earlier on your Github page. Copy/paste it from wherever you saved it last. 

The output in the terminal should look something like this: 
```
(gh) [johndoe@discovery2 git-test]$ gh auth login
? What account do you want to log into? GitHub.com
? You're already logged into github.com. Do you want to re-authenticate? Yes
? What is your preferred protocol for Git operations? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Paste an authentication token
Tip: you can generate a Personal Access Token here https://github.com/settings/tokens
The minimum required scopes are 'repo', 'read:org', 'workflow'.
? Paste your authentication token: ****************************************
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as johndoe
```

Congrats! You're now authenticated and your credentials have been cached so you don't have to enter a token every time.

To confirm, type `git config --global --list` in the terminal. You should see some information now added to your config file about where your authentication credentials have been cached. 
```
(gh) [johndoe@discovery2 git-test]$ git config --global --list
user.name=John Doe
user.email=johndoe@gmail.com
core.autocrlf=input
credential.https://github.com.helper=
credential.https://github.com.helper=!/home1/johndoe/.conda/envs/gh/bin/gh auth git-credential
credential.https://gist.github.com.helper=
credential.https://gist.github.com.helper=!/home1/johndoe/.conda/envs/gh/bin/gh auth git-credential
```

# Creating a Git Repository (i.e. "repo")
Let's make our first demo Git repo on our local machine (e.g. HPC, laptop, desktop, etc.):  

Create a new directory called "demo": `mkdir demo`<br/>
Change into the directory: `cd demo`<br/>
Create a README: `touch README.md`<br/>
Use your text editor (vim, vi, nano, etc.) of choice to add title and description to the README: `vim README.md`<br/>
e.g.
```
# demo

This is a git repo demo.
```
Initialize Git: `git init`<br/>
Check status: `git status`
At this point you should see the following:
```
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	    README.md
nothing added to commit but untracked files present (use "git add" to track)
```
This tells you a few things:
- Which branch you are on -> master
- Untracked files -> README is not being tracked by git yet

"Committing" is like taking a snapshot of your repo. In order to commit, you must first add the files you want to commit to the staging area using the "add" command: 
```
(base) [johndoe@discovery1 demo]$ git add .
(base) [johndoe@discovery1 demo]$ git commit -m "add readme"
[master (root-commit) 3548f2c] add readme
 1 file changed, 3 insertions(+)
 create mode 100644 README.md
(base) [johndoe@discovery1 demo]$ git status
# On branch master
nothing to commit, working directory clean
```
![image](https://user-images.githubusercontent.com/3221060/158644532-495bf389-3faa-474b-a1e3-b2bce8515620.png)

Congrats! You've now officially made your first Git commit. 

# Pushing to Github
So now you have a local git repository, but how do you sync to github? 

First, go to your github repositories and click the green "New" button to create a new repo: 
![image](https://user-images.githubusercontent.com/3221060/158646551-9d9d3afa-6b9e-438a-a73a-6941014fa8e5.png)

Name the repo and click create. The following page should appear: 
![image](https://user-images.githubusercontent.com/3221060/158646653-19cc4edd-b634-433e-b093-e4bec5f4960a.png)

Since we started by creating a git repo on our local machine, we will be following the instructions under "…or push an existing repository from the command line"
The first line adds the git URL as an alias, "origin":
```
git remote add origin https://github.com/josephko91/demo.git
```
The second line changes the name of the "master" branch to "main" (see reason why [here](https://sfconservancy.org/news/2020/jun/23/gitbranchname/)):
```
git branch -M main
```
This pushes the local commits to the remote Github repo. The -u flag adds an upstream reference for the specified branch (i.e. links the local and remote branch), so that in the future, you can `git push` or `git pull` without specifying the local/remote branch:
```
git push -u origin main
```

We can also confirm that the branch name has been changed to "main": 
```
(base) [johndoe@discovery1 demo]$ git status
# On branch main
nothing to commit, working directory clean
```

Refresh the page on your "demo" github repo page and you shold now see the remote repo updated with your readme:
![image](https://user-images.githubusercontent.com/3221060/158651805-6932187b-56fd-4592-b06b-20e7dcf1139d.png)

Congrats! You have successfully finished your first "push" from your local git repo to the remote Github repo.

# Starting a repo from Github (remote -> local)
What if we wanted to go the other way around - start a remote repo on Github and then sync with our local machine? We'll do an example here below. 

Let's create a new repo in Github and call it "fav-foods": 
![image](https://user-images.githubusercontent.com/3221060/158652799-285b427f-d51c-42e4-83c9-51140db6279a.png)

One added benefit of creating a repo on Github first is that you can easily add and modify the README, gitignore, and license in the browser. 

You should see the repo immediately created:
![image](https://user-images.githubusercontent.com/3221060/158653045-5a9651c7-5a11-4e28-8e77-f0c127a01b2a.png)

We now need to clone the repo to our local machine.

First, copy the HTTPS link from your Github repo: 
![image](https://user-images.githubusercontent.com/3221060/158653690-fd711589-c89b-409e-ac88-779a3ded2f9e.png)

Then go to your terminal and make sure you're not in another git repo already.

Then type `git clone https://github.com/josephko91/fav-foods.git`

This will copy the contents of the fav-food remote repo onto your local machine in your current directory. 

# What is .gitignore?
gitignore is a hidden file in your project directory that is essentially a list of all the types of files and/or directories that git should NOT track. For [example](https://www.atlassian.com/git/tutorials/saving-changes/gitignore):
- large data files
- dependency caches, such as the contents of /node_modules or /packages
- compiled code, such as .o, .pyc, and .class files
- build output directories, such as /bin, /out, or /target
- files generated at runtime, such as .log, .lock, or .tmp
- hidden system files, such as .DS_Store or Thumbs.db
- personal IDE config files, such as .idea/workspace.xml

You can edit your gitignore file using a text editor of choice e.g., `vim .gitignore`

The nice thing about starting a repo in Github (see above) is that there is the option to use boiler-plate gitignore files based on common languages (e.g., python, java, etc.). These gitignore templates are usually a good starting point and you can modify the gitignore as needed as your projects evolve.

# Branching and merging

(coming soon)

# Pull requests
(coming soon)
## What is a pull request
(coming soon)
## How to make a pull request
(coming soon)

