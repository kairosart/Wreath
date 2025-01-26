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

- **Finder** can be used to search the internet for sites with exposed `.git` directories. This is significantly less useful to an ethical hacker, although may have applications in bug bounty programmes