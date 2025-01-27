It seems we guessed right! It appears to be a carbon copy of the website running on the webserver. If there are any differences here then they are clearly not going to be immediately visible, which means we may need to look at fuzzing this site through two proxies...

Before we start messing around with fuzzing tools though, let's take a step back and think about this.

We know from the brief that *Thomas has been using git server to version control his projects -- just because the version on the webserver isn't up to date, doesn't mean that he hasn't been committing to the repo more regularly!* In other words, rather than fuzzing the server, we might be able to just download the source code for the site and review it locally.

Ideally we could just clone the repo directly from the server. This would likely require credentials, which we would need to find. Alternatively, given we already have local admin access to the git server, we could just download the repository from the hard disk and re-assemble it locally which does not require any (further) authentication.

For the sake of practice, let's use this latter option.

> [!Question]
>Use your WinRM access to look around the Git Server. What is the absolute path to the `Website.git` directory?
>`C:\Gitstack\Repositories\Website.git`

Use `evil-winrm` to download the entire directory.

From the directory above Website.git, use:  
`download PATH\TO\Website.git`

Be warned -- this will take a while, but should complete after a minute or two!

> [!Note]
>You may need to specify the local path as well as the absolute path to the Website.git directory!

Exit out of evil-winrm -- you should see that a new directory called Website.git has been created locally. If you enter into this directory you will see an oddly named subdirectory (the same as the answer to question 1 of this task).

## Git

Rename this _subdirectory_ to `.git`. #Attacking_Machine 

```
mv Website.git .git
```

Git repositories always contain a special directory called `.git` which contains all of the meta-information for the repository. This directory can be used to fully recreate a readable copy of the repository, including things like version control and branches. If the repository is local then this directory would be a part of the full repository -- the rest of which would be the items of the repository in a human-readable format; however, as the `.git` directory is enough to recreate the repository in its entirety, the server doesn't need to store the easily readable versions of the files. This means that what we've downloaded isn't actually the full repository, so much as the building blocks we can use to recreate the repo (which is exactly what happens when using `git clone` to create a local copy of a repo!).  

### GitTools

In order to extract the information from the repository, we use a suite of tools called GitTools.

Clone the GitTools repository into your current directory using:  
`git clone https://github.com/internetwache/GitTools`  

The GitTools repository contains three tools:

- **Dumper** can be used to download an exposed `.git` directory from a website should the owner of the site have forgotten to delete it.

- **Extractor** can be used to take a local `.git` directory and recreate the repository in a readable format. This is designed to work in conjunction with the Dumper, but will also work on the repo that we stole from the Git server. Unfortunately for us, whilst Extractor _will_ give us each commit in a readable format, it will not sort the commits by date  .

- **Finder** can be used to search the internet for sites with exposed `.git` directories. This is significantly less useful to an ethical hacker, although may have applications in bug bounty programmes.

Let's use Extractor to obtain a readable format of the repository!

The syntax for Extractor is as follows:  
`./extractor.sh REPO_DIR DESTINATION_DIR`

This is slightly confusing, so explaining each option:

- The `REPO_DIR` is the directory _containing_ the `.git` directory for the repository. Note that this is not the `.git` directory itself. Extractor looks for a `.git` directory _inside_ the specified directory (which is why we had to change the original name of the directory to ".git")
- The `DESTINATION_DIR` is the subdirectory into which the repository will be created  
    

For example, if we cloned the GitTools repo into the same directory as the `.git` directory we downloaded from the Git Server, we can extract the contents of the stolen repository into a subdirectory called "Website" using:  
`GitTools/Extractor/extractor.sh . Website`

This uses the current directory "`.`" (as the parent of the `.git` directory) and extracts into a newly created `Website` subdirectory.

![[The Wonders of Git-20250127205839042.webp]]

Recreate the repository -- we will perform some code analysis in the next task!

Let's head into the newly recreated repository. We see three directories:

![[The Wonders of Git-20250127211026657.webp]]

Each of these corresponds to a commit; however, as mentioned previously, these are not sorted by date...

It's up to us to piece together the order of the commits. Fortunately there are only three commits in this repository, and each commit comes with a `commit-meta.txt` file which we can use to get an idea of the order.

We could just cat each of these files out separately, but we may as well do it the fancy way with a bash one-liner:  
`separator="======================================="; for i in $(ls); do printf "\n\n$separator\n\033[4;1m$i\033[0m\n$(cat $i/commit-meta.txt)\n"; done; printf "\n\n$separator\n\n\n"`  

This gives us the three `commit-meta.txt` files in a nicely formatted order:

![[The Wonders of Git-20250127211158745.webp]]


Here we can see three commit messages: `Updated the filter`, `Initial Commit for the back-end`, and `Static Website Commit`.

> [!Note]
>The number at the start of these directories is arbitrary, and depends on the order in which GitTools extracts the directories. What matters is the hash at the end of the filename.

Logically speaking, we can guess that these are currently in reverse order based on the commit message; however, we could also check the parent value of each commit. Starting at the only commit without a parent (which must be the initial commit), we can work down the tree in stages like so:

![[The Wonders of Git-20250127211731123.webp]]

We find the commit that has no parent (`70dde80cc19ec76704567996738894828f4ee895`), and check to see which of the other commits specifies it as a direct parent (`82dfc97bec0d7582d485d9031c09abcb5c6b18f2`). We then repeat the process to find the full commit order:

1. 70dde80cc19ec76704567996738894828f4ee895
2. 82dfc97bec0d7582d485d9031c09abcb5c6b18f2
3. 345ac8b236064b431fa43f53d91c98c4834ef8f3

We _could_ also do this by checking the timestamps attached to the commits (in UNIX format, after the emails); however, it is possible to fake these. Feel free to use them, but be aware that they may not always be accurate.

If that didn't make sense, don't worry!

The short version is: the most up to date version of the site stored in the Git repository is in the `NUMBER-345ac8b236064b431fa43f53d91c98c4834ef8f3` directory.

---

# Your job

1. **Move file to Website.git** File was downloaded as `Website.git'` and needs to be `.git`. #Attacking_Machine 
```
mv Website.git .git  
```

2. **Use extractor within** `Website.git`.
```
GitTools/Extractor/extractor.sh . Website
```

 ![[The Wonders of Git-20250127210837253.webp]]
 
3. See the commits.
```
cd Website

separator="======================================="; for i in $(ls); do printf "\n\n$separator\n\033[4;1m$i\033[0m\n$(cat $i/commit-meta.txt)\n"; done; printf "\n\n$separator\n\n\n"
```

![[The Wonders of Git-20250127211555229.webp]]

**Next step: ** [[Website Code Analysis]]

