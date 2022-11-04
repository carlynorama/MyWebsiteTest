# Setting up a Publish Website on Github Pages example


## Resources
- https://github.com/JohnSundell/Publish
- https://www.createwithswift.com/static-site-generation-with-swift-using-publish/
- https://niazoff.com/posts/hello-world/
- https://swiftunwrap.com/article/building-blog-swift-publish/
- https://siddarthkalra.github.io/articles/2020-12-20-migrated-to-publish/
- https://danijelavrzan.com/posts/2022/08/publish-deploy-to-github/
- https://stackoverflow.com/questions/69577518/github-action-to-copy-specific-folders-from-one-branch-to-another-in-the-same-re/69578322#69578322
- https://stackoverflow.com/questions/7130850/how-can-i-move-all-git-content-one-level-up-in-the-folder-hierarchy

## Site Set up

### Make sure XCode / XCode CLT are ready to go
XCode command line tools must be installed, up to date, and the tools and the version of Xcode you want to use have to be pointed at each other. 

Xcode > Preferences > Locations > Command Line Tools will let you know if that is all set up. 

If you run into trouble the command line way to delete everything and start from scratch is:

```
$ softwareupdate --history
$ sudo rm -rf /Library/Developer/CommandLineTools # delete teh current command line tools
$ sudo xcode-select --install                     # reinstall CLT
$ sudo xcode-select -s </path/to/your/Xcode.app>  # make sure the CLT and Xcode are linked
$ sudo xcode-select -p                            # confirm it worked
```

### Clone and Build
As all the tutorials say, the easiest way to make a new site is to use the CLI tools built into the Publish package. It will set up everything ready to go:

```
$ git clone https://github.com/JohnSundell/Publish.git
$ cd Publish
$ make
$ mkdir MyWebsite
$ cd MyWebsite
$ publish new
$ open Package.swift
```

### Build & Preview Website

There is the option to `publish run` but I prefer using `CMD-R` in the Xcode project while running a python server pointed at the Output folder.

```
cd <location>
python3 -m http.server
```

### Deploy

Using github pages with the project in main, and the site in gh-pages. There is a CLI tool built into `Publish` but I am using a github action '/main/.github/workflows/move-static-files.yml'

- on publish of changes to Output folder in main
- sync the contents to the root directory of the gh-pages branch
- delete the moved Output directory. 

```
          files=$(find $SRC_FOLDER_PATH -type f) # get the file list
          git config --global user.name 'GitHub Action'
          git config --global user.email 'action@github.com'
          git fetch                         # fetch branches
          git checkout $TARGET_BRANCH       # checkout to your branch
          git checkout ${GITHUB_REF##*/} -- $files # copy files from the source branch
          rsync -avh --progress $SRC_FOLDER_PATH/ .
          rm -rf $SRC_FOLDER_PATH
          git add -A
          git diff-index --quiet HEAD ||  git commit -am "deploy files"  # commit to the repository (ignore if no modification)
          git push origin $TARGET_BRANCH # push to remote branch
```
