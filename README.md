# Latex docker container

## What

TexLive docker management

## Why

- Simple. Use dockerized commands just like normal: `pdflatex main.tex`
- Versioned.  Each project uses their own packages.  No conflicts
- Maintainable. You can add updates to the base without losing your changes
- Shareable.  Your project folder contains how to build your documents.
- Secure.  You decide what is in the Docker - no reliance on me

## Use

### Step 1/new - For new projects

Fork this repository, rename it to what you want, and clone it locally.

### Step 1/used - For existing projects

If you want to use in an existing latex project, or update an existing project with the new base (this does not effect your customizations):

In the root of your latex project, copy the following:
```bash
curl -L https://github.com/wmacevoy/latex-docker/tarball/master | tar -xzv --wildcards '*/dockers' '*/bin' --strip-components=1 -f latex-docker
```
You should be able to do this anytime you want to encorporate an update without harming any of your files, including your custom latex docker in `dockers/latex/Dockerfile`.

This does over-write the following files:

    bin/latex-docker
    bin/latex-docker-command
    bin/latex-docker-context
    bin/latex-docker-rebuild
    bin/latex-docker-setup
    bin/latex-docker-setup-base
    bin/latex-docker-uncontext
    bin/latexdockercmd.sh
    bin/latexdockerdaemon.sh
    bin/latexdockerdaemoncmd.sh
    dockers/latex-base/Dockerfile
    dockers/latex/Dockerfile.* (example templates)
    dockers/latex/.gitignore

### Step 2 - context

The commands in the bin folder can be executed explicitly from any working directory.
However the project bin is added to the front of your PATH with [**NOTICE DOT**]
```bash
. bin/latex-docker-context
```
in a terminal.
This command removes any occurances of the path first, so you can source context repeatedly.

Sourcing uncontext,
```bash
. bin/latex-docker-uncontext
```
removes it.

### Step 3 - setup base

After untarring/forking, build the base docker with
```bash
bin/latex-docker-setup-base
```
Builds base image (around 20 min on my computer) in docker.
If you don't already have one, this creates a default scheme-full (5+GB, 2+ hours to build) latex Dockerfile in `dockers/latex/Dockerfile`.
It does not build it in this step (you can customize it in the next step).

This means if you want to just get a cup of coffee and not think about it, you can skip the customize step and you will have a full texlive to work from.

### Step 4 - customize (optional, but very good idea)

Edit the `dockers/latex/Dockerfile` file to decide what you want.  You don't have to get it right the first time.

**DO NOT** change the first FROM line - this was written specifically by setup-base so your dockers are project specific - they are named according to the folder of the project they are in.

It can be tempting to just leave the `scheme-full` version.  However, it will be painful to get the gigantic (5+GB, 2+ hour) full texlive distribution which all your collaborators will have to repeat.  Spending a little time on this will pay off later for a lightweight tex that is easy to share.  I recommend starting from the "minimum" profile.  You can easily repeat your customization until you have everything you need.

### Step 5 - setup

You can repeat this step as often as you like (after any updates to your project dockers).  

```bash
bin/latex-docker-setup
```

This rebuilds the container and creates a number of symbolic links in your `bin` folder to all the texlive commands in your latex container. These symbolic link files are all appended to the .gitignore file in bin if not already present.

With the links you can run texlive commands directly, like

```bash
bin/pdflatex paper.tex
```

Alternatively,

```bash
bin/latex-docker <command...>
```

### Step 6 - customize (again)

If you started from the minimum, you will have customize/setup several times before everything works.  This is a good thing from the point of keeping future installs fast, small, portable and maintainable.  Your future self and collaborators will thank you (well maybe at least not curse you compared to `scheme-full`)

Helpful hints:

- Instead of editing a long `RUN tlmgr install <packages...>`, append a new `RUN` command.  The docker build caches `RUN` steps so you can add packages quickly as you find what your project needs.
- Running `bin/tlmgr search --global --file <thing>` will help you find packages that provide `<thing>` so you can append `RUN tlmgr install <package>` to your docker.
- Once it all works, you can merge it into one RUN line which simplifies the cache for docker.  Sort the packages alphabetically so it is easy for you to see if a package is already in the install list.

### Step 7 - version control

If you are using git, copy the following commands:
```bash
git add bin/.gitignore bin/latex-docker bin/latex-docker-command bin/latex-docker-setup-base bin/latex-docker-setup dockers/latex-base/Dockerfile dockers/latex/Dockerfile dockers/latex/Dockerfile.*[a-z0-9]
git commit -m 'update latex-docker'
```
Alternatively, if you want the latest updates from this master and rebuild everything and commit it,
```bash
bin/latex-docker-rebuild
```

## Use

If your project is using this framework, you and your collaborators should able to

- install docker
- clone your project 
- run `bin/latex-docker-setup` in a bash shell (docker cli shell in windows, just a bash shell for everyone else)
- find something to do for a few hours if you decided on scheme-full

After this, any time they want to work on the project,

- source (**NOTICE DOT**) `. bin/latex-docker-context` to setup their path in the terminal they are using.
- use the texlive commands, `pdflatex paper.tex` etc.

> NOTE: if you're using Windows and your project is outside Users folder, consider to check out [this](https://stackoverflow.com/a/30865500) and [this](https://stackoverflow.com/a/42435077) answers on StackOverflow.

## FAQ

Q. I need to install X.  But after I install X it still seems to be missing.

A. After building the container, its content is fixed.  Append a  `RUN` command in `dockers/latex/Dockerfile` and re-run `bin/latex-docker-setup`.  Use tlmgr to query, but installs happen in the Dockefile.  (This is a good thing, your Dockerfile should have everything to build your project with so it is maintainable).

Q. There are errors about missing fonts / packages.

A. Search for the error on the internet along with "texlive" and perhaps "debian".  The hints that show up try adding to your Dockerfile as a `RUN` command.  If they fail you can just remove them.  Update the commands with `bin/latex-docker-setup`

Q. I created/fixed a Dockerfile for X

A. Fork this, create "dockers/latex/Dockerfile.X" with some helpful comments and make a pull request.

## Thanks

Thanks to Benedikt Lang <github at benediktlang.de> [https://github.com/blang/latex-docker](https://github.com/blang/latex-docker) for the foundations of this project.

## License

See [LICENSE](LICENSE) file.
