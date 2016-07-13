+++
date = "2015-10-29T23:33:18-04:00"
tags = ["programming","gpg","leiningen","clojure","git","deployment","releases"]
title = "GPG and Leiningen"

+++

Every time I go to setup a new machine or even a new code repository, I fight the GPG and Leiningen deployment fight. I'm going to finally write it down so I don't forget.

## GPG

On Mac OSX, install both [GPG Tools](https://gpgtools.org/) and gpg-agent via Homebrew. Generate a new key pair (GPG Tools will prompt you), push the public key to GPG servers, and then immediately generate a revocation certificate and store in a safe place. If you have previous keys to revoke, do so now by importing the revocation certificates into GPG Tools and then push your (now locally-revoked) public key to GPG servers to propagate the revocation.

You shouldn't have to do any special gpg-agent setup. Run it once with `gpg-agent` to make sure it's running.

Make sure you log into [Clojars](clojars.org) and update your public key information in your profile to use your new public key.

## Git

Leiningen will try to sign the Git tags you create if you use `lein release `. If you have multiple keys imported (likely), you'll need to make sure the correct one is stored as your `user.signingkey` entry in your global `~/.gitconfig`. In my case, I have my settings stored in VCS under a `~/config` folder, so make the changes there:

    [user]
            signingkey = ABCDEFG

## Leiningen

Leiningen pulls all this together. To cut a release of a project that uses Leiningen, Leiningen needs to be able to:

 1. Sign the Git tag it makes for your released version
 2. Sign the artifacts it produces (JAR, pom.xml, etc.)
 3. Push to the specified repository

The first two involve having GPG installed, a key-pair generated, and the public key pushed to GPG servers. The third step requires credentials for the Maven repository itself, which are distinct from your GPG credentials.

For deployment to Clojars, a `:username` and `:password` is required and is your username and password you use to log into the Clojars UI. Go into Clojars and confirm you know what these are, and while you're there double-check that the correct GPG public key is in your profile.

The simplest way to set this up is to add the following to your project's `project.clj` file:

    :deploy-repositories [["releases" {:url "https://clojars.org/repo"
                                       :creds :gpg}]]

And then add a `~/.lein/credentials.clj` file that looks like:

    {#"https://clojars.org/repo"
     {:username "username" :password "password"}}

_N.B: My credentials file duplicates this value for entries of `#"releases"` and `#"clojars"` as well, since I wasn't sure how it was matching the credentials. If no credentials are found when you cut a release, try that._

Now encrypt your credentials file and remove the original unencrypted file:

    cd ~/.lein
    gpg --default-recipient-self -e credentials.clj > credentials.clj.gpg
    rm credentials.clj

Now test that Leiningen will be able to decrypt your credentials file during release:

    gpg --quiet --batch --decrypt ~/.lein/credentials.clj.gpg

If that prints out the content of your credentials file, you're good to go.

Now in your project run:

    lein release [level]

Whatever the current `x.y.z-SNAPSHOT` is, `-SNAPSHOT` will be removed and `x.y.z` is what will be deployed right now. After deployment, the `level` you specified in the `lein release` invocation will determine what the _next_ SNAPSHOT looks like, by incrementing the number appropriate to the level chosen (uses semver, see `lein help deploying` for more details).
