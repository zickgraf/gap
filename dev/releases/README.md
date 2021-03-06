# How To Make a GAP Release for version Z.X.Y
 
**This release process assumes the following**
- A stable branch of GAP has been identified or agreed upon. This branch will be identified here as `stable-Z.X.Y` and the release notes in `CHANGES.md` have been created.
- You run these scripts in the root directory of a clone of the [GAP repository](https://github.com/gap-system/gap)
- You have compiled this clone of GAP
- You have a clone of the [GapWWW repository](https://github.com/gap-system/GapWWW)

## Dependencies
Before starting the release process, the scripts have the following dependencies. Make sure you have the following installed and up to date
- All tools required to build GAP (as outlined in the GAP root `README.md`)
- `git` command line tool
- `curl` command line tool
- Python (version >= 3.6)
- Several python modules (e.g. installed using `pip3` (`pip3 install <MODULENAME>`)
  - `PyGithub`
  - `requests`
  - `python-dateutil`

## Release Process -- The quick guide

You need a GitHub access token, which the script uses to authenticate with GitHub, so that it gets permission to upload files for you; see section [GitHub access token](#github-access-token) below. 

If the GitHub token is in an environment variable called `GITHUB_TOKEN` then nothing needs to be done. 
Otherwise a flag containing the token is needed when running `update_website.py`.


1. In the terminal, change to the root directory of your clone of the GAP repository.
2. Create an annotated tag for the release in git (using command line).
    ```
    git tag -m "Version Z.X.Y" vZ.X.Y
    git push --tags
    ```  
    Note that `Z` will most likely be 4.
3. By default pushing the tag will trigger the GitHub action
   [release](https://github.com/gap-system/gap/actions/workflows/release.yml),
   which creates the release archives, creates a release on GitHub and uploads
   the archives as assets.
4. Change to the root directory of your clone of the `gap-system/GapWWW` repository.
5. Run `update_website.py` in there.
6. [optional] Remove the `tmp` directories (in GapWWW and gap directories).

## Release Process -- The more detailed guide

If the GitHub token is in an ENVIRONMENT variable called `GITHUB_TOKEN` then nothing needs to be done. 
Otherwise a flag containing the token is needed when running `make_github_release.py`.


1. In the terminal, change to the root directory of your clone of the GAP repository.
    This should be obvious why
2. Create an annotated tag for the release in git (using command line)
    ```
    git tag -m "Version Z.X.Y" vZ.X.Y
    git push --tags
    ```  
    Note that `Z` will most likely be 4.
3. By default pushing the tag will trigger the GitHub action
   [release](https://github.com/gap-system/gap/actions/workflows/release.yml),
   which runs the following steps:
   1. Run `make_archives.py`.
      - Exports repository content into new tmp directory `tmp/` via `git archive`.
      - Makes and configures GAP to check that it is available (and this is needed for the manuals).
      - Fetches the pkg tar ball.
      - Builds the manuals.
      - Cleans everything up.
      - Builds the tar ball(s) and checksum files.
      - Creates a `package-infos.json.gz` which contains the metadata of all
        distributed packages.
      - Writes a file `MANIFEST` which contains a list of all created archives.
   2. Run `make_github_release.py vZ.X.Y tmp/`, where `tmp/` is the path to the
      temporary directory created by `make_archives.py`.
      - Creates the release on GitHub which matches the tag.
      - Uploads the archives listed in the `MANIFEST` file as assets.
4. Change to the root directory of your clone of the `gap-system/GapWWW` repository. 
   This should be obvious why.
5. Run `update_website.py` in there.
   - Fetches the release assets, extracts and configures/builds GAP in a tmp directory.
   - Extracts info from the built and rewrites various YAML files.
   - Extracts info about packages and updates YAML files.
   - Commits, pushes and creates pull request to GapWWW.
6. [optional] Remove the `tmp` directories (in GapWWW and gap directories).


## GitHub access token

The `make_github_release.py` script needs limited write access to the GAP repository
in order to upload the release archives for you. In order to do this, the
scripts needs to authenticate itself with GitHub, for which it needs a
so-called "personal access token". You can generate such a token as follows
(see also <https://help.github.com/articles/creating-an-access-token-for-command-line-use>).

1. Go to <https://github.com/settings/tokens>.

2. Click **Generate new token**.

3. Select the scope "public_repo", and give your token a descriptive name.

4. Click **Generate token** at the bottom of the page.

5. Copy the token to your clipboard. For security reasons, after you navigate
   off the page, you will not be able to see the token again. You therefore
   should store it somewhere, e.g. with option 3 in the following list.
